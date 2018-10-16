---
title: Opisujące obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z opisem obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 423d1be57bc800108a08a81b72587ca2711bbc3d
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342419"
---
# <a name="describing-images"></a>Opisywanie obrazów

Algorytmy przetwarzania obrazów analizowanie zawartości obrazu. Ta analiza stanowi podstawę dla "opis" wyświetlana jako język zrozumiałą dla użytkownika w postaci kompletnych zdań. Opis podsumowuje, co znajduje się na obrazie. Algorytmy przetwarzania obrazów Generowanie opisy różnych oparte na funkcji visual zidentyfikowane na obrazie. Każdy opis jest szacowana, a współczynnik ufności wygenerowany. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.

## <a name="image-description-example"></a>Przykład opis obrazu

Następującą odpowiedź JSON przedstawiono, jakie przetwarzania obrazów zwraca podczas opisywania przykładowy obraz na podstawie jego funkcji visual.

![Kompil & budynki W](./Images/bw_buildings.png)

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