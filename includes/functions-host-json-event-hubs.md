---
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104453"
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

