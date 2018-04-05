---
title: Przykłady dla programu Azure PowerShell — zestaw skalowania z pojedynczą strefą | Microsoft Docs
description: Przykłady dla programu Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4e4f6d26c119a113bab0220828c847f438e7bc7a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Tworzenie jednostrefowego zestawu skalowania maszyn wirtualnych za pomocą programu PowerShell
Ten skrypt tworzy zestaw skalowania maszyn wirtualnych z systemem Windows Server 2016 w jednej strefie dostępności. Po uruchomieniu skryptu dostęp do maszyny wirtualnej można uzyskać za pomocą protokołu RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Uruchom następujące polecenie, aby usunąć grupę zasobów, zestaw skalowania i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa następujących poleceń w celu utworzenia wdrożenia. Każda pozycja w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Tworzy obiekt konfiguracji, który definiuje podsieć sieci wirtualnej. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Tworzy sieć wirtualną i podsieć. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Tworzy statyczny publiczny adres IP. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Tworzy obiekt konfiguracji, który definiuje fronton modułu równoważenia obciążenia, w tym publiczny adres IP. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Tworzy obiekt konfiguracji, który definiuje zaplecze modułu równoważenia obciążenia. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Tworzy moduł równoważenia obciążenia oparty na obiektach konfiguracji frontonu i zaplecza. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Tworzy sondę kondycji modułu równoważenia obciążenia umożliwiającą monitorowanie ruchu na porcie 80 protokołu TCP. Jeśli zostaną napotkane dwa kolejne błędy w odstępie 15 sekund, kondycja punktu końcowego jest określana jako zła. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Tworzy obiekt konfiguracji, który definiuje reguły modułu równoważenia obciążenia służące do dystrybucji ruchu na porcie 80 protokołu TCP z puli frontonu do puli zaplecza. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Aktualizuje moduł równoważenia obciążenia za pomocą sondy kondycji i reguł modułu równoważenia obciążenia. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Tworzy obiekt konfiguracji definiujący regułę sieciowej grupy zabezpieczeń, która zezwala na ruch na porcie 80 protokołu TCP. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Tworzy sieciową grupę zabezpieczeń i regułę. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Aktualizuje podsieć sieci wirtualnej, która ma zostać skojarzona z sieciową grupą zabezpieczeń. Wszystkie wystąpienia maszyn wirtualnych podłączone do podsieci dziedziczą reguły sieciowej grupy zabezpieczeń. |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | Aktualizuje sieć wirtualną za pomocą konfiguracji podsieci i sieciowej grupy zabezpieczeń. |
| [New-AzureRmVmssIpConfig](/powershell/module/azurerm.compute/new-azurermvmssipconfig) | Tworzy obiekt konfiguracji, który definiuje informacje o adresie IP zestawu skalowania maszyn wirtualnych, w celu połączenia wystąpień maszyn wirtualnych z pulą zaplecza modułu równoważenia obciążenia i pulą translacji NAT ruchu przychodzącego.
| [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) | Tworzy obiekt konfiguracji, który definiuje liczbę wystąpień maszyn wirtualnych w zestawie skalowania, rozmiar wystąpienia maszyny wirtualnej i tryb zasad uaktualniania. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Tworzy obiekt konfiguracji, który definiuje obraz maszyny wirtualnej w celu użycia dla wystąpień maszyn wirtualnych. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Tworzy profil konfiguracji, który definiuje nazwę wystąpienia maszyny wirtualnej i poświadczenia użytkownika. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Tworzy obiekt konfiguracji, który definiuje kartę sieciową dla każdego wystąpienia maszyny wirtualnej. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Łączy wszystkie obiekty konfiguracji w celu utworzenia zestawu skalowania maszyn wirtualnych. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Pobiera informacje o zestawie skalowania maszyn wirtualnych. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Dodaje rozszerzenie maszyny wirtualnej dla niestandardowego skryptu, aby zainstalować podstawową aplikację internetową. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aktualizuje model zestawu skalowania maszyn wirtualnych w celu zastosowania rozszerzenia maszyny wirtualnej. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Pobiera informacje o przypisanym publicznym adresie IP używanym przez moduł równoważenia obciążenia. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Usuwa grupę zasobów i wszystkie zasoby w niej zawarte. |

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell zestawu skalowania maszyn wirtualnych można znaleźć w [dokumentacji zestawu skalowania maszyn wirtualnych platformy Azure](../powershell-samples.md).