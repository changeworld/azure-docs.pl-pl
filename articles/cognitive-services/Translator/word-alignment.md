---
title: Wyrównanie Word — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Otrzymywanie informacji, wyrównanie programu word z interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.custom: seodec18
ms.openlocfilehash: 040d122810529daafcebc2e66f2fa7b2730a6a0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514663"
---
# <a name="how-to-receive-word-alignment-information"></a>Jak otrzymywać informacje wyrównanie programu word

## <a name="receiving-word-alignment-information"></a>Odbieranie informacji wyrównanie programu word
Aby otrzymywać informacje wyrównanie, używana jest metoda Translate i zawierać parametr opcjonalny includeAlignment.

## <a name="alignment-information-format"></a>Format informacji o wyrównania
Wyrównanie jest zwracana jako wartość ciągu następujący format dla każdego wyrazu w źródle. Informacje dotyczące każdego wyrazu jest oddzielona od miejsca, w tym oddzielone miejsca języków (skrypt), takich jak chiński:

[[SourceTextStartIndex]:[SourceTextEndIndex]—[TgtTextStartIndex]:[TgtTextEndIndex]] *

Przykład parametrów wyrównanie: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Innymi słowy dwukropek oddziela start końcowego indeksu, kreska oddziela językach i spacja oddziela słowa. O jeden wyraz może być dostosowanie zero, jeden lub wiele słów w innym języku, a wyrównany wyrażenie może być nieciągłe. Po udostępnieniu żadnych informacji wyrównanie elementu wyrównania jest pusta. Metoda zwraca błąd braku w takiej sytuacji.

## <a name="restrictions"></a>Ograniczenia
Wyrównanie jest zwracany tylko wtedy dla podzbioru kierunki w tym momencie:
* z języka angielskiego na dowolnym innym języku;
* z dowolnego innego języka na język angielski, chiński uproszczony, chiński tradycyjny i łotewski do języka angielskiego
* z japoński, koreański lub koreański użytkownikowi japońskiego nie będzie otrzymywać informacji wyrównanie przypadku zdanie zwięzłych tłumaczenia. Przykład zwięzłych tłumaczenia jest "To jest test", "Kocham Cię" i inne zdania wysokiej częstotliwości.

## <a name="example"></a>Przykład

Przykładowy plik JSON

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
