---
title: Wykrywanie typu obrazu - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcją wykrywania typu obrazu interfejsu API przetwarzania obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244566"
---
# <a name="detecting-image-types-with-computer-vision"></a>Wykrywanie typów obrazów za pomocą wizji komputerowej

Za pomocą interfejsu API [analizowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) usługa Computer Vision może analizować typ zawartości obrazów, wskazując, czy obraz jest obiektem clipart, czy rysunkiem liniowym.

## <a name="detecting-clip-art"></a>Wykrywanie obiektu clipart

Usługa Computer Vision analizuje obraz i ocenia prawdopodobieństwo, że obraz zostanie obiektem clipart w skali od 0 do 3, zgodnie z opisem w poniższej tabeli.

| Wartość | Znaczenie |
|-------|---------|
| 0 | Obiekt inny niż clipart |
| 1 | Niejednoznaczne |
| 2 | Normalny obiekt clip-art |
| 3 | Dobry obiekt clip-art |

### <a name="clip-art-detection-examples"></a>Przykłady wykrywania obiektów clipart

Poniższe odpowiedzi JSON ilustruje, co funkcja Przetwarzania Obrazów zwraca podczas oceniania prawdopodobieństwa, że przykładowe obrazy są obiektami clipart.

![Obraz obiektu clipart plasterka kawałka sera](./Images/cheese_clipart.png)

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

![Niebieski dom i podwórko](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Wykrywanie rysunków linii

Funkcja Wizja komputerowa analizuje obraz i zwraca wartość logiczną wskazującą, czy obraz jest rysunkiem liniowym.

### <a name="line-drawing-detection-examples"></a>Przykłady wykrywania rysunku liniowego

Poniższe odpowiedzi JSON ilustrują, co funkcja Wizja komputerowa zwraca, wskazując, czy przykładowe obrazy są rysunkami liniowymi.

![Obraz przedstawiający linię lwa](./Images/lion_drawing.png)

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

![Biały kwiat z zielonym tłem](./Images/flower.png)

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

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania typu obrazu jest częścią interfejsu API [analizy obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `ImageType` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"imageType"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)
