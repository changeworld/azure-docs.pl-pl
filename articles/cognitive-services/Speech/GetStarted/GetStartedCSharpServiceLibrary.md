---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft przy użyciu biblioteki usługi C# | Dokumentacja firmy Microsoft
description: Biblioteka usługi rozpoznawania mowy Microsoft umożliwia przekonwertowana na tekst używany.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/17/2017
ms.author: zhouwang
ms.openlocfilehash: 0320f41658a7ac4d6bf9e88ed998c853b665d485
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347469"
---
# <a name="get-started-with-the-speech-recognition-service-library-in-c35-for-net-windows"></a>Rozpoczynanie pracy z biblioteki usługi rozpoznawania mowy w języku C&#35; dla platformy .NET systemu Windows

Biblioteka usługi jest dla deweloperów, którzy mają własne usługi w chmurze i ma zostać wywołana mowy usługi z usługi. Aby wywołać usługę rozpoznawania mowy z aplikacji powiązanych z urządzenia, nie należy używać tego zestawu SDK. (Użyj innych bibliotek klienckich lub interfejsów API REST dla tej).

Aby korzystać z biblioteki usługi C#, zainstalować [pakietu NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). W bibliotece dokumentacji interfejsu API, zobacz [biblioteki usługi Microsoft mowy C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

W poniższych sekcjach opisano sposób instalowania, tworzenie i uruchamianie C# przykładowej aplikacji przy użyciu biblioteki usługi C#.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany w systemu Windows 8 i .NET 4.5 + Framework za pomocą [programu Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobierz przykładową aplikację

Przykład w klonowania [standardowej biblioteki usługi mowy C#](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) repozytorium.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie API rozpoznawania mowy i uzyskiwanie klucza bezpłatnej subskrypcji próbnej

Interfejs API mowy jest częścią usługi kognitywnych (wcześniej Oxford projektu). Możesz uzyskać klucze bezpłatnej subskrypcji próbnej z [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API mowy wybierz **uzyskać klucz interfejsu API** uzyskać klucza. Zwraca wartość klucza podstawowego i pomocniczego. Obydwu kluczy są powiązane z tego samego przydziału, dzięki czemu można użyć albo klucza.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Zanim będzie można użyć bibliotek klienckich mowy, musisz mieć [klucza subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Z podanych C# usługi biblioteki przykładowej aplikacji należy podać klucz subskrypcji jako jeden z parametrów wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Uruchom przykładową aplikację](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalowanie przykładowej aplikacji

1. Uruchom program Visual Studio 2015 i wybierz **pliku** > **Otwórz** > **projektu/rozwiązania**.

2. Kliknij dwukrotnie plik można otworzyć pliku programu Visual Studio 2015 Solution (.sln) o nazwie SpeechClient.sln. Rozwiązanie zostanie otwarty w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

Naciśnij klawisze Ctrl + Shift + B, lub wybierz **kompilacji** menu wstążki. Następnie wybierz **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji, naciśnij klawisz F5 lub wybierz **Start** menu wstążki do uruchamiania w przykładzie.

2. Otwórz katalog wyjściowy dla przykładu, na przykład SpeechClientSample\bin\Debug. Naciśnij klawisz Shift + prawym przyciskiem myszy i wybierz **Otwórz okno polecenia tutaj**.

3. Uruchom `SpeechClientSample.exe` z następującymi argumentami:

   * ARG [0]: Określ plik WAV wejściowego audio.
   * ARG [1]: Określ audio ustawień regionalnych.
   * Argument [2]: Określ tryby rozpoznawania: *krótki* dla `ShortPhrase` tryb i *długi* dla `LongDictation` trybu.
   * Argument [3]: Określ klucz subskrypcji dostęp do usługi rozpoznawania mowy.

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="recognition-modes"></a>Tryby rozpoznawania

* `ShortPhrase` tryb: utterance maksymalnie 15 sekund czasu. Dane są wysyłane do serwera, klient otrzyma wyniki częściowe wielu oraz jeden wynik najlepsze końcowego.
* `LongDictation` tryb: utterance maksymalnie 10 minut długo. Dane są wysyłane do serwera, klient otrzyma wielu wyniki częściowe oraz wielu wyników końcowych, oparte na którym serwer wskazuje pauzy zdanie.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

Interfejs API mowy obsługuje audio/WAV przy użyciu następujących koderów-dekoderów:

* Pojedynczy kanał PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferencje

Aby utworzyć SpeechClient, musisz najpierw utworzyć obiekt Preferencje. Obiekt Preferencje jest zestaw parametrów, który konfiguruje zachowania usługi mowy. Składa się z następujących pól:

* `SpeechLanguage`: Ustawienia regionalne dźwięku wysyłane do usługi mowy.
* `ServiceUri`: Punktowi końcowemu używanemu do wywołania tej usługi mowy.
* `AuthorizationProvider`: Implementacja IAuthorizationProvider umożliwia pobieranie tokenów w celu uzyskania dostępu do usługi mowy. Chociaż próbki dostawcy autoryzacji kognitywnych usług, zdecydowanie zaleca się utworzenie implementacji do obsługi pamięci podręcznej tokenu.
* `EnableAudioBuffering`: Zaawansowanej opcji. Zobacz [zarządzania połączeniami](#connection-management).

### <a name="speech-input"></a>Dane wejściowe mowy

Obiekt SpeechInput składa się z dwóch pól:

* **Audio**: strumień stosowania wybrana, z którego ściąga audio zestawu SDK. Może być dowolną [strumienia](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) obsługującej odczytu.
   > [!NOTE]
   > Zestaw SDK wykrywa koniec strumienia, gdy zwraca strumień **0** podczas odczytywania.

* **RequestMetadata**: metadane dotyczące żądania mowy. Aby uzyskać więcej informacji, zobacz [odwołania](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Żądanie mowy

Po ma wystąpienia obiektów SpeechClient i SpeechInput, należy użyć RecognizeAsync Wyślij żądanie do usługi mowy.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po zakończeniu żądanie kończy zadanie zwrócone przez RecognizeAsync. Ostatni RecognitionResult jest koniec uznania. Zadanie może zakończyć się niepowodzeniem, jeśli usługa lub zestawu SDK nie powiedzie się nieoczekiwanie.

### <a name="speech-recognition-events"></a>Zdarzenia rozpoznawania mowy

#### <a name="partial-results-event"></a>Wyniki częściowe zdarzeń

To zdarzenie jest wywoływana za każdym razem, gdy usługi mowy prognozuje, co możesz może być informujący o tym, nawet przed zakończeniem mówiąc (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeżeli używasz `DataRecognitionClient`). Będzie możliwe subskrybowanie zdarzeń za pomocą `SpeechClient.SubscribeToPartialResult()`. Lub można użyć metody subskrypcji zdarzeń ogólnego `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Zwraca format** | Opis |
------|------
**LexicalForm** | Ten formularz jest optymalna dla aplikacji wymagających wyników rozpoznawania mowy raw, nieprzetworzone.
**Wyświetlany_tekst** | Rozpoznaną frazę z tekstu odwrotność funkcji normalizacji, wielkie litery, znaki interpunkcyjne i maskowania niestosownych wyrażeń stosowane. Niestosownych wyrażeń jest maskowane gwiazdki po początkowej znaku, na przykład "d ***." Ten formularz jest optymalna dla aplikacji, wyświetlanych wyników rozpoznawania mowy do użytkownika.
**Zaufania** | Poziom zaufania rozpoznaną frazę reprezentuje skojarzone audio zgodnie z definicją w serwerze rozpoznawania mowy.
**MediaTime** | Bieżący czas względem początku strumień dźwiękowy (w 100-nanosekundowych jednostkach czasu).
**MediaDuration** | Bieżąca frazy czas trwania/długość względem audio segmentu (w 100-nanosekundowych jednostkach czasu).

#### <a name="result-event"></a>Wynik zdarzeń
Po zakończeniu mówiąc (w `ShortPhrase` tryb), to zdarzenie jest wywoływane. Opcje najlepiej n jest podany dla wyniku. W `LongDictation` trybie zdarzenia może być wywołana wiele razy, oparte na którym serwer wskazuje pauzy zdanie. Będzie możliwe subskrybowanie zdarzeń za pomocą `SpeechClient.SubscribeToRecognitionResult()`. Lub można użyć metody subskrypcji zdarzeń ogólnego `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Zwraca format** | Opis |
------|------|
**RecognitionStatus** | Stan w sposób rozpoznawania został utworzony. Na przykład został on utworzony wyniku pomyślne rozpoznawanie lub wyniku anulowanie połączenia itp.
**Wyrażenia** | Zestaw najlepiej n fraz rozpoznany bez obaw rozpoznawania.

Aby uzyskać więcej informacji na temat wyników rozpoznawania zobacz [format danych wyjściowych](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Odpowiedź rozpoznawania mowy

Przykład odpowiedzi mowy:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High) 
```

## <a name="connection-management"></a>Zarządzanie połączeniami

Interfejs API korzysta z jednego połączenia obiektu WebSocket na żądanie. Środowisko użytkownika optymalne zestawu SDK próbuje ponownie z usługą mowy i uruchomić uznanie z ostatnich RecognitionResult, który otrzymał. Na przykład jeśli audio żądanie jest długo dwie minuty, zestaw SDK Odebrano RecognitionEvent na znak co minutę i po pięciu sekundach wystąpił błąd sieci, zestaw SDK uruchamia nowe połączenie, która rozpoczyna się od znaku co minutę.

>[!NOTE]
>Zestaw SDK nie wyszukiwania wstecz do oznaczenia co minutę, ponieważ strumień może nie obsługuje operacji wyszukiwania. Zamiast tego zestawu SDK przechowuje bufor wewnętrzny używany do zbuforowania audio i czyści buforu jako odbiera zdarzenia RecognitionResult.

## <a name="buffering-behavior"></a>Zachowanie buforowania

Domyślnie zestaw SDK buforuje audio, dzięki czemu można odzyskać, gdy wystąpi przerwań sieci. W przypadku których zaleca się odrzucić audio utracone podczas rozłączenia sieci i uruchomić ponownie połączenie, najlepiej wyłączyć buforowanie audio, ustawiając `EnableAudioBuffering` obiektu preferencje do `false`.

## <a name="related-topics"></a>Powiązane tematy

[Dokumentacja biblioteki usługi Microsoft mowy C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
