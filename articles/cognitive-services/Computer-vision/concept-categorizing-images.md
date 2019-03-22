---
title: Kategoryzowanie obrazów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z funkcji kategoryzacji obrazu z interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07fdaa22532f48cc39b6c524d85fdfe625f8b80c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337143"
---
# <a name="categorize-images"></a>Kategoryzowanie obrazów

Oprócz tagów i opis przetwarzania obrazów zwraca kategorie na podstawie taksonomii wykryte na obrazie. W przeciwieństwie do tagów kategorie są zorganizowane w hierarchii dziedzicznym nadrzędne/podrzędne, wiąże się z ich (w przeciwieństwie do tysięcy tagi 86). Wszystkie nazwy kategorii są w języku angielskim. Kategoryzacja może odbywać się oddzielnie lub razem nowy model tagów.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Przetwarzanie obrazów umożliwia klasyfikowanie obrazu szeroko lub ściślej mówiąc, przy użyciu listy kategorii 86 na poniższym diagramie. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](category-taxonomy.md).

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
