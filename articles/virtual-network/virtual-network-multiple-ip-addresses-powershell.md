---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure — Program PowerShell | Dokumenty firmy Microsoft
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej za pomocą programu PowerShell. | Menedżer zasobów
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: a8bd4e4779d94cfc22ac7726c9746fe755764033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279574"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych za pomocą programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pośrednictwem modelu wdrażania usługi Azure Resource Manager przy użyciu programu PowerShell. Wielu adresów IP nie można przypisać do zasobów utworzonych za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej o modelach wdrażania platformy Azure, przeczytaj artykuł [Zrozumieć modele wdrażania.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Kroki, które należy wykonać wyjaśnić, jak utworzyć przykładową maszynę wirtualną z wieloma adresami IP, zgodnie z opisem w scenariuszu. Zmień wartości zmiennych zgodnie z wymaganiami implementacji.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze zainstalowanego i skonfigurowany program PowerShell, wykonaj kroki opisane w [artykule Jak zainstalować i skonfigurować program Azure PowerShell.](/powershell/azure/overview)
2. Zaloguj się na `Connect-AzAccount` swoje konto za pomocą polecenia.
3. Zastąp *myResourceGroup* i *westus* wybraną nazwą i lokalizacją. Utwórz grupę zasobów. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Utwórz sieć wirtualną i podsieć w tej samej lokalizacji co grupa zasobów:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Utwórz sieciową grupę zabezpieczeń (NSG) i regułę. Grupa sieciowej sieciowej zabezpiecza maszynę wirtualną przy użyciu reguł przychodzących i wychodzących. W tym przypadku jest tworzona reguła ruchu przychodzącego dla portu 3389, która umożliwia nawiązywanie przychodzących połączeń pulpitu zdalnego.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Zdefiniuj podstawową konfigurację adresu IP karty sieciowej. Zmień wartość 10.0.0.4 na prawidłowy adres w utworzonej podsieci, jeśli wartość zdefiniowana wcześniej nie została zdefiniowana. Przed przypisaniem statycznego adresu IP zaleca się, aby najpierw potwierdzić, że nie jest on jeszcze używany. Wprowadź polecenie `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Jeśli adres jest dostępny, dane wyjściowe zwracają *wartość True*. Jeśli nie jest dostępny, dane wyjściowe zwraca *False* i listę adresów, które są dostępne. 

    W poniższych poleceniach **zamień \<> z unikatową nazwą DNS.** Nazwa musi być unikatowa dla wszystkich publicznych adresów IP w regionie platformy Azure. Jest to parametr opcjonalny. Można go usunąć, jeśli chcesz połączyć się z maszyną wirtualną tylko przy użyciu publicznego adresu IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Podczas przypisywania wielu konfiguracji IP do karty sieciowej jedna konfiguracja musi być przypisana jako *-Primary*.

    > [!NOTE]
    > Publiczne adresy IP mają symboliczną opłatę. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Zdefiniuj pomocnicze konfiguracje adresów IP dla karty sieciowej. W razie potrzeby można dodawać lub usuwać konfiguracje. Każda konfiguracja IP musi mieć przypisany prywatny adres IP. Każda konfiguracja może opcjonalnie mieć przypisany jeden publiczny adres IP.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Utwórz kartę sieciową i skojarz z nią trzy konfiguracje adresów IP:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Chociaż wszystkie konfiguracje są przypisane do jednej karty sieciowej w tym artykule, można przypisać wiele konfiguracji IP do każdej karty sieciowej dołączonej do maszyny Wirtualnej. Aby dowiedzieć się, jak utworzyć maszynę wirtualną z wieloma kartami sieciowym, przeczytaj artykuł [Tworzenie maszyny wirtualnej z wieloma kartami sieciowym.](../virtual-machines/windows/multiple-nics.md)

9. Utwórz maszynę wirtualną, wprowadzając następujące polecenia:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Dodaj prywatne adresy IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dotyczące systemu operacyjnego w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznych adresów IP do systemu operacyjnego.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny Wirtualnej

Możesz dodać prywatne i publiczne adresy IP do interfejsu sieciowego platformy Azure, wykonując następujące kroki. Przykłady w poniższych sekcjach zakładają, że masz już maszynę wirtualną z trzema konfiguracjami IP opisanymi w [scenariuszu](#scenario) w tym artykule, ale nie jest wymagane.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze zainstalowanego i skonfigurowany program PowerShell, wykonaj kroki opisane w [artykule Jak zainstalować i skonfigurować program Azure PowerShell.](/powershell/azure/overview)
2. Zmień "wartości" następujących $Variables na nazwę karty sieciowej, do której chcesz dodać adres IP, oraz grupę zasobów i lokalizację, w której znajduje się karta sieciowa:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Jeśli nie znasz nazwy karty sieciowej, którą chcesz zmienić, wprowadź następujące polecenia, a następnie zmień wartości poprzednich zmiennych:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Utwórz zmienną i ustaw ją na istniejącej karcie sieciowej, wpisując następujące polecenie:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. W poniższych poleceniach zmień *myvnet* i *MySubnet* na nazwy sieci wirtualnej i podsieci, z którymi jest podłączona karta sieciowa. Wprowadź polecenia pobierania obiektów sieci wirtualnej i podsieci, do które jest podłączona karta sieciowa:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Jeśli nie znasz nazwy sieci wirtualnej lub podsieci, z którą jest podłączona karta sieciowa, wprowadź następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   W danych wyjściowych poszukaj tekstu podobnego do następującego przykładowego wyjścia:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    W tym wyjściu *MyVnet* jest siecią wirtualną, a *MySubnet* jest podsiecią, do z którymi jest podłączona karta sieciowa.

5. Wykonaj czynności opisane w jednej z poniższych sekcji w oparciu o wymagania:

   **Dodawanie prywatnego adresu IP**

   Aby dodać prywatny adres IP do karty sieciowej, należy utworzyć konfigurację adresu IP. Następujące polecenie tworzy konfigurację ze statycznym adresem IP 10.0.0.7. Podczas określania statycznego adresu IP musi to być nieużyny adres podsieci. Zaleca się, aby najpierw przetestować adres, aby upewnić `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` się, że jest on dostępny, wprowadzając polecenie. Jeśli adres IP jest dostępny, dane wyjściowe zwracają *wartość True*. Jeśli nie jest dostępny, dane wyjściowe zwraca *False*i listę adresów, które są dostępne.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Utwórz dowolną liczbę konfiguracji, używając unikatowych nazw konfiguracji i prywatnych adresów IP (w przypadku konfiguracji ze statycznymi adresami IP).

   Dodaj prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dotyczące systemu operacyjnego w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule.

   **Dodawanie publicznego adresu IP**

   Publiczny adres IP jest dodawany przez skojarzenie zasobu publicznego adresu IP z nową konfiguracją IP lub istniejącą konfiguracją IP. Wykonaj kroki w jednej z sekcji, które należy wykonać, zgodnie z wymaganiami.

   > [!NOTE]
   > Publiczne adresy IP mają symboliczną opłatę. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj stronę [cennika adresu IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Skojarzenie zasobu publicznego adresu IP z nową konfiguracją adresu IP**

   Za każdym razem, gdy dodajesz publiczny adres IP w nowej konfiguracji IP, należy również dodać prywatny adres IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP. Można dodać istniejący zasób publicznego adresu IP lub utworzyć nowy. Aby utworzyć nową, wprowadź następujące polecenie:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Aby utworzyć nową konfigurację IP ze statycznym prywatnym adresem IP i skojarzonym z nim publicznym zasóbem adresu IP *myPublicIp3,* wprowadź następujące polecenie:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Skojarzenie zasobu publicznego adresu IP z istniejącą konfiguracją adresu IP**

   Publiczny zasób adresu IP może być skojarzony tylko z konfiguracją IP, która nie ma jeszcze skojarzonego. Można określić, czy konfiguracja IP ma skojarzony publiczny adres IP, wprowadzając następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Zostaną wyświetlone dane wyjściowe podobne do następujących:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Ponieważ **PublicIpAddress** kolumna dla *IpConfig-3* jest pusta, nie publiczny adres IP zasób jest obecnie skojarzony z nim. Do protokołu IpConfig-3 można dodać istniejący publiczny zasób adresu IP lub wprowadzić następujące polecenie, aby je utworzyć:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Wprowadź następujące polecenie, aby skojarzyć publiczny zasób adresu IP z istniejącą konfiguracją IP o nazwie *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Ustaw kartę sieciową z nową konfiguracją IP, wprowadzając następujące polecenie:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Wyświetl prywatne adresy IP i publiczne zasoby adresów IP przypisane do karty sieciowej, wprowadzając następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Dodaj prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dotyczące systemu operacyjnego w sekcji [Dodaj adresy IP do systemu operacyjnego maszyny Wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]