---
title: Wyrównanie Word — interfejs API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Otrzymywanie informacji, wyrównanie programu word z interfejsu API tłumaczenia tekstu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 3db5e9651e307211e9dccee20bb8d69586bb9ef1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448160"
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
