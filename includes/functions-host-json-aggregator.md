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
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251274"
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