---
title: Kategoryzacja obrazu - Wizja komputerowa
titleSuffix: Azure Cognitive Services
description: Poznaj pojęcia związane z funkcją kategoryzacji obrazów interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244754"
---
# <a name="categorize-images-by-subject-matter"></a>Kategoryzowanie obrazów według tematu

Oprócz tagów i opisu funkcja Wizja komputerowa zwraca kategorie oparte na taksonomii wykryte na obrazie. W przeciwieństwie do tagów kategorie są zorganizowane w hierarchii dziedzicznej rodzica/dziecka, a jest ich mniej (86, w przeciwieństwie do tysięcy tagów). Wszystkie nazwy kategorii są w języku angielskim. Kategoryzacja może być wykonywana samodzielnie lub obok nowszego modelu znaczników.

## <a name="the-86-category-concept"></a>Koncepcja 86 kategorii

Wizja komputerowa może kategoryzować obraz w szerokim lub specjalnie, korzystając z listy 86 kategorii na poniższym diagramie. Aby uzyskać informacje dotyczące pełnej taksonomii w formacie tekstowym, zobacz temat [Taksonomia kategorii](category-taxonomy.md).

![Zgrupowane listy wszystkich kategorii w kategorii taksonomii](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Przykłady kategoryzacji obrazów

Poniższa odpowiedź JSON ilustruje, co funkcja Przetwarzania Obrazów zwraca podczas kategoryzowania przykładowego obrazu na podstawie jego funkcji wizualnych.

![Kobieta na dachu budynku mieszkalnego](./Images/woman_roof.png)

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

W poniższej tabeli przedstawiono typowy zestaw obrazów i kategorię zwróconą przez wizję komputerową dla każdego obrazu.

| Image (Obraz) | Kategoria |
|-------|----------|
| ![Cztery osoby pozowały razem jako rodzina](./Images/family_photo.png) | people_group |
| ![Szczeniak siedzący na trawiastym polu](./Images/cute_dog.png) | animal_dog |
| ![Osoba stojąca na górskiej skale o zachodzie słońca](./Images/mountain_vista.png) | outdoor_mountain |
| ![Stos ról chleba na stole](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja kategoryzacji jest częścią interfejsu API [analizowania obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Categories` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"categories"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Następne kroki

Poznaj powiązane pojęcia [dotyczące oznaczania obrazów](concept-tagging-images.md) i [opisywania obrazów.](concept-describing-images.md)
