---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft przy użyciu biblioteki pulpitu C# | Dokumentacja firmy Microsoft
description: Tworzenie podstawowych aplikacji systemu Windows, które przekonwertowana na tekst audio rozmowy za pomocą interfejsu API rozpoznawania mowy firmy Microsoft.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347448"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Rozpoczynanie pracy z interfejsem API rozpoznawania mowy w języku C&#35; dla platformy .NET w systemie Windows

Ta strona przedstawia sposób tworzenia podstawowej aplikacji systemu Windows, wykorzystujący interfejs API rozpoznawania mowy można przekonwertować na tekst rozmowy audio. Za pomocą biblioteki klienta umożliwia przesyłanie strumieniowe w czasie rzeczywistym, które oznacza, że gdy aplikacja kliencka wysyła audio z usługą, równocześnie i asynchronicznie odbiera wyniki częściowe rozpoznawania.

Deweloperzy, którzy chcą korzystać z aplikacji uruchamianych na dowolnym urządzeniu mowy usługi można użyć pulpitu biblioteki C#. Korzystanie z biblioteki, należy zainstalować [pakietu NuGet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) dla 32-bitowej platformy i [pakietu NuGet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) dla 64-bitowej platformy. Biblioteki klienta dokumentacja interfejsu API, zobacz [biblioteki pulpitu Microsoft mowy C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

W poniższych sekcjach opisano sposób instalowania, tworzenie i uruchamianie C# przykładowej aplikacji przy użyciu biblioteki pulpitu C#.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany w systemu Windows 8 i .NET Framework 4.5 + przy użyciu [programu Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobierz przykładową aplikację

Przykład w klonowania [standardowej biblioteki pulpitu mowy C#](https://github.com/microsoft/cognitive-speech-stt-windows) repozytorium.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API rozpoznawania mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Przed użyciem mowy bibliotek klienta musi mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Z podanych C# pulpitu przykładowej aplikacji Wklej swój klucz subskrypcji w polu tekstowym, po uruchomieniu próbki. Aby uzyskać więcej informacji, zobacz [Uruchom przykładową aplikację](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalowanie przykładowej aplikacji

1. Uruchom program Visual Studio 2015 i wybierz **pliku** > **Otwórz** > **projektu/rozwiązania**.

2. Przejdź do folderu, w którym zapisano pobranych plików API rozpoznawania mowy. Wybierz **mowy** > **Windows**, a następnie wybierz folder WP próbki.

3. Kliknij dwukrotnie plik można otworzyć pliku programu Visual Studio 2015 Solution (.sln) o nazwie SpeechToText-WPF-Samples.sln. Rozwiązanie zostanie otwarty w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

1. Jeśli chcesz użyć *rozpoznawania z zamiarem*, najpierw należy zarejestrować się w celu [usługi inteligentnego opis języka (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Następnie użyj adresu URL punktu końcowego aplikacji LUIS, aby ustawić wartość klucza `LuisEndpointUrl` w pliku app.config w folderze Przykłady/SpeechRecognitionServiceExample. Aby uzyskać więcej informacji na adres URL punktu końcowego LUIS aplikacji, zobacz [publikowanie aplikacji](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Zastąp `&` znaku w adresie URL punktu końcowego LUIS z `&amp;` aby upewnić się, że adres URL jest prawidłowo interpretowane przez analizatora składni XML.

2. Naciśnij klawisze Ctrl + Shift + B, lub wybierz **kompilacji** menu wstążki. Następnie wybierz **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji, naciśnij klawisz F5 lub wybierz **Start** menu wstążki do uruchomienia przykładu.

2. Przejdź do **mowy Oxford projektu, na przykład tekst** okna. Wklej swój klucz subskrypcji do **Wklej swój klucz subskrypcji, aby uruchomić** pola tekstowego, jak pokazano. Aby utrwalić klucz subskrypcji na komputerze lub laptop, wybierz **Zapisz klucz**. Aby usunąć klucz subskrypcji z systemu, wybierz **klawisz Delete** usunąć go z komputera lub komputera przenośnego.

   ![Rozpoznawanie mowy Wklej w klucza](../Images/SpeechRecog_paste_key.PNG)

3. W obszarze **źródła rozpoznawania mowy**, wybierz jedno ze źródeł sześciu mowy, które można podzielić na dwie główne kategorie wejściowych:

   * Użyj mikrofon komputera lub mikrofon dołączone do przechwytywania mowy.
   * Odtwarzanie pliku audio.

   Każda kategoria ma trzy tryby rozpoznawania:

    * **Tryb ShortPhrase**: utterance maksymalnie 15 sekund czasu. Dane są wysyłane na serwer, klient otrzyma wyniki częściowe wielu oraz jeden wynik końcowy wiele opcji najlepiej n.
    * **Tryb LongDictation**: utterance do dwóch minut długo. Dane są wysyłane do serwera, klient otrzyma wielu wyniki częściowe oraz wielu wyników końcowych, oparte na którym serwer wskazuje pauzy zdanie.
    * **Wykrywanie konwersji**: serwer zwraca dodatkowe strukturalnych informacje na temat wejścia mowy. Aby korzystać z opcji wykrywania, musisz najpierw uczenia modelu przy użyciu [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Przykładowe pliki audio za pomocą tej przykładowej aplikacji. Znajdź pliki w repozytorium, który pobrano z tego przykładu w folderze Przykłady/SpeechRecognitionServiceExample. Te przykładowe pliki audio uruchamiane automatycznie, jeśli żadne inne pliki są wybrane, po wybraniu **pliku wav wykorzystania dla trybu Shortphrase** lub **pliku wav wykorzystania dla trybu Longdictation** jako danych wejściowych mowy. Aktualnie obsługiwana jest tylko format audio WAV.

![Mowy interfejsu tekstu](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="recognition-events"></a>Rozpoznawanie zdarzenia

* **Częściowe wyniki zdarzenia**: to zdarzenie jest wywoływana za każdym razem, gdy usługi mowy prognozuje, co możesz może być informujący o tym, nawet przed zakończeniem mówiąc (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeżeli używasz `DataRecognitionClient`).
* **Zdarzenia błędu**: wywoływane, gdy usługa wykryje błąd.
* **Zdarzenia konwersji**: wywoływana na klientach "WithIntent" (tylko w trybie ShortPhrase) po ostatnim rozpoznawania wyników jest analizowana w strukturze zamiar JSON.
* **Powoduje zdarzenia**:
  * W `ShortPhrase` tryb, to zdarzenie jest nazywany i zwraca wyniki najlepiej n, po zakończeniu mówiąc.
  * W `LongDictation` trybie obsługi zdarzeń jest wywołana wiele razy, oparte na którym usługa identyfikuje zdanie pauzy.
  * **Dla każdej opcji najlepiej n**, zwracane są wartości zaufania i kilka różnych metod rozpoznany. Aby uzyskać więcej informacji, zobacz [format danych wyjściowych](../Concepts.md#output-format).

Programy obsługi zdarzeń są już wskazać w kodzie w formie komentarze w kodzie.

## <a name="related-topics"></a>Powiązane tematy

* [Odwołanie do biblioteki pulpitu Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku Java w systemie Android](GetStartedJavaAndroid.md)
* [Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku Objective-C w systemie iOS](Get-Started-ObjectiveC-iOS.md)
* [Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft w języku JavaScript](GetStartedJSWebsockets.md)
* [Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft za pośrednictwem interfejsu REST](GetStartedREST.md)
