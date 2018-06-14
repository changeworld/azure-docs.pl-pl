---
title: Przykładowy skrypt programu PowerShell Azure - Równoważenie obciążenia wielu witryn sieci Web przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Przykładowy skrypt programu PowerShell Azure - Równoważenie obciążenia wielu witryn sieci Web do tej samej maszyny wirtualnej
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 44cb76a09cdaacd1bffd653bae6a5735a81dc6f6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597823"
---
# <a name="load-balance-multiple-websites"></a>Równoważenie obciążenia wielu witryn sieci Web

Ten przykładowy skrypt tworzy sieć wirtualną z dwóch maszyn wirtualnych (VM), które są członkami zestawu dostępności. Moduł równoważenia obciążenia kieruje ruch dla dwóch osobnych adresów IP do dwóch maszyn wirtualnych. Po uruchomieniu skryptu, można wdrożyć oprogramowanie serwera sieci web do maszyn wirtualnych i hostów wiele witryn sieci web z własnego adresu IP.

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następujących poleceń do utworzenia grupy zasobów, sieci wirtualnej, usługi równoważenia obciążenia i wszystkie powiązane zasoby. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [Nowe AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Tworzy zbiór zapewnić wysoką dostępność dostępności Azure. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy publiczny adres IP. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Tworzy konfiguracji IP frontonu modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Tworzy Konfiguracja puli adresów zaplecza, usługi równoważenia obciążenia. |
| [Nowe AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Tworzy sondę równoważenia obciążenia sieciowego. Sonda równoważenia obciążenia sieciowego jest używana do monitorowania poszczególnych maszyn wirtualnych w zestawie równoważenia obciążenia sieciowego. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [Nowe AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Tworzy regułę równoważenia obciążenia sieciowego. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia sieciowego jest kierowany do portu 80 jednej z maszyn wirtualnych w module równoważenia obciążenia sieciowego. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Tworzy moduł równoważenia obciążenia. |
| [Nowe AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/new-azurermnetworkinterfaceipconfig) | Definiuje zaawansowane funkcje dla interfejsu sieci wirtualnej. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Tworzy interfejs sieciowy. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Tworzy maszynę wirtualną. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Dodatkowe przykłady skryptów PowerShell sieci można znaleźć w [Azure Przegląd dokumentacji](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
