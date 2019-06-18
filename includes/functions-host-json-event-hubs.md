---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131705"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxBatchSize|64|Maksymalna otrzymana liczba zdarzeń na pętli odbioru.|
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez podstawowej klasy EventProcessorHost.| 
|batchCheckpointFrequency|1|Liczba zdarzeń wsadów przetwarzana przed utworzeniem punktu kontrolnego EventHub kursora.| 
