---
title: Opisujące obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące funkcji opis obrazu z interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5b920e4ce8df131b81a9ef6ce2d66c7082d8f5e4
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583412"
---
# <a name="describe-images-with-human-readable-language"></a>Opisz obrazów za pomocą języka czytelny dla człowieka

Algorytmy przetwarzania obrazów analizowanie zawartości obrazu. Ta analiza stanowi podstawę „opisu” wyświetlanego jako język zrozumiały dla użytkownika w formie pełnych zdań. Opis zawiera podsumowanie zawartości obrazu. Algorytmy przetwarzania obrazów Generowanie opisy różnych oparte na funkcji visual zidentyfikowane na obrazie. Każdy opis jest szacowana, a współczynnik ufności wygenerowany. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.

## <a name="image-description-example"></a>Przykład opis obrazu

Następującą odpowiedź JSON przedstawiono, jakie przetwarzania obrazów zwraca podczas opisywania przykładowy obraz na podstawie jego funkcji visual.

![Czarno-biały obraz budynki w Manhattan](./Images/bw_buildings.png)

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

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [kategoryzowanie obrazów](concept-categorizing-images.md).