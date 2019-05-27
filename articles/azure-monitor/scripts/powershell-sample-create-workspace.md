---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie obszaru roboczego usługi Log Analytics | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — tworzenie obszaru roboczego usługi Log Analytics
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66129173"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Tworzenie obszaru roboczego usługi Log Analytics przy użyciu programu PowerShell

Ten skrypt szybko tworzy i konfiguruje obszar roboczy usługi Azure Log Analytics. Jest on wymagany, jeśli chcesz rozpocząć zbieranie i analizowanie danych oraz wykonywanie na nich działań.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt korzysta z poniższych poleceń w celu utworzenia nowego obszaru roboczego usługi Log Analytics w ramach subskrypcji. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Pobiera informacje o istniejącym obszarze roboczym. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Tworzy obszar roboczy w określonej grupie zasobów i lokalizacji. |


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

