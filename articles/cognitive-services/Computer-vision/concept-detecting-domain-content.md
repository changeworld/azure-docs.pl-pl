---
title: Wykrywanie specyficznego dla domeny zawartości — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak określić domenę kategoryzacji obrazu do zwrócenia bardziej szczegółowe informacje o obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e4b64e00f71768a8821c83a73b019f77089e1b3a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994490"
---
# <a name="detect-domain-specific-content"></a>Wykrywanie zawartości specyficznego dla domeny

Ponadto do znakowania i wysokiego poziomu kategoryzacji, przetwarzanie obrazów obsługuje również dalszej analizy specyficznego dla domeny przy użyciu modeli, które są przeszkoleni wyspecjalizowane danych.

Istnieją dwa sposoby użycia modeli specyficznych dla domeny: samodzielnie (analiza o określonym zakresie) lub jako rozszerzenie funkcji kategoryzacji.

### <a name="scoped-analysis"></a>Analizy o określonym zakresie

Możesz analizować obrazu przy użyciu tylko wybranym modelu specyficznego dla domeny, wywołując [modeli /\<modelu\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) interfejsu API.

Poniżej przedstawiono przykładowe JSON odpowiedź zwrócona przez **modeli/osobistości/analizowanie** interfejsu API dla danego obrazu:

![Satya Nadella stałego, uśmiech](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Rozszerzone kategoryzacji analizy

Aby uzupełnić analizy obrazów Ogólne umożliwia także modeli specyficznych dla domeny. Można to zrobić w ramach [wysokiego poziomu kategoryzacji](concept-categorizing-images.md) , określając modeli specyficznych dla domeny w *szczegóły* parametru [analizy](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) wywołania interfejsu API.

W tym przypadku klasyfikatora Taksonomia kategorii 86 nazywa się najpierw. Jeśli którykolwiek z kategorii wykryte dopasowania modelu specyficznego dla domeny, obraz, który jest przekazywane również model i wyniki są dodawane.

Następującą odpowiedź JSON pokazuje, jak specyficznego dla domeny analiza może być uwzględniany jako `detail` węzła w szerszym analizy kategoryzacji.

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

## <a name="list-the-domain-specific-models"></a>Lista modeli specyficznych dla domeny

Obecnie Usługa przetwarzania obrazów obsługuje następujące modele specyficzne dla domeny:

| Name (Nazwa) | Opis |
|------|-------------|
| osobistości | Rozpoznawanie osobistości, obsługiwane w przypadku obrazów sklasyfikować w obszarze `people_` kategorii |
| Charakterystycznych elementów krajobrazu | Rozpoznawania charakterystycznych elementów krajobrazu, obsługiwane w przypadku obrazów sklasyfikować w obszarze `outdoor_` lub `building_` kategorii |

Wywoływanie [modeli](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) interfejs API zwróci te informacje wraz z kategorii, do których każdy model można zastosować:

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

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [kategoryzowanie obrazów](concept-categorizing-images.md).
