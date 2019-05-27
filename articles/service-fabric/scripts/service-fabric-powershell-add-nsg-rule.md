---
title: Skrypt programu Azure PowerShell przykładowy — Dodawanie reguły sieciowej grupy zabezpieczeń | Dokumentacja firmy Microsoft
description: Przykładowy skrypt Azure PowerShell — dodaje sieciową grupę zabezpieczeń, aby zezwolić na ruch przychodzący na określonym porcie.
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
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 92efd6c4e63959b6098829892c5cbbaeeabe9f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159980"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Dodawanie reguły dla ruchu przychodzącego sieciowej grupy zabezpieczeń

Ten przykładowy skrypt tworzy regułę sieciowej grupy zabezpieczeń w celu zezwolenia na ruch przychodzący na porcie 8081.  Skrypt pobiera `Microsoft.Network/networkSecurityGroups` zasobu klastra znajduje się w, tworzy nową regułę konfiguracji zabezpieczeń sieci i aktualizuje sieciowej grupy zabezpieczeń. Dostosuj parametry zgodnie z potrzebami.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell przy użyciu instrukcji dostępnych w [przewodniku programu Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Pobiera zasób `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Pobiera sieciową grupę zabezpieczeń o nazwie.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Dodaje konfigurację reguły zabezpieczeń sieci do sieciowej grupy zabezpieczeń. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Ustawia stan docelowy dla sieciowej grupy zabezpieczeń.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).
