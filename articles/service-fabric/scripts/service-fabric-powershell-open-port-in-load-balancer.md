---
title: Przykładowy skrypt Azure PowerShell — otwieranie portu aplikacji w module równoważenia obciążenia | Microsoft Docs
description: Przykładowy skrypt Azure PowerShell — otwieranie portu w module równoważenia obciążenia platformy Azure dla aplikacji usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: acf26a9ed3c2f2f569c453d5a6d01c913e63fa2e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597529"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Otwieranie portu aplikacji w module równoważenia obciążenia platformy Azure

Aplikacja usługi Service Fabric uruchamiana na platformie Azure znajduje się za modułem równoważenia obciążenia platformy Azure. Ten przykładowy skrypt otwiera port w module równoważenia obciążenia platformy Azure, aby aplikacja usługi Service Fabric mogła komunikować się z klientami zewnętrznymi. Dostosuj parametry zgodnie z potrzebami. Jeśli klaster znajduje się w sieciowej grupie zabezpieczeń, należy również [dodać regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego](service-fabric-powershell-add-nsg-rule.md), aby umożliwić ruch przychodzący.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj moduł Service Fabric programu PowerShell przy użyciu [Zestawu SDK usługi Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasób platformy Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Pobiera moduł równoważenia obciążenia platformy Azure. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Dodaje konfigurację sondowania do modułu równoważenia obciążenia.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Pobiera konfigurację sondowania dla modułu równoważenia obciążenia. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Dodaje konfigurację reguły do modułu równoważenia obciążenia. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Ustawia stan docelowy dla modułu równoważenia obciążenia. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów dla programu PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
