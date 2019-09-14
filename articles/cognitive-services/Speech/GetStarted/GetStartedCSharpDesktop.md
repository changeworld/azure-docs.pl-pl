---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania rozpoznawanie mowy Bing przy C# użyciu biblioteki klasycznej | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Opracowuj podstawowe aplikacje systemu Windows korzystające z interfejsu API rozpoznawania rozpoznawanie mowy Bing, aby przekonwertować dźwięk mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966897"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Szybki start: Korzystanie z interfejsu API rozpoznawania rozpoznawanie mowy Bing w&#35; języku C dla platformy .NET w systemie Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Na tej stronie pokazano, jak utworzyć podstawową aplikację systemu Windows, która korzysta z interfejsu API rozpoznawania mowy, aby przekonwertować mówiony dźwięk na tekst. Użycie biblioteki klienta umożliwia przesyłanie strumieniowe w czasie rzeczywistym, co oznacza, że gdy aplikacja kliencka wysyła dźwięk do usługi, to jednocześnie i asynchronicznie odbiera wyniki rozpoznawania częściowej.

Deweloperzy, którzy chcą korzystać z usługi mowy z aplikacji uruchamianych na dowolnym urządzeniu, mogą C# korzystać z biblioteki pulpitu. Aby użyć biblioteki, zainstaluj [pakiet NuGet Microsoft. ProjectOxford. SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) dla platformy 32-bitowej i [pakietu NuGet Microsoft. ProjectOxford. SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) dla platformy 64-bitowej. Aby uzyskać informacje dotyczące interfejsu API biblioteki klienckiej, zobacz [Microsoft Speech C# Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

W poniższych sekcjach opisano sposób instalowania, kompilowania i uruchamiania C# przykładowej aplikacji przy użyciu biblioteki C# pulpitu.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany dla systemu Windows 8 + i .NET Framework 4.5 + przy użyciu [programu Visual Studio 2015, wersja Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobieranie przykładowej aplikacji

Sklonuj przykład z [przykładowego repozytorium C# biblioteki pulpitu mowy](https://github.com/microsoft/cognitive-speech-stt-windows) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie interfejsu API rozpoznawania mowy i pobieranie klucza subskrypcji bezpłatnej wersji próbnej

Speech API jest częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Przed rozpoczęciem korzystania z bibliotek klienta mowy należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Przy użyciu podanej C# przykładowej aplikacji pulpitu Wklej klucz subskrypcji do pola tekstowego podczas uruchamiania przykładu. Aby uzyskać więcej informacji, zobacz [Uruchamianie przykładowej aplikacji](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalowanie przykładowej aplikacji

1. Uruchom program Visual Studio 2015 i wybierz pozycję **plik** > **Otwórz** > **projekt/rozwiązanie**.

2. Przejdź do folderu, w którym zapisano pobrane pliki interfejsu API rozpoznawania mowy. Wybierz pozycję **Speech** > **Windows**, a następnie wybierz folder Sample-wp.

3. Kliknij dwukrotnie, aby otworzyć plik rozwiązania programu Visual Studio 2015 (. sln) o nazwie SpeechToText-WPF-Samples. sln. Rozwiązanie zostanie otwarte w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

1. Jeśli chcesz użyć *rozpoznawania z zamiarem*, najpierw musisz zarejestrować się w usłudze [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Następnie użyj adresu URL punktu końcowego aplikacji Luis, aby ustawić wartość klucza `LuisEndpointUrl` w pliku App. config w folderze Samples/SpeechRecognitionServiceExample. Aby uzyskać więcej informacji na temat adresu URL punktu końcowego aplikacji LUIS, zobacz temat [publikowanie aplikacji](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Zastąp `&amp;` znak w adresie URL punktu końcowego Luis, aby upewnić się, że adres URL jest poprawnie interpretowany przez parser XML. `&`

2. Naciśnij klawisze CTRL + SHIFT + B lub wybierz opcję **Kompiluj** w menu wstążki. Następnie wybierz pozycję **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji naciśnij klawisz F5 lub wybierz pozycję **Rozpocznij** w menu wstążki, aby uruchomić przykład.

2. Przejdź do okna **przykładowy projekt Oxford mowy do tekstu** . Wklej swój klucz subskrypcji do pola wyboru **Wklej swój klucz subskrypcji tutaj, aby rozpocząć** pole tekstowe. Aby zachować klucz subskrypcji na komputerze lub laptopie, wybierz pozycję **Zapisz klucz**. Aby usunąć klucz subskrypcji z systemu, wybierz pozycję **Usuń klucz** , aby usunąć go z komputera lub laptopu.

   ![Klucz wklejania funkcji rozpoznawania mowy](../Images/SpeechRecog_paste_key.PNG)

3. W obszarze **Źródło rozpoznawania mowy**wybierz jedno z sześciu źródeł mowy, które dzielą się na dwie główne kategorie danych wejściowych:

   * Przechwyć mowę przy użyciu mikrofonu komputera lub dołączonego mikrofonu.
   * Odtwórz plik audio.

   Każda kategoria ma trzy tryby rozpoznawania:

    * **Tryb ShortPhrase**: Wypowiedź do 15 sekund. Gdy dane są wysyłane do serwera, klient otrzymuje wiele wyników częściowych i jeden końcowy wynik z wieloma opcjami najlepszych z n.
    * **Tryb LongDictation**: Wypowiedź maksymalnie dwie minuty. Gdy dane są wysyłane do serwera, klient otrzymuje wiele wyników częściowych i wiele ostatecznych wyników, w zależności od tego, gdzie serwer wskazuje pauzy.
    * **Wykrywanie zamiaru**: Serwer zwraca dodatkowe informacje strukturalne na temat danych wejściowych mowy. Aby można było korzystać z wykrywania intencji, należy najpierw nauczyć model przy użyciu [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Użyj przykładowych plików audio z tą przykładową aplikacją. Znajdź pliki w repozytorium pobranym z tym przykładem w folderze Samples/SpeechRecognitionServiceExample. Te przykładowe pliki audio są uruchamiane automatycznie, jeśli nie wybrano innych plików w przypadku wybrania opcji **Użyj pliku WAV dla trybu Shortphrase** lub **użyć pliku WAV dla trybu Longdictation** jako dane wejściowe mowy. Obecnie obsługiwany jest tylko format dźwiękowy WAV.

![Interfejs zamiany mowy na tekst](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Przykłady wyjaśnione

### <a name="recognition-events"></a>Zdarzenia rozpoznawania

* **Zdarzenia częściowe wyniki**: To zdarzenie jest wywoływane za każdym razem, gdy usługa mowy przeanalizuje to, co może się powiedzieć, nawet przed zakończeniem `MicrophoneRecognitionClient`rozmowy (Jeśli używasz) lub zakończyć wysyłanie danych `DataRecognitionClient`(Jeśli używasz).
* **Zdarzenia błędów**: Wywoływana, gdy usługa wykrywa błąd.
* **Zdarzenia dotyczące intencji**: Wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po zakończeniu ostatniego wyniku rozpoznawania zostanie przeanalizowany do zamiaru strukturalnego JSON.
* **Zdarzenia wynikowe**:
  * W `ShortPhrase` trybie, to zdarzenie jest wywoływane i zwraca n najlepszych wyników po zakończeniu mówienia.
  * W `LongDictation` trybie, program obsługi zdarzeń jest wywoływany wiele razy, w zależności od tego, gdzie usługa identyfikuje zdanie pauzy.
  * **Dla każdej z najlepszych opcji**, są zwracane wartości zaufania i kilka różnych form rozpoznanego tekstu. Aby uzyskać więcej informacji, zobacz [Format danych wyjściowych](../Concepts.md#output-format).

Procedury obsługi zdarzeń są już wskazywane w kodzie w postaci komentarzy do kodu.

## <a name="related-topics"></a>Tematy pokrewne

* [Dokumentacja biblioteki Microsoft Speech Desktop](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w celu języka C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft w języku JavaScript](GetStartedJSWebsockets.md)
* [Wprowadzenie do interfejsu API rozpoznawania mowy firmy Microsoft za pośrednictwem usługi REST](GetStartedREST.md)
