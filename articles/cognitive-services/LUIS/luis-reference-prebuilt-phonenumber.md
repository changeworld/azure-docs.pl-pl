---
title: Wbudowane LUIS jednostek phone numer odwołania - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera wbudowane jednostek. informacja o numerze telefonu opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 0f72b807b9b0ec110a80d67babb1c45902b8c810
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321883"
---
# <a name="phonenumber-entity"></a>Numer telefonu jednostki
`phonenumber` Jednostki wyodrębnia różne numery telefonów, w tym numer kierunkowy kraju. Ponieważ była już uczona tej jednostki, nie trzeba dodać zniesławiających przykładzie do aplikacji. `phonenumber` Jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="types-of-phonenumber"></a>Typy numer telefonu
Numer telefonu jest zarządzany z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repozytorium Github

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Czas rozpoznawania nazw dla jednostki phonenumber wbudowane
W poniższym przykładzie przedstawiono rozpoznanie **builtin.phonenumber** jednostki.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [procent](luis-reference-prebuilt-percentage.md), [numer](luis-reference-prebuilt-number.md), i [temperatury](luis-reference-prebuilt-temperature.md) jednostek. 