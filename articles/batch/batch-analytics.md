---
title: Analiza danych usługi Azure Batch | Dokumentacja firmy Microsoft
description: Odwołanie do analizy usługi Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60721559"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch
Tematy w analizach wsadowych zawierają informacje dotyczące zdarzenia i alerty, dostępne dla zasobów usługi Batch.

Zobacz [rejestrowania diagnostycznego usługi Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) Aby uzyskać więcej informacji na temat włączania i używania dzienniki diagnostyczne usługi Batch.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługi Azure Batch emituje następujące zdarzenia dzienników diagnostycznych w okresie istnienia niektórych zasobów usługi Batch.

**Usługa dziennika zdarzeń**
* [Tworzenie puli](batch-pool-create-event.md)
* [Rozpoczęcia usuwania puli](batch-pool-delete-start-event.md)
* [Zakończenie usuwania puli](batch-pool-delete-complete-event.md)
* [Rozpoczęcia zmiany rozmiaru puli](batch-pool-resize-start-event.md)
* [Pełne rozmiaru puli](batch-pool-resize-complete-event.md)
* [Zadanie uruchamiania](batch-task-start-event.md)
* [Zadanie ukończone](batch-task-complete-event.md)
* [Niepowodzenie zadania](batch-task-fail-event.md)