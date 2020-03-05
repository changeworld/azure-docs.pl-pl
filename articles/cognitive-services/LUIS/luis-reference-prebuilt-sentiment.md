---
title: Analiza tonacji — LUIS
titleSuffix: Azure Cognitive Services
description: Jeśli jest skonfigurowana analiza tonacji, odpowiedź w formacie json usługi LUIS obejmuje analizę tonacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270417"
---
# <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli jest skonfigurowana analiza tonacji, odpowiedź w formacie json usługi LUIS obejmuje analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .


## <a name="resolution-for-sentiment"></a>Rozwiązanie dla tonacji

Dane opinii jest wynik w zakresie od 1 i 0, wskazując pozytywny (bliżej 1) lub ujemną (bliżej 0) tonacji danych.

#### <a name="english-language"></a>[Język angielski](#tab/english)

Gdy kultura jest `en-us`, odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Inne języki](#tab/other-languages)

Dla wszystkich innych języków odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

