---
title: Zamiana mowy na tekst za pomocą usług Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Zamiana mowy na tekst z usług Azure Speech Services, znana także jako Zamiana mowy na tekst, umożliwia zapisanie strumieni audio w czasie rzeczywistym w postaci tekstu, w którym aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania przy użyciu poleceń wejściowych. Ta usługa jest oparta na tej samej technologii rozpoznawania, która jest stosowana przez firmę Microsoft dla Cortany i produktów pakietu Office i bezproblemowo współpracuje z translacją i zamianą tekstu na mowę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cb9362c4d58deb5472c8d5adab39cdd1cc4e2600
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333576"
---
# <a name="what-is-speech-to-text"></a>Co to jest zamiana mowy na tekst?

Zamiana mowy na tekst z usług Azure Speech Services, znana także jako Zamiana mowy na tekst, umożliwia zapisanie strumieni audio w czasie rzeczywistym w postaci tekstu, w którym aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania przy użyciu poleceń wejściowych. Ta usługa jest oparta na tej samej technologii rozpoznawania, która jest stosowana przez firmę Microsoft dla Cortany i produktów pakietu Office i bezproblemowo współpracuje z translacją i zamianą tekstu na mowę.  Aby uzyskać pełną listę dostępnych języków zamiany mowy na tekst, zobacz [obsługiwane języki](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text).

Domyślnie usługa zamiany mowy na tekst używa modelu języka uniwersalnego. Ten model został przeszkolony przy użyciu danych firmy Microsoft i jest wdrażany w chmurze. Jest optymalny dla scenariuszy konwersacji i dyktowania. Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż.

Możesz łatwo przechwycić dźwięk z mikrofonu, odczytać ze strumienia lub uzyskać dostęp do plików audio z magazynu za pomocą zestawu Speech SDK i interfejsów API REST. Zestaw Speech SDK obsługuje format WAV/PCM 16-bitowy, 16 kHz/8 kHz, dźwięk z jednym kanałem na potrzeby rozpoznawania mowy. Dodatkowe formaty audio są obsługiwane za pomocą [punktu końcowego REST zamiany mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) lub [usługi transkrypcji partii](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Podstawowe funkcje

Poniżej przedstawiono funkcje dostępne za pośrednictwem zestawu Speech SDK i interfejsów API REST:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Transkrypcja Short wyrażenia długości (< 15 sekund). Obsługuje tylko końcowy wynik transkrypcji. | Tak | Tak |
| Ciągła transkrypcja długich wyrażenia długości i przesyłania strumieniowego audio (> 15 sekund). Obsługuje tymczasowe i końcowe wyniki transkrypcji. | Tak | Nie |
| Wyprowadzanie intencji z wyników rozpoznawania z [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Tak | Brak\* |
| Asynchroniczne transkrypcja plików audio. | Nie | Opcję\** |
| Twórz modele mowy i zarządzaj nimi. | Nie | Opcję\** |
| Twórz wdrożenia modelu niestandardowego i zarządzaj nimi. | Nie | Opcję\** |
| Utwórz testy dokładności, aby zmierzyć dokładność modelu linii bazowej w porównaniu z modelami niestandardowymi. | Nie | Opcję\** |
| Zarządzanie subskrypcjami. | Nie | Opcję\** |

\* *Usługa LUIS intencje i podmioty mogą być uzyskane przy użyciu oddzielnej subskrypcji usługi LUIS. Dzięki tej subskrypcji zestaw SDK może wywoływać LUIS i dostarczać wyniki dla obiektów i zamierzeń. Za pomocą interfejsu API REST, można wywołać usługi LUIS samodzielnie do wyprowadzenia intencje i podmioty ze swoją subskrypcją usługi LUIS.*

\** *Te usługi są dostępne za pomocą punktu końcowego cris.ai. Zobacz [Dokumentacja struktury Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Wprowadzenie do zamiany mowy na tekst

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uruchamiania kodu w mniej niż 10 minut. [Ta tabela](https://aka.ms/csspeech#5-minute-quickstarts) zawiera pełną listę przewodników szybki start dotyczących zestawu SDK, zorganizowanych według użyciu funkcji platfrom i języka.  Odwołanie do interfejsu API można również znaleźć [tutaj](https://aka.ms/csspeech#reference).

Jeśli wolisz używać usługi REST do zamiany mowy na tekst, zobacz [interfejsy API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Samouczki i przykładowy kod

Jeśli masz szansę na korzystanie z usług mowy, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

* [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi.

* [Przykłady zamiany mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Dostosowywanie

Oprócz modelu uniwersalnego używanego przez usługi mowy, można utworzyć niestandardowe modele akustyczne, językowe i wymowy charakterystyczne dla Twojego środowiska. Oto lista opcji dostosowywania:

| Modelowanie | Opis |
|-------|-------------|
| [Model akustyczny](how-to-customize-acoustic-models.md) | Tworzenie niestandardowego modelu akustycznego jest przydatne, jeśli aplikacja, narzędzia lub urządzenia są używane w konkretnym środowisku, na przykład w samochodowym lub fabryce, z określonymi warunkami rejestrowania. Przykłady obejmują akcentowaną mowę, specyficzne szumy w tle lub użycie konkretnego mikrofonu na potrzeby rejestrowania. |
| [Model językowy](how-to-customize-language-model.md) | Utwórz niestandardowy model języka w celu usprawnienia transkrypcji słownictwa i gramatyki właściwych dla branży, takich jak Terminologia medyczna lub żargon IT. |
| [Model wymowy](how-to-customize-pronunciation.md) | Przy użyciu niestandardowego modelu wymowy można zdefiniować formularz fonetyczny i wyświetlić wyraz lub termin. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko, czego potrzebujesz do rozpoczęcia jest plikiem Wymowa — plik prosty txt. |

> [!NOTE]
> Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="migration-guides"></a>Przewodniki dotyczące migracji

> [!WARNING]
> Rozpoznawanie mowy Bing zostanie zlikwidowany 15 października 2019.

Jeśli Twoje aplikacje, narzędzia lub produkty używają rozpoznawanie mowy Bing interfejsów API lub Custom Speech, zostały utworzone przewodniki ułatwiające Migrowanie do usługi Speech Services.

* [Migrowanie z rozpoznawanie mowy Bing do usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migrowanie z Custom Speech do usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](https://aka.ms/csspeech)
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

* [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
* [Pobieranie zestawu Speech SDK](speech-sdk.md)
