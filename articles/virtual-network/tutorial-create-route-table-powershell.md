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
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Kierować ruchem sieciowym z tabelą tras za pomocą programu PowerShell

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

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

## <a name="create-a-route"></a>Tworzenie trasy

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

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

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

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj do następnego kroku, dopiero po utworzeniu maszyny Wirtualnej i Azure zwraca dane wyjściowe do programu PowerShell.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) do zwrócenia publicznego adresu IP *myVmPrivate* maszyny Wirtualnej. Poniższy przykład zwraca publicznego adresu IP *myVmPrivate* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z *myVmPrivate* maszyny Wirtualnej z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie. 

W kolejnym kroku polecenia tracert.exe służy do testowania, routingu. Tracert używa kontrolki komunikat protokołu protokołu ICMP (Internet), której odmówiono przez zaporę systemu Windows. Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Chociaż trasy śledzenia służy do testowania routingu w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.

Przekazywanie dalej adresu IP w obrębie platformy Azure dla interfejsu sieciowego maszyny wirtualnej zostało włączone w sekcji [Włączanie przekazywania dalej adresu IP](#enable-ip-forwarding). W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włącz przesyłanie dalej IP w ramach systemu operacyjnego *myVmNva*.

W wierszu polecenia na *myVmPrivate* pulpitu zdalnego do maszyny Wirtualnej *myVmNva*:

``` 
mstsc /v:myvmnva
```
    
Aby włączyć przekazywanie dalej adresu IP w ramach systemu operacyjnego, wprowadź następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Uruchom ponownie maszynę wirtualną *myVmNva*, co spowoduje także odłączenie sesji pulpitu zdalnego.

Mając nadal połączenie z maszyną wirtualną *myVmPrivate*, utwórz sesję pulpitu zdalnego z maszyną wirtualną *myVmPublic* po ponownym uruchomieniu maszyny wirtualnej *myVmNva*:

``` 
mstsc /v:myVmPublic
```
    
Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Aby przetestować routing ruchu sieciowego do maszyny wirtualnej *myVmPrivate* z maszyny wirtualnej *myVmPublic*, wprowadź następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPublic*:

```
tracert myVmPrivate
```

Odpowiedź jest podobna do poniższego przykładu:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
Teraz możesz zobaczyć, że pierwszym przeskokiem jest 10.0.2.4, czyli prywatny adres IP urządzenia NVA. Drugim przeskokiem jest 10.0.1.4, prywatny adres IP maszyny wirtualnej *myVmPrivate*. Trasa dodana do tabeli tras *myRouteTablePublic* i powiązana z podsiecią *Public* spowodowała, że platforma Azure skierowała ruch przez urządzenie NVA, a nie bezpośrednio do podsieci *Private*.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*. Połączenie z maszyną wirtualną *myVmPrivate* pozostanie nadal aktywne.

Aby przetestować routing ruchu sieciowego do maszyny wirtualnej *myVmPublic* z maszyny wirtualnej *myVmPrivate*, wprowadź następujące polecenie w wierszu polecenia na maszynie wirtualnej *myVmPrivate*:

```
tracert myVmPublic
```

Odpowiedź jest podobna do poniższego przykładu:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
   
Trace complete.
```

Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.

Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule możesz utworzyć tabelę tras i skojarzone go do podsieci. Prosta sieć urządzenie wirtualne, które kierowany ruch z publicznego podsieci do podsieci prywatnej została utworzona. Wdrażanie różnych wstępnie skonfigurowanej sieci wirtualnych urządzeń wykonujących funkcji sieciowych, takich jak zapory i Optymalizacja sieci WAN z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się więcej, zobacz temat [ograniczenie dostępu do sieci do zasobów PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
