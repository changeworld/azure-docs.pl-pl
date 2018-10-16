---
title: Wykrywanie typy obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania typy obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: ecdbdd60af41ee14070f2f45dfe50875e38ac3b3
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342011"
---
# <a name="detecting-image-types"></a>Wykrywanie typów obrazów

Przetwarzanie obrazów można analizować typu zawartości obrazów, wskazując, czy obraz jest obrazem clipart, ocena prawdopodobieństwo skalowania lub rysowanie linii.

## <a name="detecting-clip-art"></a>Wykrywanie clipart

Przetwarzania obrazów analizuje obrazu i ocenia prawdopodobieństwo obrazu clipart w skali od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Wartość | Znaczenie |
|-------|---------|
| 0 | Inne niż clipart |
| 1 | niejednoznaczne |
| 2 | Normalny clipart |
| 3 | dobre clipart |

### <a name="clip-art-detection-examples"></a>Clip art wykrywanie przykładów

Następujące odpowiedzi JSON przedstawiono co przetwarzania obrazów zwraca, gdy klasyfikacja prawdopodobieństwo obrazów przykład trwa clipart.

![Wizja analizowanie ser clipart](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Wizja analizowanie Yard DOM](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Wykrywanie rysunkach

Przetwarzania obrazów analizuje obrazu i zwraca wartość logiczną wskazującą, czy obraz jest obiektem.

### <a name="line-drawing-detection-examples"></a>Rysowanie linii wykrywanie przykładów

Następujące odpowiedzi JSON przedstawiono co przetwarzania obrazów zwraca, gdy wskazujące, czy obrazy przykład rysunki.

![Wizja analizowanie Lion rysowania](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Wizja analizowanie Kwiatek](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [kategoryzowanie obrazów](concept-categorizing-images.md).