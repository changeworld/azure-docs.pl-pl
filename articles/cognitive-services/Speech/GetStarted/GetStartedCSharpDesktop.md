---
title: Wprowadzenie do interfejsu API rozpoznawania mowy Bing przy użyciu biblioteki pulpitu języka C# | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Tworzenie podstawowych aplikacji Windows, które umożliwia konwertowanie dźwięku mówionego na tekst interfejsu API rozpoznawania mowy Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5f3b70a2dd9816210ed61280be38504a3980d205
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515341"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Szybki start: Używanie rozpoznawania mowy Bing interfejsu API w języku C&#35; dla platformy .NET w Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Ta strona zawiera instrukcje tworzenia podstawowych aplikacji Windows, która korzysta z interfejsu API rozpoznawania mowy, Konwertuj dźwięk mówiony na tekst. Za pomocą biblioteki klienckiej umożliwia przesyłanie strumieniowe w czasie rzeczywistym, który oznacza, że gdy aplikacja kliencka wysyła audio z usługą, równocześnie i asynchronicznie odbiera częściowe wyniki rozpoznawania ponownie.

Deweloperzy, którzy chcą korzystać z usługi rozpoznawania mowy z aplikacji działających na dowolnym urządzeniu można używać biblioteki pulpitu języka C#. Korzystanie z biblioteki, zainstaluj [pakietu NuGet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) platformy 32-bitowe i [pakietu NuGet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) dla 64-bitowej platformie. Aby uzyskać biblioteki klienckiej, dokumentacja interfejsu API, zobacz [biblioteki pulpitu Microsoft Speech C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Poniżej opisano sposób instalowania, tworzenie i uruchamianie języka C# przykładowej aplikacji przy użyciu biblioteki pulpitu języka C#.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany dla systemu Windows 8 i nowsze oraz .NET Framework 4.5 + przy użyciu [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobieranie przykładowej aplikacji

Sklonuj przykład z [standardowej biblioteki pulpitu mowy C#](https://github.com/microsoft/cognitive-speech-stt-windows) repozytorium.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API rozpoznawania mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Przed skorzystaniem z biblioteki klienta mowy, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Podana C# pulpitu przykładowej aplikacji Wklej swój klucz subskrypcji w polu tekstowym, po uruchomieniu przykładu. Aby uzyskać więcej informacji, zobacz [uruchamianie przykładowej aplikacji](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Zainstaluj przykładową aplikację.

1. Uruchom program Visual Studio 2015 i wybierz **pliku** > **Otwórz** > **projekt/rozwiązanie**.

2. Przejdź do folderu, w której zapisano pobranych plików interfejsu API rozpoznawania mowy. Wybierz **mowy** > **Windows**, a następnie wybierz folder systemu WP próbki.

3. Kliknij dwukrotnie, można otworzyć pliku programu Visual Studio 2015 rozwiązania (.sln) o nazwie Samples.sln-SpeechToText-WPF. Rozwiązanie zostanie otwarty w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

1. Jeśli chcesz używać *uznanie z zamiarem*, najpierw musisz zarejestrować się w celu [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Następnie użyj adresu URL punktu końcowego z aplikacją usługi LUIS, aby ustawić wartość klucza `LuisEndpointUrl` w pliku app.config, w folderze samples/SpeechRecognitionServiceExample. Aby uzyskać więcej informacji na adres URL punktu końcowego aplikacji usługi LUIS, zobacz [opublikować aplikację](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Zastąp `&` znaku w adresie URL punktu końcowego usługi LUIS z `&amp;` aby upewnić się, że adres URL jest poprawnie interpretowany przez XML parser.

2. Naciśnij klawisze Ctrl + Shift + B, lub wybierz **kompilacji** menu wstążki. Następnie wybierz pozycję **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji, naciśnij klawisz F5 lub wybierz **Start** menu wstążki do uruchomienia przykładu.

2. Przejdź do **Project Oxford mowy na tekst przykładowy** okna. Wklej swój klucz subskrypcji do **Wklej swój klucz subskrypcji, aby rozpocząć** pola tekstowego, jak pokazano. Aby zachować klucz subskrypcji na komputerze PC lub laptop, wybierz **Zapisz klucz**. Aby usunąć klucz subskrypcji z systemu, wybierz **klawisz Delete** go usunąć z komputera lub laptopie.

   ![Rozpoznawanie mowy Wklej w klucza](../Images/SpeechRecog_paste_key.PNG)

3. W obszarze **źródła rozpoznawania mowy**, wybierz jedną z sześciu mowy źródeł które można podzielić na dwie główne kategorie danych wejściowych:

   * Aby przechwycić mowy, użyj komputera mikrofon lub dołączone mikrofonu.
   * Odtwarzanie pliku audio.

   Każda kategoria ma trzy tryby rozpoznawania:

    * **Tryb ShortPhrase**: Długości wypowiedź maksymalnie 15 sekund. Ponieważ dane są wysyłane na serwer, klient odbierze wiele wyników częściowych i jeden wynik końcowy z wieloma opcjami n najlepszych.
    * **Tryb LongDictation**: Wypowiedź maksymalnie dwóch minut long. Ponieważ dane są wysyłane na serwer, klient odbierze wiele wyników częściowych i wiele wyników końcowych w oparciu o którym serwer pauzy zdaniowe.
    * **Funkcja wykrywania**: Serwer zwraca dodatkowe ze strukturą informacje na temat rozpoznawania mowy, danych wejściowych. Aby korzystać z wykrywania intencji, musisz najpierw wytrenuj model przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Przykładowe pliki audio za pomocą tej przykładowej aplikacji. Znajdź pliki w repozytorium, które zostały pobrane z tego przykładu w folderze samples/SpeechRecognitionServiceExample. Te przykładowe pliki audio uruchamiane automatycznie, jeśli żadne inne pliki nie są wybierane po wybraniu **Użyj plików wav dla trybu Shortphrase** lub **Użyj plików wav dla trybu Longdictation** jako dane wejściowe mowy. Obecnie jest obsługiwany tylko format audio WAV.

![Zamiana mowy na tekst, interfejs](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="recognition-events"></a>Rozpoznawanie zdarzenia

* **Częściowe wyniki zdarzeń**: To zdarzenie jest wywoływane za każdym razem, gdy usługa rozpoznawania mowy przewiduje, użytkownik może być opinie, nawet zakończeniem wypowiedzi (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeśli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: Wywołuje się, gdy usługa wykrywa błąd.
* **Zdarzenia intencji**: Wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po ostatnim rozpoznawania wynik jest przekształcany do ze strukturą intencji JSON.
* **Wynik zdarzenia**:
  * W `ShortPhrase` tryb, to zdarzenie jest wywoływana i zwraca wyniki n najlepszych, po zakończeniu mówić.
  * W `LongDictation` tryb, program obsługi zdarzeń jest wywoływana wiele razy, oparte na którym usługa identyfikuje wstrzymuje zdania.
  * **Dla każdej opcji n najlepszych**, zwracane są wartości zaufania i kilka różnych sposobów rozpoznany tekst. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

Programy obsługi zdarzeń są już wskazano w kodzie w postaci komentarzy do kodu.

## <a name="related-topics"></a>Powiązane tematy

* [Odwołanie do biblioteki pulpitu Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku Objective C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
