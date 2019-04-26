---
title: Tworzenie i zarządzanie nimi w maszynie wirtualnej platformy Azure przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Za pomocą języka Java i usługi Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie jej zasoby pomocnicze.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 816233368c2274e34d2acdd712aed270a5e3bfa5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203928"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Tworzenie i zarządzanie nimi Windows maszyn wirtualnych na platformie Azure przy użyciu języka Java

[Maszyny wirtualnej platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) wymaga kilku pomocnicze zasoby platformy Azure. W tym artykule opisano tworzenie i usuwanie zasobów maszyny Wirtualnej przy użyciu języka Java, zarządzanie nimi. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz projekt narzędzia Maven
> * Dodaj zależności
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonaj te czynności trwa około 20 minut.

## <a name="create-a-maven-project"></a>Utwórz projekt narzędzia Maven

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [Java](https://aka.ms/azure-jdks).
2. Zainstaluj [Maven](https://maven.apache.org/download.cgi).
3. Utwórz nowy folder i projektu:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Dodaj zależności

1. W obszarze `testAzureApp` folder, otwórz `pom.xml` pliku i Dodaj konfigurację kompilacji, aby &lt;projektu&gt; umożliwiające tworzenie aplikacji:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Dodaj zależności, które są wymagane do dostępu do zestawu SDK Java usługi Azure.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Zapisz plik.

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do [jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). W kolejnym kroku, należy zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, który należy.

### <a name="create-the-authorization-file"></a>Utwórz plik autoryzacji

1. Utwórz plik o nazwie `azureauth.properties` i dodaj następujące właściwości do niego:

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

2. Zapisz plik.
3. Ustaw zmienną środowiskową o nazwie AZURE_AUTH_LOCATION w powłoce z pełną ścieżką do pliku uwierzytelniania.

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz `App.java` plik `src\main\java\com\fabrikam` i upewnij się, ta instrukcja pakietu jest u góry:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. W obszarze instrukcji pakietu, dodaj je zaimportować instrukcji:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Aby utworzyć poświadczenia usługi Active Directory, które należy wprowadzić żądań, Dodaj następujący kod do głównej metody klasy aplikacji:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Tworzenie zasobów

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby muszą być zawarte w [grupy zasobów](../../azure-resource-manager/resource-group-overview.md).

Aby określić wartości dla aplikacji i Utwórz grupę zasobów, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Tworzenie zestawu dostępności

[Zestawy dostępności](tutorial-availability-sets.md) ułatwienia obsługi maszyn wirtualnych używanych przez aplikację.

Aby utworzyć zestaw dostępności, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Tworzenie publicznego adresu IP

A [publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) umożliwia komunikację z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

Maszyna wirtualna musi być w podsieci [sieć wirtualna](../../virtual-network/virtual-networks-overview.md).

Aby utworzyć podsieć i sieć wirtualną, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Utwórz interfejs sieciowy

Maszyna wirtualna musi przejść do interfejsu sieciowego komunikacji w sieci wirtualnej.

Aby utworzyć interfejs sieciowy, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz, gdy utworzono pomocnicze zasoby, możesz utworzyć maszynę wirtualną.

Aby utworzyć maszynę wirtualną, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Ten samouczek umożliwia utworzenie maszyny wirtualnej z wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania obrazów innych, zobacz [wybierz obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i wiersza polecenia platformy Azure i Navigate](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu z witryny marketplace, należy użyć następującego kodu: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć kod, aby zautomatyzować powtarzalne lub złożone zadania.

Gdy trzeba wykonywać żadnych czynności z maszyną Wirtualną, należy uzyskać jego wystąpienie. Dodaj następujący kod do bloku try głównej metody:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie Wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zapewnić jej ustawienia, ale nadal będą naliczane opłaty dla niego lub można zatrzymać maszynę wirtualną i cofnąć jej przydział. Po cofnięciu przydziału maszyny wirtualnej, wszystkie zasoby skojarzone z nim są również zakończenia przydział zostanie cofnięty i rozliczeń dla niego.

Aby zatrzymać maszynę wirtualną bez cofnięcie jej przydziału, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Jeśli chcesz cofnąć alokację maszyny wirtualnej, należy zmienić wywołanie PowerOff ten kod:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

Można uruchomić maszyny wirtualnej, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Zmień rozmiar maszyny Wirtualnej

Wiele aspektów wdrożenia należy rozważyć podczas ustawiania rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodaj dysk danych do maszyny Wirtualnej

Aby dodać dysk danych do maszyny wirtualnej, która ma 2 GB, rozmiar, ma numer LUN 0, lub typ pamięci podręcznej odczytu i zapisu, Dodaj następujący kod do bloku try w metodzie głównej:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ opłaty są naliczane za zasoby używane w systemie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie pomocnicze zasoby, trzeba będzie usunąć grupę zasobów.

1. Aby usunąć grupę zasobów, Dodaj następujący kod do bloku try w metodzie głównej:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Zapisz plik App.java.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby zakończyć powinno zająć około pięciu minut, zanim ta aplikacja konsoli uruchomić zupełnie od początku.

1. Aby uruchomić aplikację, użyj tego polecenia narzędzia Maven:

    ```
    mvn compile exec:java
    ```

2. Przed naciśnięciem **Enter** zacząć usuwanie zasobów może potrwać kilka minut na sprawdzenie tworzeniem zasobów w witrynie Azure portal. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure dla języka Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

