---
title: LUIS wstępnie utworzonych jednostek — dokumentacja poczty e-mail
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostce poczty e-mail w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4a1bc9ae7ccf48b9dc8b47b57ea43b9259786d01
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677686"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Wstępnie utworzona jednostka poczty e-mail dla aplikacji LUIS
Wyodrębnianie wiadomości e-mail obejmuje cały adres e-mail z wypowiedź. Ponieważ ta jednostka jest już przeszkolone, nie trzeba dodawać przykładowej wyrażenia długości zawierającej wiadomości e-mail do intencji aplikacji. Jednostka poczty e-mail jest obsługiwana tylko w kulturze `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Rozwiązanie dla wstępnie skompilowanej poczty e-mail

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

W poniższym przykładzie przedstawiono rozdzielczość **wbudowanej jednostki poczty e-mail** .

```json
{
  "query": "please send the information to patti@contoso.com",
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
      "entity": "patti@contoso.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti@contoso.com"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ]
        }
    }
}
```


Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "please send the information to patti@contoso.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti@contoso.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti@contoso.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti@contoso.com",
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

* * * 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [punkcie końcowym przewidywania v3](luis-migration-api-v3.md).

Dowiedz się więcej o [liczbie](luis-reference-prebuilt-number.md), liczbie [porządkowej](luis-reference-prebuilt-ordinal.md)i [wartości procentowej](luis-reference-prebuilt-percentage.md). 
