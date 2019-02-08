---
title: Wykrywanie typy obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcji wykrywania typu obrazu, interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cd7b2a8a70a315b05c0824a863803bbc6ffabb2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872139"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typy obrazów za pomocą wizualizacji komputerowej

Przetwarzanie obrazów można analizować typu zawartości obrazów, wskazując, czy obraz jest obrazem clipart, ocena prawdopodobieństwo skalowania lub rysowanie linii.

## <a name="detecting-clip-art"></a>Wykrywanie clipart

Przetwarzania obrazów analizuje obrazu i ocenia prawdopodobieństwo obrazu clipart w skali od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Wartość | Znaczenie |
|-------|---------|
| 0 | Obiekt inny niż clipart |
| 1 | Ambiguous |
| 2 | Normal-clip-art |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Clip art wykrywanie przykładów

Następujące odpowiedzi JSON przedstawiono co przetwarzania obrazów zwraca, gdy klasyfikacja prawdopodobieństwo obrazów przykład trwa clipart.

![Obraz wycinka ser](./Images/cheese_clipart.png)

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

![Niebieski dom i yard frontonu](./Images/house_yard.png)

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

![Rysowanie linii obraz lion](./Images/lion_drawing.png)

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

![Białe Kwiatek zielonym tłem](./Images/flower.png)

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
