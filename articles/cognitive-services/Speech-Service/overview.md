---
title: Co to są usługi mowy?
titleSuffix: Azure Cognitive Services
description: Usługi mowy to zjednoczenie zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Dodaj mowę do aplikacji, narzędzi i urządzeń za pomocą zestawu Speech SDK, zestawu Speech Devices SDK lub interfejsów API REST.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/05/2019
ms.author: erhopf
ms.openlocfilehash: b1c32042fcb70ff16bb9d014a9f3546f4435e83f
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075775"
---
# <a name="what-are-the-speech-services"></a>Co to są usługi mowy?

Usługi mowy to zjednoczenie zamiany mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Można łatwo włączyć mowę dla aplikacji, narzędzi i urządzeń za pomocą [zestawu Speech SDK](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart)lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Usługi mowy zostały zastąpione interfejs API rozpoznawania mowy Bing, tłumaczenie mowy w usłudze Translator i Custom Speech. Aby uzyskać instrukcje dotyczące migracji _, zobacz przewodniki dotyczące > migracji_ .

Te funkcje składają się na usługi Azure Speech Services. Skorzystaj z łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji lub przejrzeć odwołanie do interfejsu API.

| Usługa | Funkcja | Opis | SDK | REST |
| ------- | ------- | ----------- | --- | ---- |
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiany mowy na tekst | Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Transkrypcja partii](batch-transcription.md) | Transkrypcja partii umożliwia asynchroniczne transkrypcja mowy na tekst w dużych ilościach danych. Jest to usługa oparta na protokole REST, która używa tego samego punktu końcowego jako dostosowania i zarządzania modelami. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
|         | [Transkrypcja konwersacji](conversation-transcription-service.md) | Włącza rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników. | Yes | Nie |
|         | [Tworzenie modeli Custom Speech](#customize-your-speech-experience) | Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na wymowę przez człowieka, który używa [języka SSML (Speech syntezing Language)](text-to-speech.md#speech-synthesis-markup-language-ssml). Wybieraj spośród standardowych głosów i głosów neuronowych (zobacz [Obsługa języka](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
|         | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe, które są unikatowe dla danej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia tłumaczenie mowy w czasie rzeczywistym na wiele języków w aplikacjach, narzędziach i urządzeniach. Ta usługa umożliwia tłumaczenie mowy na mowę i zamianę mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Asystenci głosu](voice-assistants.md) | Asystenci głosowi | Asystenci głosowani korzystający z usług Azure Speech Services umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla swoich aplikacji i środowisk. Usługa asystenta głosowego zapewnia szybką i niezawodną interakcję między urządzeniem a implementacją asystenta, która korzysta z kanału mowy linii Direct line lub zintegrowanego polecenia niestandardowego (wersja zapoznawcza) w celu ukończenia zadania. | [Tak](voice-assistants.md) | Nie |

## <a name="news-and-updates"></a>Wiadomości i aktualizacje

Dowiedz się, co nowego w usłudze Azure Speech Services.

- Wrzesień 2019 r.
  - Wydano 1.7.0 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).
- Sierpień 2019 r.
  - **Nowy samouczek**: [Voice Enable bot with Speech SDK, C# ](tutorial-voice-enable-your-bot-speech-sdk.md)
  - Dodano nowy styl mówiący, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)w przypadku głosu `en-US-JessaNeural`.
- Czerwiec 2019 r.
  - Wydano 1.6.0 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).
- 2019 — dokumentacja jest teraz dostępna na potrzeby [transkrypcji konwersacji](conversation-transcription-service.md), [transkrypcji centrum połączeń](call-center-transcription.md)i [asystentów głosowych](voice-assistants.md).
- Maj 2019 r.
  - Wydano 1.5.1 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).
  - Wydano 1.5.0 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).

## <a name="try-speech-services"></a>Wypróbuj usługi mowy

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze Przewodniki Szybki Start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby poznać dodatkowe języki i platformy.

| Zamiana mowy na tekst (SDK) | Zamiana tekstu na mowę (SDK) | Tłumaczenie (SDK) |
| -------------------- | -------------------- | ----------------- |
| [Rozpoznawanie mowy z pliku dźwiękowego](quickstarts/speech-to-text-from-file.md) | [Wytłumacz mowę na plik audio](quickstarts/text-to-speech-audio-file.md) | [Tłumaczenie zamiany mowy na tekst](quickstarts/translate-speech-to-text.md) |
| [Rozpoznawanie mowy przy użyciu mikrofonu](quickstarts/speech-to-text-from-microphone.md) | [Funkcja syntezy mowy z prelegentem](quickstarts/text-to-speech.md) | [Tłumaczenie mowy na wiele języków docelowych](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Rozpoznawanie mowy przechowywanej w usłudze BLOB Storage](quickstarts/from-blob.md) | [Synteza asynchroniczna dla długich postaci audio](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Tłumaczenie zamiany mowy na mowę](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> Funkcja zamiany mowy na tekst i zamiany tekstu na mowę również ma punkty końcowe REST i skojarzone Przewodniki Szybki Start.

Jeśli masz szansę na korzystanie z usług mowy, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

- [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
- [Samouczek: Voice enable the bot with Speech SDKC#](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Samouczek: Tworzenie aplikacji kolby do tłumaczenia tekstu, analizowanie tonacji i syntezowanie przetłumaczonego tekstu na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w witrynie GitHub dla każdej usługi Azure Speech Services. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

- [Przykłady zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Przykłady asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Dostosowywanie środowiska mowy

Usługi Azure Speech Services dobrze sprawdzają się w połączeniu z wbudowanymi modelami, ale warto dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki. Po skompilowaniu modelu niestandardowego można go używać z dowolnymi usługami Azure Speech.

| Usługa rozpoznawania mowy | Platforma | Opis |
| -------------- | -------- | ----------- |
| Zamiana mowy na tekst | [Custom Speech](https://aka.ms/customspeech) | Dostosuj modele rozpoznawania mowy do Twoich potrzeb i dostępnych danych. Pokonaj bariery rozpoznawania mowy, takie jak styl wypowiedzi, słownictwo i hałas w tle. |
| Zamiana tekstu na mowę | [Niestandardowy głos](https://aka.ms/customvoice) | Utwórz rozpoznawalny, unikatowy głos dla aplikacji zamieniających tekst na mowę, które korzystają z Twoich dostępnych danych mowy. Można dokładniej dostosować dane wyjściowe głosu przez dostosowanie zestawu parametrów głosowych. |

## <a name="reference-docs"></a>Dokumentacja

- [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
- [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
- [Interfejs API REST: zamiana mowy na tekst](rest-speech-to-text.md)
- [Interfejs API REST: zamiana tekstu na mowę](rest-text-to-speech.md)
- [Interfejs API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](get-started.md)
