---
title: Tłumaczenie mowy za pomocą usługi mowy
titleSuffix: Azure Cognitive Services
description: Usługa Speech Service umożliwia dodawanie do aplikacji, narzędzi i urządzeń kompleksowego tłumaczenia mowy w czasie rzeczywistym. Tego samego interfejsu API może służyć do tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a05a2bf81a278322bc4e07ed959aedb828c39b73
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815464"
---
# <a name="what-is-speech-translation"></a>Co to jest tłumaczenie mowy?

Tłumaczenie mowy z usługi mowy umożliwia korzystanie z funkcji zamiany mowy na wiele języków w czasie rzeczywistym oraz Tłumaczenie mowy na mowę i zamianę tekstu na tekst strumieni audio. Za pomocą zestawu Speech SDK aplikacje, narzędzia i urządzenia mają dostęp do transkrypcji źródłowych i danych wyjściowych tłumaczenia dla podanego dźwięku. Tymczasowe transkrypcja i wyniki tłumaczenia są zwracane w miarę wykrycia mowy, a końcowe wyniki można przekonwertować na mowę.

Aparat tłumaczenia firmy Microsoft jest obsługiwany przez dwa różne podejścia: tłumaczenia maszyn statystycznych (SMT) i neuronowych Machine Translation (NMT). SMT stosuje zaawansowaną analizę statystyczną do oszacowania najlepszych możliwych tłumaczeń z uwzględnieniem kontekstu kilku wyrazów. Dzięki NMT sieci neuronowych są używane do zapewniania dokładniejszych, naturalnych tłumaczeń przy użyciu pełnego kontekstu zdań do tłumaczenia wyrazów.

Obecnie firma Microsoft używa usługi NMT do tłumaczenia na najpopularniejsze języki. Wszystkie [języki dostępne do tłumaczenia mowy do mowy](language-support.md#speech-translation) są obsługiwane przez NMT. Tłumaczenie mowy na tekst mogą używać SMT lub NMT, w zależności od pary języka. Gdy język docelowy jest obsługiwany przez NMT, pełne tłumaczenie jest NMTe. Gdy język docelowy nie jest obsługiwany przez NMT, tłumaczenie jest hybrydą NMT i SMT przy użyciu języka angielskiego jako "Pivot" między tymi dwoma językami.

## <a name="core-features"></a>Podstawowe funkcje

Poniżej przedstawiono funkcje dostępne za pośrednictwem zestawu Speech SDK i interfejsów API REST:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Tłumaczenie zamiany mowy na tekst z wynikami rozpoznawania. | Tak | Nie |
| Tłumaczenie mowy na mowę. | Tak | Nie |
| Tymczasowe rozpoznawanie i wyniki tłumaczenia. | Tak | Nie |

## <a name="get-started-with-speech-translation"></a>Wprowadzenie do tłumaczenia mowy

Oferujemy Przewodniki Szybki Start zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera listę przewodników szybki start dotyczących tłumaczenia mowy uporządkowanych według języka.

| Szybki start | Platforma | Dokumentacja interfejsów API |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C#, Platformy UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Przeglądaj](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Przeglądaj](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Przeglądaj](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz tłumaczenie i praca z modelami niestandardowymi.

* [Przykłady zamiany mowy na tekst i translacji (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Przewodniki po migracji

Jeśli Twoje aplikacje, narzędzia lub produkty używają [interfejs API tłumaczenia mowy w usłudze translator](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), zostały utworzone przewodniki ułatwiające Migrowanie do usługi mowy.

* [Migrowanie z interfejs API tłumaczenia mowy w usłudze Translator do usługi mowy](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
* [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
* [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
* [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
