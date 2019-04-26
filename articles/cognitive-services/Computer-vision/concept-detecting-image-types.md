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
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 37cdac16a51a30bdaf1ba0266bab7fdd1f2990f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368327"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typy obrazów za pomocą wizualizacji komputerowej

Za pomocą [analizowanie obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) interfejsu API, przetwarzania obrazów może analizować typu zawartości obrazów, wskazującą, czy obraz jest clipart lub rysowanie linii.

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

Zobacz [analizowanie obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) dokumentacji, aby dowiedzieć się, jak wykryć typy obrazów.
