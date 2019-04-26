---
title: Otwieranie portów dla maszyny Wirtualnej przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy do maszyny Wirtualnej Windows przy użyciu trybu wdrożenia Menedżera zasobów platformy Azure i programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: f536b32ebf5267f407d2c32eb425ea45469cd3b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405386"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Jak otworzyć porty i punkty końcowe do maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Szybkie polecenia
Aby utworzyć sieciową grupę zabezpieczeń i listy kontroli dostępu reguły należy [najnowszą wersję programu Azure PowerShell zainstalowane](/powershell/azureps-cmdlets-docs). Możesz również [wykonaj następujące kroki w witrynie Azure portal](nsg-quickstart-portal.md).

Zaloguj się do konta platformy Azure:

```powershell
Connect-AzAccount
```

W poniższych przykładach należy zastąpić własnymi wartościami nazw parametrów. Przykładowe nazwy parametru uwzględnione *myResourceGroup*, *myNetworkSecurityGroup*, i *myVnet*.

Utwórz regułę za pomocą [New AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). Poniższy przykład tworzy regułę o nazwie *myNetworkSecurityGroupRule* umożliwia *tcp* ruch na porcie *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Następnie utwórz grupy zabezpieczeń sieci z [New AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) i przypisz zasady HTTP został utworzony w następujący sposób. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teraz możemy przypisywanie sieciowej grupy zabezpieczeń do podsieci. Poniższy przykład przypisuje istniejącą sieć wirtualną o nazwie *myVnet* do zmiennej *$vnet* z [Get AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Kojarzenie sieciowej grupy zabezpieczeń dla podsieci środowiska za pomocą [AzVirtualNetworkSubnetConfig zestaw](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig). Poniższy przykład powoduje skojarzenie podsieci o nazwie *mySubnet* za pomocą sieciowej grupy zabezpieczeń:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Na koniec zaktualizuj sieci wirtualnej przy użyciu [AzVirtualNetwork zestaw](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) w kolejności, aby zmiany zaczęły obowiązywać:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat sieciowych grup zabezpieczeń
Szybkie polecenia w tym miejscu umożliwiają rozpoczęcie pracy z ruchem przepływać do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele świetnych funkcji i stopień szczegółowości do kontrolowania dostępu do zasobów. Przeczytaj więcej o [tworzenie sieciowej grupy zabezpieczeń i listy kontroli dostępu reguły tutaj](tutorial-virtual-network.md#secure-network-traffic).

W przypadku aplikacji sieci web o wysokiej dostępności należy umieszczać maszyny wirtualne za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która pozwala na filtrowanie ruchu sieciowego. Aby uzyskać więcej informacji, zobacz [sposób ładowania równoważenia maszyn wirtualnych systemu Linux na platformie Azure do utworzenia aplikacji o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono prosta Reguła zezwalająca na ruch HTTP. Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe w następujących artykułach:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/security-overview.md)
* [Omówienie usługi Azure Resource Manager dla usługi równoważenia obciążenia](../../load-balancer/load-balancer-arm.md)

