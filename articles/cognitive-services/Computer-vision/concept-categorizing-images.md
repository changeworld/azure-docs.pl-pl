---
title: Kategoryzowanie obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z kategoryzowanie obrazów przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333483"
---
# <a name="categorizing-images"></a>Kategoryzowanie obrazów

Oprócz opisy i tagowania przetwarzania obrazów zwraca kategorie na podstawie taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są uporządkowane w formie taksonomii z użyciem dziedziczonych hierarchii obiektów nadrzędnych i podrzędnych. Wszystkie kategorie są w języku angielskim. Mogą one używane autonomicznie lub dzięki naszej nowej znakowanie modeli.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Na podstawie listy koncepcji 86 pokazano na poniższym diagramie, obrazu można sklasyfikować od szerokiej określone. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](category-taxonomy.md).

![zgrupowane listy wszystkie kategorie Taksonomia kategorii](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Przykłady kategoryzacji obrazu

Następującą odpowiedź JSON przedstawiono co przetwarzania obrazów zwraca, gdy kategoryzowanie przykładowy obraz na podstawie jego visual funkcji.

![Kobieta na dachu](./Images/woman_roof.png)

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
| ![Zdjęcie rodzinne](./Images/family_photo.png) | people_group |
| ![Słodki piesek](./Images/cute_dog.png) | animal_dog |
| ![Na zewnątrz w górach](./Images/mountain_vista.png) | outdoor_mountain |
| ![Analiza obrazu — żywność, chleb](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [opisujące obrazów](concept-describing-images.md).