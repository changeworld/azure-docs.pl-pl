---
title: Przykładowy skrypt Azure PowerShell — Dodawanie reguły sieciowej grupy zabezpieczeń | Microsoft Docs
description: Przykładowy skrypt Azure PowerShell — dodaje sieciową grupę zabezpieczeń w celu zezwolenia na ruch przychodzący na określonym porcie.
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
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 1418b85bfa2579ed2dee5211a79a75adf14f1547
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592279"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Dodawanie reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego

Ten przykładowy skrypt tworzy regułę sieciowej grupy zabezpieczeń, która zezwala na ruch przychodzący na porcie 8081.  Skrypt pobiera `Microsoft.Network/networkSecurityGroups` zasób, w którym znajduje się klaster, tworzy nową regułę konfiguracji zabezpieczeń sieci i aktualizuje sieciową grupę zabezpieczeń. Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji dostępnych w [przewodniku programu Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasób `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Pobiera sieciową grupę zabezpieczeń według nazwy.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Ustawia stan celu dla sieciowej grupy zabezpieczeń.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).
