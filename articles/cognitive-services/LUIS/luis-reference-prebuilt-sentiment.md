---
title: Analiza tonacji — LUIS
titleSuffix: Azure Cognitive Services
description: W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508024"
---
# <a name="sentiment-analysis"></a>Analiza tonacji
W przypadku skonfigurowania analizy tonacji odpowiedź JSON LUIS obejmuje analizę tonacji. Dowiedz się więcej o analizie tonacji w dokumentacji [Analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) .


## <a name="resolution-for-sentiment"></a>Rozwiązanie dla tonacji

Dane tonacji to wynik z zakresu od 1 do 0 wskazujący dodatnie (bliżej 1) lub ujemne (bliżej 0) tonacji danych.

#### <a name="english-languagetabenglish"></a>[Język angielski](#tab/english)

Gdy kultura jest `en-us`, odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Inne języki](#tab/other-languages)

W przypadku wszystkich innych kultur odpowiedź jest:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

