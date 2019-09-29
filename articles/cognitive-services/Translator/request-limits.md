---
title: Limity żądań — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono limity żądań dla interfejs API tłumaczenia tekstu w usłudze Translator. Opłaty są naliczane w oparciu o liczbę znaków, a nie częstotliwość żądań z limitem 5 000 znaków na żądanie. Limity znaków są oparte na subskrypcjach, z F0 ograniczone do 2 000 000 znaków na godzinę.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 79cf87cef4e58cf4e5a2039f30289d55038c8b92
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671968"
---
# <a name="request-limits-for-translator-text"></a>Limity żądań dla tłumaczenie tekstu w usłudze Translator

W tym artykule przedstawiono limity ograniczania dla interfejs API tłumaczenia tekstu w usłudze Translator. Usługi obejmują tłumaczenie, przekształcenie, wykrywanie długości zdania, wykrywanie języka i alternatywne tłumaczenia.

## <a name="character-and-array-limits-per-request"></a>Limity liczby znaków i tablic na żądanie

Każde żądanie tłumaczenia jest ograniczone do 5 000 znaków. Opłata jest naliczana za znak, a nie przez liczbę żądań. Zaleca się wysyłanie krótszych żądań.

W poniższej tabeli wymieniono limity elementów tablicy i znaków dla każdej operacji interfejs API tłumaczenia tekstu w usłudze Translator.

| Operacja | Maksymalny rozmiar elementu tablicy |   Maksymalna liczba elementów tablicy |  Maksymalny rozmiar żądania (znaki) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10 000 | 100 |   50,000 |
| BreakSentence | 10 000    | 100 | 50,000 |
| Wyszukiwanie w słowniku| 100 |  10  | 1000 |
| Przykłady słowników | 100 dla tekstu i 100 dla tłumaczenia (łącznie 200)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Limity znaków na godzinę

Limit znaków na godzinę jest oparty na warstwie subskrypcji tłumaczenie tekstu w usłudze Translator. 

Przydział godzinowy powinien być wykorzystany nawet w ciągu godziny. Na przykład w przypadku F0 limitu warstwy o 2 000 000 znaków na godzinę, znaki powinny być używane nie szybciej niż w przybliżeniu 33 300 znaki na minutę (2 000 000 znaki podzielone przez 60 minut).

W przypadku osiągnięcia lub przekroczenia tych limitów lub wysłania zbyt dużej części limitu przydziału w krótkim czasie prawdopodobnie otrzymasz odpowiedź na brak limitu przydziału. Nie ma ograniczeń dla współbieżnych żądań.

| Warstwa | Limit znaków |
|------|-----------------|
| F0 | 2 000 000 znaków na godzinę |
| S1 | 40 000 000 znaków na godzinę |
| S2 / C2 | 40 000 000 znaków na godzinę |
| S3/C3 | 120 000 000 znaków na godzinę |
| S4 / C4 | 200 000 000 znaków na godzinę |

Limity dla [subskrypcji dla kilku usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) są takie same jak w przypadku warstwy S1.

Limity te są ograniczone do standardowych modeli tłumaczenia firmy Microsoft. Niestandardowe modele tłumaczenia korzystające z translatora niestandardowego są ograniczone do 1 800 znaków na sekundę.

## <a name="latency"></a>Czas oczekiwania

Interfejs API tłumaczenia tekstu w usłudze Translator ma maksymalne opóźnienie wynoszące 15 sekund przy użyciu standardowych modeli. Tłumaczenie przy użyciu modeli niestandardowych ma maksymalne opóźnienie wynoszące 25 sekund. W tym momencie otrzymasz wynik lub odpowiedź na przekroczenie limitu czasu. Zwykle odpowiedzi są zwracane w 150 milisekund do 300 milisekund. Czasy odpowiedzi będą się różnić w zależności od rozmiaru pary żądań i języków. Jeśli nie otrzymasz tłumaczenia lub odpowiedź na [błąd](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) w tym okresie, sprawdź połączenie sieciowe i spróbuj ponownie.

## <a name="sentence-length-limits"></a>Limity długości zdania

W przypadku korzystania z funkcji [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) długość zdania jest ograniczona do 275 znaków. Istnieją wyjątki dla następujących języków:

| Język | Kod | Limit znaków |
|----------|------|-----------------|
| Chiński | zh | 132 |
| Niemiecki | de | 290 |
| Włoski | it | 280 |
| Japoński | ja | 150 |
| Portugalski | zmiennoprzecinkow | 290 |
| Hiszpański | es | 280 |
| Włoski | it | 280 |
| Tajlandzki | th | 258 |

> [!NOTE]
> Ten limit nie dotyczy tłumaczeń.

## <a name="next-steps"></a>Następne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja interfejs API tłumaczenia tekstu w usłudze Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
