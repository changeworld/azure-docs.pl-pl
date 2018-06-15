---
title: Uzyskuj dostęp do sieci wirtualnych sieci wirtualnej komunikacji równorzędnej - PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule Dowiedz się jak połączyć sieci wirtualnych z sieci wirtualnej komunikacji równorzędnej, przy użyciu programu Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3b4a67a06d628040d155a0fe2d78beb2eee25090
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602454"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Uzyskuj dostęp do sieci wirtualnych sieci wirtualnej komunikacji równorzędnej przy użyciu programu PowerShell

Sieci wirtualne możesz łączyć ze sobą za pomocą komunikacji równorzędnej sieci wirtualnych. Po połączeniu sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w obu sieciach wirtualnych mogą komunikować się ze sobą przy takim samym opóźnieniu i z taką samą przepustowością, jakby zasoby były w tej samej sieci wirtualnej. W tym artykule omówiono sposób wykonywania następujących zadań:

* Tworzenie dwóch sieci wirtualnych
* Łączenie dwóch sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych
* Wdrażanie maszyny wirtualnej w każdej sieci wirtualnej
* Nawiązywanie komunikacji między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli użytkownik chce zainstalować i używać środowiska PowerShell lokalnie, w tym artykule wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-virtual-networks"></a>Tworzenie sieci wirtualnych

Przed utworzeniem sieci wirtualnej, należy utworzyć grupę zasobów dla sieci wirtualnej i innych zasobów utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Utwórz sieć wirtualną przy użyciu polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Poniższy przykład tworzy sieć wirtualną o nazwie *myVirtualNetwork1* z prefiksem adresu *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Utwórz konfigurację podsieci za pomocą polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Poniższy przykład tworzy konfiguracji podsieci o prefiksie adresu 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Zapisać konfiguracji podsieci do sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), która tworzy podsieć:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzureRmVirtualNetwork
```

Tworzenie sieci wirtualnej z prefiksu adresu 10.1.0.0/16 i jedną podsieć:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzureRmVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Tworzenia komunikacji równorzędnej sieci wirtualnych

Utwórz komunikacji równorzędnej z [AzureRmVirtualNetworkPeering Dodaj](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering). Następujące elementy równorzędne przykład *myVirtualNetwork1* do *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

W danych wyjściowych zwrócona po wykonaniu poprzedniego polecenia, zobacz który **PeeringState** jest *zainicjowano*. Komunikacji równorzędnej pozostaje w *zainicjowano* stanu, dopóki nie zostaną utworzone komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. Utwórz komunikacji równorzędnej z *myVirtualNetwork2* do *myVirtualNetwork1*. 

```azurepowershell-interactive
Add-AzureRmVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

W danych wyjściowych zwrócona po wykonaniu poprzedniego polecenia, zobacz który **PeeringState** jest *połączony*. Azure również zmienić stan komunikacji równorzędnej *myVirtualNetwork1 myVirtualNetwork2* komunikacji równorzędnej do *połączony*. Upewnij się, że stan komunikacji równorzędnej *myVirtualNetwork1 myVirtualNetwork2* równorzędna zmieniony na *połączony* z [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzureRmVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Zasoby w jednej sieci wirtualnej nie może komunikować się z zasobami w innych sieci wirtualnej do **PeeringState** dla komunikacji równorzędnych w obu sieci wirtualnych jest *połączony*. 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz maszynę wirtualną w każdej sieci wirtualnej, dzięki czemu będzie można komunikować się między nimi w kolejnym kroku.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Poniższy przykład tworzy Maszynę wirtualną o nazwie *myVm1* w *myVirtualNetwork1* sieci wirtualnej. `-AsJob` Opcja tworzy maszynę Wirtualną w tle, dzięki czemu można kontynuować do następnego kroku. Po wyświetleniu monitu wprowadź nazwę użytkownika i hasło, które chcesz zalogować się do maszyny Wirtualnej z.

```azurepowershell-interactive
New-AzureRmVm `
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
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie Kontynuuj dalszych krokach dopóki Azure tworzy maszynę Wirtualną i zwraca dane wyjściowe do programu PowerShell.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

Publiczny adres IP maszyny Wirtualnej można połączyć z Internetu. Użyj polecenia [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), aby uzyskać publiczny adres IP maszyny wirtualnej. W poniższym przykładzie zwracany jest publiczny adres IP maszyny wirtualnej o nazwie *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Użyj następującego polecenia, aby utworzyć sesję pulpitu zdalnego z *myVm1* maszyny Wirtualnej z komputera lokalnego. Zastąp ciąg `<publicIpAddress>` adresem IP zwróconym w poprzednim poleceniu.

```
mstsc /v:<publicIpAddress>
```

Plik protokołu Remote Desktop Protocol (RDP) jest utworzony, pobrana na komputer i otworzyć. Wprowadź nazwę użytkownika i hasło (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić poświadczenia zostały wprowadzone podczas tworzenia maszyny Wirtualnej), a następnie kliknij przycisk **OK** . Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Kliknij przycisk **Tak** lub **Kontynuuj**, aby kontynuować nawiązywanie połączenia.

Na *myVm1* maszyny Wirtualnej, Włącz zapory kontroli protokołu ICMP (Internet Message) za pośrednictwem systemu Windows, można zbadać poleceniem ping tej maszyny Wirtualnej z *myVm2* w kolejnym kroku przy użyciu programu PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Chociaż ping jest używany do komunikacji między maszynami wirtualnymi w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.

Aby nawiązać połączenie z maszyną wirtualną *myVm2*, wprowadź następujące polecenie w wierszu polecenia maszyny wirtualnej *myVm1*:

```
mstsc /v:10.1.0.4
```

Ponieważ włączony ping *myVm1*, użytkownik może teraz wysyłać polecenia ping go za pomocą adresu IP z wiersza polecenia na *myVm2* maszyny Wirtualnej:

```
ping 10.0.0.4
```

Otrzymasz cztery odpowiedzi. Odłącz swoje sesje protokołu RDP do obu maszyn wirtualnych, *myVm1* i *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, użyj [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) można usunąć grupy zasobów i wszystkie zasoby zawiera.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób połączyć dwie sieci w tym samym regionie Azure, z sieci wirtualnej komunikacji równorzędnej. Możesz też nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych [obsługiwanych regionach](virtual-network-manage-peering.md#cross-region) i w [różnych subskrypcjach platformy Azure](create-peering-different-subscriptions.md#powershell), a także utworzyć [projekty sieci w topologii gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) z komunikacją równorzędną. Aby dowiedzieć się więcej na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Virtual network peering overview (Omówienie komunikacji równorzędnej sieci wirtualnych)](virtual-network-peering-overview.md) i [Manage virtual network peerings (Zarządzanie komunikacją równorzędną sieci wirtualnych)](virtual-network-manage-peering.md).

Możesz [połączyć swojego komputera do sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za pośrednictwem sieci VPN i interakcji z zasobami w sieci wirtualnej lub połączyć za pomocą sieci wirtualnych. Dla skryptów wielokrotnego użytku do wykonania wielu zadań omówione w artykułach sieci wirtualnej, zobacz [przykłady skryptów](powershell-samples.md).
