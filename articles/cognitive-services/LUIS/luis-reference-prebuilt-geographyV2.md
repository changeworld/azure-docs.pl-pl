---
title: Lokalizacja geograficzna V2 wstępnie utworzone jednostki — dokumentacja usługi LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera geographyV2 informacji wstępnie utworzone jednostki w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 3559bc02944f88f486104d4d9553f0c45a1f1754
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983416"
---
# <a name="geographyv2-entity"></a>GeographyV2 jednostki
Jednostki wstępnie geographyV2 wykrywa miejsc. Ponieważ przeprowadzono już uczenie tę jednostkę, dodawanie wypowiedzi przykład zawierający GeographyV2 do intencji aplikacji nie jest konieczne. Jednostka GeographyV2 jest obsługiwane w języku angielskim [kultury](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Podtypy
Lokalizacje geograficzne istnieją podtypy:

|Podtyp|Przeznaczenie|
|--|--|
|`poi`|punktu orientacyjnego|
|`city`|Nazwa miasta|
|`countryRegion`|Nazwa kraju lub regionu|
|`continent`|Nazwa kontynent|
|`state`|Nazwa województwa|


## <a name="resolution-for-geographyv2-entity"></a>Rozpoznawanie GeographyV2 jednostki
W poniższym przykładzie pokazano rozdzielczość **builtin.geographyV2** jednostki.

```JSON
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [e-mail](luis-reference-prebuilt-email.md), [numer](luis-reference-prebuilt-number.md), i [porządkowe](luis-reference-prebuilt-ordinal.md) jednostek. 