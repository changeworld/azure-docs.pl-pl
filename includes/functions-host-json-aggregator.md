---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279273"
---
Określa, ile wywołań funkcji jest agregowanych podczas [obliczania metryk usługi Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Właściwość |Domyślne  | Opis |
|---------|---------|---------| 
|batchSize|1000|Maksymalna liczba żądań do agregacji.| 
|FlushTimeout|00:00:30|Maksymalny okres do zagregowania.| 

Wywołania funkcji są agregowane po osiągnięciu pierwszego z dwóch limitów.
