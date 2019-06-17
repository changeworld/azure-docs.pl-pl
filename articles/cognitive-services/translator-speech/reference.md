---
title: Dokumentacja interfejsu API mowy usługi Translator
titleSuffix: Azure Cognitive Services
description: Dokumentacja interfejsu API tłumaczenia mowy.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3493f6d25461836d8f6e48ce4213b0f5b78b6372
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60539183"
---
# <a name="translator-speech-api"></a>Interfejs API tłumaczenia mowy w usłudze Translator

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Usługa ta oferuje interfejs API przesyłania strumieniowego do transkrypcja mowy konwersacji z jednego języka w tekst z innego języka. Interfejs API jest też zintegrowany możliwości zamiany tekstu na mowę w celu czytanie przetłumaczonego tekstu ponownie. Interfejs API mowy usługi Translator umożliwia scenariuszy, takich jak w czasie rzeczywistym tłumaczenie rozmowy w Skype Translator.

Za pomocą interfejsu API tłumaczenia mowy aplikacje klienckie przesyłanie strumieniowe audio mowy do usługi i ponownie otrzymywać strumień tekstowy wyniki, które obejmują rozpoznany tekst w języku źródła i ich tłumaczeniem w języku docelowym. Wyniki tekstowe są tworzone przez zastosowanie automatycznego rozpoznawania mowy (ASR, Automatic Speech Recognition) obsługiwanego przez głębokie sieci neuronowe do przychodzącego strumienia audio. Nowe techniki, aby lepiej odzwierciedlać intencji użytkownika o nazwie TrueText dodatkowo lepsza nieprzetworzone dane wyjściowe usługi ASR. Na przykład TrueText usuwa disfluencies (hmms i coughs) i znaki interpunkcyjne odpowiednie przywracania i wielkość liter. Oferuje ona również możliwość maskowania lub wykluczania przekleństw. Aparaty rozpoznawania i tłumaczenia są specjalnie uczone pod kątem obsługi rozmów. Usługa tłumaczenia mowy używa wyciszenia wykrywania, aby określić koniec wypowiedź. Po przerwie w użyciu głosu usługa strumieniowo prześle wynik końcowy dotyczący zakończonej wypowiedzi. Usługa może także wysyłać z powrotem wyniki częściowe, które umożliwiają pośrednie rozpoznawanie i tłumaczenie trwającej wypowiedzi. Dla wyników końcowych usługa zapewnia możliwość syntetyzowania mowy (zamiana tekstu na mowę) z tekstu mówionego w języku docelowym. W przypadku zamiany tekstu na mowę dźwięk jest tworzony w formacie określonym przez klienta. Dostępne formaty to WAV i MP3.

Interfejs API mowy usługi Translator korzysta z protokołu WebSocket, aby zapewnić komunikację pełnodupleksową kanał między klientem a serwerem. Aplikacja będzie wymagać następujące kroki, aby korzystać z niej:

## <a name="1-getting-started"></a>1. Wprowadzenie
Interfejs API tekstu usługi Translator, konieczne będzie dostęp do [konta na platformie Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Klucz subskrypcji można używać do uwierzytelniania. Interfejs API mowy usługi Translator obsługuje dwa tryby uwierzytelniania:

* **Przy użyciu tokenu dostępu:** W aplikacji należy uzyskać token dostępu z usługi tokenu. Klucz subskrypcji interfejsu API tłumaczenia mowy umożliwia uzyskanie tokenu dostępu usługi uwierzytelniania usług Azure Cognitive Services. Token dostępu jest ważny przez 10 minut. Uzyskaj nowy token dostępu co 10 minut i Zachowaj przy użyciu tego samego dostępu tokenu dla powtarzanych żądań w ciągu tych 10 minut.

* **Bezpośrednio przy użyciu klucza subskrypcji:** W aplikacji, należy przekazać swój klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` nagłówka.

Klucz subskrypcji oraz token dostępu należy traktować jako wpisy tajne, które powinny być ukryte w widoku.

## <a name="3-query-languages"></a>3. Język zapytań
**Wyślij zapytanie do zasobu języków dla bieżącego zestawu języków.** [Zasobów języków](languages-reference.md) udostępnia zbiór języków i dostępnych funkcji rozpoznawania mowy, tłumaczenie tekstu i zamiany tekstu na mowę głosów. Każdy język lub głosowych otrzymuje identyfikator, który interfejs API mowy usługi Translator używa do identyfikacji tego samego języka lub głosowych.

## <a name="4-stream-audio"></a>4. Stream audio
**Otwórz połączenie i rozpocząć przesyłanie strumieniowe audio do usługi.** Adres URL usługi jest `wss://dev.microsofttranslator.com/speech/translate`. Parametry i formaty dźwięku oczekiwano przez usługę są opisane poniżej, w `/speech/translate` operacji. Jeden z parametrów jest używany do przekazywania tokenu dostępu z kroku 2 powyżej.

## <a name="5-process-the-results"></a>5. Przetwarzanie wyników
**Przetwarzanie wyników przesyłane strumieniowo z powrotem z usługi.** Format wyników częściowych, wyniki końcowe i zamiany tekstu na mowę segmentów audio są opisane w dokumentacji `/speech/translate` operacji poniżej.

Przykłady kodu, demonstrując korzystanie z interfejsu API mowy usługi Translator są dostępne z [witryny GitHub w usłudze Translator firmy Microsoft](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

Pobierz /speech/translate Establishes sesji do tłumaczenia mowy

### <a name="connecting"></a>Łączenie
Przed nawiązaniem połączenia z usługą, przejrzyj listę parametrów podanych w dalszej części w tej sekcji. Przykładowe żądanie jest:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Żądania określa angielski prowadzone są przesyłane strumieniowo do usługi i przetłumaczyć włoski. Każdy wynik końcowy rozpoznawania wygeneruje odpowiedzi audio tekstu na mowę przy użyciu głosu żeńskiego o nazwie Elsa. Zwróć uwagę, że żądanie poświadczeń w `Ocp-Apim-Subscription-Key header`. Żądanie następuje również najlepszym rozwiązaniem, ustawiając globalnie unikatowy identyfikator w nagłówku `X-ClientTraceId`. Aplikacja kliencka powinni wylogować identyfikator śledzenia, dzięki czemu może służyć do rozwiązywania problemów w momencie ich wystąpienia.

### <a name="sending-audio"></a>Wysyłanie dźwięku
Po nawiązaniu połączenia klienta rozpoczyna strumieniowe przesyłanie audio do usługi. Klient wysyła audio we fragmentach. Każdego fragmentu są przesyłane przy użyciu protokołu Websocket komunikacie typu Binary.

Dane wejściowe audio jest w formacie pliku Audio fali (WAVE lub więcej powszechnie znane jako WAV, ponieważ jego rozszerzenie nazwy pliku). Aplikacja kliencka powinna strumienia pojedynczy kanał, podpisem 16-bitowych formacie PCM próbkowanych zgodnie 16 kHz. Pierwszy zestaw bajtów przesyłanych strumieniowo przez klienta będzie zawierać nagłówek WAV. Nagłówek 44 bajt pojedynczy kanał podpisany 16 strumienia PCM bit próbkowanych zgodnie 16 kHz jest:

|Offset|Wartość|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

Należy zauważyć, że rozmiar pliku (w bajtach 4 – 7) i rozmiar "data" (w bajtach 40-43) są ustawione na zero. Scenariusz transmisji strumieniowej, których łączny rozmiar jest nie zawsze są znane z wyprzedzeniem to OK.

Po wysłaniu nagłówka WAV (RIFF), klient wysyła fragmenty danych audio. Klient będzie zazwyczaj strumienia fragmenty o stałym rozmiarze reprezentujący stały czas trwania (np. strumień 100 MS audio w danym momencie).

### <a name="signal-the-end-of-the-utterance"></a>Sygnał koniec wypowiedź
Interfejs API mowy usługi Translator zwraca transkrypcja i tłumaczenie strumienia audio wysyłasz audio. Końcowe transkrypcji, ostateczne tłumaczenie i dźwięk przetłumaczone zostanie zwrócona do Ciebie, dopiero po upływie wypowiedź. W niektórych przypadkach można wymusić koniec wypowiedź. Wyślij 2,5 sekund wyciszenia, aby wymusić koniec wypowiedź.

### <a name="final-result"></a>wynik końcowy
Wynik rozpoznawania mowy jest generowany na końcu wypowiedź. Wynik jest przesyłane z usługi przez klienta za pomocą protokołu WebSocket komunikat typu Text. Zawartość komunikatu jest serializację ciągu JSON obiektu z następującymi właściwościami:

* `type`: Stała typu String do identyfikowania typu wyniku. Wartość jest ostatnim przypadku wyników końcowych.
* `id`: Ciąg identyfikator przypisany do wyniku rozpoznawania.
* `recognition`: Rozpoznany tekst w języku źródła. Tekst może być pustym ciągiem w przypadku uznania false.
* `translation`: Rozpoznany tekst przetłumaczony na język docelowy.
* `audioTimeOffset`: Przesunięcie czasu rozpoczęcia rozpoznawania w dziesięciomilionowych częściach sekundy (znaczników 1 = 100 nanosekund). To przesunięcie względem początku strumienia.
* `audioTimeSize`: Czas trwania w dziesięciomilionowych częściach sekundy (100 nanosekund) uznania.
* `audioStreamPosition`: Przesunięcie początku rozpoznawanie w bajtach. To przesunięcie względem początku strumienia.
* `audioSizeBytes`: Rozmiar w bajtach uznania.

Należy pamiętać, że rozpoznawanie na strumienia audio w układzie nie jest uwzględniony w wynikach domyślnie. `TimingInfo` Funkcji należy wybrać przez klienta (zobacz `features` parametru).

Przykładowy wynik końcowy jest następująca:

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

### <a name="partial-result"></a>Częściowy wynik
Wyniki rozpoznawania mowy pośrednie lub częściowe nie są przesyłane strumieniowo do klienta domyślnie. Klient może używać parametru zapytania funkcji, aby zażądać ich.

Częściowy wynik są przesyłane z usługi przez klienta za pomocą protokołu WebSocket komunikat typu Text. Zawartość komunikatu jest serializację ciągu JSON obiektu z następującymi właściwościami:

* `type`: Stała typu String do identyfikowania typu wyniku. Wartość jest częściowe wyniki częściowe.
* `id`: Ciąg identyfikator przypisany do wyniku rozpoznawania.
* `recognition`: Rozpoznany tekst w języku źródła.
* `translation`: Rozpoznany tekst przetłumaczony na język docelowy.
* `audioTimeOffset`: Przesunięcie czasu rozpoczęcia rozpoznawania w dziesięciomilionowych częściach sekundy (znaczników 1 = 100 nanosekund). To przesunięcie względem początku strumienia.
* `audioTimeSize`: Czas trwania w dziesięciomilionowych częściach sekundy (100 nanosekund) uznania.
* `audioStreamPosition`: Przesunięcie początku rozpoznawanie w bajtach. To przesunięcie względem początku strumienia.
* `audioSizeBytes`: Rozmiar w bajtach uznania.

Należy pamiętać, że rozpoznawanie na strumienia audio w układzie nie jest uwzględniony w wynikach domyślnie. Należy wybrać funkcję TimingInfo przez klienta (patrz parametr funkcji).

Przykładowy wynik końcowy jest następująca:

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
Po włączeniu funkcji zamiany tekstu na mowę (zobacz `features` parametr poniżej), wynik końcowy następuje dźwięk mówiony przetłumaczonego tekstu. Dane audio są fragmentaryczne i wysyłane z usługi do klienta jako sekwencję wiadomości protokołu Websocket typu Binary. Klient może wykrywać koniec strumienia, sprawdzając bit FIN każdej wiadomości. Ostatni komunikat binarne mają swój zestaw bitów w wynikach wyszukiwania do jednego, aby wskazać koniec strumienia. Format strumienia zależy od wartości `format` parametru.

### <a name="closing-the-connection"></a>Zamykanie połączenia
Jeśli aplikacja kliencka zostało zakończone, przesyłanie strumieniowe audio, otrzymał ostatni wynik końcowy go zamknąć połączenie, zainicjuj uzgadniania zamknięcia protokołu WebSocket. Istnieją warunki, które spowoduje, że serwer zakończyć połączenie. Następujące kody zamknięcia protokołu WebSocket mogą pojawić się przez klienta:

* `1003 - Invalid Message Type`: Serwer zostanie zakończone połączenia, ponieważ nie można zaakceptować, typ danych, który otrzymał. Dzieje się tak często, gdy przychodzący audio nie rozpoczyna się od odpowiedniego nagłówka.
* `1000 - Normal closure`: Połączenie zostało zamknięte po żądanie zostało spełnione. Serwer zamknie połączenie: po otrzymaniu bez dźwięku z klienta przez dłuższy czas; gdy wyciszenia jest przesyłany strumieniowo przez dłuższy czas; gdy sesja osiągnie maksymalny czas trwania dozwolone (około 90 minut).
* `1001 - Endpoint Unavailable`: Wskazuje, że serwer będzie niedostępny. Aplikacja kliencka może podejmować prób ponownego połączenia z limitem liczby ponownych prób.
* `1011 - Internal Server Error`: Połączenie zostanie zamknięte przez serwer, z powodu błędu na serwerze.

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:---|:---|:---|:---|:---|
|api-version|1.0|Wersja interfejsu API zażądane przez klienta. Dozwolone wartości to: `1.0`.|query   |string|
|from|(pusty)   |Określa język przychodzących mowy. Wartość jest jeden z identyfikatorów języka z `speech` zakresu w odpowiedzi z interfejsu API języków.|query|string|
|na|(pusty)|Określa język do tłumaczenia uzyskanego tekstu do. Wartość jest jeden z identyfikatorów języka z `text` zakresu w odpowiedzi z interfejsu API języków.|query|string|
|danych|(pusty)   |Rozdzielana przecinkami zestaw funkcji wybierane przez klienta. Dostępne funkcje obejmują:<ul><li>`TextToSpeech`: Określa, że usługa musi zwracać przetłumaczone audio końcowego tłumaczeniem.</li><li>`Partial`: Określa, usługa musi zwracać wyniki pośrednie rozpoznawania podczas audio jest przesyłanie strumieniowe do usługi.</li><li>`TimingInfo`: Określa, czy usługa musi zwrócić informacje o czasie skojarzonych z każdym rozpoznawania.</li></ul>Na przykład określić klienta `features=partial,texttospeech` do odbierania wyników częściowych i zamiany tekstu na mowę, ale nie informacje o czasie. Należy zauważyć, że wyniki końcowe są zawsze przesyłane strumieniowo do klienta.|query|string|
|Głos|(pusty)|Określa, jakie głosowe na potrzeby zamiany tekstu na mowę renderowania przetłumaczonego tekstu. Wartość jest jeden z identyfikatorów głosowej z zakresu tts w odpowiedzi z interfejsu API języków. Jeśli nie określono głosu, że system będzie automatycznie wybierz jedną, po włączeniu funkcji zamiany tekstu na mowę.|query|string|
|format|(pusty)|Określa format strumienia audio tekstu na mowę, zwracane przez usługę. Dostępne opcje:<ul><li>`audio/wav`: Strumień audio fali. Powinien używać klient do nagłówka WAV poprawnie interpretować audio format. WAV audio tekstu na mowę, jest 16-bitowych, pojedynczy kanał PCM o częstotliwości próbkowania 24kHz lub 16kHz.</li><li>`audio/mp3`: Strumień audio MP3.</li></ul>Wartość domyślna to `audio/wav`.|query|string|
|ProfanityAction    |(pusty)    |Określa, jak usługa powinna obsługiwać profanities rozpoznawany w mowy. Prawidłowych akcji to:<ul><li>`NoAction`: Profanities są pozostawiane się.</li><li>`Marked`: Profanities są zastępowane znacznik. Zobacz `ProfanityMarker` parametru.</li><li>`Deleted`: Profanities są usuwane. Na przykład jeśli słowo `"jackass"` jest traktowany jako wulgaryzmów frazę `"He is a jackass."` staną się `"He is a .".`</li></ul>Wartość domyślna jest oznaczona.|query|string|
|ProfanityMarker|(pusty)    |Określa, jak wykryte profanities są obsługiwane, gdy `ProfanityAction` ustawiono `Marked`. Prawidłowe opcje to:<ul><li>`Asterisk`: Profanities są zastępowane parametrami `***`. Na przykład jeśli słowo `"jackass"` jest traktowany jako wulgaryzmów frazę `"He is a jackass."` staną się `"He is a ***.".`</li><li>`Tag`: Wulgaryzmów są ujęte w tagu XML wulgaryzmów. Na przykład jeśli słowo `"jackass"` jest traktowany jako wulgaryzmów frazę `"He is a jackass."` staną się `"He is a <profanity>jackass</profanity>."`.</li></ul>Wartość domyślna to `Asterisk`.|query|string|
|Autoryzacja|(pusty)  |Określa wartość klienta tokenu elementu nośnego. Użyj prefiksu `Bearer` następuje wartość `access_token` wartości zwracanej przez usługę tokenu uwierzytelniania.|nagłówek   |string|
|OCP-Apim-Subscription-Key|(pusty)|Jeśli wymagane `Authorization` nagłówka nie jest określony.|nagłówek|string|
|access_token|(pusty)   |Alternatywny sposób przekazania prawidłowy token dostępu OAuth. Token elementu nośnego znajduje się zwykle z nagłówkiem `Authorization`. Niektóre biblioteki websocket nie zezwalają na kod klienta, aby ustawić nagłówki. W takim przypadku klient może używać `access_token` parametr do przekazania prawidłowy token zapytania. Podczas używania tokenu dostępu na potrzeby uwierzytelniania, jeśli `Authorization` nagłówka nie ustawiono, następnie `access_token` musi być ustawiona. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany. Aby przekazać token klientów należy używać tylko jednej metody.|query|string|
|subscription-key|(pusty)   |Alternatywny sposób polega na przekazaniu klucza subskrypcji. Niektóre biblioteki websocket nie zezwalają na kod klienta, aby ustawić nagłówki. W takim przypadku klient może używać `subscription-key` zapytania parametr do przekazania klucza ważnej subskrypcji. Jeśli klucz subskrypcji w celu uwierzytelnienia `Ocp-Apim-Subscription-Key` nie ustawiono nagłówka, a następnie należy ustawić klucz subskrypcji. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany. Klientów do przekazania należy używać tylko jedna metoda `subscription key`.|query|string|
|X-ClientTraceId    |(pusty)    |Identyfikator GUID generowany przez klienta umożliwia śledzenie żądań. Aby odpowiednie informacje o rozwiązywaniu problemów, klientów należy podać nową wartość z każdym żądaniem i ją.<br/>Zamiast korzystać z nagłówkiem, ta wartość może być przekazywany przy użyciu parametru zapytania `X-ClientTraceId`. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany.|nagłówek|string|
|X-CorrelationId|(pusty)    |Generowane przez klienta identyfikator korelacji wielu kanałów komunikacji. Aby umożliwić rozmowy między użytkownikami można tworzyć wiele sesji tłumaczenia mowy. W taki scenariusz wszystkie sesje tłumaczenia mowy, należy użyć tego samego Identyfikatora korelacji powiązanie kanałów. To ułatwia śledzenie i Diagnostyka. Identyfikator powinna być zgodna: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Zamiast korzystać z nagłówkiem, ta wartość może być przekazywany przy użyciu parametru zapytania `X-CorrelationId`. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany.|nagłówek|string|
|X ClientVersion|(pusty)    |Identyfikuje wersję aplikacji klienckiej. Przykład: "2.1.0.123".<br/>Zamiast korzystać z nagłówkiem, ta wartość może być przekazywany przy użyciu parametru zapytania `X-ClientVersion`. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany.|nagłówek|string|
|X-OsPlatform|(pusty)   |Określa nazwę i wersję systemu operacyjnego, który aplikacja kliencka jest uruchomiona na. Przykłady: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Zamiast korzystać z nagłówkiem, ta wartość może być przekazywany przy użyciu parametru zapytania `X-OsPlatform`. Jeśli parametr zapytania i nagłówków są skonfigurowane, parametr zapytania jest ignorowany.|nagłówek|string|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Reason|Response Model|Nagłówki|
|:--|:--|:--|:--|
|101    |Uaktualnianie protokołu WebSocket.|Model Przykładowa wartość <br/> obiekt {}|X-RequestId<br/>Wartość identyfikowanie żądania na potrzeby rozwiązywania problemów.<br/>string|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są one prawidłowe. Obiekt odpowiedzi zawiera bardziej szczegółowy opis błędu.|||
|401    |Brak autoryzacji. Upewnij się, że poświadczenia są, czy są prawidłowe i czy subskrypcja Azure rynek danych jest w dobrym stanie, za pomocą dostępnego salda.|||
|500    |Wystąpił błąd. Jeśli błąd będzie się powtarzać, raportowania identyfikator śledzenia klienta (X-ClientTraceId) lub identyfikator (identyfikator żądania X) żądania.|||
|503    |Serwer jest tymczasowo niedostępny. Ponów próbę żądania. Jeśli błąd będzie się powtarzać, raportowania identyfikator śledzenia klienta (X-ClientTraceId) lub identyfikator (identyfikator żądania X) żądania.|||
