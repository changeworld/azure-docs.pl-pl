---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure - PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przypisać wiele adresów IP do maszyny wirtualnej przy użyciu programu PowerShell | Menedżer zasobów.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: jdial;annahar
ms.openlocfilehash: 4c74833933642ec67bdd2a77d073b083d54a3038
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602844"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Przypisz wielu adresów IP do maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule opisano sposób tworzenia maszyny wirtualnej (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu programu PowerShell. Nie można przypisać wiele adresów IP do zasobów została utworzona za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [zrozumieć modele wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Utwórz maszynę Wirtualną z wielu adresów IP

Czynności, które wykonują wyjaśniono, jak utworzyć przykładowy maszyny Wirtualnej z wielu adresów IP, zgodnie z opisem w scenariuszu. Zmienianie wartości zmiennych, co jest wymagane dla implementacji.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze programu PowerShell zainstalowany i skonfigurowany, wykonaj kroki [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) artykułu.
2. Zaloguj się do konta z `Connect-AzureRmAccount` polecenia.
3. Zastąp *myResourceGroup* i *westus* przy użyciu nazwy i lokalizacji wybrane. Utwórz grupę zasobów. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

    ```powershell
    $RgName   = "MyResourceGroup"
    $Location = "westus"

    New-AzureRmResourceGroup `
    -Name $RgName `
    -Location $Location
    ```

4. Utwórz sieć wirtualną (VNet) i podsieci w tej samej lokalizacji co grupa zasobów:

    ```powershell
    
    # Create a subnet configuration
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name MySubnet `
    -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $VNet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyVNet `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $subnetConfig

    # Get the subnet object
    $Subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
    ```

5. Utwórz grupę zabezpieczeń sieci (NSG) i zasady. Grupa NSG zabezpiecza maszyny Wirtualnej za pomocą reguł ruchu przychodzącego i wychodzącego. W tym przypadku jest tworzona reguła ruchu przychodzącego dla portu 3389, która umożliwia nawiązywanie przychodzących połączeń pulpitu zdalnego.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzureRmNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Zdefiniuj podstawową konfigurację protokołu IP dla karty sieciowej. Zmień 10.0.0.4 na prawidłowy adres w podsieci, w której zostały utworzone, jeśli nie używasz wartość zdefiniowana wcześniej. Przed przypisaniem statycznego adresu IP, zalecane jest, że należy najpierw upewnij się, że nie jest już używana. Wprowadź polecenie `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Jeśli adres jest dostępny, zwraca dane wyjściowe *True*. Jeśli nie jest dostępna, zwraca dane wyjściowe *False* oraz listę adresów, które są dostępne. 

    W poniższych poleceniach **Zamień < Zamień z your unikatowy nazwa-> unikatowa nazwa DNS do użycia.** Nazwa musi być unikatowa w publicznych adresów IP w obrębie regionu platformy Azure. Jest to parametr opcjonalny. Można usunąć, jeśli chcesz się połączyć z maszyną wirtualną za pomocą publicznego adresu IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Po przypisaniu wielu konfiguracji adresów IP do karty Sieciowej, należy przypisać jedną konfigurację jako *— podstawowy*.

    > [!NOTE]
    > Publiczne adresy IP ma nominalnego opłatą. Aby dowiedzieć się więcej o cenach adresu IP, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje ograniczona liczba publicznych adresów IP, których można użyć w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

7. Zdefiniuj dodatkowej konfiguracji adresów IP dla karty sieciowej. Można dodawać i usuwać konfiguracje w razie potrzeby. Każdej konfiguracji adresu IP musi mieć przypisany prywatny adres IP. Każdej konfiguracji opcjonalnie może mieć przypisany jeden publiczny adres IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzureRmPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP ddress to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Utwórz kartę Sieciową i skojarz trzech konfiguracji IP do niej:

    ```powershell
    
    $NIC = New-AzureRmNetworkInterface `
    -Name MyNIC `
    -ResourceGroupName $RgName `
    -Location $Location `
    -NetworkSecurityGroupId $NSG.Id `
    -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```

    >[!NOTE]
    >Chociaż wszystkie konfiguracje są przypisane do jednej karty Sieciowej, w tym artykule, można przypisać wielu konfiguracji adresów IP do każdej karty Sieciowej podłączony do maszyny Wirtualnej. Aby dowiedzieć się, jak utworzyć Maszynę wirtualną z wieloma kartami sieciowymi, przeczytaj [tworzenia maszyn wirtualnych z wieloma kartami sieciowymi](../virtual-machines/windows/multiple-nics.md) artykułu.

9. Tworzenie maszyny Wirtualnej, wprowadzając następujące polecenia:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a sername and password for the VM you're reating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzureRmVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzureRmVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Dodaj prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki odpowiednie dla systemu operacyjnego w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Nie dodawaj publiczne adresy IP do systemu operacyjnego.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Prywatne i publiczne adresy IP można dodać do interfejsu sieci platformy Azure, wykonując kroki, które należy wykonać. Przykłady w poniższych sekcjach założono, że już istnieje maszyna wirtualna z tych trzech konfiguracji adresu IP, opisane w [scenariusza](#Scenario) w tym artykule, ale nie jest to wymagane wykonanie.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze programu PowerShell zainstalowany i skonfigurowany, wykonaj kroki [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) artykułu.
2. Zmień nazwę karty Sieciowej, aby dodać adres IP do grupy zasobów i lokalizacja, którą karta sieciowa istnieje w "wartości" $Variables następujące:

    ```powershell
    $NicName  = "MyNIC"
    $RgName   = "MyResourceGroup"
    $Location = "westus"
    ```

    Jeśli nie znasz nazwy karty sieciowej, aby zmienić, wpisz następujące polecenia, następnie zmienić wartości zmiennych poprzedniego:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Utwórz zmienną i ustaw ją istniejącej karty NIC, wpisując następujące polecenie:

    ```powershell
    $MyNIC = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName
    ```
4. W poniższych poleceniach zmienić *MyVNet* i *MySubnet* do nazwy sieci wirtualnej i karty interfejsu Sieciowego jest podłączony do podsieci. Wprowadź poleceń, aby pobrać obiekty sieci wirtualnej i podsieci, karta sieciowa jest połączona z:

    ```powershell
    $MyVNet = Get-AzureRMVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
    $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
    ```
    Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, karta sieciowa jest połączona z, wprowadź następujące polecenie:
    ```powershell
    $MyNIC.IpConfigurations
    ```
    W wynikach wyszukiwania tekstu, podobnie jak w poniższym przykładzie danych wyjściowych:
    
    ```
    "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
    ```
    W tym danych wyjściowych *MyVnet* jest sieci wirtualnej i *MySubnet* jest karta sieciowa jest podłączony do podsieci.

5. Wykonaj kroki jednego z następujących sekcji, w zależności od wymagań:

    **Dodaj prywatnego adresu IP**

    Aby dodać prywatnego adresu IP do karty Sieciowej, należy utworzyć konfigurację protokołu IP. Poniższe polecenie tworzy konfiguracji ze statycznym adresem IP 10.0.0.7. Określanie statycznego adresu IP, musi być nieużywany adres podsieci. Zaleca się przetestowanie adres, aby upewnić się, jest dostępna, wprowadzając `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` polecenia. Jeśli adres IP jest dostępny, zwraca dane wyjściowe *True*. Jeśli nie jest dostępna, zwraca dane wyjściowe *False*oraz listę adresów, które są dostępne.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Tworzenie konfiguracji tyle, ile potrzebujesz, przy użyciu nazwy konfiguracji unikatowy i prywatnych adresów IP (w przypadku konfiguracji statycznych adresów IP).

    Dodaj prywatnego adresu IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki odpowiednie dla systemu operacyjnego w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu.

    **Dodaj publiczny adres IP**

    Publiczny adres IP jest dodawany przez skojarzenie publicznego zasobu adresu IP do nowej konfiguracji IP lub istniejącej konfiguracji adresu IP. Wykonaj kroki opisane w sekcji, które należy wykonać, ile potrzebujesz.

    > [!NOTE]
    > Publiczne adresy IP ma nominalnego opłatą. Aby dowiedzieć się więcej o cenach adresu IP, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje ograniczona liczba publicznych adresów IP, których można użyć w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    - **Skojarz zasób publicznego adresu IP nowej konfiguracji adresu IP**
    
        Po dodaniu publicznego adresu IP w nowej konfiguracji adresu IP, musisz również dodać prywatnego adresu IP, ponieważ wszystkie konfiguracje adresów IP muszą mieć prywatnego adresu IP. Możesz dodać istniejący zasób publiczny adres IP lub Utwórz nową. Aby utworzyć nową, wprowadź następujące polecenie:
    
        ```powershell
        $myPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "myPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location `
        -AllocationMethod Static
        ```

        Aby utworzyć nową konfigurację adresu IP za pomocą statycznego prywatnego adresu IP oraz skojarzonych z nimi *myPublicIp3* publicznego adresu IP adresów zasobów, wprowadź następujące polecenie:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig `
        -Name IPConfig-4 `
        -NetworkInterface $myNIC `
        -Subnet $Subnet `
        -PrivateIpAddress 10.0.0.7 `
        -PublicIpAddress $myPublicIp3
        ```

    - **Skojarz zasób publicznego adresu IP do istniejącej konfiguracji adresu IP**

        Zasób publicznego adresu IP może być skojarzony tylko konfiguracją protokołu IP, który nie ma jeszcze skojarzone. Można określić, czy konfiguracja IP ma skojarzone publicznego adresu IP, wprowadzając następujące polecenie:

        ```powershell
        $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        Zostaną wyświetlone dane wyjściowe podobne do następującego:

        ```     
        Name       PrivateIpAddress PublicIpAddress                                           Primary
        
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False
        ```

        Ponieważ **publicznego adresu IP** kolumny dla *IpConfig 3* jest puste, żaden z zasobów publiczny adres IP jest obecnie z nią skojarzona. Dodaj istniejący zasób publicznego adresu IP do IpConfig 3 lub wprowadź następujące polecenie, aby go utworzyć:

        ```powershell
        $MyPublicIp3 = New-AzureRmPublicIpAddress `
        -Name "MyPublicIp3" `
        -ResourceGroupName $RgName `
        -Location $Location -AllocationMethod Static
        ```

        Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP do istniejącej konfiguracji IP o nazwie *IpConfig 3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig `
        -Name IpConfig-3 `
        -NetworkInterface $mynic `
        -Subnet $Subnet `
        -PublicIpAddress $myPublicIp3
        ```

6. Ustaw kartę Sieciową przy użyciu nowej konfiguracji adresu IP, wprowadzając następujące polecenie:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $MyNIC
    ```

7. Wyświetl prywatnych adresów IP i zasoby adresów publicznych adresów IP przypisanych do karty Sieciowej, wprowadzając następujące polecenie:

    ```powershell   
    $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Dodaj prywatnego adresu IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki odpowiednie dla systemu operacyjnego w [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) sekcji tego artykułu. Publiczny adres IP nie należy dodawać do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
