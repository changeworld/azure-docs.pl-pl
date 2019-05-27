---
title: Usługa LUIS ze wstępnie utworzonych jednostek e-mail odwołanie — Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera adres e-mail informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2b11446c84ede0e8ecfce23eda1026919777fc66
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072075"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wyślij wiadomość e-mail ze wstępnie utworzonych jednostek dla aplikacji usługi LUIS
Wyodrębniania wiadomości e-mail zawiera adres e-mail całego z wypowiedź. Ponieważ przeprowadzono już uczenie tej jednostki, nie musisz Dodawanie wypowiedzi przykład zawierający do intencji aplikacji poczty e-mail. Adres e-mail jednostki jest obsługiwana w `en-us` tylko kultury. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązania do obsługi poczty e-mail wstępnie

### <a name="api-version-2x"></a>Wersja interfejsu API 2.x

W poniższym przykładzie pokazano rozdzielczość **builtin.email** jednostki.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Wersja zapoznawcza interfejsu API 3.x

Następujący kod JSON jest `verbose` parametr `false`:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ]
        }
    }
}
```


Następujący kod JSON jest `verbose` parametr `true`:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti.owens@microsoft.com",
                        "startIndex": 31,
                        "length": 25,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [porządkowe](luis-reference-prebuilt-ordinal.md), i [procent](luis-reference-prebuilt-percentage.md). 
