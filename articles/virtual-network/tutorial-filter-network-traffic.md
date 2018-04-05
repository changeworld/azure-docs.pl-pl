---
title: Filtrowanie ruchu sieciowego - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak filtrować ruch sieciowy do podsieci, z sieciową grupą zabezpieczeń, za pomocą programu PowerShell.
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
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 53406150cfc2ec4229ecd9cf3356ad03d60f8e7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrowanie ruchu sieciowego z sieciową grupą zabezpieczeń, za pomocą programu PowerShell

Można filtrować ruchu sieciowego przychodzącego i wychodzącego z podsieci sieci wirtualnej z sieciową grupą zabezpieczeń. Grupy zabezpieczeń sieci zawiera zasady zabezpieczeń, które filtrowania ruchu sieciowego według adresu IP, portu i protokołu. Reguły zabezpieczeń są stosowane do zasobów wdrożone w podsieci. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie zasad grupy i zabezpieczeń zabezpieczenia sieci
> * Tworzenie sieci wirtualnej i skojarzenia sieciowej grupy zabezpieczeń do podsieci
> * Wdrażanie maszyn wirtualnych (VM) do podsieci
> * Filtry ruchu testu

Jeśli wolisz, możesz wykonać przy użyciu tego artykułu [interfejsu wiersza polecenia Azure](tutorial-filter-network-traffic-cli.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Grupa zabezpieczeń sieci zawiera zasady zabezpieczeń. Reguły zabezpieczeń Określ źródłowym i docelowym. Grupy zabezpieczeń aplikacji mogą być źródeł i miejsc docelowych.

### <a name="create-application-security-groups"></a>Utwórz grupy zabezpieczeń aplikacji

Najpierw utwórz grupę zasobów dla wszystkich zasobów, które są tworzone w tym artykule i [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupy zasobów w *eastus* lokalizacji: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Tworzenie grupy zabezpieczeń aplikacji z [AzureRmApplicationSecurityGroup nowy](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Grupa zabezpieczeń aplikacji umożliwia utworzenie grupy serwerów z portem podobne wymagania filtrowania. Poniższy przykład tworzy dwie grupy zabezpieczeń aplikacji.

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

### <a name="create-security-rules"></a>Tworzenie reguły zabezpieczeń

Utwórz regułę zabezpieczeń z [AzureRmNetworkSecurityRuleConfig nowy](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Poniższy przykład tworzy regułę, która zezwala na ruch przychodzący z Internetu, aby *myWebServers* grupy zabezpieczeń aplikacji przez porty 80 i 443:

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

W tym artykule protokołu RDP (port 3389) jest uwidoczniony w Internecie dla *myAsgMgmtServers* maszyny Wirtualnej. W środowiskach produkcyjnych, zamiast Udostępnianie portu 3389 na internet, zaleca się nawiązać zasobów platformy Azure, które mają być zarządzane przy użyciu [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [prywatnej](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) połączenie sieciowe.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Utwórz grupę zabezpieczeń sieci z [AzureRmNetworkSecurityGroup nowy](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy wirtualny o nazwie *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Tworzenie konfiguracji podsieci z [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig), a następnie zapisać konfiguracji podsieci do sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). W poniższym przykładzie dodano podsieć o nazwie *mySubnet* sieć wirtualną i skojarzy *myNsg* sieciowej grupy zabezpieczeń do niej:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Przed utworzeniem maszyn wirtualnych, Pobierz obiekt sieci wirtualnej dla podsieci z [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Tworzenie publicznego adresu IP dla każdej maszyny Wirtualnej z [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Utwórz dwa interfejsy sieci z [AzureRmNetworkInterface nowy](/powershell/module/azurerm.network/new-azurermnetworkinterface)i przypisać do publicznego adresu IP interfejsu sieciowego. Poniższy przykład tworzy interfejs sieciowy, stowarzyszonych *myVmWeb* publicznego adresu IP i ułatwia członkiem *myAsgWebServers* grupy zabezpieczeń aplikacji:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Poniższy przykład tworzy interfejs sieciowy, stowarzyszonych *myVmMgmt* publicznego adresu IP i ułatwia członkiem *myAsgMgmtServers* grupy zabezpieczeń aplikacji:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby móc weryfikować ruchu filtrowanie w kolejnym kroku. 

Tworzenie konfiguracji maszyny Wirtualnej z [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvmconfig), następnie utwórz maszynę Wirtualną z [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy maszynę Wirtualną, która będzie służyć jako serwer sieci web. `-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku: 

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

Utwórz maszynę Wirtualną jako serwera zarządzania:

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

Maszyna wirtualna ma kilka minut na utworzenie. Nie Kontynuuj do następnego kroku, dopóki platforma Azure zakończy tworzenie maszyny Wirtualnej.

## <a name="test-traffic-filters"></a>Filtry ruchu testu

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) do zwrócenia publiczny adres IP maszyny wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVmMgmt* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z *myVmMgmt* maszyny Wirtualnej z komputera lokalnego. Zastąp `<publicIpAddress>` adres IP zwrócony z poprzednie polecenie.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz **Connect**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić poświadczenia zostały wprowadzone podczas tworzenia maszyny Wirtualnej), następnie wybierz **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** Aby nawiązać połączenie. 
   
Połączenie powiedzie się, ponieważ port 3389 jest dozwolone przychodzące z Internetu, aby *myAsgMgmtServers* aplikacji grupy zabezpieczeń, który interfejs sieciowy jest dołączony do *myVmMgmt* maszyna wirtualna jest w.

Użyj następującego polecenia, aby utworzyć połączenie pulpitu zdalnego do *myVmWeb* maszyny Wirtualnej, z *myVmMgmt* maszyny Wirtualnej za pomocą następującego polecenia ze środowiska PowerShell:

``` 
mstsc /v:myvmWeb
```

Połączenie powiedzie się, ponieważ domyślną regułę zabezpieczeń w każdej grupie zabezpieczeń sieci zezwala na ruch przez wszystkie porty między wszystkich adresów IP w sieci wirtualnej. Nie można utworzyć połączenia pulpitu zdalnego z *myVmWeb* maszyny Wirtualnej z Internetu, ponieważ reguły zabezpieczeń dla *myAsgWebServers* nie zezwala na portu 3389 przychodzące z Internetu.

Użyj następującego polecenia do zainstalowania programu Microsoft IIS na *myVmWeb* maszyny Wirtualnej z programu PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Po ukończeniu instalacji usług IIS, odłącz od *myVmWeb* maszyny Wirtualnej, co spowoduje pozostawienie w *myVmMgmt* połączeń usług pulpitu zdalnego maszyny Wirtualnej. Aby wyświetlić ekran powitalny usługi IIS, otwórz przeglądarkę internetową i przejdź do http://myVmWeb.

Odłącz od *myVmMgmt* maszyny Wirtualnej.

Na komputerze, wprowadź następujące polecenie w programie PowerShell, aby pobrać publicznego adresu IP *myVmWeb* serwera:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Aby upewnić się, że masz dostęp *myVmWeb* sieci web serwera z poza platformą Azure, otwórz przeglądarkę internetową na tym komputerze i przejdź do `http://<public-ip-address-from-previous-step>`. Połączenie powiedzie się, ponieważ port 80 jest dozwolone przychodzące z Internetu, aby *myAsgWebServers* aplikacji grupy zabezpieczeń, który interfejs sieciowy jest dołączony do *myVmWeb* maszyna wirtualna jest w.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można użyć [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyć grupę zabezpieczeń sieci i jego skojarzone z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat grup zabezpieczeń sieci, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md) i [Zarządzanie sieciowej grupy zabezpieczeń](virtual-network-manage-nsg-arm-ps.md).

Azure tras ruchu między podsieciami domyślnie. Zamiast tego można kierować ruchem między podsieciami przez maszynę Wirtualną, uznawany za zaporą, np. Aby dowiedzieć się, jak utworzyć tabelę tras, przejdź do następnego artykułu.

> [!div class="nextstepaction"]
> [Utwórz tabelę tras](./tutorial-create-route-table-portal.md)