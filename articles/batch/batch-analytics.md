---
title: Partia zadań Azure Analytics | Dokumentacja firmy Microsoft
description: Odwołanie do analizy partii zadań Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312681"
---
# <a name="batch-analytics"></a>Analiza danych usługi Batch
Tematy w analizach wsadowych zawierają informacje dotyczące zdarzenia i alerty dostępne dla zasobów usługi partii.

Zobacz [rejestrowania diagnostycznego partii zadań Azure](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) Aby uzyskać więcej informacji o włączaniu i korzystanie z dzienników diagnostycznych partii.

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa partia zadań Azure emituje następujące zdarzenia dziennika diagnostycznego przez cały okres istnienia niektórych zasobów partii.

**Usługa dziennika zdarzeń**
* [Tworzenie puli](batch-pool-create-event.md)
* [Start usuwania puli](batch-pool-delete-start-event.md)
* [Usuwanie puli ukończone](batch-pool-delete-complete-event.md)
* [Początkowy rozmiar puli](batch-pool-resize-start-event.md)
* [Zmiana rozmiaru puli ukończone](batch-pool-resize-complete-event.md)
* [Rozpoczęcia zadania](batch-task-start-event.md)
* [Zadania ukończone](batch-task-complete-event.md)
* [Niepowodzenie zadania](batch-task-fail-event.md)