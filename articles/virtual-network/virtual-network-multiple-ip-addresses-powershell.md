---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure — PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu programu PowerShell. | Resource Manager
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
ms.author: kumud;annahar
ms.openlocfilehash: f4ecc9a0b41cf3b287f7601101de3aa9d077b0d5
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64730433"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną (VM) za pomocą modelu wdrażania usługi Azure Resource Manager przy użyciu programu PowerShell. Wiele adresów IP nie można przypisać do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [omówienie modeli wdrażania](../resource-manager-deployment-model.md) artykułu.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Tworzenie maszyny Wirtualnej z wieloma adresami IP

Poniższe kroki wyjaśniają, jak Utwórz przykładowy maszyny Wirtualnej z wieloma adresami IP zgodnie z opisem w tym scenariuszu. Zmień wartości zmiennych, co jest wymagane dla implementacji.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze programu PowerShell, zainstalowane i skonfigurowane, wykonaj kroki [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) artykułu.
2. Zaloguj się do Twojego konta za pomocą `Connect-AzAccount` polecenia.
3. Zastąp *myResourceGroup* i *westus* przy użyciu nazwy i lokalizacji wybrane. Utwórz grupę zasobów. Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Utwórz sieć wirtualną (VNet) i podsieci w tej samej lokalizacji co grupa zasobów:

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

5. Utwórz sieciową grupę zabezpieczeń (NSG) i reguły. Sieciowa grupa zabezpieczeń zabezpiecza maszyny Wirtualnej za pomocą reguł ruchu przychodzącego i wychodzącego. W tym przypadku jest tworzona reguła ruchu przychodzącego dla portu 3389, która umożliwia nawiązywanie przychodzących połączeń pulpitu zdalnego.

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

6. Zdefiniuj podstawowa konfiguracja adresu IP dla karty sieciowej. Zmień 10.0.0.4 na prawidłowy adres w podsieci, w której został utworzony, jeśli użyty przez Ciebie wartość zdefiniowana wcześniej. Przed przypisaniem statyczny adres IP, zalecane jest, że należy najpierw upewnij się, że nie jest już używana. Wprowadź polecenie `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Jeśli adres jest dostępny, zwraca dane wyjściowe *True*. Jeśli nie jest dostępna, zwraca dane wyjściowe *False* i listę adresów, które są dostępne. 

    W poniższych poleceniach **Zastąp \<Zastąp-przy użyciu your unikatowy name > na unikatową nazwę DNS do użycia.** Nazwa musi być unikatowa we wszystkich publicznych adresów IP w obrębie regionu platformy Azure. Jest to parametr opcjonalny. Można usunąć, jeśli chcesz się połączyć z maszyną wirtualną przy użyciu publicznego adresu IP.

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

    Po przypisaniu wielu konfiguracji adresu IP do karty Sieciowej w jednej konfiguracji musi być przypisany jako *-głównej*.

    > [!NOTE]
    > Publiczne adresy IP mają nominalnej. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).

7. Zdefiniuj konfiguracje pomocniczych adresów IP dla karty sieciowej. Można dodawać i usuwać konfiguracje zgodnie z potrzebami. Każdej konfiguracji IP musi mieć przypisany prywatny adres IP. Każda konfiguracja mogą opcjonalnie mieć przypisany jeden publiczny adres IP.

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

8. Utwórz kartę Sieciową i skojarz trzech konfiguracji IP do niego:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Chociaż wszystkie konfiguracje są przypisane do jednej karty Sieciowej w tym artykule, wielu konfiguracji adresu IP można przypisać każdą kartą Sieciową dołączoną do maszyny Wirtualnej. Aby dowiedzieć się, jak utworzyć maszynę Wirtualną z wieloma kartami sieciowymi, przeczytaj [Utwórz Maszynę wirtualną z wieloma kartami sieciowymi](../virtual-machines/windows/multiple-nics.md) artykułu.

9. Tworzenie maszyny Wirtualnej, wprowadzając następujące polecenia:

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

10. Dodaj prywatnych adresów IP do systemu operacyjnego maszyny Wirtualnej, wykonując czynności opisane w systemie operacyjnym w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu. Nie należy dodawać publiczne adresy IP do systemu operacyjnego.

## <a name="add"></a>Dodaj adresy IP do maszyny Wirtualnej

Możesz dodać prywatnych i publicznych adresów IP do interfejsu sieci platformy Azure, wykonując poniższe kroki. Przykłady podane w poniższych sekcjach przyjęto założenie, że masz już Maszynę wirtualną przy użyciu trzech konfiguracji adresów IP, opisane w [scenariusza](#scenario) w tym artykule, ale nie jest to wymagane, możesz zrobić.

1. Otwórz wiersz polecenia programu PowerShell i wykonaj pozostałe kroki w tej sekcji w ramach jednej sesji programu PowerShell. Jeśli nie masz jeszcze programu PowerShell, zainstalowane i skonfigurowane, wykonaj kroki [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) artykułu.
2. Zmień nazwę karty Sieciowej, aby dodać adres IP i grupę zasobów i lokalizacji, w której karta sieciowa istnieje w "values" $Variables następujące:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Jeśli nie znasz nazwy karty sieciowej, o których chcesz zmienić, wprowadź następujące polecenia, następnie zmień wartości zmiennych poprzedniej:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Utwórz zmienną i ustawić w niej istniejącą kartę Sieciową, wpisując następujące polecenie:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. W poniższych poleceniach zmienić *MyVNet* i *MySubnet* do nazwy sieci wirtualnej i podsieci, karta sieciowa jest połączona. Wprowadź polecenia, aby pobrać obiekty sieci wirtualnej i podsieci, którą jest połączona karta sieciowa:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Jeśli nie znasz nazwę sieci wirtualnej lub podsieci, którą jest połączona karta sieciowa, wpisz następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   W wynikach wyszukiwania tekstu podobne do następujących przykładowych danych wyjściowych:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    W poniższych danych wyjściowych *MyVnet* jest sieć wirtualna i *MySubnet* podsieć jest połączona karta sieciowa.

5. Wykonaj kroki opisane w jednej z następujących sekcji, w zależności od wymagań:

   **Dodaj prywatny adres IP**

   Aby dodać prywatny adres IP do karty Sieciowej, należy utworzyć konfigurację adresu IP. Następujące polecenie tworzy konfigurację ze statycznym adresem IP 10.0.0.7. Podczas określania statyczny adres IP, musi ona nieużywany adres podsieci. Zaleca się przetestowanie adresu, aby upewnić się, jest on dostępny, wprowadzając `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` polecenia. Jeśli adres IP jest dostępny, zwraca dane wyjściowe *True*. Jeśli nie jest dostępna, zwraca dane wyjściowe *False*oraz listę adresów, które są dostępne.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Utwórz konfiguracje tyle, ile jest potrzebne, przy użyciu nazwy unikatowej konfiguracji i prywatnymi adresami IP (w przypadku konfiguracji za pomocą statycznych adresów IP).

   Dodaj jako prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dla systemu operacyjnego w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu.

   **Dodawanie publicznego adresu IP**

   Publiczny adres IP jest dodawany przez skojarzenie publicznego zasobu adresu IP na konfigurację adresu IP nowej lub istniejącej konfiguracji adresu IP. Wykonaj kroki opisane w sekcji, które należy wykonać, ile potrzebujesz.

   > [!NOTE]
   > Publiczne adresy IP mają nominalnej. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits).
   >

   **Skojarz zasób publicznego adresu IP do nowej konfiguracji adresu IP**

   Zawsze, gdy publiczny adres IP zostanie dodana nowa konfiguracja adresu IP, możesz również dodać prywatny adres IP, ponieważ wszystkie konfiguracje adresów IP musi mieć prywatny adres IP. Możesz dodać istniejący zasób publicznego adresu IP lub Utwórz nową. Aby utworzyć nowe konto, wprowadź następujące polecenie:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Aby utworzyć nową konfigurację adresu IP za pomocą statycznego prywatnego adresu IP i skojarzonej *myPublicIp3* publiczny adres IP adresów zasobów, wpisz następujące polecenie:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Skojarz zasób publicznego adresu IP do istniejącej konfiguracji adresu IP**

   Zasób publicznego adresu IP można skojarzyć tylko do konfiguracji adresu IP, który nie ma jeszcze skojarzone. Można określić, czy konfiguracja adresu IP ma skojarzony publiczny adres IP, wprowadzając następujące polecenie:

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

   Ponieważ **publicznego adresu IP** kolumny *IpConfig 3* jest pusta, nie zasób publicznego adresu IP jest aktualnie powiązany do niego. Możesz dodać istniejący zasób publicznego adresu IP do IpConfig 3 lub wprowadź następujące polecenie, aby go utworzyć:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP do istniejącej konfiguracji adresu IP o nazwie *IpConfig 3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Ustaw karty Sieciowej przy użyciu nowej konfiguracji adresu IP, wprowadzając następujące polecenie:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Wyświetl prywatne adresy IP i publicznych zasoby adresów IP przypisanych do karty Sieciowej, wprowadzając następujące polecenie:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Dodaj jako prywatny adres IP do systemu operacyjnego maszyny Wirtualnej, wykonując kroki dla systemu operacyjnego w [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](#os-config) dalszej części tego artykułu. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]