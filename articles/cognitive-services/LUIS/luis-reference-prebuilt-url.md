---
title: Adres url jednostek LUIS wbudowane - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera adres url informacji wbudowane jednostki opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 4eacf564a295a568a3e2c8d2f44ad0af3fbbe258
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321960"
---
# <a name="url-entity"></a>Adres URL jednostki
Adres URL jednostki wyodrębnia adresów URL z nazwami domen lub adresów IP. Ponieważ była już uczona tej jednostki, Dodaj zniesławiających przykład zawierający adresy URL do aplikacji nie jest konieczne. Adres URL obsługiwany jest w `en-us` tylko kultury. 

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repozytorium Github

## <a name="resolution-for-prebuilt-url-entity"></a>Rozpoznawanie wbudowane jednostki adresu URL
W poniższym przykładzie przedstawiono rozpoznanie **builtin.url** jednostki.

```JSON
{
  "query": "http://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "http://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [porządkowej](luis-reference-prebuilt-ordinal.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek.