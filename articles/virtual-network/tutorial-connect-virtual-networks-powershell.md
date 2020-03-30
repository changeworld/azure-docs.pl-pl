---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej — azure powershell
description: W tym artykule dowiesz się, jak połączyć sieci wirtualne z komunikacją równorzędkową sieci wirtualnej przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201309"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Łączenie sieci wirtualnych z komunikacją równorzędkową sieci wirtualnej przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie dwóch sieci wirtualnych
* Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
* Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
* Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork1* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Utwórz konfigurację podsieci za pomocą [programu New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład tworzy konfigurację podsieci z prefiksem adresu 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zapisz konfigurację podsieci w sieci wirtualnej za pomocą [Sieci Set-AzVirtualNetwork,](/powershell/module/az.network/Set-azVirtualNetwork)która tworzy podsieć:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Utwórz sieć wirtualną z prefiksem adresu 10.1.0.0/16 i jedną podsiecią:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

Tworzenie komunikacji równorzędnej za pomocą [dodatku AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Poniższy przykład równorzędne *myVirtualNetwork1* do *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

W danych wyjściowych zwróconych po wykonaniu poprzedniego polecenia zostanie wyświetlony, że zainicjowano **stan równorzędny** . *Initiated* Komunikacja równorzędna pozostaje w stanie *Inicjowane,* dopóki nie utworzysz komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. Tworzenie peering z *myVirtualNetwork2* do *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

W danych wyjściowych zwróconych po wykonaniu poprzedniego polecenia widać, że **stan komunikacji równorzędnej** jest *połączony*. Platforma Azure zmieniła również stan komunikacji równorzędnej *myVirtualNetwork1-myVirtualNetwork2* w komunikacji równorzędnej *z connected*. Upewnij się, że stan komunikacji równorzędnej dla *myVirtualNetwork1-myVirtualNetwork2* peering zmieniono na *Połączony* z [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Zasoby w jednej sieci wirtualnej nie mogą komunikować się z zasobami w drugiej sieci wirtualnej, dopóki **Stan komunikacji równorzędnej** dla komunikacji równorzędnej w obu sieciach wirtualnych jest *Połączony*.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVm1* w sieci wirtualnej *myVirtualNetwork1.* Opcja `-AsJob` tworzy maszynę wirtualną w tle, dzięki czemu można przejść do następnego kroku. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, za pomocą których chcesz zalogować się do maszyny Wirtualnej.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj późniejszych kroków, dopóki platforma Azure nie utworzy maszyny Wirtualnej i nie zwróci danych wyjściowych do programu PowerShell.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Możesz połączyć się z publicznym adresem IP maszyny Wirtualnej z Internetu. Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z maszyną wirtualną *myVm1* z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Zostanie utworzony plik protokołu prow (rdp) usługi pulpitu zdalnego, który zostanie pobrany na komputer i otwarty. Wprowadź nazwę użytkownika i hasło (może być konieczne wybranie opcji **Więcej opcji,** następnie **użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej), a następnie kliknij przycisk **OK**. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

Na maszynie wirtualnej *myVm1* włącz protokół ICMP (Internet Control Message Protocol) za pośrednictwem zapory systemu Windows, aby w późniejszym kroku można było wysłać ping tej maszyny wirtualnej z *myVm2* za pomocą programu PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Chociaż ping jest używany do komunikowania się między maszynami wirtualnymi w tym artykule, zezwolenie ICMP za pośrednictwem Zapory systemu Windows dla wdrożeń produkcyjnych nie jest zalecane.

Aby nawiązać połączenie z maszyną wirtualną *myVm2*, wprowadź następujące polecenie w wierszu polecenia maszyny wirtualnej *myVm1*:

```
mstsc /v:10.1.0.4
```

Ponieważ włączono ping na *myVm1,* można teraz pingować go przez adres IP z wiersza polecenia na maszynie wirtualnej *myVm2:*

```
ping 10.0.0.4
```

Otrzymasz cztery odpowiedzi. Odłącz swoje sesje protokołu RDP do obu maszyn wirtualnych, *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, użyj [Remove-AzResourcegroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano, jak połączyć dwie sieci w tym samym regionie platformy Azure z komunikacją równorzędna sieci wirtualnej. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#powershell), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Można [podłączyć własny komputer do sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za pośrednictwem sieci VPN i wchodzić w interakcje z zasobami w sieci wirtualnej lub w sieciach wirtualnych równorzędnych. Aby uzyskać skrypty wielokrotnego użytku, aby wykonać wiele zadań objętych artykułami sieci wirtualnej, zobacz [przykłady skryptów](powershell-samples.md).