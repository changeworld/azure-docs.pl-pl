---
title: Analiza Azure Batch
description: Tematy w usłudze Batch Analytics zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 5f5063ff2b285a0613d8c7bf367007997afcb778
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935259"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch
Tematy w usłudze Batch Analytics zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi Batch.

Aby uzyskać więcej informacji na temat włączania i używania dzienników diagnostycznych usługi Batch, zobacz [Azure Batch rejestrowanie diagnostyczne](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Azure Batch emituje następujące zdarzenia dziennika diagnostycznego w okresie istnienia niektórych zasobów usługi Batch.

**Usługa dziennika zdarzeń**
* [Tworzenie puli](batch-pool-create-event.md)
* [Rozpoczęcia usuwania puli](batch-pool-delete-start-event.md)
* [Zakończenie usuwania puli](batch-pool-delete-complete-event.md)
* [Rozpoczęcia zmiany rozmiaru puli](batch-pool-resize-start-event.md)
* [Pełne rozmiaru puli](batch-pool-resize-complete-event.md)
* [Zadanie uruchamiania](batch-task-start-event.md)
* [Zadanie ukończone](batch-task-complete-event.md)
* [Niepowodzenie zadania](batch-task-fail-event.md)