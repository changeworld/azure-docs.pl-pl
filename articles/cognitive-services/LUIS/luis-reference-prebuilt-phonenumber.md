---
title: Wstępnie utworzonych jednostek numeru telefonu
titleSuffix: Azure
description: Ten artykuł zawiera wstępnie utworzone jednostki informacja o numerze telefonu w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 2a3e84de6d956e4b45945c77c11f789d4455bfe2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473199"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Numer telefonu wstępnie utworzone jednostki dla aplikacji usługi LUIS
`phonenumber` Jednostki wyodrębnia różne numery telefonów, łącznie z kodem kraju. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład do aplikacji. `phonenumber` Jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="types-of-phonenumber"></a>Typy numer telefonu
Numer telefonu jest zarządzana z [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) repozytorium GitHub

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Rozpoznawanie phonenumber wstępnie utworzone jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.phonenumber** jednostki.

```json
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
