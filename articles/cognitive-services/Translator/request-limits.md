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
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: 97b0b6256b7aaf7b42565fe9453fb87a0c414569
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605216"
---
# <a name="request-limits-for-translator-text"></a>Limity żądań dla tekstu w usłudze Translator

Ten artykuł zawiera limity ograniczania dla interfejsu API tłumaczenia tekstu. Usługi obejmują tłumaczenia, transliterację, wykrywanie długość zdania, wykrywanie języka i alternatywnych tłumaczeń.

## <a name="character-limits-per-request"></a>Limity znak na żądanie

Każde żądanie jest ograniczona do maksymalnie 5000 znaków. Opłaty są naliczane na znak, nie przez liczbę żądań. Zalecane jest, na potrzeby wysyłania żądań krótsze i mieć niektórych żądań oczekujących w danym momencie.

Nie ma żadnego limitu liczby oczekujących żądań interfejsu API tłumaczenia tekstu.

## <a name="character-limits-per-hour"></a>Limity znak na godzinę

Limit znaków na godzinę jest oparty na warstwę subskrypcji tekstu usługi Translator. Jeśli się połączyć lub przekroczenia tych ograniczeń, prawdopodobnie otrzymasz poza limit przydziału odpowiedzi:

| Warstwa | Maksymalna liczba znaków |
|------|-----------------|
| F0 | 2 miliony znaków na godzinę |
| S1 | 40 milionów znaków na godzinę |
| S2 | 40 milionów znaków na godzinę |
| S3 | 120 mln znaków na godzinę |
| S4 | 200 mln znaków na godzinę |

Limity te są ograniczone do ogólnych systemy firmy Microsoft. Systemy tłumaczenia niestandardowych, które za pomocą Centrum w usłudze Translator firmy Microsoft są ograniczone do 1800 znaku w ciągu sekundy.

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
| Tajlandzki | . | 258 |

> [!NOTE]
> To ograniczenie nie ma zastosowania do tłumaczenia.

## <a name="next-steps"></a>Kolejne kroki

* [Cennik](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Dostępność regionalna](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Dokumentacja interfejsu API tłumaczenia tekstu w wersji 3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
