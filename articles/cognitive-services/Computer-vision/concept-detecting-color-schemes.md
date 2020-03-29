---
title: Wykrywanie schematu kolorów - Vision komputerowy
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem schematu kolorów na obrazach przy użyciu interfejsu API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244736"
---
# <a name="detect-color-schemes-in-images"></a>Wykrywanie schematów kolorów na obrazach

Wizja komputerowa analizuje kolory obrazu, aby zapewnić trzy różne atrybuty: dominujący kolor pierwszego planu, dominujący kolor tła i zestaw dominujących kolorów dla obrazu jako całości. Zwrócone kolory należą do zestawu: czarny, niebieski, brązowy, szary, zielony, pomarańczowy, różowy, fioletowy, czerwony, turkusowy, biały i żółty. 

Wizja komputerowa wyodrębnia również kolor akcentu, który reprezentuje najbardziej żywy kolor obrazu, w oparciu o kombinację dominujących kolorów i nasycenia. Kolor akcentu jest zwracany jako szesnastkowy kod koloru HTML. 

Funkcja Obrazowanie komputerowe zwraca również wartość logiczną wskazującą, czy obraz jest czarno-biały.

## <a name="color-scheme-detection-examples"></a>Przykłady wykrywania schematu kolorów

Poniższy przykład ilustruje odpowiedź JSON zwróconą przez wizję komputera podczas wykrywania schematu kolorów przykładowego obrazu. W tym przypadku przykładowy obraz nie jest czarno-białym obrazem, ale dominującymi kolorami pierwszego planu i tła są czarne, a dominujące kolory dla obrazu jako całości to czarno-białe.

![Góra na świeżym powietrzu o zachodzie słońca, z sylwetką osoby](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Dominujące przykłady kolorów

W poniższej tabeli przedstawiono zwrócone kolory pierwszego planu, tła i obrazu dla każdego przykładowego obrazu.

| Image (Obraz) | Dominujące kolory |
|-------|-----------------|
|![Biały kwiat z zielonym tłem](./Images/flower.png)| Pierwszy plan: Czarny<br/>Tło: Biały<br/>Kolory: Czarny, Biały, Zielony|
![Pociąg przejeżdżając przez stację](./Images/train_station.png) | Pierwszy plan: Czarny<br/>Tło: Czarny<br/>Kolorystyka: Czarna |

### <a name="accent-color-examples"></a>Przykłady kolorów akcentu

 W poniższej tabeli przedstawiono zwrócony kolor akcentu jako szesnastkową wartość koloru HTML dla każdego przykładowego obrazu.

| Image (Obraz) | Kolor wiodący |
|-------|--------------|
|![Osoba stojąca na górskiej skale o zachodzie słońca](./Images/mountain_vista.png) | #BB6D10 |
|![Biały kwiat z zielonym tłem](./Images/flower.png) | #C6A205 |
|![Pociąg przejeżdżając przez stację](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Przykłady wykrywania czerni & bieli

W poniższej tabeli przedstawiono czarno-białą ocenę usługi Computer Vision na przykładowych obrazach.

| Image (Obraz) | Czarny & biały? |
|-------|----------------|
|![Czarno-biały obraz budynków na Manhattanie](./Images/bw_buildings.png) | true |
|![Niebieski dom i podwórko](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Używanie interfejsu API

Funkcja wykrywania schematu kolorów jest częścią interfejsu API [analizowania obrazu.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Ten interfejs API można wywołać za pośrednictwem natywnego sdk lub za pośrednictwem wywołań REST. Uwzględnij `Color` w **parametrze kwerendy visualFeatures.** Następnie po otrzymaniu pełnej odpowiedzi JSON, po prostu przeanalizować `"color"` ciąg zawartości sekcji.

* [Szybki start: obraz komputera .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Szybki start: analizowanie obrazu (INTERFEJS API REST)](./quickstarts/csharp-analyze.md)
