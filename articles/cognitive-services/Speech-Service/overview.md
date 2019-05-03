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
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2e82e033e56af10bd8e676570e9409941753eb38
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020982"
---
# <a name="what-are-the-speech-services"></a>Co to są usługi mowy?

Usługi mowy platformy Azure są ujednolicenie mowy na tekst, zamiany tekstu na mowę i tłumaczenie mowy do pojedynczej subskrypcji platformy Azure. Łatwo na mowę w celu włączenia aplikacji, narzędzi i urządzeń za pomocą [zestaw SDK rozpoznawania mowy](speech-sdk-reference.md), [zestawu Speech Devices SDK](https://aka.ms/sdsdk-quickstart), lub [interfejsów API REST](rest-apis.md).

> [!IMPORTANT]
> Zastąpiono usług przetwarzania mowy, interfejs API rozpoznawania mowy Bing, mowy usługi Translator i Custom Speech. Zobacz *prowadzi instrukcje > migracji* instrukcje migracji.

Te funkcje tworzą usługi mowy platformy Azure. Użyj linków w tej tabeli, aby dowiedzieć się więcej o typowe przypadki użycia dla każdej funkcji lub Przeglądaj, dokumentacja interfejsu API.

| Usługa | Cecha | Opis | SDK | REST |
|---------|---------|-------------|-----|------|
| [Zamiany mowy na tekst](speech-to-text.md) | Zamiana mowy na tekst | Zamiany mowy na tekst transcribes strumieni audio na tekst w czasie rzeczywistym, które aplikacje, narzędzia lub urządzenia używają lub wyświetlić. Użyj mowy na tekst za pomocą [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) wyprowadzenia użytkownika intencji z uzyskanego mowy i ustawy o poleceń głosowych. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Transkrypcja usługi Batch](batch-transcription.md) | Transkrypcja Batch umożliwia asynchroniczne przekształcania mowy na tekst z dużych ilości danych. To jest usługa oparty na protokole REST, która używa tego samego punktu końcowego jako dostosowywanie i zarządzanie modelami w usłudze. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| | [Usługa transkrypcji konwersacji](conversation-transcription-service.md) | Umożliwia rozpoznawanie mowy w czasie rzeczywistym, identyfikacja osoby mówiącej i diarization. Jest to doskonała dla przepisywania spotkań w osoby z możliwością odróżnić głośników. | Yes | Nie |
| | [Dostosowywanie](#customize-your-speech-experience) | Korzystania z funkcji rozpoznawania mowy na tekst do rozpoznawania i transkrypcji w środowisku unikatowe można tworzyć i szkolenie niestandardowe modele akustyczne, języka i Wymowa hałasu otoczenia adres lub słownictwa branżowych. | Nie | [Tak](https://westus.cris.ai/swagger/ui/index) |
| [Zamiana tekstu na mowę](text-to-speech.md) | Zamiana tekstu na mowę | Zamiana tekstu na mowę konwertuje tekst wejściowy przypominającej ludzką syntezatora mowy. Wybierając spośród standardowa głosów i głosów neuronowych (zobacz [języki](language-support.md)). | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Dostosowywanie](#customize-your-speech-experience) | Czcionki głosowe niestandardowego należy utworzyć unikatowe dla Twojej marki lub produktu. | Nie | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Tłumaczenie mowy](speech-translation.md) | Tłumaczenie mowy | Tłumaczenie mowy umożliwia w czasie rzeczywistym, wielu języków tłumaczenia mowy do aplikacji, narzędzi i urządzeń. Usługa do tłumaczenia mowy do rozpoznawania mowy i rozpoznawania mowy na tekst. | [Tak](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nie |

## <a name="news-and-updates"></a>Nowości i aktualizacje

Poznaj nowe funkcje usług mowy platformy Azure.

* Maja 2019 - wydane mowy 1.5.0 zestawu SDK. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
* Kwietnia 2019 - wydane mowy SDK 1.4.0 z obsługą tekstu na mowę (Beta) dla języka C++, C#i Java w systemach Windows i Linux. Ponadto zestaw SDK obsługuje teraz formaty audio MP3 i dziele/Ogg dla języka C++ i C# w systemie Linux. Aby uzyskać pełną listę aktualizacji, ulepszeń i znanych problemów, zobacz [informacje o wersji](releasenotes.md).
* Marca 2019 - nowy punkt końcowy zamiany tekstu na mowę (TTS), która zwraca pełną listę dostępnych głosów w określonym regionie jest teraz dostępna. Ponadto nowe regiony są teraz obsługiwane dla TTS. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API zamiany tekstu na mowę (REST)](rest-text-to-speech.md).
* Lutego 2019 - wydane mowy SDK 1.3.0 dzięki obsłudze [aparatu Unity (wersja beta)](quickstart-csharp-unity.md). Dodano obsługę `AudioInput` klasy, która pozwala na dokonanie wyboru przesyłania strumieniowego źródła audio. Aby uzyskać pełną listę znanych problemów i rozszerzeń, zobacz [informacje o wersji](releasenotes.md).
* Grudnia 2018 - wydane mowy SDK 1.2.0 dzięki obsłudze [Python](quickstart-python.md) i [Node.js](quickstart-js-node.md), jak Ubuntu 18.04 LTS. Aby uzyskać więcej informacji, zobacz [informacje o wersji](releasenotes.md).

## <a name="try-speech-services"></a>Wypróbuj usługi mowy

Firma Microsoft oferuje Przewodniki Szybki Start w najbardziej popularnych języków programowania, każde jest zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Ta tabela zawiera najpopularniejsze przewodników Szybki Start dla każdej funkcji. Użyj nawigacji po lewej stronie, aby zapoznać się z dodatkowych języków i platform.

| Mowy na tekst (SDK) | Tłumaczenia (SDK) | Zamiana tekstu na mowę (REST) | Zamiana tekstu na mowę (SDK) |
|-------------------|-------------------|-----------------------|-----------------------|
| [C#, .NET core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) |
| [JavaScript (przeglądarka)](quickstart-js-browser.md) | [C#, .NET core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Po został mieli Państwo możliwość korzystania z usług rozpoznawania mowy, wypróbuj Nasz samouczek pokazujący, jak rozpoznać intencje z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUIS.

* [Samouczek: Rozpoznawanie intencji z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUISC#](how-to-recognize-intents-from-speech-csharp.md)

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
