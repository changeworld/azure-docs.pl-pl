---
title: Przykład programu PowerShell — równoważenie obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności — Azure | Microsoft Docs
description: Ten przykładowy skrypt programu PowerShell przedstawia sposób równoważenia obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 31fd1e7d10692a1fbb552a483ae00e0088785a67
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-powershell-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Przykładowy skrypt programu PowerShell: równoważenie obciążenia ruchu do maszyn wirtualnych na potrzeby wysokiej dostępności

Ten przykładowy skrypt programu PowerShell umożliwia utworzenie wszystkich elementów potrzebnych do uruchomienia kilku maszyn wirtualnych z systemem Windows skonfigurowanych w ramach konfiguracji o wysokiej dostępności i zrównoważonym obciążeniu. Po uruchomieniu skryptu będziesz mieć trzy maszyny wirtualne dołączone do zestawu dostępności platformy Azure i dostępne za pośrednictwem usługi Azure Load Balancer.

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), a następnie uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)  | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)  | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [New-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [New-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Tworzy regułę translatora adresów sieciowych modułu równoważenia obciążenia.  Reguły translatora adresów sieciowych mapują port modułu równoważenia obciążenia na port na maszynie wirtualnej. W tym przykładzie tworzona jest reguła translatora adresów sieciowych dla ruchu SSH do każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia.  |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Tworzy regułę sieciowej grupy zabezpieczeń zezwalającą na ruch przychodzący. W tym przykładzie port 22 jest otwierany dla ruchu protokołu SSH. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Tworzy konfigurację maszyny wirtualnej. Ta konfiguracja zawiera informacje, takie jak nazwa maszyny wirtualnej, system operacyjny i poświadczenia administracyjne. Jest ona używana podczas tworzenia maszyny wirtualnej. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)  | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell dla sieci można znaleźć w [dokumentacji i omówieniu sieci platformy Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
