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
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 699192aba87bb009d7dbddddcc9579883bb71db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368139"
---
# <a name="face-detection-with-computer-vision"></a>Wykrywanie twarzy przy użyciu przetwarzania obrazów

Przetwarzanie obrazów można wykrywanie ludzkich twarzy na obrazie i wygenerować wiek, płeć i prostokąt dla każdej twarzy wykryte. 

> [!NOTE]
> Ta funkcja jest również oferowana przez Azure [twarzy](/azure/cognitive-services/face/) usługi. Zobacz tej alternatywy dla bardziej szczegółowe analizy, włącznie z rozpoznawania twarzy twarzy i stanowić wykrywania. 

## <a name="face-detection-examples"></a>Przykłady wykrywania twarzy

W poniższym przykładzie pokazano odpowiedź JSON zwrócony przez wizualizacji komputerowej dla obrazu zawierającego pojedynczego ludzkich twarzy.

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

Następny przykład pokazuje odpowiedź JSON zwracany w przypadku obrazu zawierającego wiele twarze.

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

Zobacz [analizowanie obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) dokumentacji, aby dowiedzieć się więcej o tym, jak używać funkcji wykrywania twarzy.
