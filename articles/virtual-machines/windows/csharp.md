---
title: Tworzenie i zarządzanie nimi w maszynie wirtualnej platformy Azure przy użyciu języka C# | Dokumentacja firmy Microsoft
description: Używanie języka C# i usługi Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie jej zasoby pomocnicze.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: ce05d097aa69aa1aadb8450e40722448bc5a7de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402045"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Tworzenie i zarządzanie nimi Windows maszyn wirtualnych na platformie Azure przy użyciu języka C# #

[Maszyny wirtualnej platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) wymaga kilku pomocnicze zasoby platformy Azure. W tym artykule opisano tworzenie i usuwanie zasobów maszyny Wirtualnej przy użyciu języka C#, zarządzanie nimi. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Zainstaluj pakiet
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonaj te czynności trwa około 20 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze nie, zainstaluj [programu Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz **programowanie aplikacji klasycznych dla platformy .NET** na stronie obciążeń, a następnie kliknij **zainstalować**. Podsumowując, możesz zobaczyć, że **narzędzi programistycznych .NET Framework 4 4.6** jest automatycznie wybrana. Jeśli zainstalowano już program Visual Studio, możesz dodać obciążenia platformy .NET, przy użyciu Uruchom okno programu Visual Studio.
2. W programie Visual Studio kliknij kolejno pozycje **Plik** > **Nowy** > **Projekt**.
3. W **szablony** > **Visual C#**, wybierz opcję **Aplikacja konsoli (.NET Framework)**, wprowadź *myDotnetProject* dla nazwy Projekt, wybierz lokalizację projektu, a następnie kliknij **OK**.

## <a name="install-the-package"></a>Zainstaluj pakiet

Pakiety NuGet są najprostszym sposobem zainstalowania bibliotek, które należy zakończyć te kroki. Aby uzyskać bibliotek, które należy w programie Visual Studio, wykonaj następujące kroki:

1. Kliknij przycisk **narzędzia** > **Menedżera pakietów Nuget**, a następnie kliknij przycisk **Konsola Menedżera pakietów**.
2. W konsoli, wpisz następujące polecenie:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do [jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). W kolejnym kroku, należy zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, który należy.

### <a name="create-the-authorization-file"></a>Utwórz plik autoryzacji

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element**, a następnie wybierz pozycję **plik tekstowy** w *elementy Visual C#*. Nadaj plikowi nazwę *azureauth.properties*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj następujące właściwości autoryzacji:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Zastąp **&lt;identyfikator subskrypcji&gt;** przy użyciu identyfikatora subskrypcji **&lt;identyfikator aplikacji&gt;** z aplikacją usługi Active Directory Identyfikator **&lt;klucz uwierzytelniania&gt;** z kluczem aplikacji i **&lt;identyfikator dzierżawy&gt;** z identyfikatorem dzierżawy.

3. Zapisz plik azureauth.properties. 
4. Ustaw zmienną środowiskową w Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżkę do pliku autoryzacji, który został utworzony. Na przykład można następujące polecenie programu PowerShell:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs w projekcie, który został utworzony, a następnie dodaj je za pomocą instrukcji do istniejących instrukcji w górnej części pliku:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Aby utworzyć klienta zarządzania, Dodaj następujący kod do metody Main:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą być zawarte w [grupy zasobów](../../azure-resource-manager/resource-group-overview.md).

Aby określić wartości dla aplikacji i Utwórz grupę zasobów, Dodaj następujący kod do metody Main:

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Tworzenie zestawu dostępności

[Zestawy dostępności](tutorial-availability-sets.md) ułatwienia obsługi maszyn wirtualnych używanych przez aplikację.

Aby utworzyć zestaw dostępności, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

A [publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) umożliwia komunikację z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, Dodaj następujący kod do metody Main:
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Maszyna wirtualna musi być w podsieci [sieć wirtualna](../../virtual-network/virtual-networks-overview.md).

Aby utworzyć podsieć i sieć wirtualną, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy

Maszyna wirtualna musi przejść do interfejsu sieciowego komunikacji w sieci wirtualnej.

Aby utworzyć interfejs sieciowy, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz, gdy utworzono pomocnicze zasoby, możesz utworzyć maszynę wirtualną.

Aby utworzyć maszynę wirtualną, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Ten samouczek umożliwia utworzenie maszyny wirtualnej z wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania obrazów innych, zobacz [wybierz obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i wiersza polecenia platformy Azure i Navigate](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu z witryny marketplace, należy użyć następującego kodu:

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć kod, aby zautomatyzować powtarzalne lub złożone zadania.

Gdy trzeba wykonywać żadnych czynności z maszyną Wirtualną, należy uzyskać jego wystąpienie:

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie Wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zapewnić jej ustawienia, ale nadal będą naliczane opłaty dla niego lub można zatrzymać maszynę wirtualną i cofnąć jej przydział. Po cofnięciu przydziału maszyny wirtualnej, wszystkie zasoby skojarzone z nim są również zakończenia przydział zostanie cofnięty i rozliczeń dla niego.

Aby zatrzymać maszynę wirtualną bez cofnięcie jej przydziału, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Jeśli chcesz cofnąć alokację maszyny wirtualnej, należy zmienić wywołanie PowerOff ten kod:

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

Można uruchomić maszyny wirtualnej, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>Zmień rozmiar maszyny Wirtualnej

Wiele aspektów wdrożenia należy rozważyć podczas ustawiania rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, Dodaj następujący kod do metody Main:

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodaj dysk danych do maszyny Wirtualnej

Aby dodać dysk danych do maszyny wirtualnej, Dodaj następujący kod do metody Main, aby dodać dysk danych, która ma 2 GB, rozmiar jednostki LUN 0 i typem buforowanie ReadWrite Hanowi:

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ opłaty są naliczane za zasoby używane w systemie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie pomocnicze zasoby, trzeba będzie usunąć grupę zasobów.

Aby usunąć grupę zasobów, Dodaj następujący kod do metody Main:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby zakończyć powinno zająć około pięciu minut, zanim ta aplikacja konsoli uruchomić zupełnie od początku. 

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start**.

2. Przed naciśnięciem **Enter** zacząć usuwanie zasobów może potrwać kilka minut na sprawdzenie tworzeniem zasobów w witrynie Azure portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Kolejne kroki
* Skorzystaj z zalet przy użyciu szablonu, aby utworzyć maszynę wirtualną, korzystając z informacji w [wdrożyć maszynę wirtualną platformy Azure przy użyciu języka C# i szablonu usługi Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure dla platformy .NET](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet).

