---
title: Kierować ruchem sieciowym programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak kierować ruchem sieciowym za pomocą tabeli tras przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: cd13b3a7a3bc4d5a80e44d146e08c14e81ffdb60
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730065"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Kierowanie ruchem sieciowym za pomocą tabeli tras przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie tabeli tras
* Tworzenie trasy
* Tworzenie sieci wirtualnej z wieloma podsieciami
* Kojarzenie tabeli tras z podsiecią
* Tworzenie urządzenia NVA, które kieruje ruchem
* Wdrażanie maszyn wirtualnych w różnych podsieciach
* Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, ten artykuł wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Zanim będzie można utworzyć tabelę tras, Utwórz grupę zasobów za pomocą [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* dla wszystkich zasobów utworzonych w tym artykule.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz tabelę tras z [New AzRouteTable](/powershell/module/az.network/new-azroutetable). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Tworzenie trasy

Tworzona jest trasa przez pobranie obiektu tabeli tras z [Get AzRouteTable](/powershell/module/az.network/get-azroutetable), utworzyć trasę z [AzRouteConfig Dodaj](/powershell/module/az.network/add-azrouteconfig), następnie Zapisz konfigurację trasy w tabeli tras z [ Zestaw AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz trzy podsieci, tworząc trzech konfiguracji podsieci za pomocą [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład tworzy trzy konfiguracje podsieci dla *publicznych*, *prywatnej*, i *DMZ* podsieci:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Zapisz konfiguracje podsieci sieci wirtualnej za pomocą [AzVirtualNetwork zestaw](/powershell/module/az.network/Set-azVirtualNetwork), co powoduje utworzenie podsieci w sieci wirtualnej:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Skojarz *myRouteTablePublic* tabelę tras w celu *publicznego* podsieć o [AzVirtualNetworkSubnetConfig zestaw](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) i następnie Zapisz konfigurację podsieci do sieć wirtualna z [AzVirtualNetwork zestaw](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

Przed utworzeniem maszyny Wirtualnej, Utwórz interfejs sieciowy.

### <a name="create-a-network-interface"></a>Utwórz interfejs sieciowy

Przed utworzeniem interfejsu sieciowego, należy pobrać wirtualny identyfikator za pomocą sieci [Get AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), następnie identyfikator podsieci za pomocą [Get AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Utwórz interfejs sieciowy z [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) w *DMZ* podsieci z włączonym przekazywaniem adresu IP:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć Maszynę wirtualną i do niej dołączyć istniejący interfejs sieciowy, należy najpierw utworzyć konfigurację maszyny Wirtualnej za pomocą [New AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Ta konfiguracja obejmuje interfejs sieciowy utworzony w poprzednim kroku. Po wyświetleniu monitu o nazwę użytkownika i hasło, wybierz nazwę użytkownika i hasło, które chcesz zalogować się do maszyny Wirtualnej za pomocą.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Tworzenie maszyny Wirtualnej za pomocą konfiguracji maszyny Wirtualnej za pomocą [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, dzięki czemu można sprawdzić, czy ruch z *publicznych* podsieci jest kierowany do *prywatnej* podsieci za pośrednictwem wirtualnego urządzenia sieciowego w późniejszym kroku.

Utwórz Maszynę wirtualną w *publicznych* podsieć o [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmPublic* w *publicznych* podsieci *myVirtualNetwork* sieci wirtualnej.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Utwórz Maszynę wirtualną w *prywatnej* podsieci.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj do następnego kroku, dopóki maszyna wirtualna jest tworzona i Azure zwraca dane wyjściowe do programu PowerShell.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj [Get AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) aby zwrócić publiczny adres IP *myVmPrivate* maszyny Wirtualnej. Poniższy przykład zwraca publiczny adres IP *myVmPrivate* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z *myVmPrivate* maszyny Wirtualnej z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.

W kolejnym kroku `tracert.exe` polecenie służy do testowania routingu. Tracert używa kontroli komunikat ICMP (Internet Protocol), który jest blokowany przez zaporę Windows. Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Chociaż narzędzie śledzenia trasy jest używane do testowania routingu w tym artykule, dzięki czemu protokołu ICMP przez zaporę Windows we wdrożeniach produkcyjnych nie jest zalecane.

Możesz włączyć przekazywanie dalej adresu IP w obrębie platformy Azure dla interfejsu sieciowego maszyny Wirtualnej w Włącz przekazywanie IP. W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włączanie przekazywania adresów IP w ramach systemu operacyjnego *myVmNva*.

W wierszu polecenia na *myVmPrivate* Pulpit zdalny dla maszyny Wirtualnej *myVmNva*:

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

Gdy nie jest już potrzebny, należy użyć [AzResourcegroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono tabelę tras i skojarzono ją z podsiecią. Utworzono proste sieciowe urządzenie wirtualne, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrażanie różnych wstępnie skonfigurowanych sieciowych urządzeniach wirtualnych, wykonujące funkcje sieciowe, takie jak Zapora i Optymalizacja sieci WAN z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się więcej, zobacz temat [ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources-powershell.md).