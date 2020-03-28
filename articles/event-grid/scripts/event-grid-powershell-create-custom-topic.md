---
title: Przykładowy skrypt programu Azure PowerShell — tworzenie tematu niestandardowego | Microsoft Docs
description: Ten artykuł zawiera przykładowy skrypt programu Azure PowerShell, który pokazuje, jak utworzyć temat niestandardowy usługi Event Grid.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: tomfitz
ms.openlocfilehash: 900f31ff87e6a588fc1d1e3efd854437d2c8c1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719946"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Tworzenie tematu niestandardowego usługi Event Grid przy użyciu programu PowerShell

Ten skrypt tworzy temat niestandardowy usługi Event Grid.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Przykładowy skrypt

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt używa następującego polecenia w celu utworzenia tematu niestandardowego. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Nowy-AzEventGridTopic](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridtopic) | Tworzy niestandardowy temat usługi Event Grid. |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](../overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
* Aby uzyskać więcej informacji na temat programu PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
