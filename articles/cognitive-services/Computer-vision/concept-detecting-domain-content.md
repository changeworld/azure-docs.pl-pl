---
title: Zawartość specyficzna dla domeny — Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić domenę kategoryzacji obrazów, aby zwrócić bardziej szczegółowe informacje o obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946336"
---
# <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznej dla domeny

Oprócz tagowania i kategoryzacji wysokiego poziomu, usługa Computer Vision obsługuje również dalsze analizy specyficzne dla domeny przy użyciu modeli, które zostały przeszkolone w zakresie wyspecjalizowanych danych.

Istnieją dwa sposoby używania modeli specyficznych dla domeny: same (analiza o zakresie) lub jako ulepszenie funkcji kategoryzacji.

### <a name="scoped-analysis"></a>Analiza o określonym zakresie

Obraz można analizować przy użyciu tylko wybranego modelu specyficznego dla domeny, wywołując [model/model\<\>/analizowanie](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) interfejsu API.

Poniżej przedstawiono przykładową odpowiedź JSON zwróconą przez **modelki/gwiazdy/analizowanie** interfejsu API dla danego obrazu:

![Satya Nadella stojąca, uśmiechnięta](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Ulepszona analiza kategoryzacji

Można również użyć modeli specyficznych dla domeny, aby uzupełnić ogólną analizę obrazu. Można to zrobić w ramach [kategoryzacji wysokiego poziomu,](concept-categorizing-images.md) określając modele specyficzne dla domeny w parametrze *szczegółów* [wywołania interfejsu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API analizy.

W takim przypadku klasyfikator taksonomii 86 kategorii jest nazywany jako pierwszy. Jeśli którakolwiek z wykrytych kategorii ma pasujący model specyficzny dla domeny, obraz jest również przekazywany przez ten model i wyniki są dodawane.

Poniższa odpowiedź JSON pokazuje, jak analiza specyficzne `detail` dla domeny mogą być uwzględnione jako węzeł w szerszej analizy kategoryzacji.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Wyświetlanie listy modeli specyficznych dla domeny

Obecnie usługa Computer Vision obsługuje następujące modele specyficzne dla domeny:

| Nazwa | Opis |
|------|-------------|
| Gwiazdy | Rozpoznawanie gwiazd, obsługiwane dla `people_` obrazów sklasyfikowanych w kategorii |
| Zabytki | Rozpoznawanie punktów orientacyjnych, obsługiwane `outdoor_` `building_` dla obrazów sklasyfikowanych w kategoriach lub |

Wywołanie interfejsu API [modeli](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) zwróci te informacje wraz z kategoriami, do których każdy model może zastosować:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Poznaj pojęcia dotyczące [kategoryzowania obrazów](concept-categorizing-images.md).
