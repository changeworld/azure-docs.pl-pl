---
title: Usługa LUIS ze wstępnie utworzonych jednostek phone odwołania do numerów — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera wstępnie utworzone jednostki informacja o numerze telefonu w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1ae14f72f0dc610b9399e675f49ef5fff51a6965
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238995"
---
# <a name="phonenumber-entity"></a>Phonenumber, jednostka
`phonenumber` Jednostki wyodrębnia różne numery telefonów, łącznie z kodem kraju. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład do aplikacji. `phonenumber` Jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="types-of-phonenumber"></a>Typy numer telefonu
Numer telefonu jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repozytorium Github

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Rozpoznawanie phonenumber wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.phonenumber** jednostki.

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