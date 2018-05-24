---
title: Filtrowanie ruchu sieciowego — samouczek — Azure PowerShell | Microsoft Docs
description: W tym samouczku dowiesz się, jak filtrować ruch sieciowy do podsieci za pomocą sieciowej grupy zabezpieczeń i programu PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 165bd6770109348bd19ebb4fa1735bedf83004b1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261321"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>Samouczek: filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń i programu PowerShell

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
> * Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
> * Wdrażanie maszyn wirtualnych w podsieci
> * Testowanie filtrów ruchu

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-filter-network-traffic-cli.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera reguły zabezpieczeń. Reguły zabezpieczeń określają źródło i lokalizację docelową. Źródła i lokalizacje docelowe mogą być grupami zabezpieczeń aplikacji.

### <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów utworzonych w tym samouczku przy użyciu polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz grupę zabezpieczeń aplikacji przy użyciu polecenia [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych wymaganiach w zakresie filtrowania portów. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

Utwórz regułę zabezpieczeń przy użyciu polecenia [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myWebServers* przez porty 80 i 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

W tym samouczku protokół RDP (port 3389) jest uwidoczniony w Internecie dla maszyny wirtualnej *myAsgMgmtServers*. W przypadku środowisk produkcyjnych zamiast uwidaczniania portu 3389 w Internecie zaleca się połączenie z zasobami platformy Azure, którymi chcesz zarządzać, przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [prywatnego](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączenia sieciowego.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz sieciową grupę zabezpieczeń przy użyciu polecenia [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz konfigurację podsieci przy użyciu polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig), a następnie zapisz konfigurację podsieci w sieci wirtualnej przy użyciu polecenia [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). Poniższy przykład dodaje podsieć o nazwie *mySubnet* do sieci wirtualnej i kojarzy z nią sieciową grupę zabezpieczeń *myNsg*:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Przed utworzeniem maszyn wirtualnych pobierz obiekt sieci wirtualnej z podsiecią przy użyciu polecenia [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Utwórz publiczny adres IP dla każdej maszyny wirtualnej przy użyciu polecenia [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Utwórz dwa interfejsy sieciowe przy użyciu polecenia [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) i przypisz publiczny adres IP do interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy, przypisuje do niego publiczny adres IP *myVmWeb* oraz ustawia go jako element członkowski grupy zabezpieczeń aplikacji *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Poniższy przykład tworzy interfejs sieciowy, przypisuje do niego publiczny adres IP *myVmMgmt* oraz ustawia go jako element członkowski grupy zabezpieczeń aplikacji *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby umożliwić weryfikację filtrowania ruchu w kolejnym kroku. 

Utwórz konfigurację maszyny wirtualnej przy użyciu polecenia [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), a następnie utwórz maszynę wirtualną przy użyciu polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy maszynę wirtualną, która będzie służyć jako serwer internetowy. Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Utwórz maszynę wirtualną, która będzie służyć jako serwer zarządzania:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Tworzenie maszyny wirtualnej zajmuje kilka minut. Nie przechodź do następnego kroku, dopóki platforma Azure nie ukończy tworzenia maszyny wirtualnej.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

Użyj polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z maszyną wirtualną *myVmMgmt* z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie. 
   
Połączenie powiedzie się, ponieważ port 3389 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgMgmtServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmMgmt*.

Użyj następującego polecenia, aby utworzyć połączenie pulpitu zdalnego z maszyną wirtualną *myVmWeb* z maszyny wirtualnej *myVmMgmt*, korzystając z następującego polecenia w programie PowerShell:

``` 
mstsc /v:myvmWeb
```

Połączenie powiedzie się, ponieważ domyślne reguły zabezpieczeń w każdej sieciowej grupie zabezpieczeń zezwalają na ruch na wszystkich portach pomiędzy wszystkimi adresami IP w sieci wirtualnej. Nie możesz utworzyć połączenia pulpitu zdalnego z maszyną wirtualną *myVmWeb* z Internetu, ponieważ reguły zabezpieczeń *myAsgWebServers* nie zezwalają na ruch przychodzący z Internetu przez port 3389.

Użyj następującego polecenia, aby zainstalować usługi Microsoft IIS na maszynie wirtualnej *myVmWeb* z poziomu programu PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po zakończeniu instalacji usług IIS odłącz się od maszyny wirtualnej *myVmWeb*, co spowoduje pozostanie w połączeniu pulpitu zdalnego z maszyną wirtualną *myVmMgmt*. Aby wyświetlić ekran powitalny usług IIS, otwórz przeglądarkę internetową i przejdź do adresu http://myVmWeb.

Odłącz się od maszyny wirtualnej *myVmMgmt*.

Na komputerze wprowadź następujące polecenie w programie PowerShell, aby pobrać publiczny adres IP serwera *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aby potwierdzić, że możesz uzyskać dostęp do serwera internetowego *myVmWeb* spoza platformy Azure, otwórz przeglądarkę internetową na komputerze i przejdź do adresu `http://<public-ip-address-from-previous-step>`. Połączenie powiedzie się, ponieważ port 80 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgWebServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmWeb*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup):

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](security-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się więcej o sposobie tworzenia tabeli tras, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Tworzenie tabeli tras](./tutorial-create-route-table-portal.md)
