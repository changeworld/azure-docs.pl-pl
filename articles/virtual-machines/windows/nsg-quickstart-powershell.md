---
title: Otwieranie portów z maszyną wirtualną przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak otworzyć port / utworzyć punktu końcowego maszyny Wirtualnej systemu Windows przy użyciu wdrożeń w trybie Menedżera zasobów platformy Azure i programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: b8eb36e4abad12cb46493846a024da00f9146d40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Jak otworzyć porty i punktów końcowych do maszyny Wirtualnej na platformie Azure przy użyciu programu PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Szybkie polecenia
Aby utworzyć grupę zabezpieczeń sieci i listy ACL zasady należy [najnowszą wersję programu Azure PowerShell zainstalowany](/powershell/azureps-cmdlets-docs). Możesz również [wykonaj te czynności przy użyciu portalu Azure](nsg-quickstart-portal.md).

Zaloguj się do konta platformy Azure:

```powershell
Connect-AzureRmAccount
```

W poniższych przykładach zastąpić parametr własne wartości. Przykład nazwy parametrów uwzględnione *myResourceGroup*, *myNetworkSecurityGroup*, i *myVnet*.

Utworzyć regułę z [AzureRmNetworkSecurityRuleConfig nowy](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Poniższy przykład tworzy reguły o nazwie *myNetworkSecurityGroupRule* umożliwia *tcp* ruch na porcie *80*:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig `
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

Następnie utwórz grupy zabezpieczeń sieci z [AzureRmNetworkSecurityGroup nowy](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) i Przypisz reguły HTTP utworzony w następujący sposób. Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Teraz umożliwia przypisywanie sieciowej grupy zabezpieczeń do podsieci. W poniższym przykładzie przypisano istniejącej sieci wirtualnej o nazwie *myVnet* do zmiennej *$vnet* z [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell
$vnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Skojarz grupy zabezpieczeń sieci z podsieci z [AzureRmVirtualNetworkSubnetConfig zestawu](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig). Poniższy przykład powoduje skojarzenie podsieci o nazwie *mySubnet* z grupy zabezpieczeń sieci:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Na koniec, aktualizacja sieci wirtualnej z [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) aby zmiany zaczęły obowiązywać:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Więcej informacji na temat grup zabezpieczeń sieci
Szybkie polecenia tutaj pozwala rozpocząć pracę z ruchem przepływać do maszyny Wirtualnej. Grupy zabezpieczeń sieci zapewniają wiele funkcje i poziom szczegółowości kontrolowania dostępu do zasobów. Możesz przeczytać dodatkowe informacje [Tworzenie grupy zabezpieczeń sieci i listy ACL zasady tutaj](tutorial-virtual-network.md#secure-network-traffic).

Dla aplikacji sieci web wysokiej dostępności należy umieszczać maszyny wirtualne za usługą równoważenia obciążenia Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, który umożliwia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [jak załadować saldo maszyn wirtualnych systemu Linux na platformie Azure, aby utworzyć aplikację wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Kolejne kroki
W tym przykładzie utworzono prosta Reguła zezwalająca na ruch HTTP. Można znaleźć informacje dotyczące tworzenia środowisk bardziej szczegółowe zawierają następujące artykuły:

* [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Co to jest sieciowa grupa zabezpieczeń?](../../virtual-network/virtual-networks-nsg.md)
* [Omówienie usługi Azure Resource Manager dla usługi równoważenia obciążenia](../../load-balancer/load-balancer-arm.md)

