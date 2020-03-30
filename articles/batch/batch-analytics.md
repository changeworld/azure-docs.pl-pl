---
title: Azure Batch Analytics
description: Tematy w analizie wsadowej zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi wsadowej.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025966"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch
Tematy w analizie wsadowej zawierają informacje referencyjne dotyczące zdarzeń i alertów dostępnych dla zasobów usługi wsadowej.

Zobacz [rejestrowanie diagnostyczne usługi Azure Batch, aby](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) uzyskać więcej informacji na temat włączania i korzystania z dzienników diagnostycznych partii.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Azure Batch emituje następujące zdarzenia dziennika diagnostycznego w okresie istnienia niektórych zasobów usługi Batch.

**Zdarzenia dziennika usługi**
* [Tworzenie puli](batch-pool-create-event.md)
* [Początek usuwania puli](batch-pool-delete-start-event.md)
* [Ukończono usuwanie puli](batch-pool-delete-complete-event.md)
* [Rozpoczęcie ponownego rozmiaru puli](batch-pool-resize-start-event.md)
* [Rozmiar rozmiaru puli został ukończony](batch-pool-resize-complete-event.md)
* [Rozpoczęcie zadania](batch-task-start-event.md)
* [Zadanie ukończone](batch-task-complete-event.md)
* [Niepowodzenie zadania](batch-task-fail-event.md)