---
title: Kierować ruchem sieciowym programu Azure PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się, jak kierować ruchem sieciowym za pomocą tabeli tras przy użyciu programu PowerShell.
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
ms.openlocfilehash: d077f64081ff7ca53a0ec569ea99658d29654cda
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429348"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Kierowanie ruchem sieciowym za pomocą tabeli tras przy użyciu programu PowerShell

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

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten artykuł wymaga modułu Azure PowerShell w wersji 5.4.1 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

Zanim będzie można utworzyć tabelę tras, Utwórz grupę zasobów za pomocą [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* dla wszystkich zasobów utworzonych w tym artykule. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz tabelę tras z [New AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). Poniższy przykład tworzy tabelę tras o nazwie *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Tworzenie trasy

Tworzona jest trasa przez pobranie obiektu tabeli tras z [Get AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), utworzyć trasę z [AzureRmRouteConfig Dodaj](/powershell/module/azurerm.network/add-azurermrouteconfig), następnie Zapisz konfigurację trasy w tabeli tras z [AzureRmRouteTable zestaw](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć. Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Utwórz trzy podsieci, tworząc trzech konfiguracji podsieci za pomocą [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy trzy konfiguracje podsieci dla *publicznych*, *prywatnej*, i *DMZ* podsieci:

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

Zapisz konfiguracje podsieci sieci wirtualnej za pomocą [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), co powoduje utworzenie podsieci w sieci wirtualnej:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Skojarz *myRouteTablePublic* tabelę tras w celu *publicznych* podsieć o [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) , a następnie Zapisz konfigurację podsieci do sieć wirtualna z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

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

Przed utworzeniem maszyny Wirtualnej, Utwórz interfejs sieciowy.

### <a name="create-a-network-interface"></a>Utwórz interfejs sieciowy

Przed utworzeniem interfejsu sieciowego, należy pobrać wirtualny identyfikator za pomocą sieci [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), następnie identyfikator podsieci za pomocą [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Utwórz interfejs sieciowy z [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) w *DMZ* podsieci z włączonym przekazywaniem adresu IP:

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

Aby utworzyć Maszynę wirtualną i do niej dołączyć istniejący interfejs sieciowy, należy najpierw utworzyć konfigurację maszyny Wirtualnej za pomocą [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Ta konfiguracja obejmuje interfejs sieciowy utworzony w poprzednim kroku. Po wyświetleniu monitu o nazwę użytkownika i hasło, wybierz nazwę użytkownika i hasło, które chcesz zalogować się do maszyny Wirtualnej za pomocą. 

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

Tworzenie maszyny Wirtualnej za pomocą konfiguracji maszyny Wirtualnej za pomocą [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, dzięki czemu można sprawdzić, czy ruch z *publicznych* podsieci jest kierowany do *prywatnej* podsieci za pośrednictwem wirtualnego urządzenia sieciowego w późniejszym kroku. 

Utwórz Maszynę wirtualną w *publicznych* podsieć o [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVmPublic* w *publicznych* podsieci *myVirtualNetwork* sieci wirtualnej. 

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

Utwórz Maszynę wirtualną w *prywatnej* podsieci.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj do następnego kroku, dopóki maszyna wirtualna jest tworzona i Azure zwraca dane wyjściowe do programu PowerShell.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

Użyj [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) aby zwrócić publiczny adres IP *myVmPrivate* maszyny Wirtualnej. Poniższy przykład zwraca publiczny adres IP *myVmPrivate* maszyny Wirtualnej:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
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

W kolejnym kroku polecenie tracert.exe jest używane do testowania routingu. Tracert używa kontroli komunikat ICMP (Internet Protocol), który jest blokowany przez zaporę Windows. Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Chociaż narzędzie śledzenia trasy jest używane do testowania routingu w tym artykule, dzięki czemu protokołu ICMP przez zaporę Windows we wdrożeniach produkcyjnych nie jest zalecane.

Przekazywanie dalej adresu IP w obrębie platformy Azure dla interfejsu sieciowego maszyny wirtualnej zostało włączone w sekcji [Włączanie przekazywania dalej adresu IP](#enable-ip-forwarding). W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włączanie przekazywania adresów IP w ramach systemu operacyjnego *myVmNva*.

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

Gdy nie jest już potrzebny, należy użyć [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule utworzono tabelę tras i skojarzono ją z podsiecią. Utworzono proste sieciowe urządzenie wirtualne, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrażanie różnych wstępnie skonfigurowanych sieciowych urządzeniach wirtualnych, wykonujące funkcje sieciowe, takie jak Zapora i Optymalizacja sieci WAN z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się więcej, zobacz temat [ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
