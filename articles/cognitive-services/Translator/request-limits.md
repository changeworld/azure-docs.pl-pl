---
title: Limity — interfejs API tekstu usługi Translator żądań
titleSuffix: Azure Cognitive Services
description: W tym artykule wymieniono limity żądań do interfejsu API tłumaczenia tekstu. Opłaty są naliczane na podstawie liczby znaków, nie żądania częstotliwość limit 5000 znaków na żądanie. Limity znaków są subskrypcji, za pomocą F0 ograniczona do 2 miliony znaków na godzinę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514652"
---
# <a name="request-limits-for-translator-text"></a>Limity żądań dla tekstu w usłudze Translator

Ten artykuł zawiera limity ograniczania dla interfejsu API tłumaczenia tekstu. Usługi obejmują tłumaczenia, transliterację, wykrywanie długość zdania, wykrywanie języka i alternatywnych tłumaczeń.

## <a name="character-and-array-limits-per-request"></a>Limity znak i tablicy na żądanie

Każde żądanie Translate jest ograniczona do maksymalnie 5000 znaków. Opłaty są naliczane na znak, nie przez liczbę żądań. Zaleca się wysyłanie żądań krótszy.

Następujące tabeli listy tablicy znaków i element limity dla każdej operacji interfejsu API tłumaczenia tekstu.

| Operacja | Maksymalny rozmiar elementu tablicy |   Maksymalna liczba elementów tablicy |  Maksymalny rozmiar żądania (w znakach) |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10 000 | 100 |   50,000 |
| BreakSentence | 10 000    | 100 | 5,0000 |
| Wyszukiwanie w słowniku| 100 |  10  | 1000 |
| Przykłady słowników | 100 dla tekstu i 100 tłumaczenia (200 całkowita)| 10|   2000 |

## <a name="character-limits-per-hour"></a>Limity znak na godzinę

Limit znaków na godzinę jest oparty na warstwę subskrypcji tekstu usługi Translator. Limit godzinny powinny być wykorzystywane równomiernie w ciągu godziny. Jeśli dotrzeć do lub przekroczenie limitów lub wysłać zbyt duży, jaka część kwoty w krótkim czasie, prawdopodobnie otrzymasz poza limit przydziału odpowiedzi.

| Warstwa | Maksymalna liczba znaków |
|------|-----------------|
| F0 | 2 miliony znaków na godzinę |
| S1 | 40 milionów znaków na godzinę |
| S2 / C2 | 40 milionów znaków na godzinę |
| S3 / C3 | 120 mln znaków na godzinę |
| S4 / C4 | 200 mln znaków na godzinę |

Limity dla [subskrypcji z wieloma usługami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) są takie same jak w warstwie S1.

Limity te są ograniczone do modele tłumaczenia standard firmy Microsoft. Modele tłumaczenia niestandardowych, korzystających z niestandardowych w usłudze Translator są ograniczone do 1800 znaku w ciągu sekundy.

## <a name="latency"></a>Opóźnienie

Interfejs API tekstu usługi Translator ma maksymalny czas oczekiwania 15 sekund przy użyciu standardowych modeli. Tłumaczenie przy użyciu niestandardowych modeli zawiera maksymalny czas oczekiwania 25 sekund. Po tym czasie będzie Odebrano wynik lub limit czasu odpowiedzi. Zazwyczaj odpowiedzi są zwracane w milisekundach 150 do 300 MS. Czasy odpowiedzi będzie zależeć od rozmiaru pary żądania i język. Jeśli nie otrzymasz tłumaczenia lub [odpowiedzi na błąd](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) w określonym czasie, należy sprawdzić połączenie sieciowe i spróbuj ponownie.

## <a name="sentence-length-limits"></a>Limity długości zdania

Korzystając z [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) funkcji zdania długość jest ograniczona do 275 znaków. Istnieją jednak wyjątki dla następujących języków:

| Język | Kod | Maksymalna liczba znaków |
|----------|------|-----------------|
| Chiński | nazwy zh | 132 |
| Niemiecki | de | 290 |
| Włoski | it | 280 |
| Japoński | ja | 150 |
| Portugalski | pt | 290 |
| Hiszpański | es | 280 |
| Włoski | it | 280 |
| Tajlandzki | TH | 258 |

> [!NOTE]
> To ograniczenie nie ma zastosowania do tłumaczenia.

## <a name="next-steps"></a>Kolejne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja interfejsu API tłumaczenia tekstu w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
