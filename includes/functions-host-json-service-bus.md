---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134237"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxConcurrentCalls|16|Maksymalna liczba równoczesnych wywołań wywołanie zwrotne, które powinno zainicjować "pompy komunikatów". Domyślnie środowisko uruchomieniowe usługi Functions przetwarza wiele wiadomości jednocześnie. Aby skierowania czasu przetwarzania tylko jednej kolejki lub tematu wiadomości w czasie, należy ustawić `maxConcurrentCalls` 1. | 
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez MessageReceiver bazowego.| 
|autoRenewTimeout|00:05:00|Maksymalny czas trwania, w którym blokadę komunikatu zostanie odnowiony automatycznie.| 
