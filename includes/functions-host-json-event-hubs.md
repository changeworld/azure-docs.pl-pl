---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183025"
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
|maxBatchSize (rozmiar maxBatchSize)|64|Maksymalna liczba zdarzeń odebranych na pętlę odbioru.|
|Konto prefetch|Nie dotyczy|Domyślny plik PrefetchCount, który będzie używany przez podstawową hosta EventProcessorHost.| 
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora EventHub.| 
