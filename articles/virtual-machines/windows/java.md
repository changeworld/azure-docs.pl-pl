---
title: Tworzenie i zarządzanie nimi maszyny wirtualnej platformy Azure przy użyciu języka Java | Dokumentacja firmy Microsoft
description: Użyj Java i usługi Azure Resource Manager, aby wdrożyć maszynę wirtualną i wszystkie dodatkowe zasoby.
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
ms.openlocfilehash: e4c77514bb38b6c3c5766bdb4af22e5271c9bffa
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525234"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Tworzenie i zarządzanie maszynami wirtualnymi systemu Windows na platformie Azure przy użyciu języka Java

[Maszyny wirtualnej Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM), wymaga kilku obsługi zasobów platformy Azure. W tym artykule omówiono tworzenia, zarządzania i usuwania zasobów maszyny Wirtualnej przy użyciu języka Java. Omawiane kwestie:

> [!div class="checklist"]
> * Utwórz projekt Maven
> * Dodaj zależności
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonaj te kroki trwa około 20 minut.

## <a name="create-a-maven-project"></a>Utwórz projekt Maven

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
2. Zainstaluj [Maven](http://maven.apache.org/download.cgi).
3. Utwórz nowy folder i projektu:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Dodaj zależności

1. W obszarze `testAzureApp` folder, otwórz `pom.xml` i Dodaj konfigurację kompilacji, &lt;projektu&gt; umożliwiające tworzenie aplikacji:

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

2. Dodaj zależności, które są niezbędne do dostępu do zestawu SDK Java usługi Azure.

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

Przed rozpoczęciem tego kroku upewnij się, że masz dostęp do [nazwy głównej usługi Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawy, które są potrzebne w kolejnym kroku.

### <a name="create-the-authorization-file"></a>Tworzenie pliku autoryzacji

1. Utwórz plik o nazwie `azureauth.properties` i Dodaj do niej następujące właściwości:

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

    Zastąp **&lt;identyfikator subskrypcji&gt;** z identyfikatorem subskrypcji **&lt;identyfikator aplikacji&gt;** z identyfikatorem aplikacji usługi Active Directory **&lt;klucz uwierzytelniania&gt;** z klucz aplikacji i **&lt;identyfikator dzierżawcy&gt;** przy użyciu identyfikatora dzierżawcy.

2. Zapisz plik.
3. Ustaw zmienną środowiskową o nazwie AZURE_AUTH_LOCATION w powłoki z pełną ścieżką do pliku uwierzytelniania.

### <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz `App.java` plików w obszarze `src\main\java\com\fabrikam` i upewnij się, że znajduje się na początku tej instrukcji pakietu:

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

2. Aby utworzyć poświadczenia usługi Active Directory, które są potrzebne do tworzenia żądań, Dodaj ten kod do głównej metody klasy aplikacji:
   
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

Określ wartości dla aplikacji i utworzyć grupę zasobów, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Tworzenie zestawu dostępności

[Zestawy dostępności](tutorial-availability-sets.md) ułatwienia obsługi maszyn wirtualnych używanych przez aplikację.

Aby utworzyć zbiór dostępności, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Utwórz publiczny adres IP

A [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) jest potrzebne do komunikowania się z maszyną wirtualną.

Aby utworzyć publiczny adres IP dla maszyny wirtualnej, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Utwórz sieć wirtualną

Maszyna wirtualna musi należeć do podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

Aby utworzyć podsieć i sieć wirtualną, Dodaj ten kod do bloku try w głównej metody:

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

### <a name="create-the-network-interface"></a>Tworzenie interfejsu sieciowego

Maszyna wirtualna musi komunikować się w sieci wirtualnej do interfejsu sieciowego.

Aby utworzyć interfejsu sieciowego, Dodaj ten kod do bloku try w głównej metody:

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

Teraz, gdy utworzono pomocnicze zasoby, można utworzyć maszyny wirtualnej.

Aby utworzyć maszynę wirtualną, Dodaj ten kod do bloku try w głównej metody:

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
> W tym samouczku tworzy maszynę wirtualną z wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej o wybieraniu innych obrazów, zobacz [Nawigacja i wybierz obrazów maszyny wirtualnej platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Jeśli chcesz użyć istniejącego dysku zamiast obrazu z witryny marketplace, użyj tego kodu: 

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

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można utworzyć kod do automatyzowania zadań powtarzających się lub złożonych.

Gdy trzeba wykonywać żadnych czynności z maszyną Wirtualną, należy uzyskać wystąpienie. Dodaj ten kod do bloku try głównej metody:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Uzyskiwanie informacji o Maszynie wirtualnej

Aby uzyskać informacje o maszynie wirtualnej, Dodaj ten kod do bloku try w głównej metody:

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

Należy zatrzymać maszynę wirtualną i zachować wszystkie jego ustawienia, ale nadal naliczane za jej lub można zatrzymać maszyny wirtualnej i jej cofnąć. Po cofnięciu przydziału maszyny wirtualnej, wszystkie zasoby skojarzone z nim są również zakończenia deallocated i rozliczeń dla niego.

Aby zatrzymać maszynę wirtualną bez dealokowanie go, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Jeśli chcesz Cofnij Przydział maszyny wirtualnej, należy zmienić wywołanie PowerOff ten kod:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Uruchom maszynę Wirtualną

Aby uruchomić maszynę wirtualną, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Zmień rozmiar maszyny Wirtualnej

Wiele aspektów wdrożenia należy uwzględnić przy podejmowaniu decyzji o rozmiarze dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](sizes.md).  

Aby zmienić rozmiar maszyny wirtualnej, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Dodaj dysk danych do maszyny Wirtualnej

Aby dodać dysk z danymi do maszyny wirtualnej, która wynosi 2 GB, rozmiar, ma numer LUN 0 i buforowania typu ReadWrite, Dodaj ten kod do bloku try w głównej metody:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ naliczane są opłaty za zasoby używane na platformie Azure, zawsze jest dobrym rozwiązaniem, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyn wirtualnych i pomocnicze zasoby, musisz wykonać będzie usunąć grupę zasobów.

1. Aby usunąć grupę zasobów, Dodaj ten kod do bloku try w głównej metody:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Zapisz plik App.java.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Zakończ powinno zająć około pięciu minut dla tej aplikacji konsoli uruchomić zupełnie od początku.

1. Aby uruchomić aplikację, należy użyć tego polecenia Maven:

    ```
    mvn compile exec:java
    ```

2. Przed naciśnięciem przycisku **Enter** zacząć usuwanie zasobów może potrwać kilka minut, aby zweryfikować utworzenie zasobów w portalu Azure. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.


## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o korzystaniu z [bibliotek platformy Azure dla języka Java](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

