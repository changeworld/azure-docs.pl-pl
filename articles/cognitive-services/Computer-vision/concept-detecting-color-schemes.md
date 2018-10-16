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
ms.openlocfilehash: 5d0cb6ca751c844846288e8fe26f6ae542e89831
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339496"
---
# <a name="detecting-color-schemes"></a>Wykrywanie schematów kolorów

Przetwarzania obrazów wyodrębnia kolory z obrazu. Kolory są następnie analizowane w trzech różnych kontekstach: dominujący kolor, kolor tła dominujący i kolory dominujące obrazu jako całości. Są one podzielone na 12 dominujących kolorów akcentu. Te kolory akcentu są czarne, niebieski, brown, szary, zielony, orange, różowy, fioletowy, red, zielonomodrym, biały i żółty. Przetwarzania obrazów analizuje kolory wyodrębnione z obrazu do zwrócenia reprezentujące najbardziej aktywnej kolorów dla obrazu do przeglądarki przy użyciu kombinacji kolory dominujące i nasycenie kolorem akcentu. W zależności od kolory na obrazie proste czarno biały lub kolorów akcentu mogą być zwracane w szesnastkowe kody kolorów. Przetwarzania obrazów zwraca również wartość logiczną, wskazującą, czy obraz jest czarno- & białe.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schemat kolorów

Poniższy przykład ilustruje odpowiedź JSON zwróciło przetwarzania obrazów podczas wykrywania schemat kolorów na przykładowym obrazie. W takim przypadku przykładowy obraz jest czarno-biały obrazu jednak dominujący kolory pierwszego planu i tła są czarne oraz kolory dominujące obrazu jako całość czarno-biały.

![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png)

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
|![Wizja analizowanie Kwiatek](./Images/flower.png)| Pierwszy plan: czarny<br/>Tło: biały<br/>Kolory: Czarny, biały, zielony|
![Wizja analizowanie Train stacji](./Images/train_station.png) | Pierwszy plan: czarny<br/>Tło: czarny<br/>Kolory: czarny |

### <a name="accent-color-examples"></a>Przykłady kolor akcentu

 W poniższej tabeli opisano kolor akcentu jako wartość szesnastkową kolor HTML, dla każdego obrazu przykład zwrócone przez przetwarzania obrazów.

| Image (Obraz) | Kolor akcentu |
|-------|--------------|
|![Aktywność na świeżym powietrzu górski](./Images/mountain_vista.png) | #BB6D10 |
|![Wizja analizowanie Kwiatek](./Images/flower.png) | #C6A205 |
|![Wizja analizowanie Train stacji](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady wykrywania czarno-biały

Poniższa tabela wskazuje, czy każdy przykładowy obraz jest czarny & biały, zwrócone przez przetwarzania obrazów.

| Image (Obraz) | Czarno -biały? |
|-------|----------------|
|![Wizja analizowanie kompilowanie](./Images/bw_buildings.png) | true |
|![Wizja analizowanie Yard DOM](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie typy obrazów](concept-detecting-image-types.md).