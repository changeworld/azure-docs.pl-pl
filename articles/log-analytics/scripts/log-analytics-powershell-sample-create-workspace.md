---
title: Skrypt programu PowerShell Azure przykładowe — Tworzenie obszaru roboczego analizy dzienników | Dokumentacja firmy Microsoft
description: Skrypt programu PowerShell Azure przykładowe — Tworzenie obszaru roboczego analizy dzienników do
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855439"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Tworzenie obszaru roboczego analizy dzienników przy użyciu programu PowerShell

Ten skrypt pobiera możesz działa szybko z obszarem roboczym usługi Analiza dzienników Azure, który jest wymagany, jeśli chcesz uruchomić zbierania, analizowanie i podjęcie działań na danych.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Wyjaśnienie skryptu

Ten skrypt używa następujących poleceń w celu utworzenia nowego obszaru roboczego analizy dzienników w ramach subskrypcji. Każde polecenie w tabeli łącza do dokumentacji określonego polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Pobiera informacje o istniejącym obszarem roboczym. |
| [Nowe AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Obszar roboczy tworzy w określonej grupy zasobów i lokalizacji. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących modułu Azure PowerShell, zobacz [dokumentacji programu Azure PowerShell](/powershell/azure/overview).

