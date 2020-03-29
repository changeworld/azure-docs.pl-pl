---
title: Opisy obrazów - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją opisu obrazu interfejsu API przetwarzania obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244719"
---
# <a name="describe-images-with-human-readable-language"></a>Opis obrazów w języku czytelnym dla człowieka

Wizja komputerowa może analizować obraz i generować czytelne dla człowieka zdanie opisujące jego zawartość. Algorytm faktycznie zwraca kilka opisów na podstawie różnych funkcji wizualnych, a każdy opis otrzymuje wynik zaufania. Ostateczne dane wyjściowe to lista opisów uporządkowanych od najwyższego do najniższego zaufania.

## <a name="image-description-example"></a>Przykład opisu obrazu

Poniższa odpowiedź JSON ilustruje, co funkcja Przetwarzania Obrazów zwraca podczas opisywania przykładowego obrazu na podstawie jego funkcji wizualnych.

![Czarno-biały obraz budynków na Manhattanie](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja opisu obrazu jest częścią interfejsu API [analizy obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Description` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"description"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane pojęcia [dotyczące oznaczania obrazów](concept-tagging-images.md) i [kategoryzowania obrazów.](concept-categorizing-images.md)
