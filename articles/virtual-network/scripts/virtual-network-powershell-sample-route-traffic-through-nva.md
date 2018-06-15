---
title: Przykładowy skrypt programu Azure PowerShell — kierowanie ruchu przez wirtualne urządzenie sieciowe | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — kierowanie ruchu przez wirtualne urządzenie sieciowe w ramach zapory.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: b74498da80391624025887546fcfdf137bacc9d8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601138"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Przykładowy skrypt służący do kierowania ruchu przez wirtualne urządzenie sieciowe

Ten przykładowy skrypt tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Tworzy również maszynę wirtualną z włączonym przekazywaniem adresu IP, aby przekierowywać ruch między dwiema podsieciami. Po uruchomieniu skryptu można wdrażać oprogramowanie sieciowe, takie jak aplikacja zapory, na maszynie wirtualnej.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/powershell) lub z lokalnej instalacji programu PowerShell. Jeśli używasz programu PowerShell lokalnie, ten skrypt wymaga modułu AzureRM PowerShell w wersji 5.4.1 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable AzureRM`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt


[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```
## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, sieci wirtualnej i sieciowych grup zabezpieczeń. Każde polecenie w poniższej tabeli stanowi link do dokumentacji polecenia:

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną i podsieć frontonu platformy Azure. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy podsieci zaplecza i strefy DMZ. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy publiczny adres IP umożliwiający uzyskiwanie dostępu do maszyny wirtualnej z Internetu. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Tworzy interfejs sieci wirtualnej i włącza przekazywanie adresów IP. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Tworzy reguły sieciowych grup zabezpieczeń, które zezwalają na użycie portów HTTP i HTTPS dla ruchu przychodzącego do maszyny wirtualnej. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| Tworzy skojarzenie sieciowych grup zabezpieczeń i tabel tras z podsieciami. |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| Tworzy tabelę tras dla wszystkich tras. |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| Tworzy trasy na potrzeby kierowania ruchem między podsieciami i Internetem za pośrednictwem maszyny wirtualnej. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Tworzy maszynę wirtualną i dołącza do niej kartę sieciową. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | Usuwa grupę zasobów i wszystkie zasoby, które zawiera. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Więcej przykładowych skryptów programu PowerShell dla sieci wirtualnej można znaleźć w temacie [Virtual network PowerShell samples](../powershell-samples.md) (Przykładowe skrypty programu PowerShell dla sieci wirtualnej).
