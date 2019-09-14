---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft C# przy użyciu biblioteki usług | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Użyj biblioteki usługi rozpoznawania rozpoznawanie mowy Bing, aby skonwertować język mówiony na tekst.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965858"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Szybki start: Korzystanie z biblioteki usługi rozpoznawania rozpoznawanie mowy Bing w języku&#35; C dla systemu .NET Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Biblioteka usług jest przeznaczony dla deweloperów, którzy mają własną usługę w chmurze i chcą korzystać z usługi mowy z ich usługi. Jeśli chcesz wywołać usługę rozpoznawania mowy z aplikacji powiązanych z urządzeniami, nie używaj tego zestawu SDK. (Użyj innych bibliotek klienta lub interfejsów API REST dla tego programu).

Aby użyć biblioteki C# usług, zainstaluj [pakiet NuGet Microsoft. Bing. Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Aby uzyskać informacje na temat dokumentacji interfejsu API biblioteki, zobacz [Biblioteka usługi Microsoft Speech C# ](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

W poniższych sekcjach opisano sposób instalowania, kompilowania i uruchamiania C# przykładowej aplikacji przy użyciu biblioteki C# usług.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany dla systemu Windows 8 + i .NET 4.5 + Framework przy użyciu [programu Visual Studio 2015, wersja Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobieranie przykładowej aplikacji

Sklonuj przykład z [przykładowego repozytorium C# biblioteki usługi Speech Service](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie interfejsu API rozpoznawania mowy i pobieranie klucza subskrypcji bezpłatnej wersji próbnej

Speech API jest częścią Cognitive Services (wcześniej Project Oxford). Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać ze strony [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) . Po wybraniu Speech API wybierz pozycję **Pobierz klucz interfejsu API** , aby uzyskać klucz. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tym samym limitem przydziału, więc można użyć obu kluczy.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Aby można było korzystać z bibliotek klienckich mowy, należy dysponować [kluczem subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Korzystając z podanej C# przykładowej aplikacji biblioteki usług, musisz podać swój klucz subskrypcji jako jeden z parametrów wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Uruchamianie przykładowej aplikacji](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Instalowanie przykładowej aplikacji

1. Uruchom program Visual Studio 2015 i wybierz pozycję **plik** > **Otwórz** > **projekt/rozwiązanie**.

2. Kliknij dwukrotnie, aby otworzyć plik rozwiązania programu Visual Studio 2015 (. sln) o nazwie SpeechClient. sln. Rozwiązanie zostanie otwarte w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

Naciśnij klawisze CTRL + SHIFT + B lub wybierz opcję **Kompiluj** w menu wstążki. Następnie wybierz pozycję **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji naciśnij klawisz F5 lub wybierz pozycję **Rozpocznij** w menu wstążki, aby uruchomić przykład.

2. Otwórz katalog wyjściowy dla przykładu, na przykład SpeechClientSample\bin\Debug. Naciśnij klawisze Shift + kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Otwórz okno polecenia tutaj**.

3. Uruchom `SpeechClientSample.exe` z następującymi argumentami:

   * ARG [0]: Określ wejściowy plik dźwiękowy WAV.
   * ARG [1]: Określ ustawienia regionalne audio.
   * ARG [2]: Określ tryby rozpoznawania: *Krótki* trybidługośćdlatrybu.`ShortPhrase` `LongDictation`
   * ARG [3]: Określ klucz subskrypcji, aby uzyskać dostęp do usługi rozpoznawania mowy.

## <a name="samples-explained"></a>Przykłady wyjaśnione

### <a name="recognition-modes"></a>Tryby rozpoznawania

* `ShortPhrase`wyst Wypowiedź do 15 sekund. Gdy dane są wysyłane do serwera, klient otrzymuje wiele wyników częściowych i jeden ostateczny najlepszy wynik.
* `LongDictation`wyst Wypowiedź maksymalnie 10 minut. Gdy dane są wysyłane do serwera, klient otrzymuje wiele wyników częściowych i wiele ostatecznych wyników, w zależności od tego, gdzie serwer wskazuje pauzy.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

Speech API obsługuje dźwięk/WAV, korzystając z następujących koderów-dekoder:

* Pojedynczy kanał PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferencje

Aby utworzyć SpeechClient, należy najpierw utworzyć obiekt Preferences (Preferencje). Obiekt Preferences to zestaw parametrów, które konfigurują zachowanie usługi Speech. Składa się z następujących pól:

* `SpeechLanguage`: Ustawienia regionalne dźwięku wysyłanego do usługi mowy.
* `ServiceUri`: Punkt końcowy używany do wywoływania usługi mowy.
* `AuthorizationProvider`: Implementacja IAuthorizationProvider, która umożliwia pobieranie tokenów w celu uzyskania dostępu do usługi mowy. Mimo że przykład zapewnia Cognitive Services dostawcę autoryzacji, zdecydowanie zalecamy utworzenie własnej implementacji do obsługi buforowania tokenów.
* `EnableAudioBuffering`: Opcja zaawansowana. Zobacz [Zarządzanie połączeniami](#connection-management).

### <a name="speech-input"></a>Wprowadzanie mowy

Obiekt SpeechInput składa się z dwóch pól:

* **Dźwięk**: Implementacja strumienia, z której wybrano zestaw SDK, który pobiera dźwięk. Może to być dowolny [strumień](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , który obsługuje odczytywanie.
   > [!NOTE]
   > Zestaw SDK wykrywa koniec strumienia, gdy strumień zwraca **0** w trybie odczytu.

* **RequestMetadata**: Metadane dotyczące żądania mowy. Aby uzyskać więcej informacji, zobacz [odwołanie](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Żądanie mowy

Po utworzeniu wystąpienia obiektów SpeechClient i SpeechInput należy użyć RecognizeAsync, aby wysłać żądanie do usługi Speech.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po zakończeniu żądania zadanie zwrócone przez RecognizeAsync kończy się. Ostatnim RecognitionResultem jest koniec rozpoznawania. Zadanie może zakończyć się niepowodzeniem, jeśli usługa lub zestaw SDK nieoczekiwanie ulegnie awarii.

### <a name="speech-recognition-events"></a>Zdarzenia rozpoznawania mowy

#### <a name="partial-results-event"></a>Częściowe zdarzenie wyników

To zdarzenie jest wywoływane za każdym razem, gdy usługa mowy przeanalizuje to, co może się powiedzieć, nawet przed zakończeniem `MicrophoneRecognitionClient`rozmowy (Jeśli używasz) lub zakończyć wysyłanie danych `DataRecognitionClient`(Jeśli używasz). Możesz subskrybować zdarzenie, używając polecenia `SpeechClient.SubscribeToPartialResult()`. Można też użyć metody `SpeechClient.SubscribeTo<RecognitionPartialResult>()`subskrypcji zdarzeń ogólnych.

**Format powrotu** | Opis |
------|------
**LexicalForm** | Ten formularz jest optymalny do użycia przez aplikacje wymagające nieprzetworzonych, nieprzetworzonych wyników rozpoznawania mowy.
**DisplayText** | Rozpoznano frazę z zastosowanym normalizem tekstu odwrotnego, wersalikami, interpunkcją i maską wulgarności. Nieznaczność jest maskowany przy użyciu gwiazdek po znaku początkowym, na przykład "d * * *". Ten formularz jest optymalny do użycia przez aplikacje, które wyświetlają wyniki rozpoznawania mowy użytkownikowi.
**Zachowanie** | Poziom zaufania rozpoznanej frazy reprezentuje dla skojarzonego audio zdefiniowanego przez serwer rozpoznawania mowy.
**MediaTime** | Bieżący czas względem początku strumienia audio (w jednostkach czasu 100-nanosekund).
**MediaDuration** | Bieżąca fraza czas trwania/długość odnosząca się do segmentu audio (w jednostkach czasu 100 – nanosekund).

#### <a name="result-event"></a>Zdarzenie wynikowe
Gdy skończysz mówić (w `ShortPhrase` trybie), to zdarzenie jest wywoływane. Na potrzeby wyniku są dostępne n-najlepsze opcje. W `LongDictation` trybie w tym zdarzeniu można wywołać wiele razy, w zależności od tego, gdzie serwer wskazuje pauzy zdań. Możesz subskrybować zdarzenie, używając polecenia `SpeechClient.SubscribeToRecognitionResult()`. Można też użyć metody `SpeechClient.SubscribeTo<RecognitionResult>()`subskrypcji zdarzeń ogólnych.

**Format powrotu** | Opis |
------|------|
**RecognitionStatus** | Stan sposobu wygenerowania rozpoznawania. Na przykład zostało utworzone w wyniku pomyślnego rozpoznania lub w wyniku anulowania połączenia itp.
**"** | Zestaw n-najlepszych rozpoznanych fraz z pewnością rozpoznawania.

Aby uzyskać więcej informacji na temat wyników rozpoznawania, zobacz [Format danych wyjściowych](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Odpowiedź dotycząca rozpoznawania mowy

Przykład odpowiedzi na mowę:
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

Interfejs API wykorzystuje pojedyncze połączenie protokołu WebSocket dla każdego żądania. W celu zapewnienia optymalnego środowiska użytkownika zestaw SDK próbuje ponownie nawiązać połączenie z usługą mowy i rozpocząć rozpoznawanie od ostatniego odebranego RecognitionResult. Na przykład jeśli żądanie audio ma dwie minuty Long, zestaw SDK otrzymał RecognitionEvent w jednominutowym znaczniku, a awaria sieci wystąpiła po pięciu sekundach, zestaw SDK uruchamia nowe połączenie, które zaczyna się od znaku 1 minuty.

>[!NOTE]
>Zestaw SDK nie zwraca z powrotem do jednominutowego znaku, ponieważ strumień może nie obsługiwać wyszukiwania. Zamiast tego zestaw SDK utrzymuje wewnętrzny bufor, którego używa do buforowania dźwięku i Czyści bufor w miarę odbierania zdarzeń RecognitionResult.

## <a name="buffering-behavior"></a>Zachowanie buforowania

Domyślnie zestaw SDK buforuje dźwięk, aby możliwe było odzyskanie w przypadku wystąpienia przerwy w sieci. W scenariuszu, w którym warto odrzucić dźwięk utracony podczas odłączania i ponownego uruchomienia połączenia, najlepszym rozwiązaniem jest wyłączenie buforowania audio przez ustawienie `EnableAudioBuffering` w obiekcie Preferences na. `false`

## <a name="related-topics"></a>Tematy pokrewne

[Dokumentacja biblioteki C# usługi Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
