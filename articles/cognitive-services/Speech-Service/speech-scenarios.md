---
title: Scenariusze mowy usług Azure Cognitive Services
description: Scenariusze
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 5179f0a81ea67c1b20e9cbf77e18595e0b4bafa4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495285"
---
# <a name="speech-scenarios"></a>Scenariusze z zastosowaniem mowy

Istnieje wiele scenariuszy, które mogą większe możliwości przy użyciu technologii rozpoznawania mowy. Firma Microsoft jest analiza kilka najbardziej typowych i zwracają uwagę na odpowiednie funkcje w dokumentacji. Dla większości zawartości [SDK](speech-sdk.md) stanowi podstawę korzystania z tych scenariuszy.

W tym artykule omówiono strony jak:
> [!div class="checklist"]
> * Twórz aplikacje głosu wyzwolone
> * Transkrypcja rozmów audio Centrum wywołania
> * Boty głosu

## <a name="voice-triggered-apps"></a>Głos wyzwalane aplikacji

Wielu użytkowników chcesz włączyć wprowadzania danych głosowych w swoich aplikacjach. Wprowadzania danych głosowych jest doskonały sposób, aby zapewnić aplikacji elastyczny i korzystania z niego ręce bezpłatna (na przykład w samochodzie) lub przyspieszenia różne zadania, takie jak pytanie kierunkach informacje, czy też o pogodzie. 

### <a name="voice-triggered-apps-with-baseline-models"></a>Głos wyzwalane aplikacji przy użyciu modeli podstawowych

Jeśli aplikacja ma służyć oglądu przez ogół w środowiskach, w którym hałas w tle nie jest zbyt duża, sposób najłatwiejszych i najszybszych obecnie w tym celu po prostu odbywać się pobieranie naszych [zestaw SDK rozpoznawania mowy](speech-sdk.md) i zgodnie z odpowiednią [ Przykłady](quickstart-csharp-dotnet-windows.md). Obsługiwane przez zestaw SDK usługi [klucz subskrypcji platformy Azure](https://azure.microsoft.com/try/cognitive-services/) umożliwia deweloperom i przekaż plik audio do linii bazowej modeli rozpoznawania mowy, które stanowią podstawę Cortana i Skype. Modele są najnowocześniejsze oraz są używane przez wyżej produktów. Możesz rozpocząć pracę w ciągu kilku minut.

### <a name="voice-triggered-apps-with-custom-models"></a>Głos wyzwalane aplikacji za pomocą niestandardowych modeli

Jeśli aplikacja adresy określonej domeny, (chemia powiedzieć, związane z biologią lub specjalnymi potrzebami diecie), a następnie warto wziąć pod uwagę w celu dostosowania [modelu językowego](how-to-customize-language-model.md). Dostosowanie modelu języka nauczy dekodera o najbardziej typowych fraz i słów używanych przez aplikację. Dekoder będzie także dokładniej głosowych danych wejściowych z modelu języka niestandardowego dla określonej domeny, a nie w modelu odniesienia. Podobnie jeśli szumu tła, gdzie aplikacja ma być używany jest wyraźną możesz dostosować model akustyczny. Zapoznaj się z dokumentacją w pozostałych przypadkach, w którym [dostosowania języka](how-to-customize-language-model.md) i [akustyczna](how-to-customize-acoustic-models.md) Podaj wartość, a następnie odwiedź nasze [dostosowanie portalu](https://customspeech.ai) dla kick-Starting środowisko tworzenia modelu. Podobnie jak modeli podstawowych, niestandardowe modele są wywoływane za pośrednictwem naszego [zestaw SDK rozpoznawania mowy](speech-sdk.md) i zgodnie z odpowiednią [przykłady](quickstart-csharp-dotnet-windows.md).

## <a name="transcribe-call-center-audio-calls"></a>Transkrypcja rozmów audio Centrum wywołania

Biurem obsługi gromadzenie dużych ilości audio. Ukryte wewnątrz tych wartości polega na pliki audio, który można uzyskać za pośrednictwem transkrypcji. Czas trwania wywołania tonacji, zadowolenia klienta i ogólne wartość wywołanie do obiektu wywołującego może być rozpoznana, uzyskując zapisy rozmów.

Punktem wyjścia najlepiej jest [transkrypcji interfejsu API partii](batch-transcription.md) wraz z powiązanych [przykładowe](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

Musisz najpierw uzyskać [klucz subskrypcji platformy Azure](https://azure.microsoft.com/try/cognitive-services/) i należy zapoznać się z [dokumentacji]([Batch transcription API](batch-transcription.md)).

### <a name="transcribe-call-center-audio-calls-with-baseline-models"></a>Transkrypcja wywołanie Centrum wywołań dźwięku z modelami linii bazowej

Decyzja, który musi zapewnić to, czy będziesz używać modeli wewnętrzny punkt odniesienia do przeprowadzenia transkrypcji, dostosować język model akustyczny i/lub. Korzystać z modeli bazowego interfejsu API wymaga tylko klucz interfejsu API. Wewnętrznie interfejsu API wywołania najlepszy model danych i dostosowania.

### <a name="transcribe-call-center-audio-calls-with-custom-models"></a>Transkrypcja wywołań audio wywołania z Centrum w przypadku niestandardowych modeli

Jeśli planujesz użyć modelu niestandardowego, będzie konieczne identyfikator modelu wraz z kluczem interfejsu API. Identyfikator modelu jest uzyskiwana z [dostosowanie portalu](https://customspeech.ai). Nie jest identyfikator punktu końcowego, który można znaleźć w widoku "Szczegóły punktu końcowego", ale identyfikator modelu, który można pobrać po kliknięciu "Details" tego modelu.

## <a name="voice-bots"></a>Boty głosu

Deweloperzy zwiększać możliwości swoich aplikacji z danymi wyjściowymi głosu. Usługi mowy może mowy synthetize szereg [języków](supported-languages.md) i zapewnia [punktów końcowych](rest-apis.md) do uzyskiwania dostępu i dodając tej możliwości do swojej aplikacji.

Ponadto dla użytkowników, które chcesz dodać więcej osobowość i unikatowości do ich botów usługa mowy umożliwia deweloperom dostosowywać unikalna czcionka głosowa. Podobnie jak dostosowywanie rozpoznawania mowy, czcionki głosowe modeli wymaga danych użytkownika. Deweloperzy mają przekazywania tych danych w naszym [głosu dostosowanie portalu](https://customspeech.ai) i zacznij tworzyć swoją markę unikatowy głosu dla bota. Szczegóły opisano [tutaj](how-to-text-to-speech.md) , jak również [— często zadawane pytania](faq-text-to-speech.md) stron 

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
* [Uruchom przy użyciu zestawu SDK funkcji rozpoznawania mowy](speech-sdk.md)
