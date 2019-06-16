---
title: Rozpoczynanie pracy z interfejsem API rozpoznawania mowy firmy Microsoft przy użyciu biblioteki usługi C# | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Biblioteka usług rozpoznawania mowy Bing umożliwia konwertowanie mowy na tekst.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f445d1fff48ee7a04c0b1c1d64c808f87d824b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515225"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Szybki start: Korzystanie z biblioteki usługi rozpoznawania mowy Bing w języku C&#35; dla Windows .NET

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

To biblioteka usług dla deweloperów, którzy mają swoje własne usługi w chmurze i ma zostać wywołana usługa rozpoznawania mowy z ich usług. Jeśli chcesz wywołać usługę rozpoznawania mowy z aplikacji powiązanych z urządzenia, nie używaj tego zestawu SDK. (Użyj innych bibliotek klienta lub interfejsów API REST dla tego).

Aby korzystać z biblioteki usługi C#, należy zainstalować [pakietu NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Dokumentacja interfejsu API biblioteki, zobacz [Biblioteka usług Microsoft Speech C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Poniżej opisano sposób instalowania, tworzenie i uruchamianie języka C# przykładowej aplikacji przy użyciu biblioteki usługi C#.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="platform-requirements"></a>Wymagania dotyczące platformy

Poniższy przykład został opracowany dla systemu Windows 8 + i .NET 4.5 + Framework za pomocą [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Pobieranie przykładowej aplikacji

Sklonuj przykład z [standardowej biblioteki usługi mowy C#](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) repozytorium.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Subskrybowanie do interfejsu API rozpoznawania mowy i Uzyskaj klucz subskrypcji wersji próbnej

Interfejs API mowy jest częścią usług Cognitive Services (wcześniej Project Oxford). Możesz uzyskać bezpłatną subskrypcję próbną kluczy z [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony. Po wybraniu interfejsu API rozpoznawania mowy, wybierz opcję **Uzyskaj klucz interfejsu API** można pobrać klucza. Zwraca klucz podstawowy i pomocniczy. Oba klucze są powiązane z tego samego limitu przydziału, aby można było używać żadnego z nich.

> [!IMPORTANT]
> * Pobierz klucz subskrypcji. Korzystanie z biblioteki klienta mowy, konieczne jest posiadanie [klucz subskrypcji](https://azure.microsoft.com/try/cognitive-services/).
>
> * Użyj klucza subskrypcji. Podana C# usługi biblioteki przykładowej aplikacji musisz podać klucz subskrypcji jako jeden z parametrów wiersza polecenia. Aby uzyskać więcej informacji, zobacz [uruchamianie przykładowej aplikacji](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Krok 1: Zainstaluj przykładową aplikację.

1. Uruchom program Visual Studio 2015 i wybierz **pliku** > **Otwórz** > **projekt/rozwiązanie**.

2. Kliknij dwukrotnie, aby otworzyć plik programu Visual Studio 2015 rozwiązania (.sln) o nazwie SpeechClient.sln. Rozwiązanie zostanie otwarty w programie Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Krok 2: Tworzenie przykładowej aplikacji

Naciśnij klawisze Ctrl + Shift + B, lub wybierz **kompilacji** menu wstążki. Następnie wybierz pozycję **Kompiluj rozwiązanie**.

## <a name="step-3-run-the-sample-application"></a>Krok 3: Uruchamianie przykładowej aplikacji

1. Po zakończeniu kompilacji, naciśnij klawisz F5 lub wybierz **Start** w menu wstążki, aby uruchomić przykład.

2. Otwórz katalog wyjściowy dla przykładu, na przykład SpeechClientSample\bin\Debug. Naciśnij klawisze Shift + Strzałka w prawo kliknij i zaznacz **Otwórz okno polecenia tutaj**.

3. Uruchom `SpeechClientSample.exe` z następującymi argumentami:

   * ARG [0]: Określ plik wejściowy WAV audio.
   * ARG [1]: Określ ustawienia regionalne audio.
   * Argument [2]: Określ tryby rozpoznawania: *Krótki* dla `ShortPhrase` tryb i *długie* dla `LongDictation` trybu.
   * Argument [3]: Określ klucz subskrypcji dostęp do usługi rozpoznawania mowy.

## <a name="samples-explained"></a>Przykłady wyjaśniono

### <a name="recognition-modes"></a>Tryby rozpoznawania

* `ShortPhrase` tryb: Długości wypowiedź maksymalnie 15 sekund. Ponieważ dane są wysyłane do serwera, klient odbierze wiele wyników częściowych i jeden końcowy wynik najlepsze.
* `LongDictation` tryb: Wypowiedź maksymalnie 10 minut long. Ponieważ dane są wysyłane na serwer, klient odbierze wiele wyników częściowych i wiele wyników końcowych w oparciu o którym serwer pauzy zdaniowe.

### <a name="supported-audio-formats"></a>Obsługiwane formaty audio

Interfejs API mowy obsługuje audio/WAV przy użyciu następujących koderów-dekoderów:

* Pojedynczy kanał modułu PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferencje

Aby utworzyć SpeechClient, musisz najpierw utwórz obiekt preferencji. Obiekt preferencji jest zestaw parametrów, który służy do konfigurowania zachowania usługi mowy. Składa się z następujących pól:

* `SpeechLanguage`: Ustawienia regionalne audio wysyłane do usługi rozpoznawania mowy.
* `ServiceUri`: Punkt końcowy, używany do wywoływania usługi mowy.
* `AuthorizationProvider`: Implementacja IAuthorizationProvider używane do pobierania tokenów w celu uzyskania dostępu do usługi rozpoznawania mowy. Mimo że przykład zawiera dostawcę autoryzacji usług Cognitive Services, firma Microsoft zdecydowanie zaleca się utworzenie własnej implementacji w celu obsługi pamięci podręcznej tokenu.
* `EnableAudioBuffering`: Zaawansowana opcja. Zobacz [zarządzania połączeniami](#connection-management).

### <a name="speech-input"></a>Dane wejściowe mowy

Obiekt SpeechInput składa się z dwóch pól:

* **Dźwięk**: Strumień implementacja dowolnie, z której zestaw SDK pobiera audio. Może być dowolną [strumienia](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) , która obsługuje Odczyt.
   > [!NOTE]
   > Zestaw SDK wykrywa koniec strumienia, gdy strumień zwraca **0** podczas odczytywania.

* **RequestMetadata**: Metadane o żądaniu rozpoznawania mowy. Aby uzyskać więcej informacji, zobacz [odwołania](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Żądanie rozpoznawania mowy

Po mają uruchomiony SpeechClient i SpeechInput obiektów, należy użyć RecognizeAsync, aby wysyłać żądania do usługi rozpoznawania mowy.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Po ukończeniu żądania zadanie zwracane przez RecognizeAsync się nie zakończy. Ostatni RecognitionResult jest końcem rozpoznawanie. Zadanie może zakończyć się niepowodzeniem, jeśli usługa lub zestawu SDK nie powiedzie się nieoczekiwanie.

### <a name="speech-recognition-events"></a>Zdarzenia rozpoznawania mowy

#### <a name="partial-results-event"></a>Zdarzenie wyniki częściowe

To zdarzenie jest wywoływane za każdym razem, gdy usługa rozpoznawania mowy przewiduje, użytkownik może być opinie, nawet zakończeniem wypowiedzi (Jeśli używasz `MicrophoneRecognitionClient`) lub zakończenie wysyłania danych (Jeśli używasz `DataRecognitionClient`). Można subskrybować zdarzenia przy użyciu `SpeechClient.SubscribeToPartialResult()`. Możesz też metoda subskrypcji zdarzenia ogólne `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Zwraca format** | Opis |
------|------
**LexicalForm** | Ta forma jest optymalna dla aplikacji wymagających wyniki rozpoznawania mowy raw, nieprzetworzonych.
**DisplayText** | Rozpoznane fraza normalizacji odwrotność tekstu, wielkość liter, znaki interpunkcyjne i maskowania wulgaryzmów stosowane. Wulgaryzmów są maskowane symbolami gwiazdki po początkowej znaku, na przykład, "d ***." Ta forma jest optymalna do używania przez aplikacje, które wyświetlają wyniki rozpoznawania mowy do użytkownika.
**zaufania** | Poziom zaufania rozpoznaną frazę reprezentuje dźwięk skojarzonego zgodnie z definicją serwera rozpoznawania mowy.
**MediaTime** | Bieżąca godzina względem początku strumienia audio (w jednostkach 100-nanosekundowych czasu).
**MediaDuration** | Bieżąca frazy czas trwania/długość względem audio segmentu (w jednostkach 100-nanosekundowych czasu).

#### <a name="result-event"></a>Wynik zdarzenia
Po zakończeniu wypowiedzi (w `ShortPhrase` tryb), to zdarzenie jest wywoływane. Otrzymasz n najlepszych opcji dla wyniku. W `LongDictation` trybie zdarzenia może być wywoływana wiele razy, oparte na którym serwer pauzy zdaniowe. Można subskrybować zdarzenia przy użyciu `SpeechClient.SubscribeToRecognitionResult()`. Możesz też metoda subskrypcji zdarzenia ogólne `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Zwraca format** | Opis |
------|------|
**RecognitionStatus** | Stan jak rozpoznawanie został utworzony. Na przykład zostało wygenerowane w wyniku pomyślne rozpoznawanie lub w wyniku anulowanie połączenia itp.
**Zwroty** | Zestaw wyrażeń rozpoznawanym n najlepszych bez obaw rozpoznawania.

Aby uzyskać więcej informacji na temat rozpoznawania wyników, zobacz [format danych wyjściowych](../Concepts.md#output-format).

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

Interfejs API korzysta z jednego połączenia protokołu WebSocket na żądanie. Środowisko użytkownika optymalne zestaw SDK próbuje ponownie połączyć się z usługa mowy i Rozpocznij rozpoznawanie od ostatniego RecognitionResult, który otrzymał. Na przykład jeśli żądanie audio wynosi dwie minuty, długie, zestaw SDK Odebrano RecognitionEvent pozycji co minutę i wystąpił błąd sieci po 5 sekundach, zestaw SDK uruchamia nowego połączenia, który rozpoczyna się od znaku jednej minuty.

>[!NOTE]
>Zestaw SDK nie starać się wstecz do oznaczenia jednej minuty, ponieważ strumień może nie obsługiwać wyszukiwanie. Zamiast tego zestaw SDK śledzi buforu wewnętrznego wykorzystuje do buforowania audio i czyści bufor, gdy staje się RecognitionResult zdarzenia.

## <a name="buffering-behavior"></a>Zachowanie buforowania

Domyślnie zestaw SDK buforuje audio, dzięki czemu możliwe jest Odzyskiwanie po wystąpieniu przerwania sieci. W przypadku, gdy jest to korzystniejsze odrzucenie audio utracone podczas rozłączania sieci i uruchomić ponownie połączenie, warto wyłączyć buforowanie audio, ustawiając `EnableAudioBuffering` w obiekcie preferencje, aby `false`.

## <a name="related-topics"></a>Tematy pokrewne

[Dokumentacja biblioteki usługa Microsoft Speech C#](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
