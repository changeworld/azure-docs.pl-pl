---
title: Keyphrase prekompilowana jednostka — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera wstępnie skompilowane informacje o jednostkach keyphrase w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: e55c0453c117c51e5a8e4986631516d3e61ed10b
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677591"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase prekompilowaną jednostkę dla aplikacji LUIS
Jednostka keyPhrase wyodrębnia różne kluczowe frazy z wypowiedź. Nie musisz dodawać przykładowej wyrażenia długości zawierającej keyPhrase do aplikacji. Jednostka keyPhrase jest obsługiwana w [wielu kulturach](luis-language-support.md#languages-supported) w ramach funkcji [analizy tekstu](../text-analytics/overview.md) . 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Rozwiązanie dla wstępnie skompilowanej jednostki keyPhrase

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Odpowiedź punktu końcowego przewidywania wersji 2](#tab/V2)

Poniższy przykład pokazuje rozdzielczość jednostki **wbudowanej. keyPhrase** .

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```
#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Odpowiedź punktu końcowego przewidywania v3](#tab/V3)

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `false`:

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ]
        }
    }
}
```

Poniższy kod JSON jest z parametrem `verbose` ustawionym na `true`:

```json
{
    "query": "where is the educational requirements form for the development and engineering group",
    "prediction": {
        "normalizedQuery": "where is the educational requirements form for the development and engineering group",
        "topIntent": "GetJobInformation",
        "intents": {
            "GetJobInformation": {
                "score": 0.157861546
            }
        },
        "entities": {
            "keyPhrase": [
                "educational requirements",
                "development"
            ],
            "$instance": {
                "keyPhrase": [
                    {
                        "type": "builtin.keyPhrase",
                        "text": "educational requirements",
                        "startIndex": 13,
                        "length": 24,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    },
                    {
                        "type": "builtin.keyPhrase",
                        "text": "development",
                        "startIndex": 51,
                        "length": 11,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
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

Dowiedz się więcej o jednostkach [wartości procentowych](luis-reference-prebuilt-percentage.md), [liczbowych](luis-reference-prebuilt-number.md)i [wieku](luis-reference-prebuilt-age.md) .
