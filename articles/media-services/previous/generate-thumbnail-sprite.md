---
title: Generowanie ikonki miniatur za pomocą usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak wygenerować ikonkę miniatur za pomocą usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61229054"
---
# <a name="generate-a-thumbnail-sprite"></a>Generowanie sprite’u miniatury  

Za pomocą programu Media Encoder Standard można wygenerować ikonkę miniatur, która jest plikiem JPEG zawierającym wiele miniatur o małej rozdzielczości zszytych w jeden (duży) obraz wraz z plikiem VTT. Ten plik VTT określa zakres czasu w wejściowym filmie wideo, który reprezentuje każda miniatura, wraz z rozmiarem i współrzędnymi tej miniatury w dużym pliku JPEG. Odtwarzacze wideo używają pliku VTT i obrazu sprite'a, aby pokazać "wizualny" pasek wyszukiwania, zapewniając widzowi wizualne informacje zwrotne podczas szorowania do tyłu i do przodu wzdłuż osi czasu wideo.

Aby wygenerować ikonę Sprite miniatury, można użyć standardu embarki multimediów, ustawienie predefiniowane:

1. Musi używać formatu miniatury JPG
2. Należy określić wartości start/krok/zakres jako znaczniki czasu lub wartości % (a nie liczbę klatek) 
    
    1. Można mieszać znaczniki czasu i wartości %

3. Musi mieć wartość SpriteColumn, jako liczbę nieujemną większą lub równą 1

    1. Jeśli SpriteColumn jest ustawiony na M >= 1, obraz wyjściowy jest prostokątem z kolumnami M. Jeśli liczba miniatur generowanych za pośrednictwem #2 nie jest dokładną wielokrotnością M, ostatni wiersz będzie niekompletny i pozostawiony czarnym pikselom.  

Oto przykład:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Znane problemy

1.  Nie jest możliwe wygenerowanie obrazu sprite z jednym wierszem obrazów (SpriteColumn = 1 powoduje obraz z pojedynczą kolumną).
2.  Fragmentowanie obrazów sprite'a na obrazy JPEG o umiarkowanej wielkości nie jest jeszcze obsługiwane. W związku z tym należy zadbać o ograniczenie liczby miniatur i ich rozmiaru, tak aby wynikowy zszyty miniatura Sprite miał około 8 mln pikseli lub mniej.
3.  Usługa Azure Media Player obsługuje sprite'y w przeglądarkach Microsoft Edge, Chrome i Firefox. Analizowanie VTT nie jest obsługiwane w IE11.

## <a name="next-steps"></a>Następne kroki

[Kodowanie zawartości](media-services-encode-asset.md)
