---
title: Wykrywanie schematy kolorów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania schemat kolorów na obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 90eacdb120603fdb68c0f6c3de3457964ec5fd75
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579366"
---
# <a name="detect-color-schemes-in-images"></a>Wykrywanie schematy kolorów na obrazach

Przetwarzania obrazów wyodrębnia kolory z obrazu. Kolory są następnie analizowane w trzech różnych kontekstach: dominujący kolor, kolor tła dominujący i kolory dominujące obrazu jako całości. Są one podzielone na 12 dominujących kolorów akcentu. Kolory wiodące to: czarny, niebieski, brązowy, szary, zielony, pomarańczowy, różowy, fioletowy, czerwony, zielonomodry, biały i żółty. Przetwarzania obrazów analizuje kolory wyodrębnione z obrazu do zwrócenia reprezentujące najbardziej aktywnej kolorów dla obrazu do przeglądarki przy użyciu kombinacji kolory dominujące i nasycenie kolorem akcentu. W zależności od kolorów na obrazie zwykły czarny lub biały bądź kolory wiodące mogą być zwracane w szesnastkowych kodach kolorów. Przetwarzania obrazów zwraca również wartość logiczną, wskazującą, czy obraz jest czarno- & białe.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schemat kolorów

Poniższy przykład ilustruje odpowiedź JSON zwróciło przetwarzania obrazów podczas wykrywania schemat kolorów na przykładowym obrazie. W takim przypadku przykładowy obraz jest czarno-biały obrazu jednak dominujący kolory pierwszego planu i tła są czarne oraz kolory dominujące obrazu jako całość czarno-biały.

![Na zewnątrz w górach](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Dominujący kolor przykłady

W poniższej tabeli opisano dominujący pierwszego planu, tła i kolory z obrazu dla każdego obrazu przykład zwrócone przez przetwarzania obrazów.

| Image (Obraz) | Kolory dominujące |
|-------|-----------------|
|![Białe Kwiatek zielonym tłem](./Images/flower.png)| Na pierwszy plan: Czarny<br/>Tło: Biały<br/>Kolory: Czarny, biały i zielony|
![Szkolenie z za pośrednictwem stacji](./Images/train_station.png) | Na pierwszy plan: Czarny<br/>Tło: Czarny<br/>Kolory: Czarny |

### <a name="accent-color-examples"></a>Przykłady kolor akcentu

 W poniższej tabeli opisano kolor akcentu jako wartość szesnastkową kolor HTML, dla każdego obrazu przykład zwrócone przez przetwarzania obrazów.

| Image (Obraz) | Kolor wiodący |
|-------|--------------|
|![Osoba stojących na rock górski zachodzie słońca](./Images/mountain_vista.png) | #BB6D10 |
|![Białe Kwiatek zielonym tłem](./Images/flower.png) | #C6A205 |
|![Szkolenie z za pośrednictwem stacji](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady wykrywania czarno-biały

Poniższa tabela wskazuje, czy każdy przykładowy obraz jest czarny & biały, zwrócone przez przetwarzania obrazów.

| Image (Obraz) | Czarno -biały? |
|-------|----------------|
|![Czarno-biały obraz budynki w Manhattan](./Images/bw_buildings.png) | true |
|![Niebieski dom i yard frontonu](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie typy obrazów](concept-detecting-image-types.md).