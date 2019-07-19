---
title: Azure Batch Analytics | Microsoft Docs
description: Informacje dotyczące Azure Batch analizy.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 0f8233846f3835a2bbccf9a7072f223d435b90af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323826"
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