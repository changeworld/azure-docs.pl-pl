---
title: Limity żądań — interfejs API tekstu tłumacza
titleSuffix: Azure Cognitive Services
description: W tym artykule wymieniono limity żądań dla interfejsu API tekstu tłumacza. Opłaty są naliczane na podstawie liczby znaków, a nie częstotliwości żądania z limitem 5000 znaków na żądanie. Limity znaków są oparte na subskrypcji, z F0 ograniczona do 2 milionów znaków na godzinę.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498937"
---
# <a name="request-limits-for-translator-text"></a>Limity żądań dla tekstu tłumacza

Ten artykuł zawiera limity ograniczania przepustowości dla interfejsu API tekstu translatora. Usługi obejmują tłumaczenie, transliterację, wykrywanie długości zdania, wykrywanie języka i tłumaczenia alternatywne.

## <a name="character-and-array-limits-per-request"></a>Limity znaków i tablic na żądanie

Każde żądanie tłumaczenia jest ograniczone do 5000 znaków we wszystkich językach docelowych, na które tłumaczysz. Na przykład wysłanie żądania tłumaczenia 1500 znaków do przetłumaczenia na 3 różne języki powoduje rozmiar żądania 1500x3 = 4500 znaków, co spełnia limit żądania. Opłata jest naliczana za znak, a nie od liczby żądań. Zaleca się wysyłanie krótszych żądań.

W poniższej tabeli wymieniono limity elementów tablicy i znaków dla każdej operacji interfejsu API tekstu translatora.

| Operacja | Maksymalny rozmiar elementu tablicy |   Maksymalna liczba elementów tablicy |  Maksymalny rozmiar żądania (znaki) |
|:----|:----|:----|:----|
| Tłumaczenie | 5000 | 100   | 5000 |
| Transliteracja | 5000 | 10    | 5000 |
| Wykrywanie | 10 000 | 100 |   50 000 |
| BreakSentence | 10 000    | 100 | 50 000 |
| Wyszukiwanie w słowniku| 100 |  10  | 1000 |
| Przykłady słowników | 100 dla tekstu i 100 do tłumaczenia (łącznie 200)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Limity znaków na godzinę

Limit znaków na godzinę jest oparty na warstwie subskrypcji translatora. 

Przydział godzinowy powinien być zużywany równomiernie przez całą godzinę. Na przykład przy limicie warstwy F0 wynoszącym 2 miliony znaków na godzinę znaki powinny być używane nie szybciej niż około 33 300 znaków na minutę okna przesuwnego (2 miliony znaków podzielonych przez 60 minut).

Jeśli osiągniesz lub przekroczysz te limity lub wyślesz zbyt dużą część przydziału w krótkim czasie, prawdopodobnie otrzymasz odpowiedź poza przydziałem. Nie ma żadnych ograniczeń dotyczących równoczesnych żądań.

| Warstwa | Limit znaków |
|------|-----------------|
| F0 | 2 miliony znaków na godzinę |
| S1 | 40 milionów znaków na godzinę |
| S2 / C2 | 40 milionów znaków na godzinę |
| S3 / C3 | 120 milionów znaków na godzinę |
| S4 / C4 | 200 milionów znaków na godzinę |

Limity [dla subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) są takie same jak w warstwie S1.

Limity te są ograniczone do standardowych modeli tłumaczenia firmy Microsoft. Niestandardowe modele tłumaczenia korzystające z usługi Custom Translator są ograniczone do 1800 znaków na sekundę.

## <a name="latency"></a>Opóźnienie

Interfejs API tekstu translatora ma maksymalne opóźnienie 15 sekund przy użyciu modeli standardowych i 120 sekund podczas korzystania z modeli niestandardowych. Zazwyczaj odpowiedzi *dla tekstu w ciągu 100 znaków* są zwracane w 150 milisekund do 300 milisekund. Niestandardowe modele tłumacza mają podobne właściwości opóźnienia w przypadku trwałej szybkości żądania i mogą mieć większe opóźnienie, gdy szybkość żądania jest przerywana. Czas odpowiedzi zależy od rozmiaru żądania i pary języków. Jeśli nie otrzymasz tłumaczenia lub [odpowiedzi na błąd](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) w tym przedziale czasowym, sprawdź kod, połączenie sieciowe i ponów próbę. 

## <a name="sentence-length-limits"></a>Limity długości zdania

W przypadku korzystania z funkcji [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) długość zdania jest ograniczona do 275 znaków. Istnieją wyjątki dla tych języków:

| Język | Code | Limit znaków |
|----------|------|-----------------|
| Chiński | Zh | 132 |
| Niemiecki | de | 290 |
| Włoski | it | 280 |
| Japoński | ja | 150 |
| Portugalski | pkt. | 290 |
| Hiszpański | Tak | 280 |
| Włoski | it | 280 |
| Tajski | Th | 258 |

> [!NOTE]
> Ten limit nie ma zastosowania do tłumaczeń.

## <a name="next-steps"></a>Następne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja interfejsu API v3 tłumaczenia tekstu w usłudze Translator](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
