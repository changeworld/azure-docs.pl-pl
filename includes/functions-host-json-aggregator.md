---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: da0cbab59d9c801419ac4b3704fee3275f337fd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737201"
---
Określa, ile wywołania funkcji są agregowane, kiedy [obliczanie metryki dla usługi Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

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
|batchSize|1000|Maksymalna liczba żądań do zagregowania.| 
|flushTimeout|00:00:30|Maksymalny czas okresu do zagregowania.| 

Wywołania funkcji są agregowane, gdy pierwsze z tych limitów osiągnięciu.