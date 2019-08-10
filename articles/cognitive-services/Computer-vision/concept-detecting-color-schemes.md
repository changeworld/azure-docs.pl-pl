---
title: Wykrywanie schematu kolorów — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem schematu kolorów w obrazach przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945274"
---
# <a name="detect-color-schemes-in-images"></a>Wykryj schematy kolorów w obrazach

Przetwarzanie obrazów analizuje kolory w obrazie, aby zapewnić trzy różne atrybuty: kolor pierwszego planu, kolor tła, a także zestaw kolorów dla obrazu jako całości. Zwrócone kolory należą do zestawu: czerń, niebieska, brązowy, szary, zielony, pomarańczowy, różowy, purpurowy, czerwony, turkusowy, biały i żółty. 

Przetwarzanie obrazów również wyodrębnia kolor akcentu, który reprezentuje najbardziej żywe kolory obrazu, na podstawie kombinacji dominujących kolorów i nasycenia. Kolor akcentu jest zwracany jako szesnastkowy kod HTML. 

Przetwarzanie obrazów zwraca również wartość logiczną wskazującą, czy obraz jest czarno biały.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schematu kolorów

Poniższy przykład ilustruje odpowiedź JSON zwracaną przez przetwarzanie obrazów podczas wykrywania schematu kolorów przykładowego obrazu. W takim przypadku przykładowym obrazem nie jest czarny i biały obraz, ale dominujący kolor pierwszego planu i tła są czarne, a dominujące kolory obrazu jako całości są czarne i białe.

![Górski grunty na zachód słońca i Silhouette osoby](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Przykłady kolorów dominujących

W poniższej tabeli przedstawiono zwrócone kolory pierwszego planu, tła i obrazu dla każdego przykładowego obrazu.

| Image | Kolory dominujące |
|-------|-----------------|
|![Biały kwiat z zielonym tłem](./Images/flower.png)| Pierwszego planu Czarny<br/>Tle Biały<br/>Świat Czarny, biały, zielony|
![Pociąg działający za pomocą stacji](./Images/train_station.png) | Pierwszego planu Czarny<br/>Tle Czarny<br/>Świat Czarny |

### <a name="accent-color-examples"></a>Przykłady koloru akcentu

 W poniższej tabeli przedstawiono zwrócony kolor akcentu jako szesnastkową wartość koloru HTML dla każdego przykładowego obrazu.

| Image | Kolor wiodący |
|-------|--------------|
|![Osoba stojąca na skałie górskim o zachodzie słońca](./Images/mountain_vista.png) | #BB6D10 |
|![Biały kwiat z zielonym tłem](./Images/flower.png) | #C6A205 |
|![Pociąg działający za pomocą stacji](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady czerni & białych

W poniższej tabeli przedstawiono czarną i białą ocenę przetwarzanie obrazów na przykładowych obrazach.

| Image | Czarny & biały? |
|-------|----------------|
|![Czarno-białe zdjęcie budynków w Manhattan](./Images/bw_buildings.png) | true |
|![Niebieska i czołowa Jard](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Następne kroki

Poznaj koncepcje dotyczące [wykrywania typów obrazów](concept-detecting-image-types.md).
