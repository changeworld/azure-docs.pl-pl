---
title: Co to są usługi mowy platformy Azure?
titleSuffix: Azure Cognitive Services
description: Usługi mowy platformy Azure są ujednolicenie mowy na tekst, zamiany tekstu na mowę i tłumaczenie mowy w ramach jednej subskrypcji platformy Azure. To ułatwia dodawanie funkcji rozpoznawania mowy, aplikacji, narzędzi i urządzeń za pomocą zestawu SDK rozpoznawania mowy, zestawu Speech Devices SDK lub interfejsów API REST. Dodawanie funkcji rozpoznawania mowy do istniejących czatbot, Konwertuj tekst na mowę w aplikacji tłumaczenia lub transkrypcja dużych ilości danych Centrum połączeń.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d9664ac9fb72a5f094674856a20230199270f01d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603146"
---
# <a name="what-are-the-speech-services"></a>Co to są usługi mowy?

Usługi mowy platformy Azure są ujednolicenie mowy na tekst, zamiany tekstu na mowę i tłumaczenie mowy do pojedynczej subskrypcji platformy Azure. Łatwo na mowę w celu włączenia aplikacji, narzędzi i urządzeń za pomocą [zestaw SDK rozpoznawania mowy](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart), lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Zastąpiono usług przetwarzania mowy, interfejs API rozpoznawania mowy Bing, mowy usługi Translator i Custom Speech. Zobacz *prowadzi instrukcje > migracji* instrukcje migracji.

Te funkcje tworzą usługi mowy platformy Azure. Użyj linków w tej tabeli, aby dowiedzieć się więcej o typowe przypadki użycia dla każdej funkcji lub Przeglądaj, dokumentacja interfejsu API.

| Usługa | Cecha | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiany mowy na tekst](speech-to-text.md) | Zamiany mowy na tekst | Zamiany mowy na tekst transcribes strumieni audio na tekst w czasie rzeczywistym, które aplikacje, narzędzia lub urządzenia używają lub wyświetlić. Użyj mowy na tekst za pomocą [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) wyprowadzenia użytkownika intencji z uzyskanego mowy i ustawy o poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transkrypcja usługi Batch](batch-transcription.md) | Transkrypcja Batch umożliwia asynchroniczne przekształcania mowy na tekst z dużych ilości danych. To jest usługa oparty na protokole REST, która używa tego samego punktu końcowego jako dostosowywanie i zarządzanie modelami w usłudze. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| | [Transkrypcja konwersacji](conversation-transcription-service.md) | Umożliwia rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Jest to doskonała dla przepisywania spotkań w osoby z możliwością odróżnić głośników. | Tak | Nie |
| | [Tworzenie niestandardowych modeli mowy](#customize-your-speech-experience) | Korzystania z funkcji rozpoznawania mowy na tekst do rozpoznawania i transkrypcji w środowisku unikatowe można tworzyć i szkolenie niestandardowe modele akustyczne, języka i Wymowa hałasu otoczenia adres lub słownictwa branżowych. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę konwertuje tekst wejściowy syntezatora mowy przypominającej ludzką za pomocą [mowy syntezy Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Wybierając spośród standardowa głosów i głosów neuronowych (zobacz [języki](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Tworzenie niestandardowych głosów](#customize-your-speech-experience) | Czcionki głosowe niestandardowego należy utworzyć unikatowe dla Twojej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia w czasie rzeczywistym, wielu języków tłumaczenia mowy do aplikacji, narzędzi i urządzeń. Usługa do tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |
| [Wirtualnych asystentów głosowych pierwszy](voice-first-virtual-assistants.md) | Wirtualnych asystentów głosowych pierwszy | Niestandardowe asystentów wirtualnego przy użyciu usług przetwarzania mowy platformy Azure zwiększenie możliwości dostępnych dla deweloperów do tworzenia naturalnych, przypominającej ludzką interfejsy konwersacyjne na potrzeby swoich aplikacji i środowisk. Kanał bezpośredniego wiersza mowy platformy Bot Framework rozszerza te możliwości, zapewniając punktem wejścia skoordynowane, zorganizowane do zgodnych bot, który umożliwia głosu w głosu się interakcji z małymi opóźnieniami i wysoką niezawodnością. | [Tak](voice-first-virtual-assistants.md) | Nie |

## <a name="news-and-updates"></a>Nowości i aktualizacje

Poznaj nowe funkcje usług mowy platformy Azure.

* 2019 czerwca
    * Wydana mowy SDK 1.6.0. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
* Maja 2019 - dokumentacji jest teraz dostępna dla [transkrypcji konwersacji](conversation-transcription-service.md), [wywołać transkrypcji Centrum](call-center-transcription.md), i [wirtualnego asystentów głosowych pierwszy](voice-first-virtual-assistants.md).
* Maja 2019 r
    * Wydana mowy SDK 1.5.1. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
    * Wydana mowy 1.5.0 zestawu SDK. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
* Kwietnia 2019 - wydane mowy SDK 1.4.0 z obsługą tekstu na mowę (Beta) dla języka C++, C#i Java w systemach Windows i Linux. Ponadto zestaw SDK obsługuje teraz formaty audio MP3 i dziele/Ogg dla języka C++ i C# w systemie Linux. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
* Marca 2019 - nowy punkt końcowy zamiany tekstu na mowę (TTS), która zwraca pełną listę dostępnych głosów w określonym regionie jest teraz dostępna. Ponadto nowe regiony są teraz obsługiwane dla TTS. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API zamiany tekstu na mowę (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Wypróbuj usługi mowy

Firma Microsoft oferuje Przewodniki Szybki Start w najbardziej popularnych języków programowania, każde jest zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze przewodników Szybki Start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby zapoznać się z dodatkowych języków i platform.

| Mowy na tekst (SDK) | Zamiana tekstu na mowę (SDK) | Tłumaczenia (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (przeglądarka)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Zamiany mowy na tekst i zamiany tekstu na mowę również mieć punkty końcowe REST i skojarzone przewodników Szybki Start.

Po został mieli Państwo możliwość korzystania z usług rozpoznawania mowy, wypróbuj Nasz samouczek pokazujący, jak rozpoznać intencje z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUIS.

* [Samouczek: Rozpoznawanie intencji z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUISC#](how-to-recognize-intents-from-speech-csharp.md)
* [Samouczek: Tworzenie aplikacji Flask tłumaczenie tekstu, analizowanie tonacji i syntetyzowania przetłumaczony tekst na mowę, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Pobieranie przykładowego kodu

Przykładowy kod jest dostępny w witrynie GitHub dla każdej z usług przetwarzania mowy platformy Azure. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie dźwięku z pliku lub strumienia, ciągłe i pojedynczego zrzutu ręcznego i Praca z modelami niestandardowych. Użyj poniższych linków, aby wyświetlić przykłady zestawu SDK i REST:

* [Przykłady tłumaczenia mowy na tekst, zamiany tekstu na mowę i mowy (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji dla usługi Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Przykłady zamiany tekstu na mowę (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>Dostosowywanie doświadczenia z mowy

Usługi mowy platformy Azure działa dobrze z wbudowanych modeli, jednak warto dalsze dostosowywanie i dostrojenie środowiska dla danego produktu lub środowiska. Dostosowywanie opcje z zakresu od model akustyczny dostroić czcionki głosowe unikatowy dla Twojej marki. Po utworzeniu modelu niestandardowego służy za pomocą dowolnej z usług przetwarzania mowy platformy Azure.

| Usługa rozpoznawania mowy | Modelowanie | Opis |
|----------------|-------|-------------|
| Zamiana mowy na tekst | [Model akustyczny](how-to-customize-acoustic-models.md) | Utwórz niestandardowy model akustyczny dla aplikacji, narzędzi, lub urządzenia, które są używane w szczególności środowiskach, takich jak jadąca samochodem, lub w fabrykach, każdy z rejestrowania określonych warunków. Przykłady obejmują akcentowanych mowy, określonych szum lub przy użyciu określonych mikrofonu rejestrowania. |
| | [Model językowy](how-to-customize-language-model.md) | Utwórz model języka niestandardowego usprawniających transkrypcji słownika specyficzne dla pola i gramatyki, takie jak terminologia medycznych lub żargon IT. |
| | [Model wymowy](how-to-customize-pronunciation.md) | Za pomocą modelu niestandardowego Wymowa można zdefiniować fonetycznych formularza i wyświetlanie słowa lub terminy. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko, czego potrzebujesz do rozpoczęcia jest plikiem Wymowa — plik prosty txt. |
| Zamiana tekstu na mowę | [Czcionka głosowa](how-to-customize-voice-font.md) | Czcionki głosowe niestandardowe umożliwiają tworzenie mówiącą, jeden z rodzajem głosu dla Twojej marki. Trwa tylko niewielką ilość danych, aby rozpocząć pracę. Im więcej danych, możesz podać więcej naturalnego i przypominającej ludzką dźwiękowe czcionki głosowe. |

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiany mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
