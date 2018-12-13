---
title: Adres URL ze wstępnie utworzonych jednostek
titleSuffix: Azure
description: Ten artykuł zawiera adres url informacji o wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: e01bcd022b5688b6cf00f63fcf4a0f06a477ebed
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093628"
---
# <a name="url-entity"></a>URL, jednostka
Adres URL jednostki wyodrębnia adresy URL przy użyciu nazwy domen i adresów IP. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierające adresy URL do aplikacji. Jednostki adres URL jest obsługiwana w `en-us` tylko kultury. 

## <a name="types-of-urls"></a>Typy adresów URL
Adres URL jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) repozytorium Github

## <a name="resolution-for-prebuilt-url-entity"></a>Rozwiązania dla wstępnie utworzone jednostki adresu URL
W poniższym przykładzie pokazano rozdzielczość **builtin.url** jednostki.

```json
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

Dowiedz się więcej o [porządkowe](luis-reference-prebuilt-ordinal.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek.