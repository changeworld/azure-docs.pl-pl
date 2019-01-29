---
title: Kategoryzowanie obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcji kategoryzacji obrazu z interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f182110d150583ee1c241c23a2e1924d9f3e3bd4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158047"
---
# <a name="image-categorization-with-computer-vision"></a>Kategoryzacja obrazu za pomocą wizualizacji komputerowej

Oprócz opisy i tagowania przetwarzania obrazów zwraca kategorie na podstawie taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są uporządkowane w formie taksonomii z użyciem dziedziczonych hierarchii obiektów nadrzędnych i podrzędnych. Wszystkie kategorie są w języku angielskim. Mogą one używane autonomicznie lub dzięki naszej nowej znakowanie modeli.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Na podstawie listy koncepcji 86 pokazano na poniższym diagramie, obrazu można sklasyfikować od szerokiej określone. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](category-taxonomy.md).

![zgrupowane listy wszystkie kategorie Taksonomia kategorii](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Przykłady kategoryzacji obrazu

Następującą odpowiedź JSON przedstawiono co przetwarzania obrazów zwraca, gdy kategoryzowanie przykładowy obraz na podstawie jego visual funkcji.

![Kobieta na dachu budynku apartamentu](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

W poniższej tabeli przedstawiono zestaw typowych obrazu i kategorii zwrócony przez wizualizacji komputerowej dla każdego obrazu.

| Image (Obraz) | Kategoria |
|-------|----------|
| ![Czterech osób związane ze sobą jako rodzina](./Images/family_photo.png) | people_group |
| ![Puppy, znajdują się w polu grassy](./Images/cute_dog.png) | animal_dog |
| ![Osoba stojących na rock górski zachodzie słońca](./Images/mountain_vista.png) | outdoor_mountain |
| ![Wiele ról chleb na tabelę](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [opisujące obrazów](concept-describing-images.md).
