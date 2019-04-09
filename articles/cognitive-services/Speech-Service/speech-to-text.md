---
title: Mowy na tekst za pomocą usług przetwarzania mowy platformy Azure
titleSuffix: Azure Cognitive Services
description: Mowy na tekst z usług przetwarzania mowy platformy Azure, znany także jako zamiany mowy na tekst, umożliwia w czasie rzeczywistym transkrypcji strumieni audio na tekst, którą może wykorzystać aplikacje, narzędzia lub urządzeń, wyświetlania i podejmij działanie jako dane wejściowe polecenia. Ta usługa jest obsługiwana w tej samej technologii rozpoznawania firmy Microsoft korzysta z produktów Cortana i pakietu Office, która bezproblemowo współdziała z przesunięcia i zamiany tekstu na mowę.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9e6bc1264e668ba5c6593ce36e721f54e685c391
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59008496"
---
# <a name="what-is-speech-to-text"></a>Co to jest mowy na tekst?

Mowy na tekst z usług przetwarzania mowy platformy Azure, znany także jako zamiany mowy na tekst, umożliwia w czasie rzeczywistym transkrypcji strumieni audio na tekst, którą może wykorzystać aplikacje, narzędzia lub urządzeń, wyświetlania i podejmij działanie jako dane wejściowe polecenia. Ta usługa jest obsługiwana w tej samej technologii rozpoznawania firmy Microsoft korzysta z produktów Cortana i pakietu Office, która bezproblemowo współdziała z przesunięcia i zamiany tekstu na mowę.  Aby uzyskać pełną listę dostępnych języków mowy na tekst, zobacz [obsługiwane języki](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#speech-to-text).

Domyślnie usługa mowy na tekst używa modelu językowego uniwersalnych. Ten model został uczony przy użyciu danych należącymi do firmy Microsoft i jest wdrożona w chmurze. Jest optymalna do konwersacji i dyktowanie scenariuszy. Korzystania z funkcji rozpoznawania mowy na tekst do rozpoznawania i transkrypcji w środowisku unikatowe można tworzyć i szkolenie niestandardowe modele akustyczne, języka i Wymowa hałasu otoczenia adres lub słownictwa branżowych. 

Można łatwo przechwytywania dźwięku z mikrofonu, odczytywania ze strumienia lub dostępu do plików dźwiękowych z magazynu przy użyciu zestawu SDK rozpoznawania mowy i interfejsów API REST. Zestaw SDK rozpoznawania mowy obsługuje WAV/PCM 16-bitowy, 16 kHz, dźwięk jednego kanału rozpoznawania mowy. Dodatkowe formaty audio są obsługiwane przy użyciu [punktu końcowego REST mowy na tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) lub [transkrypcji, usługa batch](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Podstawowe funkcje

Oto funkcje dostępne za pośrednictwem mowy zestawu SDK i interfejsy API REST:

| Przypadek użycia | SDK | REST |
|----------|-----|------|
| Transkrypcja wypowiedzi krótki (< 15 sekund). Obsługuje tylko wynik końcowy transkrypcji. | Yes | Yes |
| Ciągłe transkrypcji długie wypowiedzi i przesyłania strumieniowego audio (> 15 sekund). Obsługuje wyniki tymczasowe i końcowe transkrypcji. | Yes | Nie |
| Dziedziczyć intencji wyniki rozpoznawania [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Yes | Brak\* |
| Asynchronicznie partii transkrypcji audio plików. | Nie | Tak\** |
| Tworzenie i zarządzanie modelami mowy. | Nie | Tak\** |
| Tworzenie i zarządzanie wdrożeniami modelu niestandardowego. | Nie | Tak\** |
| Utwórz testy dokładności do mierzenia dokładności modelu odniesienia, a modele niestandardowe. | Nie | Tak\** |
| Zarządzaj subskrypcjami. | Nie | Tak\** |

\* *Usługa LUIS intencje i podmioty mogą być uzyskane przy użyciu oddzielnej subskrypcji usługi LUIS. Z tą subskrypcją zestawu SDK można wywołać usługi LUIS i jednostki i wyniki konwersji. Za pomocą interfejsu API REST, można wywołać usługi LUIS samodzielnie do wyprowadzenia intencje i podmioty ze swoją subskrypcją usługi LUIS.*

\** *Te usługi są dostępne przy użyciu punktu końcowego cris.ai. Zobacz [Swagger odwołania](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Rozpoczynanie pracy z usługą mowy na tekst

Firma Microsoft oferuje Przewodniki Szybki Start w najbardziej popularnych języków programowania, każde jest zaprojektowane do uruchamiania kodu w mniej niż 10 minut. Poniższa tabela zawiera listę wszystkich SDK mowy przewodników Szybki Start, uporządkowane według języka.

| Szybki start | Platforma | Dokumentacja interfejsu API |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET framework](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, PLATFORMY UNIWERSALNEJ SYSTEMU WINDOWS](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Browse](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Browse](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| [JavaScript, przeglądarki](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Przeglądarka, Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [JavaScript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tv) |
| [Obiektowy C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Browse](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Browse](https://aka.ms/AA434tr)  |

Jeśli wolisz korzystać z usługi REST mowy na tekst, zobacz [interfejsów API REST](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Samouczki i przykładowy kod

Po został mieli Państwo możliwość korzystania z usług rozpoznawania mowy, wypróbuj Nasz samouczek pokazujący, jak rozpoznać intencje z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUIS.

* [Samouczek: Rozpoznawanie intencji z wypowiedzi przy użyciu zestawu SDK rozpoznawania mowy i LUISC#](how-to-recognize-intents-from-speech-csharp.md)

Przykładowy kod dla zestawu SDK rozpoznawania mowy jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie dźwięku z pliku lub strumienia, ciągłe i pojedynczego zrzutu ręcznego i Praca z modelami niestandardowych.

* [Przykłady mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Przykłady transkrypcji dla usługi Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Dostosowywanie

Oprócz modelu Universal używane przez usługi rozpoznawania mowy możesz utworzyć niestandardowe modele akustyczne, języka i Wymowa określonego środowiska. Poniżej przedstawiono listę opcji dostosowywania:

| Modelowanie | Opis |
|-------|-------------|
| [Model akustyczny](how-to-customize-acoustic-models.md) | Tworzenie niestandardowy model akustyczny jest przydatne, jeśli w Twojej aplikacji, narzędzia lub urządzeń są używane w określonym środowisku, takich jak w samochodu lub fabrykę przy użyciu rejestrowania określonych warunków. Przykłady obejmują akcentowaną mowę, specyficzne szumy w tle lub użycie konkretnego mikrofonu na potrzeby rejestrowania. |
| [Model języka](how-to-customize-language-model.md) | Utwórz model języka niestandardowego usprawniających transkrypcji słownika specyficzne dla branży i gramatyki, takie jak terminologia medycznych lub żargon IT. |
| [Wymowa modelu](how-to-customize-pronunciation.md) | Za pomocą modelu niestandardowego Wymowa można zdefiniować fonetycznych formularza i wyświetlanie słowa lub terminy. Jest to przydatne do obsługi warunki niestandardowe, takie jak nazwy produktów lub akronimów. Wszystko, czego potrzebujesz do rozpoczęcia jest plikiem Wymowa — plik prosty txt. |

> [!NOTE]
> Opcje dostosowywania zależy od języka/ustawienia regionalne (zobacz [obsługiwane języki](supported-languages.md)).

## <a name="migration-guides"></a>Przewodniki dotyczące migracji

> [!WARNING]
> Rozpoznawanie mowy Bing zostanie zamknięty 15 października 2019 r.

Twoje aplikacje, narzędzia lub produkty korzystania z interfejsów API rozpoznawania mowy Bing lub Custom Speech, utworzyliśmy przewodniki ułatwiające migrację do usług przetwarzania mowy.

* [Migracja z rozpoznawania mowy Bing do usług przetwarzania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Migracja z Custom Speech do usług przetwarzania mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Dokumentacja

* [Zestaw SDK rozpoznawania mowy](speech-sdk-reference.md)
* [Zestaw Speech Devices SDK](speech-devices-sdk.md)
* [INTERFEJS API REST: Zamiana mowy na tekst](rest-speech-to-text.md)
* [INTERFEJS API REST: Zamiana tekstu na mowę](rest-text-to-speech.md)
* [INTERFEJS API REST: Dostosowywanie i transkrypcji usługi Batch](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Kolejne kroki

* [Bezpłatnie Uzyskaj klucz subskrypcji usług przetwarzania mowy](get-started.md)
* [Pobierz zestaw SDK funkcji rozpoznawania mowy](speech-sdk.md)
