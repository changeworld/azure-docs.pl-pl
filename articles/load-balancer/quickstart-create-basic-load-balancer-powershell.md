---
title: 'Szybki start: tworzenie podstawowego modułu równoważenia obciążenia — Azure PowerShell | Microsoft Docs'
description: W tym przewodniku Szybki start przedstawiono sposób tworzenia podstawowego modułu równoważenia obciążenia przy użyciu programu PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms:custom: mvc
ms.openlocfilehash: bc9883ee64f2d682a6b7b69bd1fb168cddd60001
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38539995"
---
# <a name="get-started"></a>Szybki start: tworzenie publicznego modułu równoważenia obciążenia przy użyciu interfejsu programu Azure PowerShell
W tym przewodniku Szybki start przedstawiono sposób tworzenia podstawowego modułu równoważenia obciążenia przy użyciu programu Azure PowerShell. W celu przetestowania modułu równoważenia obciążenia wdrożysz dwie maszyny wirtualne z systemem Windows Server i zrównoważysz obciążenie aplikacji internetowej między maszynami wirtualnymi.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Zanim będzie można utworzyć moduł równoważenia obciążenia, musisz utworzyć grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). W poniższym przykładzie pokazano sposób tworzenia grupy zasobów o nazwie *myResourceGroupLB* w lokalizacji *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP
Aby uzyskać dostęp do aplikacji za pośrednictwem Internetu, potrzebujesz publicznego adresu IP modułu równoważenia obciążenia. Utwórz publiczny adres IP przy użyciu polecenia [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). W poniższym przykładzie zostanie utworzony publiczny adres IP o nazwie *myPublicIP* w grupie zasobów *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Dynamic" `
  -Name "myPublicIP"
```
## <a name="create-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia
 W tej sekcji skonfigurujesz adres IP frontonu i pulę adresów zaplecza na potrzeby modułu równoważenia, a następnie utworzysz podstawowy moduł równoważenia obciążenia.
 
### <a name="create-frontend-ip"></a>Tworzenie adresu IP frontonu
Utwórz adres IP frontonu przy użyciu polecenia [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). Poniższy przykład obejmuje tworzenie konfiguracji adresu IP frontonu o nazwie *myFrontEnd* i dołączanie do niej adresu *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>Konfigurowanie puli adresów zaplecza

Utwórz pulę adresów zaplecza przy użyciu polecenia [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). W kolejnych krokach maszyny wirtualne zostaną dołączone do tej puli zaplecza. Poniższy przykład przedstawia sposób tworzenia puli adresów zaplecza o nazwie *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji
Sonda kondycji umożliwia modułowi równoważenia obciążenia monitorowanie stanu aplikacji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Domyślnie maszyna wirtualna jest wykluczana z dystrybucji ruchu przez moduł równoważenia obciążenia, jeśli dwie kolejne próby podejmowane w 15-sekundowych odstępach zakończą się niepowodzeniem. Sonda kondycji jest tworzona z użyciem protokołu lub konkretnej strony kontroli kondycji aplikacji. 

W poniższym przykładzie zostanie utworzona sonda TCP. Możesz także tworzyć niestandardowe sondy HTTP na potrzeby bardziej szczegółowych kontroli kondycji. W przypadku użycia niestandardowej sondy HTTP należy utworzyć stronę kontroli kondycji, na przykład *healthcheck.aspx*. Aby dany host pozostał w rotacji, sonda musi zwrócić do modułu równoważenia obciążenia kod odpowiedzi **HTTP 200 OK**.

Aby utworzyć sondę kondycji TCP, użyj polecenia [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). W poniższym przykładzie zostanie utworzona sonda kondycji o nazwie *myHealthProbe* monitorująca poszczególne maszyny wirtualne przy użyciu portu *HTTP* *80*:

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Aby mieć pewność, że ruch będzie kierowany tylko do maszyn wirtualnych w dobrej kondycji, zdefiniuj również sondę kondycji do użycia.

Utwórz regułę modułu równoważenia obciążenia przy użyciu polecenia [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). W poniższym przykładzie zostanie utworzona reguła modułu równoważenia obciążenia o nazwie *myLoadBalancerRule* w celu równoważenia obciążenia na porcie *TCP* *80*:

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Tworzenie reguł NAT

Utwórz reguły NAT przy użyciu polecenia [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig). Poniższy przykład obejmuje tworzenie reguł NAT o nazwie *myLoadBalancerRDP1* i *myLoadBalancerRDP2* w celu zezwolenia na nawiązywanie połączeń protokołu RDP z serwerami zaplecza przy użyciu portów 4221 i 4222:

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Następnie utwórz podstawowy moduł równoważenia obciążenia przy użyciu polecenia [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). Poniższy przykład obejmuje tworzenie publicznego podstawowego modułu równoważenia obciążenia o nazwie myLoadBalancer przy użyciu konfiguracji adresu IP frontonu, puli zaplecza, sondy kondycji, reguły równoważenia obciążenia i reguł NAT, które zostały utworzone w poprzednich krokach:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych
Zanim będzie możliwe wdrożenie maszyn wirtualnych i przetestowanie modułu równoważenia obciążenia, musisz utworzyć pomocnicze zasoby sieci wirtualnej — sieć wirtualną i wirtualnej karty sieciowe. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład obejmuje tworzenie sieci wirtualnej o nazwie *myVnet* z podsiecią *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń w celu zdefiniowania połączeń przychodzących do sieci wirtualnej.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 3389
Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia protokołu RDP za pośrednictwem portu 3389 przy użyciu polecenia [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Tworzenie reguły sieciowej grupy zabezpieczeń dla portu 80
Utwórz regułę sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące za pośrednictwem portu 80 przy użyciu polecenia [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup`
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup'`
-SecurityRules $rule1,$rule2
```

###<a name="create-nics"></a>Tworzenie kart sieciowych
Utwórz wirtualne karty sieciowe przy użyciu polecenia [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). W poniższym przykładzie zostaną utworzone dwie wirtualne karty sieciowe. (Po jednej karcie na każdą maszynę wirtualną, która zostanie utworzona na potrzeby aplikacji w kolejnych krokach). Możesz w dowolnym momencie utworzyć i dodać do modułu równoważenia obciążenia dodatkowe wirtualne karty sieciowe i maszyny wirtualne:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych
Aby poprawić wysoką dostępność aplikacji, umieść maszyny wirtualne w zestawie dostępności.

Aby utworzyć zestaw dostępności, użyj polecenia [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). W poniższym przykładzie zostanie utworzony zestaw dostępności o nazwie *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Ustaw nazwę użytkownika i hasło administratora maszyn wirtualnych przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Następnie utwórz maszyny wirtualne za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład obejmuje tworzenie dwóch maszyn wirtualnych oraz wymaganych składników sieci wirtualnej, jeśli jeszcze nie istnieją:

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Job`. Utworzenie i skonfigurowanie obu maszyn wirtualnych może potrwać kilka minut.

### <a name="install-iis-with-custom-web-page"></a>Instalowanie usług IIS przy użyciu niestandardowej strony internetowej
 
Zainstaluj usługi IIS przy użyciu niestandardowej strony internetowej na obu maszynach wirtualnych zaplecza w następujący sposób:

1. Pobierz publiczny adres IP modułu równoważenia obciążenia. Przy użyciu polecenia `Get-AzureRmPublicIPAdress` uzyskaj publiczny adres IP usługi Load Balancer.

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Utwórz połączenie pulpitu zdalnego z maszyną wirtualną VM1, używając publicznego adresu adresu IP uzyskanego w poprzednim kroku. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Wprowadź poświadczenia maszyny wirtualnej *VM1*, aby uruchomić sesję protokołu RDP.
4. Uruchom program Windows PowerShell na maszynie wirtualnej VM1 i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Zamknij połączenie protokołu RDP z maszyną wirtualną *myVM1*.
6. Utwórz połączenie protokołu RDP z maszyną wirtualną *myVM2*, uruchamiając polecenie `mstsc /v:PublicIpAddress:4222`, i powtórz krok 4 dla maszyny wirtualnej *VM2*.

## <a name="test-load-balancer"></a>Testowanie modułu równoważenia obciążenia
Uzyskaj publiczny adres IP modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). W poniższym przykładzie uzyskano utworzony wcześniej adres IP *myPublicIP*:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Następnie możesz wprowadzić publiczny adres IP w przeglądarce internetowej. Zostanie wyświetlona witryna internetowa z nazwą hosta maszyny wirtualnej, do której moduł równoważenia obciążenia kieruje ruch, jak pokazano na poniższym przykładzie:

![Testowanie modułu równoważenia obciążenia](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia rozdziela ruch między dwie maszyny wirtualne uruchamiające aplikację, możesz wymusić odświeżenie w przeglądarce internetowej.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono podstawowy moduł równoważenia obciążenia, dołączono do niego maszyny wirtualne, skonfigurowano regułę ruchu modułu równoważenia obciążenia i sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
