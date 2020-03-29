---
title: Analiza tonacji — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Jeśli jest skonfigurowana analiza tonacji, odpowiedź json usługi LUIS zawiera analizę tonacji.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270417"
---
# <a name="sentiment-analysis"></a>Analiza tonacji
Jeśli jest skonfigurowana analiza tonacji, odpowiedź json usługi LUIS zawiera analizę tonacji. Dowiedz się więcej o analizie opinii w dokumentacji [analizy tekstu.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Rozwiązanie dla sentymentu

Dane o nastrojach to wynik od 1 do 0 wskazujący pozytywny (bliżej 1) lub ujemny (bliżej 0) sentyment danych.

#### <a name="english-language"></a>[Język angielski](#tab/english)

Gdy kultura `en-us`jest , odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Inne języki](#tab/other-languages)

Dla wszystkich innych kultur odpowiedzią jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania V3](luis-migration-api-v3.md).

