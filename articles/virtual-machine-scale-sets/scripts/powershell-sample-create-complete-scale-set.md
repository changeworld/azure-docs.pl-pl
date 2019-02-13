---
title: Przykłady dla programu Azure PowerShell — tworzenie pełnego zestawu skalowania maszyn wirtualnych | Microsoft Docs
description: Przykłady dla programu Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 20bf851cc9b2965a355286699e1ef255887d7650
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697092"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Tworzenie pełnego zestawu skalowania maszyn wirtualnych przy użyciu programu PowerShell
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016. Poszczególne zasoby są konfigurowane i tworzone bez konieczności korzystania z [wbudowanych opcji tworzenia zasobów dostępnych tutaj w skrypcie New-AzureRmVmss](powershell-sample-create-simple-scale-set.md). Po uruchomieniu skryptu możesz uzyskać dostęp do wystąpień maszyn wirtualnych za pośrednictwem protokołu RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy konfigurację podsieci. Ta konfiguracja jest używana podczas procesu tworzenia sieci wirtualnej. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy publiczny adres IP. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Tworzy konfigurację adresu IP frontonu na potrzeby modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Tworzy konfigurację puli adresów zaplecza na potrzeby modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Tworzy konfigurację reguły NAT dla ruchu przychodzącego na potrzeby modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Tworzy moduł równoważenia obciążenia. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Tworzy konfigurację sondowania na potrzeby modułu równoważenia obciążenia. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Tworzy konfigurację reguły na potrzeby modułu równoważenia obciążenia. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | Zaktualizuj usługę równoważenia obciążenia przy użyciu podanych informacji. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | Utwórz konfigurację protokołu IP dla zestawu skalowania wystąpień maszyny wirtualnej. Wystąpienia maszyny wirtualnej są połączone z pulą zaplecza modułu równoważenia obciążenia, pulą translacji NAT i podsiecią sieci wirtualnej. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | Tworzy konfigurację zestawu skali. Ta konfiguracja zawiera informacje, takie jak liczba tworzonych wystąpień maszyn wirtualnych, jednostka magazynowa maszyny wirtualnej (rozmiar) i tryb zasad uaktualniania. Konfiguracja jest dodawana przez dodatkowe polecenia cmdlet i jest używana podczas tworzenia zestawu skalowania. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Zdefiniuj obraz, który ma służyć do tworzenia wystąpień maszyn wirtualnych, i dodaj go do konfiguracji zestawu skalowania. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Zdefiniuj nazwę i hasło użytkownika z uprawnieniami administracyjnymi oraz prefiks nazwy maszyny wirtualnej. Dodaj te wartości do konfiguracji zestawu skalowania. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | Dodaj interfejs sieci wirtualnej do wystąpień maszyn wirtualnych w oparciu o konfigurację IP. Dodaj te wartości do konfiguracji zestawu skalowania. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | W oparciu o informacje zawarte w konfiguracji zestawu skalowania utwórz zestaw skalowania. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../powershell-samples.md).
