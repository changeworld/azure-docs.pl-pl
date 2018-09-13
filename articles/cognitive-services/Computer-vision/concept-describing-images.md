---
title: Opisujące obrazów
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Pojęcia związane z opisem obrazów przy użyciu przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: be59055a2c6cd1366c8c52370fa97158ab8d6c88
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725396"
---
# <a name="describing-images"></a>Opisujące obrazów

Algorytmy przetwarzania obrazów analizowanie zawartości obrazu. Ta analiza stanowi podstawę dla "opis" wyświetlana jako język zrozumiałą dla użytkownika w postaci kompletnych zdań. Opis podsumowuje, co znajduje się na obrazie. Algorytmy przetwarzania obrazów Generowanie opisy różnych oparte na funkcji visual zidentyfikowane na obrazie. Poszczególne opisy są oceniane, po czym generowany jest współczynnik ufności. Następnie zwracana jest lista uporządkowana od najwyższego do najniższego współczynnika ufności.

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

Przykład bot, korzystającą z tej technologii w celu generowania podpisów obrazów można znaleźć [tutaj](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [kategoryzowanie obrazów](concept-categorizing-images.md).