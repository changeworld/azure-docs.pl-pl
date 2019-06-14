---
title: Wykrywanie schematy kolorów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia dotyczące wykrywania schemat kolorów na obrazach za pomocą interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368591"
---
# <a name="detect-color-schemes-in-images"></a>Wykrywanie schematy kolorów na obrazach

Przetwarzania obrazów analizuje kolory na obrazie, aby zapewnić trzech różnych atrybutów: dominujący kolor, kolor tła dominujący i zestaw kolory dominujące obrazu jako całości. Zwrócone, kolory, należy do zestawu: czarny, niebieski, brown, szary, zielony, orange, różowy, fioletowy, czerwony, zielonomodrym, biały i żółty. 

Przetwarzania obrazów wyodrębnia również kolor akcentu, który reprezentuje kolor najbardziej aktywny na obrazie, oparte na kombinacji kolory dominujące Nasycenie. Kolor akcentu jest zwracana jako kod szesnastkowy koloru HTML. 

Przetwarzania obrazów zwraca również wartość logiczną wskazującą, czy obraz jest czarno biały.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schemat kolorów

Poniższy przykład ilustruje odpowiedź JSON zwróciło przetwarzania obrazów podczas wykrywania schemat kolorów na przykładowym obrazie. W takim przypadku przykładowy obraz jest czarno-biały obrazu, jednak dominujący pierwszego planu i tła kolorów są czarne oraz kolory dominujące obrazu jako całość czarno-biały.

![Górski możliwością zachodzie słońca z nakładające osoby](./Images/mountain_vista.png)

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

W poniższej tabeli przedstawiono zwrócone pierwszego planu, tła i kolory z obrazu dla każdego obrazu próbki.

| Image | Kolory dominujące |
|-------|-----------------|
|![Białe Kwiatek zielonym tłem](./Images/flower.png)| Na pierwszy plan: Czarny<br/>Tło: Biały<br/>Kolory: Czarny, biały i zielony|
![Szkolenie z za pośrednictwem stacji](./Images/train_station.png) | Na pierwszy plan: Czarny<br/>Tło: Czarny<br/>Kolory: Czarny |

### <a name="accent-color-examples"></a>Przykłady kolor akcentu

 W poniższej tabeli przedstawiono kolor akcentu zwracane jako wartość szesnastkową kolor HTML, dla każdego obrazu przykładu.

| Image | Kolor wiodący |
|-------|--------------|
|![Osoba stojących na rock górski zachodzie słońca](./Images/mountain_vista.png) | #BB6D10 |
|![Białe Kwiatek zielonym tłem](./Images/flower.png) | #C6A205 |
|![Szkolenie z za pośrednictwem stacji](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady wykrywania czarno-biały

W poniższej tabeli przedstawiono oceny czarno-biały przetwarzania obrazów w przykładowe obrazy.

| Image | Czarno -biały? |
|-------|----------------|
|![Czarno-biały obraz budynki w Manhattan](./Images/bw_buildings.png) | true |
|![Niebieski dom i yard frontonu](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Kolejne kroki

Pojęcia dotyczące [wykrywanie typy obrazów](concept-detecting-image-types.md).
