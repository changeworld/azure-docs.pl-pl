---
title: Tłumaczenie mowy za pomocą usługi mowy
titleSuffix: Azure Cognitive Services
description: Usługa mowy umożliwia dodawanie kompleksowego, w czasie rzeczywistym, wielojęzycznego tłumaczenia mowy do aplikacji, narzędzi i urządzeń. Ten sam interfejs API może służyć do tłumaczeń polegających na zamianie mowy na mowę i mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052615"
---
# <a name="what-is-speech-translation"></a>Co to jest tłumaczenie mowy?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Tłumaczenie mowy z usługi Mowy umożliwia tłumaczenie strumieni audio w czasie rzeczywistym, wielojęzycznych na mowę i zamianę mowy na tekst. Dzięki zestawowi SDK mowy aplikacje, narzędzia i urządzenia mają dostęp do transkrypcji źródłowych i wyjść tłumaczeniowych dla dostarczonego dźwięku. Tymczasowe wyniki transkrypcji i tłumaczenia są zwracane w miarę wykrywania mowy, a wyniki finałów mogą być konwertowane na mowę syntetyzowaną.

Aparat tłumaczeniowy firmy Microsoft jest oparty na dwóch różnych podejściach: statystycznego tłumaczenia maszynowego (SMT) i neuronowego tłumaczenia maszynowego (NMT). SMT wykorzystuje zaawansowaną analizę statystyczną do oszacowania najlepszych możliwych tłumaczeń, biorąc pod uwagę kontekst kilku słów. W przypadku NMT sieci neuronowe są używane w celu zapewnienia dokładniejszych, naturalnie brzmiących tłumaczeń przy użyciu pełnego kontekstu zdań do tłumaczenia słów.

Obecnie firma Microsoft używa nmt do tłumaczenia na najpopularniejsze języki. Wszystkie [języki dostępne do tłumaczenia mowy na mowę](language-support.md#speech-translation) są obsługiwane przez NMT. Tłumaczenie zamiany mowy na tekst może używać SMT lub NMT w zależności od pary języków. Gdy język docelowy jest obsługiwany przez NMT, pełne tłumaczenie jest zasilane nmt. Gdy język docelowy nie jest obsługiwany przez NMT, tłumaczenie jest hybrydą NMT i SMT, przy użyciu języka angielskiego jako "pivot" między dwoma językami.

## <a name="core-features"></a>Podstawowe funkcje

Oto funkcje dostępne za pośrednictwem interfejsu SDK mowy i interfejsów API REST:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Tłumaczenie zamiany mowy na tekst z wynikami rozpoznawania. | Tak | Nie |
| Tłumaczenie mowy na mowę. | Tak | Nie |
| Tymczasowe uznawanie i wyniki tłumaczeń. | Tak | Nie |

## <a name="get-started-with-speech-translation"></a>Wprowadzenie do tłumaczenia mowy

Oferujemy szybkie starty zaprojektowane tak, aby kod był uruchamiany w mniej niż 10 minut. Ta tabela zawiera listę szybkich startów tłumaczenia mowy uporządkowanych według języka.

| Szybki start | Platforma | Odwołanie API |
|------------|----------|---------------|
| [C#, rdzeń .NET](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Przeglądaj](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zestawu SDK mowy jest dostępny w usłudze GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczyt dźwięku z pliku lub strumienia, ciągłe i pojedyncze ujęcie rozpoznawania/tłumaczenia i pracy z modelami niestandardowymi.

* [Przykłady zamiany mowy na tekst i tłumaczenia (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Przewodniki po migracji

Jeśli aplikacje, narzędzia lub produkty korzystają z [interfejsu API mowy tłumacza,](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)utworzyliśmy przewodniki ułatwiające migrację do usługi Mowy.

* [Migrowanie z interfejsu API mowy tłumacza do usługi mowy](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumenty referencyjne

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatny klucz subskrypcji usługi mowy](get-started.md)
* [Pobierz sdk mowy](speech-sdk.md)
