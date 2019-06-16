---
title: Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej — PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak połączyć sieci wirtualne za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
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
ms.openlocfilehash: f5c90b7d79b31c321b00869c90b0261c0b4730d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66727753"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie dwóch sieci wirtualnych
* Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
* Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
* Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać lokalnie, ten artykuł wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

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

Utwórz konfigurację podsieci przy użyciu [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Poniższy przykład umożliwia utworzenie konfiguracji podsieci z prefiksem adresu 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zapisz konfigurację podsieci sieci wirtualnej za pomocą [AzVirtualNetwork zestaw](/powershell/module/az.network/Set-azVirtualNetwork), które tworzy podsieć:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Tworzenie sieci wirtualnej przy użyciu prefiksów adresów 10.1.0.0/16 i jedną podsieć:

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

Utwórz połączenie równorzędne [AzVirtualNetworkPeering Dodaj](/powershell/module/az.network/add-azvirtualnetworkpeering). Poniższy przykład elementów równorzędnych *myVirtualNetwork1* do *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

W danych wyjściowych zwracane po wykonaniu poprzedniego polecenia, zobaczysz, że **PeeringState** jest *zainicjowano*. Komunikacja równorzędna pozostanie w *zainicjowano* stanu do czasu utworzenia komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. Tworzenie komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

W danych wyjściowych zwracane po wykonaniu poprzedniego polecenia, zobaczysz, że **PeeringState** jest *połączono*. Platforma Azure również zmieniła stan komunikacji równorzędnej z *myVirtualNetwork1 myVirtualNetwork2* komunikacji równorzędnej do *połączono*. Upewnij się, że stan komunikacji równorzędnej dla *myVirtualNetwork1 myVirtualNetwork2* komunikacji równorzędnej zmieniony na *połączono* z [Get AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Zasoby w jednej sieci wirtualnej nie może komunikować się z zasobami w innej sieci wirtualnej do momentu **PeeringState** dla komunikacji równorzędnej w obu sieciach wirtualnych jest *połączono*.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną przy użyciu polecenia [New-AzVM](/powershell/module/az.compute/new-azvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVm1* w *myVirtualNetwork1* sieci wirtualnej. `-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które chcesz zalogować się do maszyny Wirtualnej za pomocą.

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

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie należy kontynuować o kolejnych krokach, dopóki platforma Azure tworzy maszynę Wirtualną i zwraca dane wyjściowe do programu PowerShell.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Publiczny adres IP maszyny Wirtualnej można nawiązać z Internetu. Użyj polecenia [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję usług pulpitu zdalnego z *myVm1* maszyny Wirtualnej z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Plik Remote Desktop Protocol (RDP) zostanie utworzony, pobrany na komputer i otwarty. Wprowadź nazwę użytkownika i hasło (może być konieczne wybranie **więcej opcji**, następnie **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej), a następnie kliknij przycisk **OK** . Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

Na *myVm1* maszyny Wirtualnej, Włącz kontroli komunikat ICMP (Internet Protocol) za pośrednictwem Windows zapory, dzięki czemu możesz wysłać polecenie ping do tej maszyny Wirtualnej na *myVm2* na późniejszym etapie, za pomocą programu PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Chociaż ping jest używane do komunikacji między maszynami wirtualnymi w tym artykule, dzięki czemu protokołu ICMP przez zaporę Windows we wdrożeniach produkcyjnych nie jest zalecane.

Aby nawiązać połączenie z maszyną wirtualną *myVm2*, wprowadź następujące polecenie w wierszu polecenia maszyny wirtualnej *myVm1*:

```
mstsc /v:10.1.0.4
```

Ponieważ polecenie ping zostało włączone na *myVm1*, użytkownik może teraz wykonać polecenie ping przy użyciu adresu IP z poziomu wiersza polecenia na *myVm2* maszyny Wirtualnej:

```
ping 10.0.0.4
```

Otrzymasz cztery odpowiedzi. Odłącz swoje sesje protokołu RDP do obu maszyn wirtualnych, *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, należy użyć [AzResourcegroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) Aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób połączenia dwóch sieci, w tym samym regionie platformy Azure za pomocą komunikacji równorzędnej sieci wirtualnej. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#powershell), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Możesz [połączyć własny komputer z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za pośrednictwem sieci VPN i interakcji z zasobami w sieci wirtualnej lub w wirtualnych sieciach równorzędnych. W przypadku skryptów wielokrotnego użytku do wykonania wielu zadań omówione w artykułach z sieci wirtualnej, zobacz [przykładowych skryptów](powershell-samples.md).