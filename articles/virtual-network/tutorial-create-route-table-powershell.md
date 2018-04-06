---
title: Kierować ruchem sieciowym programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak można kierować ruchem sieciowym z tabelą tras za pomocą programu PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f6f3bd2a9683daf5f523cc5cfe43e568fb508694
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Kierować ruchem sieciowym z tabelą tras za pomocą programu PowerShell

Azure automatycznie tras ruchu między wszystkich podsieci w sieci wirtualnej, domyślnie. Można utworzyć trasy do przesłonięcia Azure routing domyślny. Możliwość tworzenia niestandardowych tras jest przydatne, jeśli na przykład chcesz kierować ruchem między podsieciami przez urządzenie wirtualne sieci (NVA). W tym artykule dowiesz się, jak:

* Utwórz tabelę tras
* Utwórz trasę
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Skojarz tabelę tras z podsiecią
* Utwórz NVA, który przekierowuje ruch
* Wdrażanie maszyn wirtualnych (VM) w różnych podsieciach
* Kierować ruchem z jednej podsieci do drugiego za pomocą NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-route-table"></a>Utwórz tabelę tras

Przed utworzeniem tabelę tras, Utwórz grupę zasobów o [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* dla wszystkich zasobów utworzone w tym artykule. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz tabelę tras z [AzureRmRouteTable nowy](/powershell/module/azurerm.network/new-azurermroutetable). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Utwórz trasę

Tworzona jest trasa, pobierając obiekt tabeli tras z [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), tworzona jest trasa z [AzureRmRouteConfig Dodaj](/powershell/module/azurerm.network/add-azurermrouteconfig), następnie zapisu konfiguracji tras na tabelę tras z [AzureRmRouteTable zestawu](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Skojarz tabelę tras z podsiecią

Aby móc skojarzyć tabelę tras z podsiecią, należy utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz trzy podsieci, tworząc trzy konfiguracje podsieci z [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy trzy konfiguracje podsieci *publicznego*, *prywatnej*, i *DMZ* podsieci:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapisać konfiguracje podsieci sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), co powoduje podsieci w sieci wirtualnej:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Skojarz *myRouteTablePublic* tabeli tras do *publicznego* podsieć o [AzureRmVirtualNetworkSubnetConfig zestaw](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) , a następnie zapisać konfiguracji podsieci sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Utwórz NVA

NVA jest maszynę Wirtualną, która pełni funkcję sieci, takich jak routing, zapory lub Optymalizacja sieci WAN.

Przed utworzeniem maszyny Wirtualnej, Utwórz nowy interfejs sieciowy.

### <a name="create-a-network-interface"></a>Tworzenie interfejsu sieciowego

Przed utworzeniem karty sieciowej, należy pobrać wirtualnej sieci z identyfikatorem odpowiadającym [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), następnie identyfikator podsieci z [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Tworzenie interfejsu sieciowego z [AzureRmNetworkInterface nowy](/powershell/module/azurerm.network/new-azurermnetworkinterface) w *DMZ* podsieci IP przesyłanie dalej:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć Maszynę wirtualną i dołączenia do istniejącego interfejsu sieciowego, należy najpierw utworzyć konfiguracji maszyny Wirtualnej z [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvmconfig). Konfiguracja obejmuje interfejs sieciowy utworzony w poprzednim kroku. Po wyświetleniu monitu o nazwę użytkownika i hasło, wybierz nazwę użytkownika i hasło, które chcesz zalogować się do maszyny Wirtualnej z. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Tworzenie maszyny Wirtualnej za pomocą konfiguracji maszyny Wirtualnej z [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby móc weryfikować tego ruchu z *publicznego* podsieci jest kierowany do *prywatnej* podsieci za pośrednictwem sieci urządzenia wirtualnego w kolejnym kroku. 

Utwórz maszynę Wirtualną w *publicznego* podsieć o [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmPublic* w *publicznego* podsieć *myVirtualNetwork* sieci wirtualnej. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Utwórz maszynę Wirtualną w *prywatnej* podsieci.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Maszyna wirtualna ma kilka minut na utworzenie. Nie Kontynuuj do następnego kroku, dopiero po utworzeniu maszyny Wirtualnej i Azure zwraca dane wyjściowe do programu PowerShell.

## <a name="route-traffic-through-an-nva"></a>Kierować ruchem za pośrednictwem NVA

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) do zwrócenia publicznego adresu IP *myVmPrivate* maszyny Wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVmPrivate* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z *myVmPrivate* maszyny Wirtualnej z komputera lokalnego. Zastąp `<publicIpAddress>` adres IP zwrócony z poprzednie polecenie.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz **Connect**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić poświadczenia zostały wprowadzone podczas tworzenia maszyny Wirtualnej), następnie wybierz **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** Aby nawiązać połączenie. 

W kolejnym kroku polecenia tracert.exe służy do testowania, routingu. Tracert używa kontrolki komunikat protokołu protokołu ICMP (Internet), której odmówiono przez zaporę systemu Windows. Włączanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie z programu PowerShell *myVmPrivate* maszyny Wirtualnej:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Chociaż trasy śledzenia służy do testowania routingu w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.

Włączone przesyłanie dalej IP w obrębie platformy Azure dla interfejsu sieciowego maszyny Wirtualnej w [fowarding włączyć IP](#enable-ip-forwarding). W ramach maszyny Wirtualnej systemu operacyjnego lub aplikacji działających w Maszynie wirtualnej, musi także do przesyłania dalej ruchu sieciowego. Włącz przesyłanie dalej IP w ramach systemu operacyjnego *myVmNva*.

W wierszu polecenia na *myVmPrivate* pulpitu zdalnego do maszyny Wirtualnej *myVmNva*:

``` 
mstsc /v:myvmnva
```
    
Aby włączyć przesyłanie dalej IP w ramach systemu operacyjnego, wprowadź następujące polecenie w programie PowerShell z *myVmNva* maszyny Wirtualnej:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Uruchom ponownie *myVmNva* maszynę Wirtualną, co spowoduje także odłączenie sesji usług pulpitu zdalnego.

Podczas nadal połączony *myVmPrivate* maszynę Wirtualną, utwórz sesję pulpitu zdalnego do *myVmPublic* maszyny Wirtualnej, po *myVmNva* ponownego uruchomienia maszyny Wirtualnej:

``` 
mstsc /v:myVmPublic
```
    
Włączanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie z programu PowerShell *myVmPublic* maszyny Wirtualnej:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Aby przetestować routingu ruchu sieciowego do *myVmPrivate* maszyny Wirtualnej z *myVmPublic* maszyny Wirtualnej, wprowadź następujące polecenie z programu PowerShell *myVmPublic* maszyny Wirtualnej:

```
tracert myVmPrivate
```

Odpowiedź jest podobny do poniższego przykładu:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Widać, że pierwszym przeskoku jest 10.0.2.4, czyli NVA prywatnego adresu IP. Drugi przeskok jest 10.0.1.4, prywatnego adresu IP *myVmPrivate* maszyny Wirtualnej. Trasy dodane do *myRouteTablePublic* tabeli tras i powiązanych z *publicznego* podsieci spowodował Azure, aby kierować ruchem przez analizę NVA, a nie bezpośrednio do *prywatnego* podsieci.

Zamykanie sesji usług pulpitu zdalnego do *myVmPublic* maszyny Wirtualnej, co spowoduje pozostawienie połączenie *myVmPrivate* maszyny Wirtualnej.

Aby przetestować routingu ruchu sieciowego do *myVmPublic* maszyny Wirtualnej z *myVmPrivate* maszyny Wirtualnej, wprowadź następujące polecenie w wierszu polecenia *myVmPrivate* maszyny Wirtualnej:

```
tracert myVmPublic
```

Odpowiedź jest podobny do poniższego przykładu:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
   
Trace complete.
```

Widać, że ruch jest kierowany bezpośrednio z *myVmPrivate* maszyny Wirtualnej, aby *myVmPublic* maszyny Wirtualnej. Domyślnie ruch trasy Azure bezpośrednio między podsieciami.

Zamykanie sesji usług pulpitu zdalnego do *myVmPrivate* maszyny Wirtualnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule możesz utworzyć tabelę tras i skojarzone go do podsieci. Prosta sieć urządzenie wirtualne, które kierowany ruch z publicznego podsieci do podsieci prywatnej została utworzona. Wdrażanie różnych wstępnie skonfigurowanej sieci wirtualnych urządzeń wykonujących funkcji sieciowych, takich jak zapory i Optymalizacja sieci WAN z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md) i [Zarządzanie tabelę tras](manage-route-table.md).

Podczas wdrażania można wielu zasobów platformy Azure w ramach sieci wirtualnej, zasobów w przypadku niektórych usług Azure PaaS nie można wdrożyć w sieci wirtualnej. Możesz nadal ograniczyć dostęp do zasobów pewnych usług Azure PaaS ruch tylko z podsieci sieci wirtualnej jednak. Aby dowiedzieć się więcej, zobacz temat [ograniczenie dostępu do sieci do zasobów PaaS](tutorial-restrict-network-access-to-resources-powershell.md).