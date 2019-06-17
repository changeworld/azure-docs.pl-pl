---
title: Tłumaczenie mowy z usług przetwarzania mowy platformy Azure
titlesuffix: Azure Cognitive Services
description: Usługi mowy umożliwiają dodawanie end-to-end, w czasie rzeczywistym, wielu języków tłumaczenia mowy do aplikacji, narzędzi i urządzeń. Tego samego interfejsu API może służyć do tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 84d212129c5225fd0efebfca5640cfc3d32e8a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072407"
---
# <a name="what-is-speech-translation"></a>Co to jest tłumaczenia mowy?

Tłumaczenie mowy z usług przetwarzania mowy platformy Azure, umożliwia w czasie rzeczywistym, wielu języków tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst, strumieni audio. Przy użyciu zestawu SDK rozpoznawania mowy aplikacji, narzędzi i urządzeń korzystać ze źródła transkrypcji i tłumaczenia w danych wyjściowych dla podanego audio. Tymczasowe transkrypcja i tłumaczenie są zwracane jako mowy są wykrywane i finały wyniki mogą być konwertowane na syntezatora mowy.

Aparat tłumaczenia firmy Microsoft jest obsługiwana przez dwa różne podejścia: statystycznego tłumaczenia maszynowego (SMT) i neuronowego tłumaczenia maszynowego (NMT). SMT używa zaawansowanych analiz statystycznych, aby oszacować najlepsze możliwe tłumaczenia podany kontekst kilka słów. Za pomocą NMT sieci neuronowych są używane do podać tłumaczenia dokładniejszych, brzmiącą naturalnego przy użyciu pełnego kontekstu zdania do translacji słów.

Obecnie firma Microsoft używa NMT celu translacji na najbardziej popularnych języków. Wszystkie [języki dostępne do tłumaczenia mowy do mowy](language-support.md#speech-translation) są obsługiwane przez NMT. Tłumaczenie mowy na tekst mogą używać SMT lub NMT, w zależności od pary języka. Język docelowy jest obsługiwany przez NMT, pełne tłumaczenie jest zasilane z NMT. Język docelowy nie jest obsługiwany przez NMT, tłumaczenie jest hybrydowa NMT i SMT, przy użyciu języka angielskiego jako "pivot" między dwa języki.

## <a name="core-features"></a>Podstawowe funkcje

Oto funkcje dostępne za pośrednictwem mowy zestawu SDK i interfejsy API REST:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Tłumaczenie mowy na tekst za pomocą wyniki rozpoznawania. | Tak | Nie |
| Tłumaczenie mowy do rozpoznawania mowy. | Tak | Nie |
| Wyników pośrednich rozpoznawanie i tłumaczenia. | Tak | Nie |

## <a name="get-started-with-speech-translation"></a>Rozpoczynanie pracy z usługą tłumaczenia mowy

Firma Microsoft oferuje przewodników Szybki Start, zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Poniższa tabela zawiera listę uporządkowane według języka mowy tłumaczenia przewodników Szybki Start.

| Szybki start | Platforma | Dokumentacja interfejsów API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Przeglądaj](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zestawu SDK rozpoznawania mowy jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie dźwięku z pliku lub strumienia, ciągłe i pojedynczego zrzutu rozpoznawania/tłumaczenia i Praca z modelami niestandardowych.

* [Przykłady mowy na tekst i tłumaczenie (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Przewodniki dotyczące migracji

> [!WARNING]
> Mowy usługi Translator zostanie zamknięty 15 października 2019 r.

Jeśli Twoje aplikacje, narzędzia lub produkty z mowy usługi Translator, utworzyliśmy przewodniki ułatwiające migrację do usług przetwarzania mowy.

* [Migracja z API mowy usługi Translator do usług przetwarzania mowy](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiany mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

* [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
* [Pobierz zestaw SDK funkcji rozpoznawania mowy](speech-sdk.md)
