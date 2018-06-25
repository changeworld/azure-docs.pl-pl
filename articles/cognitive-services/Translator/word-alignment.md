---
title: Wyrównanie informacji przy użyciu interfejsu API Microsoft Translator tekstu w programie Word | Dokumentacja firmy Microsoft
description: Otrzymywanie informacji wyrównanie programu word z interfejsu API Microsoft Translator tekstu.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 9d8284db61235284ec0d5a1594c34687c89560e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347689"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak odbierać informacje wyrównanie programu word

## <a name="receiving-word-alignment-information"></a>Odbieranie informacji wyrównanie programu word
Aby odbierać informacje wyrównania, używana jest metoda Przetłumacz i dodać parametr opcjonalny includeAlignment.

## <a name="alignment-information-format"></a>Format informacji o wyrównania
Wyrównanie jest zwracana jako wartość ciągu następującego formatu dla każdego wyrazu źródła. Informacje dotyczące każdego wyrazu jest oddzielona od miejsca, w tym dla rozdzielonej spacjami języków (skrypty), takich jak angielski, chiński:

[[SourceTextStartIndex]: [SourceTextEndIndex] — [TgtTextStartIndex]: [TgtTextEndIndex]] *

Przykład wyrównanie ciąg: "0:0-7:10 11 1:2:20 0 3:4:3 3:4-4:5:5-21 6:21".

Innymi słowy dwukropek oddziela start Indeks końcowy, kreska oddziela języki i spacją wyrazy. O jedno słowo w mogą być wyrównane z zero, co najmniej kilka słów w innym języku, a wyrównany wyrażenie może być nieciągłych. Jeśli wyrównanie informacje są niedostępne, element wyrównania jest pusta. Metoda zwraca błąd braku w takiej sytuacji.

## <a name="restrictions"></a>Ograniczenia
Wyrównanie jest zwracany wyłącznie dla podzbioru kierunki w tym momencie:
* z języka angielskiego na inny język;
* z dowolnego innego języka na język angielski, chiński uproszczony, chiński tradycyjny i łotewski język angielski
* z japoński, koreański lub koreański użytkownikowi japońskiego nie będą otrzymywać informacji wyrównania w przypadku zdanie zwięzłych tłumaczenia. Przykład zwięzłych tłumaczenia jest "To jest test", "Świetnie musisz" i innych zdania o dużej częstotliwości.

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
