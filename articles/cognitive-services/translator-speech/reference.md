---
title: Informacje interfejs API tłumaczenia mowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Dokumentacja referencyjna interfejs API tłumaczenia mowy w usłudze Translator.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9d2f78d05de6b966dd872e0b57a90d1c8e890975
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965369"
---
# <a name="translator-speech-api"></a>Interfejs API tłumaczenia mowy w usłudze Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Ta usługa oferuje interfejs API przesyłania strumieniowego do transkrypcja rozmowy głosowej z jednego języka do tekstu innego języka. Interfejs API jest też zintegrowany możliwości zamiany tekstu na mowę w celu czytanie przetłumaczonego tekstu ponownie. Interfejs API tłumaczenia mowy w usłudze Translator umożliwia scenariusze, takie jak tłumaczenie konwersacji w czasie rzeczywistym, jak w usłudze Skype Translator.

Dzięki interfejs API tłumaczenia mowy w usłudze Translator aplikacje klienckie przesyłają strumieniowo dźwięk mowy do usługi i odbierają strumienie wyników tekstowych, które zawierają rozpoznany tekst w języku źródłowym i jego tłumaczenie w języku docelowym. Wyniki tekstowe są tworzone przez zastosowanie automatycznego rozpoznawania mowy (ASR, Automatic Speech Recognition) obsługiwanego przez głębokie sieci neuronowe do przychodzącego strumienia audio. Pierwotne dane wyjściowe usługi ASR zostały jeszcze udoskonalone przez nową technikę o nazwie TrueText, aby dokładniej odzwierciedlić intencje użytkownika. Na przykład TrueText usuwa disfluencies (hmms i coughs) i przywraca odpowiednie znaki interpunkcyjne i wielkie litery. Oferuje ona również możliwość maskowania lub wykluczania przekleństw. Aparaty rozpoznawania i tłumaczenia są specjalnie uczone pod kątem obsługi rozmów. Usługa tłumaczenia mowy używa wykrywania ciszi do określenia końca wypowiedź. Po przerwie w użyciu głosu usługa strumieniowo prześle wynik końcowy dotyczący zakończonej wypowiedzi. Usługa może także wysyłać z powrotem wyniki częściowe, które umożliwiają pośrednie rozpoznawanie i tłumaczenie trwającej wypowiedzi. W przypadku końcowych wyników usługa zapewnia możliwość syntezy mowy (zamiany tekstu na mowę) z mówionego tekstu w języku docelowym. W przypadku zamiany tekstu na mowę dźwięk jest tworzony w formacie określonym przez klienta. Dostępne formaty to WAV i MP3.

Interfejs API tłumaczenia mowy w usłudze Translator korzysta z protokołu WebSocket, aby zapewnić kanał komunikacji pełny dupleks między klientem a serwerem. Aby można było korzystać z usługi, aplikacja będzie wymagać następujących czynności:

## <a name="1-getting-started"></a>1. Wprowadzenie
Aby uzyskać dostęp do interfejs API tłumaczenia tekstu w usłudze Translator konieczne będzie [zarejestrowanie się w usłudze Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Użyj klucza subskrypcji do uwierzytelnienia. Interfejs API tłumaczenia mowy w usłudze Translator obsługuje dwa tryby uwierzytelniania:

* **Przy użyciu tokenu dostępu:** W aplikacji Uzyskaj token dostępu z usługi tokenu. Użyj klucza subskrypcji interfejs API tłumaczenia mowy w usłudze Translator, aby uzyskać token dostępu z usługi Azure Cognitive Services Authentication. Token dostępu jest ważny przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i Kontynuuj używanie tego samego tokenu dostępu dla powtarzanych żądań w ciągu 10 minut.

* **Bezpośrednie używanie klucza subskrypcji:** W aplikacji Przekaż swój klucz subskrypcji jako wartość w `Ocp-Apim-Subscription-Key` nagłówku.

Traktuj klucz subskrypcji i token dostępu jako wpisy tajne, które powinny być ukryte przed wyświetleniem.

## <a name="3-query-languages"></a>3. Języki zapytań
**Wykonaj zapytanie o zasób języków dla bieżącego zestawu obsługiwanych języków.** [Zasoby języków](languages-reference.md) udostępniają zestaw języków i głosów dostępnych do rozpoznawania mowy w przypadku tłumaczenia tekstu oraz zamiany tekstu na mowę. Każdy język lub głos otrzymuje identyfikator, którego interfejs API tłumaczenia mowy w usłudze Translator używa do identyfikowania tego samego języka lub głosu.

## <a name="4-stream-audio"></a>4. Strumień audio
**Otwórz połączenie i Rozpocznij przesyłanie strumieniowe audio do usługi.** Adres URL usługi to `wss://dev.microsofttranslator.com/speech/translate`. Parametry i formaty audio oczekiwane przez usługę są opisane poniżej w `/speech/translate` operacji. Jeden z parametrów jest używany do przekazywania tokenu dostępu z kroku 2 powyżej.

## <a name="5-process-the-results"></a>5. Przetwarzanie wyników
**Przetwórz wyniki przesyłane strumieniowo z usługi.** Format wyników częściowych, wyników końcowych i segmentów audio zamiany tekstu na mowę opisano w dokumentacji `/speech/translate` operacji poniżej.

Przykłady kodu ilustrujące korzystanie z interfejs API tłumaczenia mowy w usłudze Translator są dostępne w [witrynie GitHub usługi Microsoft Translator](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

GET/Speech/translate nawiązuje sesję na potrzeby tłumaczenia mowy

### <a name="connecting"></a>Łączenie
Przed nawiązaniem połączenia z usługą Przejrzyj listę parametrów podaną w dalszej części tej sekcji. Przykładowe żądanie:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Żądanie określa, że wypowiadane angielskie zostanie przesłane do usługi i przetłumaczone na włoski. Każdy końcowy wynik rozpoznawania generuje odpowiedź dźwiękową typu text-to-Speech z głosem żeńskim o nazwie Elsa. Zwróć uwagę, że żądanie zawiera poświadczenia w `Ocp-Apim-Subscription-Key header`. Żądanie jest również zgodne z najlepszymi rozwiązaniami, ustawiając globalnie unikatowy identyfikator w `X-ClientTraceId`nagłówku. Aplikacja kliencka powinna rejestrować identyfikator śledzenia, aby można było go użyć do rozwiązywania problemów, gdy wystąpią.

### <a name="sending-audio"></a>Wysyłanie audio
Po nawiązaniu połączenia klient zacznie przesyłać strumieniowo dźwięk do usługi. Klient wysyła dźwięk w fragmentach. Każdy fragment jest przesyłany przy użyciu komunikatu protokołu WebSocket typu binary.

Wejście audio jest w formacie dźwięku wave (WAVE lub częściej nazywanej WAV ze względu na rozszerzenie nazwy pliku). Aplikacja kliencka powinna przesyłać strumieniowo jeden kanał, podpisaną z 16bitm audio PCM z 16 kHz. Pierwszy zestaw bajtów przesyłanych strumieniowo przez klienta będzie zawierać nagłówek WAV. Nagłówek 44-bajtowy dla pojedynczego kanału z 16-bitowym strumieńem PCM próbkowany o 16 kHz:

|Offset|Value|
|:---|:---|
|0 - 3|RIFF|
|4 - 7|0|
|8 - 11|DŹWIĘK|
|12 - 15|FMT|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 – 33|2|
|34 – 35|16|
|36 – 39|Data|
|40 – 43|0|

Należy zauważyć, że całkowity rozmiar pliku (bajty 4-7) i rozmiar "dane" (bajty 40-43) są ustawione na wartość zero. Jest to prawidłowe dla scenariusza przesyłania strumieniowego, w którym łączny rozmiar nie jest koniecznie znany z góry.

Po wysłaniu nagłówka WAV (RIFF) klient wysyła fragmenty danych audio. Klient będzie zazwyczaj miał fragmenty o ustalonym rozmiarze strumienia reprezentujące stały czas trwania (np. strumień 100 ms audio w danym momencie).

### <a name="signal-the-end-of-the-utterance"></a>Sygnalizowanie końca wypowiedź
Interfejs API tłumaczenia mowy w usłudze Translator zwraca transkrypcję i tłumaczenie strumienia audio w trakcie wysyłania dźwięku. Ostateczne Transkrypcja, końcowe tłumaczenie i przetłumaczone audio zostaną zwrócone do Ciebie dopiero po zakończeniu wypowiedź. W niektórych przypadkach warto wymusić zakończenie wypowiedź. Aby wymusić koniec wypowiedź, należy wysłać 2,5 sekund cisza.

### <a name="final-result"></a>Wynik końcowy
Końcowy wynik rozpoznawania mowy jest generowany na końcu wypowiedź. Wynik jest przesyłany z usługi do klienta przy użyciu komunikatu protokołu WebSocket typu Text. Zawartość komunikatu to Serializacja JSON obiektu o następujących właściwościach:

* `type`: Stała ciąg identyfikująca typ wyniku. Wartość jest końcowa dla końcowych wyników.
* `id`: Identyfikator ciągu przypisany do wyniku rozpoznawania.
* `recognition`: Rozpoznany tekst w języku źródłowym. Tekst może być pustym ciągiem w przypadku fałszywego rozpoznawania.
* `translation`: Rozpoznany tekst przetłumaczony w języku docelowym.
* `audioTimeOffset`: Przesunięcie czasu rozpoczęcia rozpoznawania w taktach (1 takt = 100 nanosekund). Przesunięcie jest względem początku przesyłania strumieniowego.
* `audioTimeSize`: Czas trwania w taktach (100 nanosekund) rozpoznawania.
* `audioStreamPosition`: Przesunięcie bajtu początku rozpoznawania. Przesunięcie jest względem początku strumienia.
* `audioSizeBytes`: Rozmiar w bajtach rozpoznawania.

Należy zauważyć, że rozmieszczenie rozpoznawania w strumieniu audio nie jest domyślnie uwzględniane w wynikach. Ta `TimingInfo` funkcja musi zostać wybrana przez klienta (patrz `features` parametr).

Przykładowy wynik końcowy jest następujący:

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Wynik częściowy
Częściowe lub pośrednie wyniki rozpoznawania mowy nie są domyślnie przesyłane strumieniowo do klienta. Klient może użyć parametru query Features, aby je zażądać.

Częściowe wyniki są przesyłane z usługi do klienta przy użyciu komunikatu protokołu WebSocket typu Text. Zawartość komunikatu to Serializacja JSON obiektu o następujących właściwościach:

* `type`: Stała ciąg identyfikująca typ wyniku. Wartość jest częściowa dla wyników częściowych.
* `id`: Identyfikator ciągu przypisany do wyniku rozpoznawania.
* `recognition`: Rozpoznany tekst w języku źródłowym.
* `translation`: Rozpoznany tekst przetłumaczony w języku docelowym.
* `audioTimeOffset`: Przesunięcie czasu rozpoczęcia rozpoznawania w taktach (1 takt = 100 nanosekund). Przesunięcie jest względem początku przesyłania strumieniowego.
* `audioTimeSize`: Czas trwania w taktach (100 nanosekund) rozpoznawania.
* `audioStreamPosition`: Przesunięcie bajtu początku rozpoznawania. Przesunięcie jest względem początku strumienia.
* `audioSizeBytes`: Rozmiar w bajtach rozpoznawania.

Należy zauważyć, że rozmieszczenie rozpoznawania w strumieniu audio nie jest domyślnie uwzględniane w wynikach. Funkcja TimingInfo musi zostać wybrana przez klienta (patrz Features Parameter).

Przykładowy wynik końcowy jest następujący:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Zamiana tekstu na mowę
Gdy funkcja zamiany tekstu na mowę jest włączona (patrz `features` parametr poniżej), końcowym wynikiem jest dźwięk wypowiadanego tekstu. Dane audio są fragmenty i wysyłane z usługi do klienta jako sekwencja komunikatów protokołu WebSocket typu binary. Klient może wykryć koniec strumienia, sprawdzając bit FIN poszczególnych komunikatów. Ostatni komunikat binarny będzie miał swój bit FIN ustawiony na wartość one, aby wskazać koniec strumienia. Format strumienia zależy od wartości `format` parametru.

### <a name="closing-the-connection"></a>Zamykanie połączenia
Gdy aplikacja kliencka zakończyła przesyłanie strumieniowe audio i otrzymała ostatni wynik końcowy, należy zamknąć połączenie przez zainicjowanie uzgadniania zamknięcia protokołu WebSocket. Istnieją warunki, które spowodują przerwanie połączenia serwera. Klient może odbierać następujące kody zamknięte przez protokół WebSocket:

* `1003 - Invalid Message Type`: Serwer przerywa połączenie, ponieważ nie może zaakceptować otrzymanego typu danych. Zwykle zdarza się to, gdy przychodzące audio nie zaczyna się od prawidłowego nagłówka.
* `1000 - Normal closure`: Połączenie zostało zamknięte po spełnieniu żądania. Serwer zamknie połączenie: w przypadku braku dźwięku od klienta przez dłuższy czas; gdy wyciszenie jest przesyłane strumieniowo przez dłuższy czas; gdy sesja osiągnie maksymalny dozwolony czas trwania (około 90 minut).
* `1001 - Endpoint Unavailable`: Wskazuje, że serwer stanie się niedostępny. Aplikacja kliencka może próbować ponownie nawiązać połączenie z limitem liczby ponownych prób.
* `1011 - Internal Server Error`: Połączenie zostanie zamknięte przez serwer z powodu błędu na serwerze.

### <a name="parameters"></a>Parametry

|Parametr|Value|Opis|Typ parametru|Typ danych|
|:---|:---|:---|:---|:---|
|api-version|1.0|Wersja interfejsu API żądana przez klienta. Dozwolone wartości to: `1.0`.|query   |ciąg|
|from|ciągiem   |Określa język przychodzącej mowy. Wartość jest jednym z identyfikatorów języka z `speech` zakresu w odpowiedzi z interfejsu API języków.|query|ciąg|
|to|ciągiem|Określa język, w którym ma zostać przetłumaczony tekst uzyskanego. Wartość jest jednym z identyfikatorów języka z `text` zakresu w odpowiedzi z interfejsu API języków.|query|ciąg|
|danych|ciągiem   |Rozdzielany przecinkami zestaw funkcji wybranych przez klienta. Dostępne funkcje to m.in.:<ul><li>`TextToSpeech`: określa, że usługa musi zwrócić przetłumaczony dźwięk końcowego zdania przetłumaczonego.</li><li>`Partial`: określa, że usługa musi zwracać wyniki rozpoznawania pośredniego, gdy dźwięk jest przesyłany strumieniowo do usługi.</li><li>`TimingInfo`: określa, że usługa musi zwracać informacje o chronometrażu związane z każdym rozpoznawaniem.</li></ul>Na przykład klient powinien określić `features=partial,texttospeech` , że będą otrzymywać częściowe wyniki i zamiany tekstu na mowę, ale bez informacji o chronometrażu. Zwróć uwagę, że końcowe wyniki są zawsze przesyłane strumieniowo do klienta.|query|ciąg|
|Rozmów|ciągiem|Identyfikuje głos używany do renderowania tekstu na mowę przetłumaczonego tekstu. Wartość jest jednym z identyfikatorów głosu z zakresu TTS w odpowiedzi z interfejsu API języków. Jeśli nie określono głosu, system zostanie automatycznie wybrany po włączeniu funkcji zamiany tekstu na mowę.|query|ciąg|
|format|ciągiem|Określa format strumienia audio zamiany tekstu na mowę zwracanego przez usługę. Dostępne opcje:<ul><li>`audio/wav`: Strumień audio Wave. Aby poprawnie zinterpretować format audio, klient powinien użyć nagłówka WAV. Dźwięk WAV dla zamiany tekstu na mowę to 16-bitowy, pojedynczy kanał PCM z częstotliwością próbkowania wynoszącą 24kHz lub 16kHz.</li><li>`audio/mp3`: Strumień audio MP3.</li></ul>Wartość domyślna to `audio/wav`.|query|ciąg|
|ProfanityAction    |ciągiem    |Określa, w jaki sposób usługa powinna obsługiwać wulgarne informacje rozpoznawane w mowę. Prawidłowe akcje to:<ul><li>`NoAction`: Wulgarne są pozostawione w takiej postaci, w jakiej są.</li><li>`Marked`: Wulgarne są zastępowane znacznikiem. Zobacz `ProfanityMarker` parametr.</li><li>`Deleted`: Usunięto wulgarne. Na przykład, jeśli wyraz `"jackass"` jest traktowany jako wulgarny, fraza `"He is a jackass."` stanie się`"He is a .".`</li></ul>Wartość domyślna to oznaczona.|query|ciąg|
|ProfanityMarker|ciągiem    |Określa, jak wykryte wulgarne są obsługiwane `ProfanityAction` , gdy jest `Marked`ustawiony na. Prawidłowe opcje to:<ul><li>`Asterisk`: Wulgarne są zastępowane ciągiem `***`. Na przykład, jeśli wyraz `"jackass"` jest traktowany jako wulgarny, fraza `"He is a jackass."` stanie się`"He is a ***.".`</li><li>`Tag`: Brak znaku w kodzie XML. Na przykład, jeśli wyraz `"jackass"` jest traktowany jako wulgarny, fraza `"He is a jackass."` stanie się `"He is a <profanity>jackass</profanity>."`.</li></ul>Wartość domyślna to `Asterisk`.|query|ciąg|
|Authorization|ciągiem  |Określa wartość tokenu okaziciela klienta. Użyj prefiksu `Bearer` , po którym następuje wartość `access_token` zwrócona przez usługę tokenu uwierzytelniania.|nagłówek   |ciąg|
|OCP-Apim-Subscription-Key|ciągiem|Wymagane, `Authorization` Jeśli nagłówek nie został określony.|nagłówek|ciąg|
|access_token|ciągiem   |Alternatywny sposób przekazania prawidłowego tokenu dostępu OAuth. Token okaziciela jest zwykle dostarczany z nagłówkiem `Authorization`. Niektóre biblioteki WebSocket nie zezwalają kodowi klienta na ustawianie nagłówków. W takim przypadku klient może użyć parametru zapytania, `access_token` aby przekazać prawidłowy token. W przypadku użycia tokenu dostępu do uwierzytelniania, jeśli `Authorization` nagłówek nie jest ustawiony `access_token` , należy ustawić wartość. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany. Klienci powinni używać tylko jednej metody do przekazywania tokenu.|query|ciąg|
|klucz subskrypcji|ciągiem   |Alternatywny sposób przekazywania klucza subskrypcji. Niektóre biblioteki WebSocket nie zezwalają kodowi klienta na ustawianie nagłówków. W takim przypadku klient może użyć parametru zapytania, `subscription-key` aby przekazać prawidłowy klucz subskrypcji. W przypadku korzystania z klucza subskrypcji do uwierzytelniania, `Ocp-Apim-Subscription-Key` Jeśli nagłówek nie jest ustawiony, należy ustawić klucz subskrypcji. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany. Klienci powinni używać tylko jednej metody do przekazywania `subscription key`.|query|ciąg|
|X-ClientTraceId    |ciągiem    |Wygenerowany przez klienta identyfikator GUID służący do śledzenia żądania. W celu prawidłowego rozwiązywania problemów klienci powinni podać nową wartość przy każdym żądaniu i zalogować się.<br/>Zamiast korzystać z nagłówka, tę wartość można przesłać za pomocą parametru `X-ClientTraceId`zapytania. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany.|nagłówek|ciąg|
|X-CorrelationId|ciągiem    |Wygenerowany przez klienta identyfikator używany do skorelowania wielu kanałów w konwersacji. Można utworzyć wiele sesji tłumaczenia mowy, aby umożliwić konwersację między użytkownikami. W takim scenariuszu wszystkie sesje tłumaczenia mowy używają tego samego identyfikatora korelacji do łączenia kanałów. Ułatwia to śledzenie i diagnostykę. Identyfikator powinien być zgodny z:`^[a-zA-Z0-9-_.]{1,64}$`<br/>Zamiast korzystać z nagłówka, tę wartość można przesłać za pomocą parametru `X-CorrelationId`zapytania. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany.|nagłówek|ciąg|
|X-ClientVersion|ciągiem    |Identyfikuje wersję aplikacji klienckiej. Przykład: "2.1.0.123".<br/>Zamiast korzystać z nagłówka, tę wartość można przesłać za pomocą parametru `X-ClientVersion`zapytania. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany.|nagłówek|ciąg|
|X-OsPlatform|ciągiem   |Określa nazwę i wersję systemu operacyjnego, na którym działa aplikacja kliencka. Przykłady: "Android 5,0", "iOs 8.1.3", "Windows 8.1".<br/>Zamiast korzystać z nagłówka, tę wartość można przesłać za pomocą parametru `X-OsPlatform`zapytania. Jeśli ustawiono oba parametry nagłówka i kwerendy, parametr zapytania jest ignorowany.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|Model odpowiedzi|Nagłówki|
|:--|:--|:--|:--|
|101    |Uaktualnianie protokołu WebSocket.|Przykładowa wartość modelu <br/> Stream{}|X-RequestId<br/>Wartość identyfikująca żądanie na potrzeby rozwiązywania problemów.<br/>ciąg|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są prawidłowe. Obiekt Response zawiera bardziej szczegółowy opis błędu.|||
|401    |Próby. Upewnij się, że skonfigurowano poświadczenia, czy są one prawidłowe i że subskrypcja usługi Azure Data Market jest w dobrym stanie z dostępnym saldem.|||
|500    |Wystąpił błąd. Jeśli błąd będzie się powtarzać, zgłoś go za pomocą identyfikatora śledzenia klienta (X-ClientTraceId) lub identyfikatora żądania (X-Numer_id_żądania).|||
|503    |Serwer jest tymczasowo niedostępny. Spróbuj ponownie wykonać żądanie. Jeśli błąd będzie się powtarzać, zgłoś go za pomocą identyfikatora śledzenia klienta (X-ClientTraceId) lub identyfikatora żądania (X-Numer_id_żądania).|||
