---
title: Przykładowy skrypt programu Azure PowerShell — zmienianie zakresu portów protokołu RDP | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — zmiany zakresu portów protokołu we wdrożonym klastrze.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 871fe502a40b4c22f3e57a2f357e940c7f659314
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663931"
---
# <a name="update-the-rdp-port-range-values"></a>Aktualizowanie wartości zakresu portów protokołu RDP

Ten przykładowy skrypt umożliwia zmianę wartości zakresu portów protokołu RDP na maszynach wirtualnych węzła klastra po wdrożeniu klastra.  Dzięki użyciu programu Azure PowerShell cykle podstawowych maszyn wirtualnych nie są wykonywane.  Skrypt pobiera zasób `Microsoft.Network/loadBalancers` w grupie zasobów klastra i aktualizuje wartości `inboundNatPools.frontendPortRangeStart` i `inboundNatPools.frontendPortRangeEnd`. Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji dostępnej w [przewodniku programu Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasób `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Aktualizuje zasób `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
