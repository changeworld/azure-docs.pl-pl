---
title: Zamiana mowy na tekst — usługa mowy
titleSuffix: Azure Cognitive Services
description: Funkcja zamiany mowy na tekst umożliwia transkrypcję strumieni dźwiękowych w czasie rzeczywistym w postaci tekstu, w jakim aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania w trybie wprowadzania poleceń. Ta usługa współpracuje bezproblemowo z funkcją zamiany tekstu na mowę (synteza mowy) i funkcją tłumaczenia mowy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49bfa4a0dbf0adc498d545a2908c20f0ffa35b4b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075731"
---
# <a name="what-is-speech-to-text"></a>Co to jest zamiana mowy na tekst?

Zamiana mowy na tekst z usług Azure Speech Services, znana także jako Zamiana mowy na tekst, umożliwia zapisanie strumieni audio w czasie rzeczywistym w postaci tekstu, w którym aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania przy użyciu poleceń wejściowych. Ta usługa jest oparta na tej samej technologii rozpoznawania, która jest stosowana przez firmę Microsoft dla Cortany i produktów pakietu Office i bezproblemowo współpracuje z translacją i zamianą tekstu na mowę. Aby uzyskać pełną listę dostępnych języków zamiany mowy na tekst, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Domyślnie usługa zamiany mowy na tekst używa modelu języka uniwersalnego. Ten model został przeszkolony przy użyciu danych firmy Microsoft i jest wdrażany w chmurze. Jest optymalny dla scenariuszy konwersacji i dyktowania. Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż.

Możesz łatwo przechwycić dźwięk z mikrofonu, odczytać ze strumienia lub uzyskać dostęp do plików audio z magazynu za pomocą zestawu Speech SDK i interfejsów API REST. Zestaw SDK usługi Mowa obsługuje na potrzeby rozpoznawania mowy jednokanałowy, 16-bitowy dźwięk w formacie WAV/PCM z częstotliwością próbkowania 16 kHz/8 kHz. Inne formaty dźwięku są obsługiwane za pomocą [punktu końcowego REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) lub [usługi transkrypcji wsadowej](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Podstawowe funkcje

Poniżej przedstawiono funkcje dostępne za pośrednictwem zestawu Speech SDK i interfejsów API REST:

| Przypadek użycia | SDK | REST |
|--------- | --- | ---- |
| Transkrypcja Short wyrażenia długości (< 15 sekund). Obsługuje tylko końcowy wynik transkrypcji. | Yes | Yes |
| Ciągła transkrypcja długich wyrażenia długości i przesyłania strumieniowego audio (> 15 sekund). Obsługuje tymczasowe i końcowe wyniki transkrypcji. | Yes | Nie |
| Wyprowadzanie intencji z wyników rozpoznawania z [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Yes | Brak\* |
| Asynchroniczne transkrypcja plików audio. | Nie  | Tak\*\* |
| Twórz modele mowy i zarządzaj nimi. | Nie | Tak\*\* |
| Twórz wdrożenia modelu niestandardowego i zarządzaj nimi. | Nie  | Tak\*\* |
| Utwórz testy dokładności, aby zmierzyć dokładność modelu linii bazowej w porównaniu z modelami niestandardowymi. | Nie  | Tak\*\* |
| Zarządzanie subskrypcjami. | Nie  | Tak\*\* |

\*_intencje i jednostki mogą być wyprowadzane przy użyciu oddzielnej subskrypcji Luis. Dzięki tej subskrypcji zestaw SDK może wywoływać LUIS i dostarczać wyniki dla obiektów i zamierzeń. Korzystając z interfejsu API REST, możesz samodzielnie wywoływać LUIS, aby uzyskać intencje i jednostki z subskrypcją LUIS._

\*\*_te usługi są dostępne za pomocą punktu końcowego CRIS.AI. Zobacz [Dokumentacja struktury Swagger](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Wprowadzenie do zamiany mowy na tekst

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uruchamiania kodu w mniej niż 10 minut. [Ta tabela](https://aka.ms/csspeech#5-minute-quickstarts) zawiera pełną listę przewodników szybki start dotyczących zestawu SDK, zorganizowanych według platformy i języka. Odwołanie do interfejsu API można również znaleźć [tutaj](https://aka.ms/csspeech#reference).

Jeśli wolisz używać usługi REST do zamiany mowy na tekst, zobacz [interfejsy API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Samouczki i przykładowy kod

Jeśli masz szansę na korzystanie z usług mowy, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi.

- [Przykłady zamiany mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Dostosowywanie

Oprócz standardowego modelu linii bazowej używanego przez usługi mowy, można dostosować modele do własnych potrzeb, korzystając z dostępnych danych, aby przezwyciężyć bariery rozpoznawania mowy, takie jak styl mowy, słownictwo i hałas w tle, zobacz [Custom Speech](how-to-custom-speech.md)

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="migration-guides"></a>Przewodniki po migracji

> [!WARNING]
> Rozpoznawanie mowy Bing został zlikwidowany 15 października 2019.

Jeśli Twoje aplikacje, narzędzia lub produkty używają rozpoznawanie mowy Bing interfejsów API lub Custom Speech, zostały utworzone przewodniki ułatwiające Migrowanie do usługi Speech Services.

- [Migrowanie z rozpoznawanie mowy Bing do usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Migrowanie z Custom Speech do usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Dokumentacja

- [Zestaw SDK rozpoznawania mowy](https://aka.ms/csspeech)
- [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
- [Pobieranie zestawu Speech SDK](speech-sdk.md)
