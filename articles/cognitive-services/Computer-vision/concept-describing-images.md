---
title: Opisy obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją opisu obrazu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945263"
---
# <a name="describe-images-with-human-readable-language"></a>Opisywanie obrazów w języku czytelnym dla ludzi

Przetwarzanie obrazów może analizować obraz i generować zdanie odczytane przez człowieka, które opisuje jego zawartość. Algorytm faktycznie zwraca kilka opisów opartych na różnych funkcjach wizualnych, a każdy opis otrzymuje wynik zaufania. Ostateczne dane wyjściowe to lista opisów uporządkowanych od najwyższego do najniższego zaufania.

## <a name="image-description-example"></a>Przykład opisu obrazu

Poniższa odpowiedź JSON ilustruje, co przetwarzanie obrazów zwraca podczas opisywania przykładowego obrazu na podstawie jego funkcji wizualizacji.

![Czarno-białe zdjęcie budynków w Manhattan](./Images/bw_buildings.png)

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

## <a name="next-steps"></a>Następne kroki

Poznaj koncepcje dotyczące [tagowania obrazów](concept-tagging-images.md) i [kategoryzacji obrazów](concept-categorizing-images.md).
