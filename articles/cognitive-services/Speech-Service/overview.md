---
title: Co to są usługi mowy?
titleSuffix: Azure Cognitive Services
description: Usługi mowy to zjednoczenie zamiany mowy na tekst, zamiany tekstu na mowę i Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Można łatwo dodawać mowę dla aplikacji, narzędzi i urządzeń za pomocą zestawu Speech SDK, zestawu Speech Devices SDK lub interfejsów API REST. Dodaj funkcję mowy do istniejącej bot rozmowy, Konwertuj tekst na mowę w aplikacji do translacji lub transkrypcja duże ilości danych centrum połączeń.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: da88c6d0195236a6f93f81d3296e4624228125f2
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624262"
---
# <a name="what-are-the-speech-services"></a>Co to są usługi mowy?

Usługi mowy to zjednoczenie zamiany mowy na tekst i zamiany tekstu na mowę oraz Tłumaczenie mowy na pojedynczą subskrypcję platformy Azure. Można łatwo włączyć mowę dla aplikacji, narzędzi i urządzeń za pomocą [zestawu Speech SDK](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart)lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Usługi mowy zostały zastąpione interfejs API rozpoznawania mowy Bing, tłumaczenie mowy w usłudze Translator i Custom Speech. Aby uzyskać instrukcje dotyczące migracji *, zobacz przewodniki dotyczące > migracji* .

Te funkcje składają się na usługi Azure Speech Services. Skorzystaj z łączy w tej tabeli, aby dowiedzieć się więcej na temat typowych przypadków użycia dla każdej funkcji lub przejrzeć odwołanie do interfejsu API.

| Usługa | Cecha | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiana mowy na tekst](speech-to-text.md) | Zamiany mowy na tekst | Funkcja zamiany mowy na tekst przekształca strumienie audio do tekstu w czasie rzeczywistym, które mogą być używane przez aplikacje, narzędzia lub urządzenia. Używaj zamiany mowy na tekst za pomocą [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) , aby uzyskać intencje użytkownika z uzyskanego mowy i korzystać z poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transkrypcja partii](batch-transcription.md) | Transkrypcja partii umożliwia asynchroniczne transkrypcja mowy na tekst w dużych ilościach danych. Jest to usługa oparta na protokole REST, która używa tego samego punktu końcowego jako dostosowania i zarządzania modelami. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| | [Transkrypcja konwersacji](conversation-transcription-service.md) | Włącza rozpoznawanie mowy w czasie rzeczywistym, identyfikację głosu i diarization. Doskonale nadaje się do jego przepisywaniach spotkań osób z możliwością odróżniania głośników. | Tak | Nie |
| | [Tworzenie modeli Custom Speech](#customize-your-speech-experience) | Jeśli używasz funkcji zamiany mowy na tekst do rozpoznawania i transkrypcji w unikatowym środowisku, możesz tworzyć i uczenia niestandardowych modeli akustycznych, językowych i wymowych w celu obsługi szumów otoczenia lub słownictwa specyficznego dla branż. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę polega na konwertowaniu tekstu wejściowego na wymowę przez człowieka, który używa [języka SSML (Speech syntezing Language)](text-to-speech.md#speech-synthesis-markup-language-ssml). Wybieraj spośród standardowych głosów i głosów neuronowych (zobacz [Obsługa języka](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Twórz niestandardowe czcionki głosowe, które są unikatowe dla danej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia tłumaczenie mowy w czasie rzeczywistym na wiele języków w aplikacjach, narzędziach i urządzeniach. Ta usługa umożliwia tłumaczenie mowy na mowę i zamianę mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Wirtualne Asystenci z dźwiękiem](voice-first-virtual-assistants.md) | Wirtualne Asystenci z dźwiękiem | Niestandardowe Asystenci wirtualne korzystający z usług Azure Speech Services umożliwiają deweloperom tworzenie naturalnych, przypominających konwersacje interfejsów dla swoich aplikacji i środowisk. Kanał mowy liniowej w systemie bot Framework rozszerza te możliwości przez zapewnienie skoordynowanego, zorganizowanego punktu wejścia do zgodnej bot, która umożliwia korzystanie z głosu w programie, prowadzenie komunikacji głosowej z małymi opóźnieniami i wysoką niezawodnością. | [Tak](voice-first-virtual-assistants.md) | Nie |

## <a name="news-and-updates"></a>Wiadomości i aktualizacje

Dowiedz się, co nowego w usłudze Azure Speech Services.

* 2019 sierpnia
  * **Nowy samouczek**: [Dodawanie obsługi głosu do bota przy użyciu zestawu SDK rozpoznawania mowy i języka C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Dodano nowy styl mówiący, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles) `en-US-JessaNeural` dla głosu. 
* Czerwiec 2019
  * Wydano 1.6.0 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).
* 2019 — dokumentacja jest teraz dostępna na potrzeby [transkrypcji konwersacji](conversation-transcription-service.md), [transkrypcji centrum połączeń](call-center-transcription.md)i [wirtualnych asystentów głosowych](voice-first-virtual-assistants.md).
* 2019 maja
  * Wydano 1.5.1 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).
  * Wydano 1.5.0 zestawu mowy SDK. Aby zapoznać się z pełną listą aktualizacji, ulepszeń i znanych problemów, zobacz [Informacje o wersji](releasenotes.md).

## <a name="try-speech-services"></a>Wypróbuj usługi mowy

Oferujemy Przewodniki Szybki Start w najpopularniejszych językach programowania, z których każdy jest przeznaczony do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze Przewodniki Szybki Start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby poznać dodatkowe języki i platformy.

| Zamiana mowy na tekst (SDK) | Zamiana tekstu na mowę (SDK) | Tłumaczenie (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (system Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (przeglądarka)](quickstart-js-browser.md) | [C++Systemy](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++System](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (system Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++Systemy](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Funkcja zamiany mowy na tekst i zamiany tekstu na mowę również ma punkty końcowe REST i skojarzone Przewodniki Szybki Start.

Jeśli masz szansę na korzystanie z usług mowy, wypróbuj nasz samouczek, który uczy się, jak rozpoznać intencje z mowy przy użyciu zestawu Speech SDK i LUIS.

* [Samouczek: Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK i LUIS,C#](how-to-recognize-intents-from-speech-csharp.md)
* [Samouczek: Bote głosowe za pomocą zestawu Speech SDK,C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Samouczek: Tworzenie aplikacji do kolby do tłumaczenia tekstu, analizowanie tonacji i syntezowanie przetłumaczonego tekstu na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w witrynie GitHub dla każdej usługi Azure Speech Services. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi. Użyj tych linków, aby wyświetlić zestaw SDK i przykłady REST:

* [Przykłady zamiany mowy na tekst, zamiany tekstu na mowę i tłumaczenia mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Przykłady wirtualnego asystenta głosowego (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Dostosowywanie środowiska mowy

Usługi Azure Speech Services dobrze sprawdzają się w połączeniu z wbudowanymi modelami, ale warto dodatkowo dostosowywać i dostrajać środowisko dla danego produktu lub środowiska. Opcje dostosowania przestają być od modelu akustycznego na unikatowe czcionki głosowe dla marki. Po skompilowaniu modelu niestandardowego można go używać z dowolnymi usługami Azure Speech.

| Usługa rozpoznawania mowy | Modelowanie | Opis |
|----------------|-------|-------------|
| Zamiana mowy na tekst | [Model akustyczny](how-to-customize-acoustic-models.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi lub urządzeń używanych w określonych środowiskach, takich jak samochód lub piętro fabryki, z których każdy ma określone warunki rejestrowania. Przykłady obejmują akcentowanie mowy, konkretne zakłócenia w tle lub użycie określonego mikrofonu do nagrywania. |
| | [Model językowy](how-to-customize-language-model.md) | Utwórz niestandardowy model języka w celu usprawnienia transkrypcji słownictwa i gramatyki związanej z polami, takich jak Terminologia medyczna lub żargon IT. |
| | [Model wymowy](how-to-customize-pronunciation.md) | Przy użyciu niestandardowego modelu wymowy można zdefiniować formularz fonetyczny i wyświetlić wyraz lub termin. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko, czego potrzebujesz do rozpoczęcia jest plikiem Wymowa — plik prosty txt. |
| Zamiana tekstu na mowę | [Czcionka głosowa](how-to-customize-voice-font.md) | Niestandardowe czcionki głosowe umożliwiają utworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Rozpoczęcie pracy wymaga jedynie niewielkiej ilości danych. Im więcej danych zapewniasz, tym bardziej naturalna i przypominająca Twoja czcionka głosu będzie dźwiękowa. |

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw SDK urządzeń mowy](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Transkrypcja i dostosowywanie partii](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskaj bezpłatnie klucz subskrypcji usługi Speech Services](get-started.md)
