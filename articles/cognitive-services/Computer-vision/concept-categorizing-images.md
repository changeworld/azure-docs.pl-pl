---
title: Kategoryzowanie obrazów
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Pojęcia związane z kategoryzowanie obrazów przy użyciu przetwarzania obrazów w usługach Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 9c15392e7f7df346553f925c5bf255ee900d25fe
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725395"
---
# <a name="categorizing-images"></a>Kategoryzowanie obrazów

Oprócz opisy i tagowania przetwarzania obrazów zwraca kategorie na podstawie taksonomii zdefiniowane w poprzednich wersjach. Te kategorie są uporządkowane jako taksonomii z dziedzicznym hierarchie nadrzędny/podrzędny. Wszystkie kategorie są w języku angielskim. Mogą one używane autonomicznie lub dzięki naszej nowej znakowanie modeli.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Na podstawie listy koncepcji 86 pokazano na poniższym diagramie, obrazu można sklasyfikować od szerokiej określone. Dla pełnej taksonomię w formacie tekstowym, zobacz [Taksonomia kategorii](category-taxonomy.md).

![Analizowanie kategorii](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>Przykłady kategoryzacji obrazu

Następującą odpowiedź JSON przedstawiono co przetwarzania obrazów zwraca, gdy kategoryzowanie przykładowy obraz na podstawie jego visual funkcji.

![Kobieta dachu](./Images/woman_roof.png)

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
| ![Rodzina zdjęć](./Images/family_photo.png) | people_group |
| ![Urocze pies](./Images/cute_dog.png) | animal_dog |
| ![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png) | outdoor_mountain |
| ![Wizja analizowanie chleb żywności](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [tagowanie obrazów](concept-tagging-images.md) i [opisujące obrazów](concept-describing-images.md).