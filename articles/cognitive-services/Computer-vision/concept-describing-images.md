---
title: Opisujące obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące funkcji opis obrazu z interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368399"
---
# <a name="describe-images-with-human-readable-language"></a>Opisz obrazów za pomocą języka czytelny dla człowieka

Przetwarzanie obrazów można analizować obrazu i wygenerować zdania zrozumiałą dla użytkownika, który opisuje jego zawartość. Algorytm faktycznie retruns kilka opisy na podstawie różnych funkcji visual i każdy opis otrzymuje współczynnik ufności. Końcowych danych wyjściowych znajduje się lista Opisy uporządkowane od najwyższego do najniższego zaufania.

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
