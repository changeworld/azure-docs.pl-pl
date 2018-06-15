---
title: Skrypt programu PowerShell Azure przykładowe — Dodawanie reguły grupy zabezpieczeń sieci | Dokumentacja firmy Microsoft
description: Przykładowy skrypt programu PowerShell Azure - dodaje sieciową grupę zabezpieczeń, aby zezwalać na ruch przychodzący na określonym porcie.
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
ms.date: 11/28/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd3c648ee63c45bef305658832a4d31dfdb213be
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2017
ms.locfileid: "25987384"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Dodaj regułę grupy zabezpieczeń sieci dla ruchu przychodzącego

Ten przykładowy skrypt tworzy reguły grupy zabezpieczeń sieci, aby zezwalać na ruch przychodzący na porcie 8081.  Pobiera skrypt `Microsoft.Network/networkSecurityGroups` zasobu klastra znajduje się w, tworzy nową regułę konfiguracji zabezpieczeń sieci i aktualizuje grupę zabezpieczeń sieci. Dostosuj parametry zgodnie z potrzebami.

W razie potrzeby zainstalować program Azure PowerShell, korzystając z instrukcji w [Przewodnik programu Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Pobiera `Microsoft.Network/networkSecurityGroups` zasobów. |
|[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)| Pobiera sieciową grupę zabezpieczeń o nazwie.|
|[Dodaj AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)| Dodaje konfiguracji reguły zabezpieczeń sieci do grupy zabezpieczeń sieci. |
|[Zestaw AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)| Ustawia stan cel dla grupy zabezpieczeń sieci.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).
