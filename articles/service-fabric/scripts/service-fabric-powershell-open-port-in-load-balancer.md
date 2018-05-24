---
title: Przykładowy skrypt Azure PowerShell — otwieranie portu aplikacji w module równoważenia obciążenia | Microsoft Docs
description: Przykładowy skrypt Azure PowerShell — otwieranie portu w module równoważenia obciążenia platformy Azure dla aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 0549f5f2b5b0f8fdfc18b8c091c1065d6137b8c6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366175"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Otwieranie portu aplikacji w module równoważenia obciążenia platformy Azure

Aplikacja usługi Service Fabric uruchamiana na platformie Azure znajduje się za modułem równoważenia obciążenia platformy Azure. Ten przykładowy skrypt otwiera port w module równoważenia obciążenia platformy Azure, aby aplikacja usługi Service Fabric mogła komunikować się z klientami zewnętrznymi. Dostosuj parametry zgodnie z potrzebami. Jeśli klaster znajduje się w sieciowej grupie zabezpieczeń, należy również [dodać regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego](service-fabric-powershell-add-nsg-rule.md), aby umożliwić ruch przychodzący.

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Pobiera zasób platformy Azure.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Pobiera moduł równoważenia obciążenia platformy Azure. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Dodaje konfigurację sondowania do modułu równoważenia obciążenia.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Pobiera konfigurację sondowania dla modułu równoważenia obciążenia. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Dodaje konfigurację reguły do modułu równoważenia obciążenia. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Ustawia stan docelowy dla modułu równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
