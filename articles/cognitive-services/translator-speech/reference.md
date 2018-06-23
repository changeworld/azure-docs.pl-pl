---
title: Dokumentacja interfejsu API Microsoft Translator mowy | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: Dokumentacji interfejsu API usługi Microsoft Translator mowy.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349712"
---
# <a name="microsoft-translator-speech-api"></a>Interfejs API mowy usługi Microsoft Translator

Usługa ta oferuje interfejs API przesyłania strumieniowego wykonać transkrypcji mowy konwersacji z jednego języka na tekst innego języka. Interfejs API integruje się również mowę porozmawiać tłumaczenia ponownie. Interfejs API Microsoft Translator mowy umożliwia scenariuszy, takich jak w czasie rzeczywistym tłumaczenia konwersacji w Skype Translator.

Z interfejsem API mowy Translator firmy Microsoft aplikacje klienckie przesyłanie strumieniowe audio mowy do usługi i ponownie odbierać strumień tekstowy wyników, które obejmują rozpoznany języka źródłowego i jego tłumaczenia w języku docelowym. Wyniki tekstu są produkowane przez zastosowanie automatycznego rozpoznawania mowy (ASR) obsługiwane przez usługę sieci głębokość neuronowe do przychodzącego strumieniem audio. Nowe techniki o nazwie Tekst_prawdy w celu lepszego odzwierciedlenia zamiarów użytkownika dalsze poprawia nieprzetworzone dane wyjściowe funkcji automatycznego odzyskiwania systemu. Na przykład Tekst_prawdy usuwa disfluencies (hmms i coughs) i znaki interpunkcyjne prawidłowego przywracania i wielkość liter. Możliwość zamaskować lub wykluczyć profanities jest również uwzględniony. Aparaty rozpoznawania i translację są uczone specjalnie do obsługi mowy konwersacji. Usługi tłumaczenia mowy używa wyciszenia wykrywania w celu określenia koniec utterance. Po przerwie w działaniu głosowych usługa zostanie strumienia końcowego wyniku utterance ukończone. Usługa może także wysłać ponownie częściowe wyniki, które zapewnią uznania pośredniego, tłumaczenia utterance w toku. Dla wyników końcowych usługa zapewnia możliwość syntetyzowania mowy (tekst na mowę) z rozmowy tekstu w języku docelowym. Tekst na mowę audio jest tworzony w formacie określonym przez klienta. WAV i MP3 formaty są dostępne.

Interfejs API mowy Translator Microsoft wykorzystuje protokół WebSocket w celu zapewnienia kanał pełnego dupleksu komunikacji między klientem a serwerem. Aplikacja będzie wymagać te kroki, aby korzystać z usługi:

## <a name="1-getting-started"></a>1. Wprowadzenie
Translator tekst interfejs API Microsoft konieczne będzie dostęp do [tworzenia konta platformy Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Do uwierzytelniania za pomocą klawisza subskrypcji. Interfejs API Microsoft Translator mowy obsługuje dwa tryby uwierzytelniania:

* **Przy użyciu tokenu dostępu:** w aplikacji, należy uzyskać token dostępu z usługi tokenu. Uzyskać token dostępu z kognitywnych usługi uwierzytelniania przy użyciu klucza interfejsu API usługi Microsoft Translator mowy subskrypcji. Token dostępu jest ważny przez 10 minut. Uzyskać nowy token dostępu co 10 minut, a także zapewnić za pomocą dostępu do tego samego tokenu do ponownego żądania w ciągu tych 10 minut.

* **Bezpośrednio przy użyciu klucza subskrypcji:** w aplikacji, należy przekazać swój klucz subskrypcji jako wartość `Ocp-Apim-Subscription-Key` nagłówka.

Traktuj klucz subskrypcji i token dostępu jako klucze tajne, które powinny być ukrywane.

## <a name="3-query-languages"></a>3. Języki zapytania
**Wyślij zapytanie do zasobu języków dla bieżącego zestawu obsługiwanych języków.** [Zasobów języków](languages-reference.md) udostępnia zestaw języków i przekazanie rozpoznawania mowy tłumaczenie tekstu na mowę głosy. Każdego języka lub głosu podano identyfikator używający interfejsu API usługi Microsoft Translator mowy do identyfikowania tego samego języka lub głosu.

## <a name="4-stream-audio"></a>4. Strumień audio
**Otwórz połączenie i rozpocząć przesyłanie strumieniowe audio do usługi.** Adres URL usługi jest `wss://dev.microsofttranslator.com/speech/translate`. Parametry i formatów audio oczekiwane przez usługę są opisane poniżej, w `/speech/translate` operacji. Jeden z parametrów jest używany do przekazywania tokenu dostępu z kroku 2. powyżej.

## <a name="5-process-the-results"></a>5. Przetwarzanie wyników
**Przetwarzanie wyników przesyłane strumieniowo z usługi.** Format wyników częściowych, wyniki końcowe i zamiany tekstu na mowę segmentów audio są opisane w dokumentacji `/speech/translate` operacji poniżej.

Przykłady kodu prezentacja Użyj interfejsu API mowy Translator usługi Microsoft są dostępne w [witryny Microsoft Translator Github](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Uwagi dotyczące implementacji

Pobierz /speech/translate Establishes sesji do tłumaczenia mowy

### <a name="connecting"></a>Łączenie
Przed połączeniem się z usługą, zapoznaj się z listą parametrów później podanych w tej sekcji. Oto przykład żądania jest:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

Żądanie określa rozmowy angielski strumieniowo do usługi i przetłumaczyć włoski. Każdy wynik końcowy rozpoznawania wygeneruje z głosu żeńskiego o nazwie Elsa tekst na mowę odpowiedzi audio. Zwróć uwagę, że żądanie zawiera poświadczenia w `Ocp-Apim-Subscription-Key header`. Żądanie następuje także najlepszym rozwiązaniem przez ustawienie globalnie unikatowy identyfikator w nagłówku `X-ClientTraceId`. Aplikacja kliencka powinny być rejestrowane identyfikator śledzenia, dzięki czemu może służyć do rozwiązywania problemów w momencie wystąpienia.

### <a name="sending-audio"></a>Wysyłanie dźwięku
Po nawiązaniu połączenia klienta rozpocznie się przesyłanie strumieniowe audio do usługi. Klient wysyła audio w fragmentów. Każdego fragmentu są przesyłane przy użyciu protokołu Websocket komunikat typu Binary.

Wejście audio jest w formacie pliku Audio fali (WAVE lub więcej powszechnie znane jako WAV ze względu na jego rozszerzenie nazwy pliku). Aplikacja kliencka powinna strumienia pojedynczego kanału, próbkowany przy 16 kHz audio PCM podpisem 16-bitowych. Pierwszy zestaw bajtów przesyłanych strumieniowo przez klienta będzie zawierać nagłówek WAV. Nagłówek 44-bajtowych dla pojedynczego kanału podpisany 16 strumienia PCM bit próbkowany przy 16 kHz jest:

|Przesunięcie|Wartość|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"formatowaniu"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 – 35|16|
|36 - 39|"dane"|
|40 - 43|0|

Należy zauważyć, że rozmiar pliku (w bajtach 4-7) oraz rozmiar "data" (w bajtach 40-43) zostaną ustawione na zero. Jest to OK scenariusz transmisji strumieniowej, gdy łączny rozmiar jest nie zawsze są znane nabywanych usług.

Po wysłaniu nagłówków WAV (RIFF), klient wysyła fragmentów danych audio. Klient będzie zwykle strumienia fragmenty o ustalonym rozmiarze reprezentujący stały czas trwania (np. strumień 100ms audio w czasie).

### <a name="final-result"></a>Wynik końcowy
Na koniec utterance generowany jest wynik rozpoznawania mowy. Wynik jest przekazywany z usługi do klienta przy użyciu wiadomości protokołu WebSocket typu Text. Zawartość komunikatu jest serializacji JSON obiektu z następującymi właściwościami:

* `type`: Stała ciągu do identyfikowania typu wyniku. Wartość jest metodą końcową dla wyników końcowych.
* `id`: Identyfikator przypisany do wyniku rozpoznawania ciągu.
* `recognition`: Rozpoznany tekst w języku źródła. Tekst może być pustym ciągiem w przypadku uznania wartość false.
* `translation`: Translacji rozpoznany tekst w języku docelowym.
* `audioTimeOffset`: Przesunięcie początku rozpoznawania w taktach czas (znaczników 1 = 100 nanosekundach). Przesunięcie jest określana względem początku przesyłania strumieniowego.
* `audioTimeSize`: Czas trwania w taktach (100 nanosekundach) uznania.
* `audioStreamPosition`: Przesunięcie bajtów początek uznania. Przesunięcie jest określana względem początkiem strumienia.
* `audioSizeBytes`: Rozmiar w bajtach uznania.

Należy pamiętać, że pozycjonowanie rozpoznawania w strumieniem audio nie jest uwzględniony w wynikach domyślnie. `TimingInfo` Funkcji należy wybrać przez klienta (zobacz `features` parametru).

Wynik końcowy próbki jest następujący:

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
Wyników rozpoznawania mowy pośredniego lub częściowe nie są przesyłane strumieniowo do klienta domyślnie. Klient może używać funkcji parametru zapytania, aby zażądać ich.

Częściowy wynik jest przekazywany z usługi do klienta przy użyciu wiadomości protokołu WebSocket typu Text. Zawartość komunikatu jest serializacji JSON obiektu z następującymi właściwościami:

* `type`: Stała ciągu do identyfikowania typu wyniku. Wartość jest częściowe wyniki częściowe.
* `id`: Identyfikator przypisany do wyniku rozpoznawania ciągu.
* `recognition`: Rozpoznany tekst w języku źródła.
* `translation`: Translacji rozpoznany tekst w języku docelowym.
* `audioTimeOffset`: Przesunięcie początku rozpoznawania w taktach czas (znaczników 1 = 100 nanosekundach). Przesunięcie jest określana względem początku przesyłania strumieniowego.
* `audioTimeSize`: Czas trwania w taktach (100 nanosekundach) uznania.
* `audioStreamPosition`: Przesunięcie bajtów początek uznania. Przesunięcie jest określana względem początkiem strumienia.
* `audioSizeBytes`: Rozmiar w bajtach uznania.

Należy pamiętać, że pozycjonowanie rozpoznawania w strumieniem audio nie jest uwzględniony w wynikach domyślnie. Należy wybrać funkcję TimingInfo przez klienta (patrz parametr funkcji).

Wynik końcowy próbki jest następujący:

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

### <a name="text-to-speech"></a>Tekst na mowę
Po włączeniu funkcji zamiany tekstu na mowę (zobacz `features` parametru poniżej), wynik końcowy następuje audio rozmowy tłumaczenia. Dane audio jest fragmentaryczne i wysłane z usługi do klienta za pomocą sekwencji komunikatów protokołu Websocket typu Binary. Klient może wykryć koniec strumienia sprawdzając bit Znajdź każdego komunikatu. Ostatniego komunikatu binarnego mają jej zestawu bitu w wynikach wyszukiwania na taki, aby wskazać koniec strumienia. Format strumienia zależy od wartości `format` parametru.

### <a name="closing-the-connection"></a>Zamykanie połączenia
Gdy aplikacja kliencka zakończeniu przesyłania strumieniowego audio i otrzymał ostatni wynik końcowy, go zamknąć połączenie, inicjując uzgadniania zamknięcia obiektu WebSocket. Istnieją warunki, które spowoduje, że serwer zakończenie połączenia. Następujące kody zamknięcia obiektu WebSocket może być odbierany przez klienta:

* `1003 - Invalid Message Type`: Serwer przerywa połączenia, ponieważ nie akceptuje typu danych, który otrzymał. Dzieje się tak najczęściej, gdy przychodzący audio nie rozpoczyna się od odpowiedniego nagłówka.
* `1000 - Normal closure`: Połączenie zostało zamknięte po żądanie zostało spełnione. Serwer zamknie połączenie: gdy brak audio nie są odbierane z klienta przez dłuższy czas; gdy przesyłane strumieniowo wyciszenia przez dłuższy czas; gdy sesja osiągnie maksymalny dopuszczalny czas trwania (około 90 minut).
* `1001 - Endpoint Unavailable`: Wskazuje, że serwer będzie niedostępny. Aplikacja kliencka może podjąć próbę ponownego połączenia z limitem liczby ponownych prób.
* `1011 - Internal Server Error`: Połączenie zostanie zamknięte przez serwer z powodu błędu na serwerze.

### <a name="parameters"></a>Parametry

|Parametr|Wartość|Opis|Typ parametru|Typ danych|
|:---|:---|:---|:---|:---|
|wersja interfejsu API|1.0|Wersja interfejsu API żądanego przez klienta. Dozwolone wartości to: `1.0`.|query   |ciąg|
|z|(puste)   |Określa język przychodzące mowy. Wartość jest jeden z identyfikatorów języka z `speech` zakresu w odpowiedzi z języków interfejsu API.|query|ciąg|
|na|(puste)|Określa język tłumaczenia transcribed tekstu do. Wartość jest jeden z identyfikatorów języka z `text` zakresu w odpowiedzi z języków interfejsu API.|query|ciąg|
|danych|(puste)   |Rozdzielana przecinkami zestaw funkcji wybrany przez klienta. Funkcje dostępne:<ul><li>`TextToSpeech`: Określa, czy usługa musi zwracać przetłumaczonego audio ostatniego zdania przetłumaczonego.</li><li>`Partial`: Określa, czy usługa musi zwracać wyniki pośrednie rozpoznawania podczas dźwięk jest przesyłania strumieniowego z usługą.</li><li>`TimingInfo`: Określa, czy usługa musi zwracać informacji skojarzonych z każdym rozpoznawania.</li></ul>Na przykład określić klienta `features=partial,texttospeech` otrzymywać wyniki częściowe i zamiany tekstu na mowę, ale żadne informacje chronometrażu. Należy pamiętać, że wyniki końcowe są zawsze przesyłane strumieniowo do klienta.|query|ciąg|
|głosu|(puste)|Określa, jakie głosu do użycia na potrzeby renderowania tekst na mowę przetłumaczonego tekstu. Wartość jest jeden z identyfikatorów głosu z zakresu tts w odpowiedzi z języków interfejsu API. Jeśli nie określono głosu, że system będzie automatycznie wybrać jeden po włączeniu funkcji zamiany tekstu na mowę.|query|ciąg|
|Format|(puste)|Określa format tekst na mowę strumieniem audio zwrócona przez usługę. Dostępne są następujące opcje:<ul><li>`audio/wav`: Fali strumieniem audio. Klient powinien używać nagłówka WAV poprawnie interpretować audio format. Audio WAV tekstu na mowę jest 16-bitowy, pojedynczy kanał PCM z 24kHz lub 16kHz próbkowania.</li><li>`audio/mp3`: MP3 strumieniem audio.</li></ul>Wartość domyślna to `audio/wav`.|query|ciąg|
|ProfanityAction    |(puste)    |Określa sposób obsługi usługi profanities w mowy. Prawidłowe akcje to:<ul><li>`NoAction`: Profanities są pozostawione tak jak jest.</li><li>`Marked`: Profanities są zastępowane znacznika. Zobacz `ProfanityMarker` parametru.</li><li>`Deleted`: Profanities są usuwane. Na przykład jeśli słowo `"jackass"` jest traktowany jako niestosownych wyrażeń, wyrażenie `"He is a jackass."` staną się `"He is a .".`</li></ul>Wartość domyślna jest oznaczony jako.|query|ciąg|
|profanityMarker|(puste)    |Określa, jak wykryto profanities są obsługiwane, gdy `ProfanityAction` ma ustawioną wartość `Marked`. Prawidłowe opcje to:<ul><li>`Asterisk`: Profanities są zamieniane na ciąg `***`. Na przykład jeśli słowo `"jackass"` jest traktowany jako niestosownych wyrażeń, wyrażenie `"He is a jackass."` staną się `"He is a ***.".`</li><li>`Tag`: Niestosownych wyrażeń są ujęte w niestosownych wyrażeń tagu XML. Na przykład jeśli słowo `"jackass"` jest traktowany jako niestosownych wyrażeń, wyrażenie `"He is a jackass."` staną się `"He is a <profanity>jackass</profanity>."`.</li></ul>Wartość domyślna to `Asterisk`.|query|ciąg|
|Autoryzacja|(puste)  |Określa wartość tokenu elementu nośnego klienta. Użyj prefiksu `Bearer` następuje wartość `access_token` wartość zwrócona przez usługę tokenu uwierzytelniania.|nagłówek   |ciąg|
|OCP-Apim subskrypcji — klawisz|(puste)|Jeśli wymagane `Authorization` nagłówek nie został określony.|nagłówek|ciąg|
|access_token|(puste)   |Innym sposobem Przekaż prawidłowy token dostępu protokołu OAuth. Zazwyczaj otrzymuje token elementu nośnego nagłówka `Authorization`. Niektóre biblioteki obiektu websocket nie zezwalają na kod klienta można ustawić nagłówków. W takim przypadku klient może używać `access_token` parametr do przekazania prawidłowy token zapytania. Jeśli przy użyciu tokenu dostępu na potrzeby uwierzytelniania, jeśli `Authorization` nagłówka nie jest ustawiona, następnie `access_token` musi być ustawiona. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana. Klientów należy używać tylko jednej metody do przekazywania tokenu.|query|ciąg|
|klucz subskrypcji|(puste)   |Innym sposobem subskrypcji klucz dostępu. Niektóre biblioteki obiektu websocket nie zezwalają na kod klienta można ustawić nagłówków. W takim przypadku klient może używać `subscription-key` parametr do przekazania klucza ważnej subskrypcji zapytania. Przy użyciu klucza subskrypcji na potrzeby uwierzytelniania, jeśli `Ocp-Apim-Subscription-Key` nie ustawiono nagłówka, a następnie klawisz subskrypcji musi być ustawiona. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana. Klienci do przekazania należy używać tylko jedna metoda `subscription key`.|query|ciąg|
|X ClientTraceId    |(puste)    |Identyfikator GUID generowany przez klienta używane do śledzenia żądanie. Uzyskać odpowiednie informacje o rozwiązywaniu problemów, klientów należy udostępnić nową wartość każdego żądania i zalogować go.<br/>Zamiast używać nagłówka, ta wartość mogą zostać przekazane za pomocą parametru zapytania `X-ClientTraceId`. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana.|nagłówek|ciąg|
|X CorrelationId|(puste)    |Generowane przez klientów identyfikator korelacji wielu kanałów komunikacji. Aby włączyć konwersacji między użytkownikami można tworzyć wiele sesji tłumaczenia mowy. W takiej sytuacji wszystkie sesje tłumaczenia mowy Użyj ten sam identyfikator korelacji powiązać kanały razem. Ułatwia to śledzenie i informacji diagnostycznych. Identyfikator powinna być zgodna z: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Zamiast używać nagłówka, ta wartość mogą zostać przekazane za pomocą parametru zapytania `X-CorrelationId`. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana.|nagłówek|ciąg|
|X ClientVersion|(puste)    |Identyfikuje wersję aplikacji klienckiej. Przykład: "2.1.0.123".<br/>Zamiast używać nagłówka, ta wartość mogą zostać przekazane za pomocą parametru zapytania `X-ClientVersion`. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana.|nagłówek|ciąg|
|X OsPlatform|(puste)   |Określa nazwę i wersję systemu operacyjnego, którego aplikacja kliencka jest uruchomiona na. Przykłady: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Zamiast używać nagłówka, ta wartość mogą zostać przekazane za pomocą parametru zapytania `X-OsPlatform`. Jeśli ustawiono zarówno nagłówka, jak i parametr zapytania parametru zapytania jest ignorowana.|nagłówek|ciąg|

### <a name="response-messages"></a>Komunikaty odpowiedzi

|Kod stanu HTTP|Przyczyna|Model odpowiedzi|Nagłówki|
|:--|:--|:--|:--|
|101    |Uaktualnienia obiektu WebSocket.|Model Przykładowa wartość <br/> obiekt {}|Identyfikator żądania X<br/>Wartość identyfikowanie żądania na potrzeby rozwiązywania problemów.<br/>ciąg|
|400    |Nieprawidłowe żądanie. Sprawdź parametry wejściowe, aby upewnić się, że są one prawidłowe. Obiekt odpowiedzi zawiera bardziej szczegółowy opis błędu.|||
|401    |Bez autoryzacji. Upewnij się, ustawić poświadczeń, że są prawidłowe i czy subskrypcji Azure rynku danych jest w dobrym stanie z dostępne saldo.|||
|500    |Wystąpił błąd. Jeśli błąd będzie się powtarzać, raportu o identyfikatorze śledzenia klienta (X-ClientTraceId) lub identyfikator (X-RequestId) żądania.|||
|503    |Serwer jest tymczasowo niedostępny. Ponów żądanie. Jeśli błąd będzie się powtarzać, raportu o identyfikatorze śledzenia klienta (X-ClientTraceId) lub identyfikator (X-RequestId) żądania.|||

    


    





    
    




    




    




    

            




        









