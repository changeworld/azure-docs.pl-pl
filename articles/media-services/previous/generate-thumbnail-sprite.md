---
title: Generowanie miniatur sprite za pomocą usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób generowania miniatur sprite za pomocą usługi Azure Media Services.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229054"
---
# <a name="generate-a-thumbnail-sprite"></a>Generowanie sprite’u miniatury  

Usługi Media Encoder Standard umożliwia generowanie miniatur sprite, który plik JPEG, który zawiera wiele miniatur małych rozpoznawania połączonych w jeden obraz (duża liczba godzin), wraz z plik VTT. Ten plik VTT określa zakres czasu w wejściowy plik wideo, który reprezentuje poszczególne miniatury, wraz z rozmiarem i współrzędne tego miniaturę w dużych plików JPEG. Odtwarzacze wideo użyć obrazu pliku i sprite VTT Pokaż "visual" seekbar, dostarczając przeglądarka wizualną opinię podczas szybkiej kontroli Wstecz i do przodu osi wideo.

Aby można było używać usługi Media Encoder Standard, aby wygenerować miniatury Sprite, ustawienie wstępne:

1. Należy użyć formatu miniaturę JPG
2. Należy określić Start/krok/zakresu wartości co w przypadku obu sygnatury czasowe, lub wartości % (a nie liczby ramki) 
    
    1. Można mieszać wartości sygnatur czasowych i %

3. Musi mieć wartość SpriteColumn jako liczba ujemna większa niż lub równa 1

    1. Jeśli ustawiono SpriteColumn M > = 1, obraz danych wyjściowych jest prostokąt z kolumnami M. Jeśli liczba miniatur generowany za pośrednictwem #2 nie jest wielokrotnością M, ostatni wiersz będzie niekompletna i po lewej stronie czarne pikseli.  

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

1.  Nie jest możliwe do generowania obrazu sprite, z jednym wierszem obrazów (SpriteColumn = 1 powoduje obrazu z jedną kolumną).
2.  Segmentu obrazów sprite na umiarkowanie wielkości obrazy JPEG nie jest jeszcze obsługiwany. Z tego powodu należy uważać na ograniczenie liczby miniatur i ich rozmiar, tak aby wynikowe połączone Sprite miniaturę wokół 8 pikseli M lub mniej.
3.  Usługa Azure Media Player obsługuje obrazki w przeglądarkach Microsoft Edge, Chrome i Firefox. Podczas analizowania VTT nie jest obsługiwana w programie IE11.

## <a name="next-steps"></a>Kolejne kroki

[Kodowanie zawartości](media-services-encode-asset.md)
