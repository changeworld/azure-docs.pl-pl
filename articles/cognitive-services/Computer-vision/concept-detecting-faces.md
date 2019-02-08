---
title: Wykrywanie twarzy — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcji wykrywania twarzy interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 81e846c6ffc0361b7154c962dbc047b9e5ae55e7
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872989"
---
# <a name="face-detection-with-computer-vision"></a>Wykrywanie twarzy przy użyciu przetwarzania obrazów

Przetwarzanie obrazów wykrywa twarze osób na zdjęciu i generuje wiek, płeć i prostokąt dla każdej twarzy wykryte. Przetwarzanie obrazów zawiera podzestaw funkcji, które można znaleźć w usłudze [rozpoznawania twarzy](/azure/cognitive-services/face/), a usługa rozpoznawania twarzy umożliwia bardziej szczegółową analizę, np. identyfikację twarzy i wykrywanie póz.  

## <a name="face-detection-examples"></a>Przykłady wykrywania twarzy

Pierwszy przykład przedstawia odpowiedź JSON zwrócony przez wizualizacji komputerowej dla obrazu zawierającego pojedynczego ludzkich twarzy.

![Analiza obrazu — kobieta, dach, twarz](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Drugi przykład przedstawia odpowiedź JSON zwracany w przypadku obrazu zawierającego wiele twarze.

![Wizja analizowanie rodziny fotografii twarzy](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie specyficznego dla domeny zawartości](concept-detecting-domain-content.md).
