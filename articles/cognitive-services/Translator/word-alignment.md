---
title: Wyrównanie programu Word — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: Aby otrzymywać informacje o wyrównaniu, należy użyć Translate metody i dołączyć opcjonalne includeAlignment parametr.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837226"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak odbierać informacje o wyrównaniu wyrazów

## <a name="receiving-word-alignment-information"></a>Odbieranie informacji o wyrównaniu wyrazów
Aby otrzymywać informacje o wyrównaniu, należy użyć Translate metody i dołączyć opcjonalne includeAlignment parametr.

## <a name="alignment-information-format"></a>Format informacji o wyrównaniu
Wyrównanie jest zwracane jako wartość ciągu następującego formatu dla każdego wyrazu źródła. Informacje dla każdego wyrazu są oddzielone spacją, w tym dla języków niedzielonych przestrzenią (skryptów), takich jak chiński:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Przykład wyrównania ciąg: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Innymi słowy dwukropek oddziela indeks początkowy i końcowy, kreska oddziela języki, a spacja oddziela wyrazy. Jedno słowo może być wyrównane z wartością zero, jeden lub wiele wyrazów w drugim języku, a wyrównane wyrazy mogą być niesłacalne. Gdy nie są dostępne żadne informacje o wyrównaniu, element Wyrównanie będzie pusty. Metoda zwraca błąd w tym przypadku.

## <a name="restrictions"></a>Ograniczenia
Wyrównanie jest zwracany tylko dla podzbioru par języków w tym momencie:
* z angielskiego na jakikolwiek inny język;
* z dowolnego innego języka na angielski, z wyjątkiem języka chińskiego uproszczonego, chińskiego tradycyjnego i łotewskiego na angielski
* z japońskiego na koreański lub koreański do japoński nie otrzymasz informacji o wyrównaniu, jeśli zdanie jest tłumaczeniem w puszkach. Przykładem tłumaczenia w puszkach jest "To jest test", "Kocham cię" i inne zdania o wysokiej częstotliwości.

## <a name="example"></a>Przykład

Przykład JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
