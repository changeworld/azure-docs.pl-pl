---
title: Rozpoznawanie mowy Microsoft protokół WebSocket | Dokumentacja firmy Microsoft
description: Dokumentacja protokołu mowy usługi oparte na protokół WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 17954536e8bdb49c09204c2e522586b79cb1bef5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347496"
---
# <a name="speech-service-websocket-protocol"></a>Protokół WebSocket usługi mowy

  Usługa rozpoznawania mowy to oparte na chmurze platforma, która funkcji najbardziej zaawansowane algorytmy dostępne do konwertowania rozmowy audio na tekst. Protokół usługi mowy definiuje [ustawienia połączenia](#connection-establishment) między aplikacjami klienta i usługi i rozpoznawania mowy wiadomości wymieniane między partnerami ([klienta pochodzi wiadomości](#client-originated-messages)i [pochodzi usługi wiadomości](#service-originated-messages)). Ponadto [wiadomości telemetrii](#telemetry-schema) i [obsługi błędów](#error-handling) opisano.

## <a name="connection-establishment"></a>Ustanawianie połączenia

Protokół usługi mowy zgodna ze specyfikacją standardowego protokołu WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Połączenia obiektu WebSocket rozpoczyna się jako żądanie HTTP, która zawiera nagłówki HTTP, które wskazuje zamiar uaktualnienia połączenia do WebSocket zamiast za pomocą semantyki HTTP klienta. Serwer wskazuje chęć uczestnictwa w połączenia obiektu WebSocket, zwracając HTTP `101 Switching Protocols` odpowiedzi. Po wymianie tego uzgadniania zarówno klient, jak i usługa gniazda nie zamykaj i rozpocząć korzystanie opartego na komunikat protokołu do wysyłania i odbierania informacji.

Aby rozpocząć uzgadnianie protokołu WebSocket, aplikacja kliencka wysyła żądanie HTTPS GET do usługi. Obejmuje standardowych nagłówków uaktualnienia obiektu WebSocket wraz z innymi nagłówki, które są specyficzne dla mowy.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

Usługa odpowiada następująco:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Wszystkie żądania mowy wymagają [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) szyfrowania. Użycie mowy niezaszyfrowane żądania nie jest obsługiwane. Następujące TLS, wersja jest obsługiwana:

* TLS 1.2

### <a name="connection-identifier"></a>Identyfikator połączenia

Usługa rozpoznawania mowy wymaga, aby wszyscy klienci obejmują unikatowy identyfikator połączenia. Klienci *musi* obejmują *X ConnectionId* nagłówka po rozpoczęciu uzgadniania protokołu WebSocket. *X ConnectionId* nagłówek musi być [unikatowym identyfikatorem](https://en.wikipedia.org/wiki/Universally_unique_identifier) wartość (UUID). Żądania uaktualnienia protokołu WebSocket, które nie zawierają *X ConnectionId*, nie określaj wartości dla *X ConnectionId* nagłówka, lub nie zawiera prawidłowej wartości identyfikatora UUID są odrzucane przez usługę za pomocą protokołu HTTP `400 Bad Request` odpowiedzi.

### <a name="authorization"></a>Autoryzacja

Oprócz standardowych nagłówków uzgadnianie protokołu WebSocket mowy wymagają *autoryzacji* nagłówka. Bez tego nagłówka są odrzucane przez usługę za pomocą protokołu HTTP żądania połączeń `403 Forbidden` odpowiedzi.

*Autoryzacji* nagłówek musi zawierać token dostępu tokenu Web JSON (JWT).

Aby uzyskać informacje dotyczące subskrypcji i uzyskanie kluczy interfejsu API, które są używane do pobierania prawidłowych tokenów dostępu JWT, zobacz [subskrypcji usługi kognitywnych](https://azure.microsoft.com/try/cognitive-services/) strony.

Klucz interfejsu API jest przekazywany do usługi tokenu. Na przykład:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Następujące informacje nagłówka są wymagane do uzyskania tokenu dostępu.

| Name (Nazwa) | Format | Opis |
|----|----|----|
| OCP-Apim subskrypcji — klawisz | ASCII | Twój klucz subskrypcji |

Usługa tokenu zwraca token dostępu JWT jako `text/plain`. Następnie tokenu JWT jest przekazywany jako `Base64 access_token` do uzgadniania jako *autoryzacji* nagłówka poprzedzona ciągiem `Bearer`. Na przykład:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Pliki cookie

Klienci *musi* obsługujących pliki cookie HTTP, jak określono w [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Przekierowywanie HTTP

Klienci *musi* obsługuje mechanizmów przekierowania standardowych określony przez [Specyfikacja protokołu HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Punkty końcowe mowy

Klienci *musi* użycia odpowiedniego punktu końcowego usługi mowy. Punkt końcowy jest oparta na tryb rozpoznawania i języka. W tabeli przedstawiono przykłady.

| Tryb | Ścieżka | Identyfikator URI usługi |
| -----|-----|-----|
| Interaktywne | /Speech/Recognition/Interactive/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konwersacja | /Speech/Recognition/Conversation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| dyktowania | /Speech/Recognition/Dictation/cognitiveservices/V1 |https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Aby uzyskać więcej informacji, zobacz [identyfikator URI usługi](../GetStarted/GetStartedREST.md#service-uri) strony.

### <a name="report-connection-problems"></a>Problemy z połączeniem raportu

Klienci powinni zgłaszać natychmiast wszystkie problemy podczas nawiązywania połączenia. Message protocol dla raportowania nieudane połączenia jest opisany w [telemetrii awarii połączenia](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Ograniczenia czasu trwania połączenia

W porównaniu z połączenia HTTP usługi sieci web typowe, ostatni połączeń protokołu WebSocket *długi* czasu. Usługa rozpoznawania mowy wprowadza ograniczenia na czas trwania połączenia obiektu WebSocket do usługi:

 * Maksymalny czas trwania dowolnego aktywnego połączenia obiektu WebSocket to 10 minut. Połączenie jest aktywne, jeśli usługa lub klient wysyła komunikaty protokołu WebSocket za pośrednictwem tego połączenia. Usługa przerywa połączenie bez ostrzeżenia, po osiągnięciu limitu. Klientów należy opracować scenariuszy użytkownika, które nie wymagają połączenia pozostają aktywne, lub prawie połączenia maksymalny okres istnienia.

 * Maksymalny czas trwania dowolnego nieaktywnego połączenia obiektu WebSocket jest 180 sekund. Połączenie jest aktywne, jeśli usługa ani klient wysłał komunikat protokołu WebSocket za pośrednictwem połączenia. Po osiągnięciu maksymalny okres istnienia nieaktywne zakończenie usługi następuje nieaktywnego połączenia obiektu WebSocket.

## <a name="message-types"></a>Typy komunikatu

Po nawiązaniu połączenia obiektu WebSocket między klientem a usługą Klient i usługa może wysyłać wiadomości. W tej sekcji opisano format te komunikaty protokołu WebSocket.

[Grupa robocza IETF RFC 6455](https://tools.ietf.org/html/rfc6455) Określa komunikaty protokołu WebSocket mogą przesyłać dane przy użyciu tekstu lub kodowania binarnego. Dwa kodowania używać różnych formatów w locie. Każdy format jest zoptymalizowana pod kątem efektywne kodowanie transmisji i dekodowanie ładunek komunikatu.

### <a name="text-websocket-messages"></a>Tekst wiadomości protokołu WebSocket

Tekst wiadomości protokołu WebSocket przenoszenia ładunku informacji tekstowych, która składa się z sekcją nagłówków i treści oddzielone w pary znanych o podwójnej precyzji karetki znakami nowego wiersza dla wiadomości HTTP. I, takie jak wiadomości HTTP, wiadomości tekstowych protokołu WebSocket Określ nagłówków w *Nazwa: wartość* format oddzielone parę jednym karetki znakami nowego wiersza. Tekst zawarte w wiadomości SMS protokołu WebSocket *musi* użyj [UTF-8](https://tools.ietf.org/html/rfc3629) kodowania.

Tekst wiadomości protokołu WebSocket należy określić ścieżkę wiadomości w nagłówku *ścieżki*. Wartość tego nagłówka musi być jednego z typów wiadomości protokołu mowy zdefiniowane w dalszej części tego dokumentu.

### <a name="binary-websocket-messages"></a>Komunikaty protokołu WebSocket binarne

Binarny wiadomości protokołu WebSocket przenoszenia ładunek danych binarnych. W protokole mowy usługi audio jest przesyłane do i odbierane z usługi przy użyciu binarnej wiadomości protokołu WebSocket. Wszystkie inne komunikaty są wiadomości tekstowe protokołu WebSocket. 

Podobnie jak tekst wiadomości protokołu WebSocket binary wiadomości protokołu WebSocket składają się z nagłówek i treść sekcji. Określ pierwsze 2 bajty obiektu binarnego komunikat protokołu WebSocket, [big-endian](https://en.wikipedia.org/wiki/Endianness) kolejność sekcji nagłówka rozmiar 16-bitową liczbę całkowitą. Rozmiar sekcji nagłówka minimalna jest 0 bajtów. Maksymalny rozmiar to 8192 bajtów. Tekst w nagłówkach wiadomości protokołu WebSocket binarne *musi* użyj [US-ASCII](https://tools.ietf.org/html/rfc20) kodowania.

Nagłówków binarne komunikatu protokołu WebSocket są kodowane w tym samym formacie jak tekst wiadomości protokołu WebSocket. *Nazwa: wartość* format jest oddzielona parę jednym karetki znakami nowego wiersza. Binarny wiadomości protokołu WebSocket należy określić ścieżkę wiadomości w nagłówku *ścieżki*. Wartość tego nagłówka musi być jednego z typów wiadomości protokołu mowy zdefiniowane w dalszej części tego dokumentu.

Tekst i binarne wiadomości protokołu WebSocket są używane w protokole mowy usługi. 

## <a name="client-originated-messages"></a>Zainicjowano klienta wiadomości

Po nawiązaniu połączenia zarówno klient, jak i usługi można uruchomić do wysyłania wiadomości. W tej sekcji opisano format i ładunku wiadomości, które aplikacje klienckie wysłać do usługi mowy. Sekcja [pochodzi usługi wiadomości](#service-originated-messages) przedstawia komunikaty, które pochodzą z usługi rozpoznawania mowy i są wysyłane do aplikacji klienckich.

Główne komunikaty wysyłane przez klienta do usług są `speech.config`, `audio`, i `telemetry` wiadomości. Zanim firma Microsoft należy wziąć pod uwagę każdy komunikat szczegółowo, typowe wymagane nagłówki komunikatów komunikaty te są opisane.

### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

Następujące nagłówki są wymagane dla wszystkich wiadomości pochodzących od klienta.

| Nagłówek | Wartość |
|----|----|
| Ścieżka | Ścieżka określona w tym dokumencie |
| Identyfikator żądania X | Identyfikator UUID w formacie "nie-dash" |
| Sygnatura czasowa X | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |

#### <a name="x-requestid-header"></a>Nagłówek X-RequestId

Zainicjowano klienta żądań jest unikatowo identyfikowana przez *X-RequestId* nagłówek komunikatu. Ten nagłówek jest wymagany dla wszystkich wiadomości pochodzących od klienta. *X-RequestId* wartość nagłówka musi być UUID w formie "nie-dash", na przykład *123e4567e89b12d3a456426655440000*. On *nie* być w formie kanonicznej *123e4567-e89b-12d3-a456-426655440000*. Żądania bez *X-RequestId* nagłówka lub z wartością nagłówka, który używa nieprawidłowy format dla identyfikatorów UUID spowodować przerwanie połączenia obiektu WebSocket.

#### <a name="x-timestamp-header"></a>Nagłówek X-znacznik czasu

Każdy komunikat wysyłany do usługi mowy przez aplikację kliencką *musi* obejmują *sygnatury czasowej X* nagłówka. Wartość tego nagłówka jest czas, kiedy klient wysyła wiadomość. Żądania bez *sygnatury czasowej X* nagłówka lub z wartością nagłówka, który korzysta z niewłaściwym formacie spowodować przerwanie połączenia obiektu WebSocket.

*Sygnatury czasowej X* wartość nagłówka musi mieć formę "yyyy"-"MM"-"dd'T" HH ":"mm":" ss "." fffffffZ ""fffffff"w przypadku ułamek sekund. Na przykład "12,5" oznacza "12 + 5/10 sekund i"12.526"oznacza 12 oraz sekund 526/1000". Ten format jest zgodny z [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i, w przeciwieństwie do standardowego protokołu HTTP *data* nagłówka, może udostępnić rozpoznawania milisekund. Aplikacje klienckie mogą okrągłe sygnatury czasowe do najbliższej milisekund. Aplikacje klienckie konieczne upewnij się, że zegar urządzenia dokładnie śledzi czas za pomocą [serwera protokołu NTP (Network Time)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Komunikat `speech.config`

Usługa rozpoznawania mowy musi wiedzieć, właściwości aplikacji, aby zapewnić najlepsze możliwe mowę. Dane wymagane właściwości zawierają informacje o urządzeniu i systemu operacyjnego, który obsługuje aplikacja. Podaj te informacje w `speech.config` wiadomości.

Klienci *musi* wysyłania `speech.config` komunikatu natychmiast po nawiązaniu połączenia usługi mowy i przed wysyłają wszelkie `audio` wiadomości. Aby wysłać `speech.config` wiadomości tylko raz dla każdego połączenia.

| Pole | Opis |
|----|----|
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Treść | Ładunek w strukturze JSON |

#### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

| Nazwa nagłówka | Wartość |
|----|----|
| Ścieżka | `speech.config` |
| Sygnatura czasowa X | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | Application/json; charset = utf-8 |

Podobnie jak w przypadku wszystkich wiadomości pochodzących od klienta w protokole mowy usługi `speech.config` komunikat *musi* obejmują *sygnatury czasowej X* nagłówek, który rejestruje czas zegara klienta UTC wysłania wiadomości do usługi. `speech.config` Komunikat *nie* wymagają *X-RequestId* nagłówka, ponieważ ten komunikat nie jest skojarzone z żądaniem określonego mowy.

#### <a name="message-payload"></a>Ładunek komunikatu
Ładunek `speech.config` wiadomość jest strukturze JSON, który zawiera informacje o aplikacji. W poniższym przykładzie przedstawiono te informacje. Zawiera informacje o kontekście klientów i urządzeń *kontekstu* element strukturze JSON. 

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>Element systemu

System.version element `speech.config` komunikat zawiera wersję mowy oprogramowania SDK używany przez aplikację klienta lub urządzenia. Wartość jest w formie *major.minor.build.branch*. Można pominąć *gałęzi* składników, jeśli nie ma zastosowania.

##### <a name="os-element"></a>Element systemu operacyjnego

| Pole | Opis | Sposób użycia |
|-|-|-|
| OS.platform | Systemu operacyjnego platformy, która obsługuje aplikację, na przykład systemu Windows, Android, iOS albo Linux |Wymagane |
| OS.name | Nazwa produktu systemu operacyjnego, na przykład Debian lub Windows 10 | Wymagane |
| OS.Version | Wersja systemu operacyjnego w postaci *major.minor.build.branch* | Wymagane |

##### <a name="device-element"></a>Element urządzenia

| Pole | Opis | Sposób użycia |
|-|-|-|
| Device.Manufacturer | Producenta urządzenia | Wymagane |
| Device.model | Model urządzenia | Wymagane |
| Device.Version | Wersja oprogramowania urządzenia dostarczone przez producenta urządzenia. Ta wartość określa wersję urządzenia, które mogą być śledzone przez producenta. | Wymagane |

### <a name="message-audio"></a>Komunikat `audio`

Aplikacje klienckie obsługujące rozpoznawanie mowy wysłać audio do usługi mowy konwertując strumieniem audio do serii fragmentów audio. Każdego fragmentu audio niesie segment rozmowy audio, który ma zostać przetłumaczone przez usługę. Maksymalny rozmiar jednego fragmentu audio jest 8192 bajtów. Strumień audio wiadomości są *komunikatów binarnych WebSocket*.

Klienci używają `audio` komunikat do wysłania do usługi fragmentu audio. Klienci odczytu dźwięku z mikrofonów w fragmentów i wysyłanie tych fragmentów mowy usługi do zapisu. Pierwszy `audio` wiadomości musi zawierać poprawnie sformułowanym nagłówek, który prawidłowo Określa, że audio zgodne z jednym z formatów kodowania obsługiwane przez usługę. Dodatkowe `audio` wiadomości zawierają tylko dane odczytane ze mikrofon strumienia binarnego audio.

Klienci mogą opcjonalnie wysyłają `audio` komunikat o treści o zerowej długości. Ten komunikat informuje usługi, że klient zna użytkownik zatrzymał mówiąc, utterance została zakończona i mikrofon jest wyłączona.

Usługa rozpoznawania mowy używa pierwszego `audio` komunikat, który zawiera identyfikator unikatowy żądania sygnalizują uruchomienia nowego cyklu żądania/odpowiedzi lub *włączyć*. Po otrzymaniu usługi `audio` wiadomości przy użyciu nowego identyfikatora żądania odrzuca komunikaty w kolejce lub Niewysłane, które są skojarzone z wszelkie poprzednie Włącz.

| Pole | Opis |
|-------------|----------------|
| Kodowanie komunikatu protokołu WebSocket | Binarny |
| Treść | Dane binarne dla fragmentu audio. Maksymalny rozmiar to 8192 bajtów. |

#### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

Następujące nagłówki są wymagane dla wszystkich `audio` wiadomości.

| Nagłówek         |  Wartość     |
| ------------- | ---------------- |
| Ścieżka | `audio` |
| Identyfikator żądania X | Identyfikator UUID w formacie "nie-dash" |
| Sygnatura czasowa X | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | Typ zawartości audio. Typ musi być albo *audio/x-wav* (PCM) lub *audio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Obsługiwane są audio

W tej sekcji opisano audio koderów-dekoderów, obsługiwane przez usługę mowy.

##### <a name="pcm"></a>PCM

Usługa rozpoznawania mowy akceptuje nieskompresowanych pulse kodu modulacji (PCM) audio. Dźwięk jest wysyłane do usługi w [WAV](https://en.wikipedia.org/wiki/WAV) sformatować, więc bryłkach pierwszy audio *musi* zawierać prawidłową [Interchange Format pliku zasobu](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) nagłówka (RIFF). Jeśli klient inicjuje Włącz z fragmentu audio, który wykonuje *nie* zawierają nieprawidłowy nagłówek RIFF, usługa odrzuca żądanie i przerywa połączenia obiektu WebSocket.

PCM audio *musi* będą próbkowane przy 16 kHz z 16 bitów na przykład i jeden kanał (*riff-16khz-16-bitowych-mono-pcm*). Usługa rozpoznawania mowy nie obsługuje stereo strumieni audio i odrzuca strumieni audio, które nie korzystają z określona szybkość transmisji bitów, częstotliwość próbkowania lub liczba kanałów.

##### <a name="opus"></a>Dziele

Dziele jest otwarty, bez licencjonowanych, wszechstronny kodera-dekodera audio. Usługa rozpoznawania mowy obsługuje dziele na szybkość transmisji bitów stałej `32000` lub `16000`. Tylko `OGG` kontener dziele jest obecnie obsługiwany określonym przez `audio/ogg` typ mime.

Aby użyć dziele, zmodyfikuj [próbki JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) i zmienić `RecognizerSetup` metody do zwrócenia.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>Wykryj koniec mowy

Ludzi nie można jawnie sygnalizować, gdy będą gotowe, mówiąc. Każda aplikacja, która akceptuje mowy jako dane wejściowe ma dwie opcje obsługi koniec mowy w strumień audio: usługa wykrywania zakończenia z mowy i wykrywania zakończenia z mowy klienta. Tych dwóch opcji wykrywania zakończenia z mowy usługi zwykle zapewnia lepsze środowisko pracy użytkownika.

##### <a name="service-end-of-speech-detection"></a>Wykrywanie zakończenia z mowy usługi

Aby utworzyć środowisko idealne mowy bezobsługowego, aplikacje pozwalają usłudze na Wykryj, kiedy użytkownik zakończył mówiąc. Klienci wysyłają dźwięku z mikrofonów jako *audio* fragmentów, dopóki usługa wykrywa wyciszenia i odpowiada z powrotem `speech.endDetected` wiadomości.

##### <a name="client-end-of-speech-detection"></a>Wykrywanie zakończenia z mowy klienta

Aplikacje klienckie, które umożliwiają użytkownikom sygnalizuje koniec mowy w jakiś sposób również zapewnić usługę który sygnału. Na przykład aplikacja klienta może być "Stop" lub użytkownik może nacisnąć przycisk "Wyciszenia". Sygnalizują zakończenia z mowy, aplikacje klienckie wysyłania *audio* fragmentu komunikat o treści o zerowej długości. Usługa rozpoznawania mowy interpretuje tego komunikatu jako koniec strumienia przychodzącego audio.

### <a name="message-telemetry"></a>Komunikat `telemetry`

Aplikacje klienckie *musi* potwierdzić koniec każda Włączanie wysyłając dane telemetryczne dotyczące Włącz usługi mowy. Włącz end potwierdzenia umożliwia usłudze mowy upewnij się, że wszystkie komunikaty niezbędne do zakończenia żądania i odpowiedzi prawidłowo zostały odebrane przez klienta. Włącz end potwierdzenia umożliwia również mowy usługi sprawdzić, czy aplikacje klienckie działają zgodnie z oczekiwaniami. Informacje te są cenne, jeśli potrzebujesz pomocy, rozwiązywanie problemów z aplikacji z obsługą mowy.

Klienci muszą potwierdzić koniec Włącz wysyłając `telemetry` wiadomości wkrótce od momentu odebrania `turn.end` wiadomości. Klienci powinien próbować rozpoznać `turn.end` tak szybko, jak to możliwe. W przypadku niepowodzenia aplikacji klienckiej potwierdzić zakończenia Włącz mowy usługi może być przerwanie połączenia z powodu błędu. Klientów należy wysłać tylko jeden `telemetry` komunikat dla każdego żądania i odpowiedzi identyfikowane przez *X-RequestId* wartość.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `telemetry` |
| Sygnatura czasowa X | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | `application/json` |
| Treść | Struktura JSON, która zawiera informacje o kliencie o ruchu |

Schemat dla treści `telemetry` wiadomości jest zdefiniowany w [schematu Telemetrii](#telemetry-schema) sekcji.

#### <a name="telemetry-for-interrupted-connections"></a>Dane telemetryczne dla przerwanego połączenia

Jeśli połączenie sieciowe nie powiedzie się z jakiegokolwiek powodu w kolejce i funkcji klienta *nie* odbierania `turn.end` komunikatów z usługi, klient wysyła `telemetry` wiadomości. Ten komunikat przedstawia nieudanych żądań, przy następnym klient nawiązuje połączenie z usługą. Klienci nie muszą podejmować natychmiast połączenie do wysłania `telemetry` wiadomości. Komunikat może być buforowane na kliencie i wysyłany przez połączenie przyszłych użytkownik zażądał. `telemetry` Komunikat dla żądań zakończonych niepowodzeniem *musi* użyj *X-RequestId* wartość z zakresu od nieudanych żądań. Mogą być wysyłane do usługi, jak jest nawiązywane połączenie, nie trzeba czekać do wysyłania i odbierania wiadomości innych.

## <a name="service-originated-messages"></a>Zainicjowano usługę wiadomości

W tej sekcji opisano komunikaty, które pochodzą z usługi rozpoznawania mowy i są wysyłane do klienta. Usługa mowy przechowuje rejestr możliwości klienta i generuje komunikaty wymagane przez każdego klienta, więc nie wszyscy klienci otrzymają wszystkie komunikaty, które zostały opisane w tym miejscu. Jednak wiadomości odwołuje się wartość *ścieżki* nagłówka. Na przykład możemy odwoływać się do obiektu WebSocket wiadomości tekstowych z *ścieżki* wartość `speech.hypothesis` jako speech.hypothesis wiadomości.

### <a name="message-speechstartdetected"></a>Komunikat `speech.startDetected`

`speech.startDetected` Komunikat wskazuje, że usługa mowy wykryto mowy w strumieniem audio.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `speech.startDetected` |
| Content-Type | Application/json; charset = utf-8 |
| Treść | Struktura JSON, która zawiera informacje o warunkach, jeśli wykryto początku mowy. *Przesunięcie* pola struktury określa przesunięcie (w jednostkach 100 nanosekund) wykrycia mowy w strumieniu audio, względem początku strumienia. |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Komunikat `speech.hypothesis`

Podczas rozpoznawania mowy usługi mowy okresowo generuje hipotezę o słowa usługi rozpoznany. Usługa mowy wysyła te hipotez do klienta, co około 300 milisekund. `speech.hypothesis` Odpowiedni *tylko* dla ulepszenia środowiska mowy. Nie należy wykonać jej zależności od zawartości lub dokładności tekst wiadomości.

 `speech.hypothesis` Wiadomości ma zastosowanie do tych klientów, którzy mają możliwość renderowania tekstu i chcesz wyrazić swoją opinię niemal czasie rzeczywistym w trakcie rozpoznawania osobie, która jest czytanie.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `speech.hypothesis` |
| Identyfikator żądania X | Identyfikator UUID w formacie "nie-dash" |
| Content-Type | application/json |
| Treść | Hipoteza mowy strukturze JSON |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

*Przesunięcie* element określa przesunięcie (w jednostkach 100 nanosekund) po frazie został rozpoznany, względem początku strumieniem audio.

*Czas trwania* element określa czas trwania (w jednostkach 100 nanosekund) Ta fraza mowy.

Klientów nie musi wprowadzać żadnym założeniu o częstotliwości, czas lub tekst zawarty w hipoteza mowy lub spójności tekstu w dowolnej hipotez dwóch mowy. Hipotez są tylko migawki z procesem przekształcania w usłudze. Reprezentuje stabilna akumulacji przekształcania. Na przykład pierwszej hipoteza mowy mogą zawierać słowa "fun poprawnie", a drugi hipoteza mogą zawierać słowa "Rady odnaleźć." Usługa rozpoznawania mowy nie wykonywać żadnych przetwarzania końcowego (na przykład wielkie litery, znaki interpunkcyjne) tekst w hipoteza mowy.

### <a name="message-speechphrase"></a>Komunikat `speech.phrase`

Gdy mowy usługi określa, czy ma on wystarczających informacji do utworzenia wyniku rozpoznawania, która nie zmienia tworzy usługi `speech.phrase` wiadomości. Usługa rozpoznawania mowy tworzy te wyniki po wykryciu, że użytkownik zakończył zdanie lub frazę.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `speech.phrase` |
| Content-Type | application/json |
| Treść | Wyrażenie mowy strukturze JSON |

Schematu JSON frazy mowy zawiera następujące pola: `RecognitionStatus`, `DisplayText`, `Offset`, i `Duration`. Aby uzyskać więcej informacji o tych pól, zobacz [zapisu odpowiedzi](../concepts.md#transcription-responses).

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>Komunikat `speech.endDetected`

`speech.endDetected` Komunikat informuje, że aplikacja kliencka powinna zostać przerwana przesyłania strumieniowego audio do usługi.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `speech.endDetected` |
| Treść | Struktura JSON, która zawiera przesunięcie, gdy wykryto zakończenie mowy. Przesunięcie jest reprezentowana w jednostki 100-nanosekundowych przesunięcie od początku dźwięk, który służy do rozpoznawania. |
| Content-Type | Application/json; charset = utf-8 |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Przesunięcie* element określa przesunięcie (w jednostkach 100 nanosekund) po frazie został rozpoznany, względem początku strumieniem audio.

### <a name="message-turnstart"></a>Komunikat `turn.start`

`turn.start` Sygnalizuje początek Włącz względem usługi. `turn.start` Komunikat zawsze jest pierwszy komunikat odpowiedzi, pojawi się na każde żądanie. Jeśli użytkownik nie otrzyma `turn.start` komunikatów, założono, że stan połączenia usługi jest nieprawidłowa.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `turn.start` |
| Content-Type | Application/json; charset = utf-8 |
| Treść | Struktura JSON |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

Treść `turn.start` wiadomości jest strukturą JSON, która zawiera kontekst na początku ruch. *Kontekstu* element zawiera *serviceTag* właściwości. Ta właściwość określa wartość tagu skojarzoną z Włącz usługę. Ta wartość może być używana przez firmę Microsoft, jeśli potrzebujesz pomocy, rozwiązywanie problemów z błędami w aplikacji.

### <a name="message-turnend"></a>Komunikat `turn.end`

`turn.end` Sygnalizuje koniec Włącz względem usługi. `turn.end` Wiadomości jest zawsze ostatniego komunikatu odpowiedzi, pojawi się na każde żądanie. Klienci mogą używać odebranie tej wiadomości jako sygnał oczyszczania działań i przejście do stanu bezczynności. Jeśli użytkownik nie otrzyma `turn.end` komunikatów, założono, że stan połączenia usługi jest nieprawidłowa. W takich przypadkach Zamknij istniejące połączenie z usługą i ponownie.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Tekst |
| Ścieżka | `turn.end` |
| Treść | Brak |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schemat telemetrii

Treść *telemetrii* komunikat jest strukturą JSON, która zawiera informacje o kliencie o Wyłącz lub próby połączenia. Struktura składa się z sygnatury czasowe klienta służące do rejestrowania, gdy wystąpienia zdarzeń klienta. Każdy sygnatura czasowa musi być w formacie ISO 8601 zgodnie z opisem w sekcji "Nagłówek X-znacznik czasu". *Dane telemetryczne* wiadomości, który nie należy określać wszystkie wymagane pola w strukturze JSON lub nie używaj format sygnatury czasowej poprawne może spowodować, że usługa zakończenie połączenia do klienta. Klienci *musi* podać prawidłowe wartości dla wszystkich wymaganych pól. Klienci *powinien* Podaj wartości dla pól opcjonalnych zawsze, gdy jest to odpowiednie. Wartości widoczne w próbek w tej sekcji są tylko do celów ilustracyjnych.

Dane telemetryczne schematu jest podzielona na następujące elementy: Odebrano komunikat sygnatury czasowe i metryki. Format i użycia każdej części określonego w poniższych sekcjach.

### <a name="received-message-time-stamps"></a>Sygnatury czasowe odebranego komunikatu

Klienci muszą zawierać wartości czas od przyjęcia dla wszystkich wiadomości, które otrzymają po pomyślnym połączeniu się z usługą. Te wartości muszą zarejestrować czas podczas klienta *Odebrano* każdy komunikat z sieci. Wartość nie należy zarejestrować innym czasie. Na przykład klient nie powinien zarejestrować czas po jego *działał* w komunikacie. Sygnatury czasowe odebranego komunikatu są określone w tablicy *Nazwa: wartość* pary. Określa nazwę pary *ścieżki* wartość komunikatu. Wartość pary określa czas klienta, kiedy wiadomość została odebrana. Lub, jeśli więcej niż jeden komunikat o określonej nazwie została odebrana, wartość pary jest tablicę sygnatury czasowe, która wskazuje, kiedy zostały odebrane wiadomości.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienci *musi* otrzymanie wszystkich wiadomości wysłanych przez usługę w tym sygnatury czasowe dla tych wiadomości w treści JSON. Jeśli klient nie otrzymanie komunikatu, usługa może przerwać połączenie.

### <a name="metrics"></a>Metryki

Klienci muszą zawierać informacje o zdarzeniach, które wystąpiły w czasie trwania żądanie. Obsługiwane są następujące metryki: `Connection`, `Microphone`, i `ListeningTrigger`.

### <a name="metric-connection"></a>Metryka `Connection`

`Connection` Metryka określa szczegółowe informacje o próby nawiązania połączenia przez klienta. Metryka musi zawierać sygnatury czasowe, gdy połączenia obiektu WebSocket zostało rozpoczęcia i zakończenia. `Connection` Metryka jest wymagana *tylko dla pierwszego Włącz połączenia*. Włącza kolejnych nie wymagane jest uwzględnienie tych informacji. Jeśli klient przesyła wiele prób połączenia przed jest nawiązywane połączenie, informacje o *wszystkie* próby nawiązania połączenia należy dołączyć. Aby uzyskać więcej informacji, zobacz [telemetrii awarii połączenia](#connection-failure-telemetry).

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | `Connection` | Wymagane |
| Identyfikator | Wartość identyfikatora połączenia, który był używany w *X ConnectionId* nagłówek dla tego żądania połączenia | Wymagane |
| Uruchamianie | Czas wysłania żądania połączenia klienta | Wymagane |
| Koniec | Podczas gdy klient otrzymał powiadomienie, że połączenie zostało ustanowione pomyślnie, lub w przypadku błędów odrzucony, zostało odrzucone lub nie powiodło się | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli połączenie powiodło się, klienci należy pominąć to pole. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadku błąd, w przeciwnym razie pominięcia |

Opis błędu powinien mieć maksymalnie 50 znaków i najlepiej musi mieć jedną z wartości wymienione w poniższej tabeli. Jeśli warunek błędu nie odpowiada jednej z tych wartości, klienci mogą używać zwięzły opis warunku błędu przy użyciu [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez biały znak. Umożliwia wysyłanie *telemetrii* komunikat wymaga połączenia z usługą, więc tylko przejściowego lub tymczasowe błędy mogą być zgłaszane *telemetrii* wiadomości. Błędów, które *trwale* bloku klienta z nawiązywania połączenia z usługą uniemożliwia wysyłanie wiadomości z usługą przez klienta w tym *telemetrii* wiadomości.

| Błąd | Sposób użycia |
| ----- | ----- |
| DNSfailure | Klient nie może połączyć się z usługą z powodu błędu DNS w stos sieciowy. |
| NoNetwork | Klient próbował połączenie, ale stos sieci zgłosił, że nie sieci fizycznej była dostępna. |
| NoAuthorization | Połączenie klienta nie powiodło się podczas próby uzyskania tokenu autoryzacji dla połączenia. |
| NoResources | Klient zabrakło niektórych zasobów lokalnych (na przykład pamięci) podczas próby nawiązania połączenia. |
| Zabroniony | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `403 Forbidden` kod stanu na żądanie uaktualnienia obiektu WebSocket. |
| Brak autoryzacji | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `401 Unauthorized` kod stanu na żądanie uaktualnienia obiektu WebSocket. |
| BadRequest | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `400 Bad Request` kod stanu na żądanie uaktualnienia obiektu WebSocket. |
| ServerUnavailable | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `503 Server Unavailable` kod stanu na żądanie uaktualnienia obiektu WebSocket. |
| ServerError | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła `HTTP 500` kod stanu błędu wewnętrznego na żądanie uaktualnienia obiektu WebSocket. |
| Limit czasu | Przekroczono limit czasu bez odpowiedzi z usługi żądania połączenia klienta. *Zakończenia* pole zawiera godzinę, kiedy klient upłynął limit czasu i zatrzymanie oczekiwania dla połączenia. |
| ClientError | Klient zakończył połączenie ze względu na błąd wewnętrzny klienta. | 

### <a name="metric-microphone"></a>Metryka `Microphone`

`Microphone` Metryka jest wymagana dla wszystkich włącza mowy. Ta metryka mierzy czas na kliencie, w którym wejście audio jest używana dla żądania mowy.

Użyj poniższych przykładach jako wskazówki do rejestrowania *Start* czasu wartości `Microphone` metryki w aplikacji klienta:

* Aplikacja kliencka wymaga, czy użytkownik musi nacisnąć fizycznego przycisku w celu rozpoczęcia mikrofon. Po naciśnięcie przycisku aplikacja kliencka odczytuje dane wejściowe mikrofon i wysyła je z usługą rozpoznawania mowy. *Start* wartość `Microphone` Metryka rejestruje czas po naciśnięcie przycisku, gdy mikrofon jest zainicjowana i gotowa do podawanie danych wejściowych. *Zakończenia* wartość `Microphone` Metryka rejestruje czas, gdy aplikacja kliencka zatrzymana, przesyłania strumieniowego audio do usługi, po odebraniu go `speech.endDetected` komunikatów z usługi.

* Aplikacja kliencka używa spotter — słowo kluczowe "zawsze" nasłuchiwania. Tylko wtedy, gdy spotter — słowo kluczowe wykrywa frazę rozmowy wyzwalacza aplikacja kliencka zbierania danych wejściowych z mikrofon i wysyłają je do usługi mowy. *Start* wartość `Microphone` Metryka rejestruje czas, kiedy spotter — słowo kluczowe powiadomienia klienta, aby rozpocząć korzystanie z danych wejściowych z mikrofonu. *Zakończenia* wartość `Microphone` Metryka rejestruje czas, gdy aplikacja kliencka zatrzymana, przesyłania strumieniowego audio do usługi, po odebraniu go `speech.endDetected` komunikatów z usługi.

* Aplikacja kliencka ma dostęp do stały strumień audio i przeprowadza wykrywanie wyciszenia/mowy na strumieniu audio w *modułu wykrywania mowy*. *Start* wartość `Microphone` Metryka rejestruje czas po *modułu wykrywania mowy* powiadomienia klienta, aby rozpocząć korzystanie z danych wejściowych z strumieniem audio. *Zakończenia* wartość `Microphone` Metryka rejestruje czas, gdy aplikacja kliencka zatrzymana, przesyłania strumieniowego audio do usługi, po odebraniu go `speech.endDetected` komunikatów z usługi.

* Aplikacja kliencka jest przetwarzanie drugi Włącz żądania wielu Włącz i zostanie poinformowany przez komunikat odpowiedzi usługi, aby włączyć mikrofon do zbierania danych wejściowych dla drugiego Włącz. *Start* wartość `Microphone` Metryka rejestruje czas, kiedy aplikacja kliencka umożliwia mikrofon i zacznie korzystać z tego źródła audio danych wejściowych. *Zakończenia* wartość `Microphone` Metryka rejestruje czas, gdy aplikacja kliencka zatrzymana, przesyłania strumieniowego audio do usługi, po odebraniu go `speech.endDetected` komunikatów z usługi.

*Zakończenia* czasu wartość `Microphone` Metryka rejestruje czas, gdy aplikacja kliencka zatrzymana przesyłania strumieniowego audio danych wejściowych. W większości sytuacji to zdarzenie wystąpi, wkrótce po klienta odebranych `speech.endDetected` komunikatów z usługi. Aplikacje klienckie sprawdzić, czy ich jest prawidłowo odpowiadających protokołu przez zapewnienie, że *zakończenia* czasu wartość `Microphone` Metryka nastąpi później niż wartość czasu otrzymania `speech.endDetected` wiadomości. I jest zwykle opóźnienia między koniec ruchu i uruchomienia innego Włącz, klienci może sprawdzić zgodność protokołu przez zapewnienie, że *Start* czas `Microphone` metryki dla dowolnego kolejnych Włącz poprawnie rejestruje czas podczas klienta *uruchomiona* przy użyciu mikrofonu do strumienia wejście audio do usługi.

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | Mikrofon | Wymagane |
| Uruchamianie | Klient uruchamiania przy użyciu wejściowych danych audio mikrofonu lub innych strumieniem audio lub odebrano wyzwalacz spotter — słowo kluczowe | Wymagane |
| Koniec | Czas, kiedy klienta przestało używać strumienia mikrofon lub audio | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli operacje mikrofon zakończy się powodzeniem, klientów należy pominąć to pole. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadku błąd, w przeciwnym razie pominięcia |

### <a name="metric-listeningtrigger"></a>Metryka `ListeningTrigger`
`ListeningTrigger` Metryka mierzy czas, gdy użytkownik wykonuje akcję, która inicjuje wejście mowy. `ListeningTrigger` Metryka jest opcjonalne, ale zachęcamy klientów, którzy mogą zapewnić ta metryka, aby to zrobić.

Użyj poniższych przykładach jako wskazówki do rejestrowania *Start* i *zakończenia* czasu wartości `ListeningTrigger` metryki w aplikacji klienta.

* Aplikacja kliencka wymaga, czy użytkownik musi nacisnąć fizycznego przycisku w celu rozpoczęcia mikrofon. *Start* wartość ta metryka rejestruje czas naciśnięcie przycisku. *Zakończenia* wartość rejestruje czas, po zakończeniu naciśnięcie przycisku.

* Aplikacja kliencka używa spotter — słowo kluczowe "zawsze" nasłuchiwania. Po słowie kluczowym spotter wykrywa frazę wyzwalacza rozmowy, aplikacja kliencka odczytuje dane wejściowe mikrofon i wysyła je do usługi mowy. *Start* wartość ta metryka rejestruje czas po spotter — słowo kluczowe otrzymał dźwięk, który następnie wykryto frazy wyzwalacza. *Zakończenia* wartość rejestruje czas, gdy wyraz ostatniej frazy wyzwalacz był używany przez użytkownika.

* Aplikacja kliencka ma dostęp do stały strumień audio i przeprowadza wykrywanie wyciszenia/mowy na strumieniu audio w *modułu wykrywania mowy*. *Start* wartość ta metryka rejestruje czas który *modułu wykrywania mowy* Odebrano audio, który następnie wykryto mowy. *Zakończenia* wartość rejestruje czas po *modułu wykrywania mowy* wykryto mowy.

* Aplikacja kliencka jest przetwarzanie drugi Włącz żądania wielu Włącz i zostanie poinformowany przez komunikat odpowiedzi usługi, aby włączyć mikrofon do zbierania danych wejściowych dla drugiego Włącz. Aplikacja kliencka powinna *nie* obejmują `ListeningTrigger` metryki dla tego ruchu.

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | ListeningTrigger | Optional (Opcjonalność) |
| Uruchamianie | Czas rozpoczęcia wyzwalacza nasłuchiwania klienta | Wymagane |
| Koniec | Czas, po zakończeniu wyzwalacza nasłuchiwania klienta | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli operacja wyzwalacza zakończyło się pomyślnie, klientów należy pominąć to pole. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadku błąd, w przeciwnym razie pominięcia |

#### <a name="sample-message"></a>Przykładowy komunikat

Poniższy przykład przedstawia wiadomość dane telemetryczne z części zarówno ReceivedMessages i metryki:

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>Obsługa błędów

W tej sekcji opisano typy komunikatów o błędach i warunków, które aplikacja musi obsługiwać.

### <a name="http-status-codes"></a>Kody stanu HTTP

Podczas żądanie uaktualnienia obiektu WebSocket mowy usługi może zwracać standardowe kody stanu HTTP, takie jak `400 Bad Request`itp. Aplikacja musi obsługiwać poprawnie te warunki błędów.

#### <a name="authorization-errors"></a>Błędy autoryzacji

Jeśli niepoprawne autoryzacji została podana podczas uaktualnienia obiektu WebSocket mowy usługi zwraca HTTP `403 Forbidden` kod stanu. Warunki, które mogą wyzwalać tego kodu błędu należą:

* Brak *autoryzacji* nagłówka

* Token autoryzacji nieprawidłowy

* Token autoryzacji wygasły

`403 Forbidden` Komunikat o błędzie nie wskazują na problem z usługą rozpoznawania mowy. Ten komunikat o błędzie wskazuje na problem z aplikacją klienta.

### <a name="protocol-violation-errors"></a>Błędy naruszenie protokołu

Jeśli usługa mowy wykryje naruszenie protokołu od klienta, usługa przerywa połączenia obiektu WebSocket po powrocie *kod stanu* i *Przyczyna* dla zakończenia połączenia. Aplikacje klienckie, można użyć tych informacji rozwiązanie naruszenia.

#### <a name="incorrect-message-format"></a>Format komunikatu niepoprawne

Jeśli klient wysyła tekst lub binarny komunikatu do usługi, która nie jest zakodowany w nieprawidłowym formacie podane w tej specyfikacji, Usługa zamyka połączenie z *1007 nieprawidłowych danych ładunku* kod stanu. 

Usługa zwraca ten kod stanu z różnych powodów, jak pokazano w poniższych przykładach:

* "Komunikat niepoprawny format. Komunikatu binarnego ma nieprawidłowy nagłówek rozmiar prefiksu". Klient wysłał wiadomość binarne prefiksie rozmiar nieprawidłowy nagłówek.

* "Komunikat niepoprawny format. Binarny wiadomość ma nieprawidłowy nagłówek rozmiar". Klient wysłany komunikat binarne określony rozmiar nieprawidłowy nagłówek.

* "Komunikat niepoprawny format. Nagłówki komunikatów binarnych dekodowania w formacie UTF-8 nie powiodło się." Klient wysłał komunikatu binarnego, która zawiera nagłówki, które nie zostały poprawnie zakodowany w formacie UTF-8.

* "Komunikat niepoprawny format. Tekst wiadomości nie zawiera danych." Klient wysłał wiadomość SMS, która nie zawiera żadnych danych treści.

* "Komunikat niepoprawny format. Wiadomość SMS dekodowania w formacie UTF-8 nie powiodło się." Klient wysłał wiadomość SMS, który nie został poprawnie zakodowany w formacie UTF-8.

* "Komunikat niepoprawny format. Wiadomość SMS zawiera separatora nagłówka". Klient wysłał wiadomość SMS, który nie zawiera nagłówka separator ani używany separatora nieprawidłowy nagłówek.

#### <a name="missing-or-empty-headers"></a>Nagłówki lub jest pusty

Jeśli klient wysyła komunikat, który nie ma wymaganego nagłówki *X-RequestId* lub *ścieżki*, Usługa zamyka połączenie z *błąd protokołu 1002* kod stanu. Komunikat jest "Brak lub być pusty nagłówek. {Nazwa nagłówka}."

#### <a name="requestid-values"></a>Identyfikator żądania wartości

Jeśli klient wysyła komunikat, który określa *X-RequestId* nagłówka w niepoprawnym formacie, Usługa zamyka połączenie i zwraca *błąd protokołu 1002* stanu. Komunikat jest "nieprawidłowe żądanie. Wartość nagłówka X-RequestId nie została określona w formacie UUID dash nie."

#### <a name="audio-encoding-errors"></a>Błędy kodowania audio

Jeśli klient wyśle fragmentu audio, który inicjuje Włącz i audio format lub kodowanie nie jest zgodny z wymaganymi specyfikacjami, Usługa zamyka połączenie i zwraca *1007 nieprawidłowych danych ładunku* kod stanu. Komunikat wskazuje format kodowania źródła błędu.

#### <a name="requestid-reuse"></a>Identyfikator żądania ponownego użycia

Po zakończeniu Włącz, jeśli klient wyśle wiadomość ponownie używa identyfikatora żądania z tego Włącz, Usługa zamyka połączenie i zwraca *błąd protokołu 1002* kod stanu. Komunikat jest "nieprawidłowe żądanie. Ponowne użycie identyfikatorów żądania nie jest dozwolone."

## <a name="connection-failure-telemetry"></a>Telemetrii awarii połączenia

Aby zapewnić najlepsze możliwe środowisko korzystania, klienci muszą informuje mowy usługi sygnatury czasowe dla ważne punkty kontrolne w ciągu połączenia za pomocą *telemetrii* wiadomości. Jest równie ważne jest, że klienci poinformować usługi połączeń, które zostały próby, ale nie powiodło się.

Próba każdego połączenia nie powiodło się, klienci tworzą *telemetrii* wiadomości przy użyciu unikatowego *X-RequestId* wartość nagłówka. Ponieważ klient nie może ustanowić połączenia, *ReceivedMessages* pola w treści JSON można pominąć. Tylko `Connection` wpis w *metryki* pole jest uwzględniane. Ten wpis zawiera początek i koniec sygnatury czasowe, a także warunek błędu, który napotkano.

### <a name="connection-retries-in-telemetry"></a>Ponowne próby połączenia w telemetrii

Klienci powinna wskazywać *ponownych prób* z *próby nawiązania połączenia z wielu* przez zdarzenie powodujące próba nawiązania połączenia. Prób połączenia, które są przeprowadzane programowo bez żadnych danych wprowadzonych przez użytkownika są ponownych prób. Wielokrotne próby połączenia, które są wykonywane w odpowiedzi na dane wejściowe użytkownika są wielokrotne próby połączenia. Klienci Nadaj każdej próbie wyzwalane przez użytkownika połączenia unikatowych *X-RequestId* i *telemetrii* wiadomości. Ponowne użycie klientów *X-RequestId* dla programowe ponownych prób. Jeśli wprowadzono wiele ponownych prób dla pojedynczego połączenia, kolejnymi ponowieniami prób jest uwzględniona jako `Connection` wpis w *telemetrii* wiadomości.

Na przykład, załóżmy, że użytkownik mówi trigger — słowo kluczowe, aby uruchamiały połączenie, a pierwsza próba połączenia zakończy się niepowodzeniem z powodu błędów DNS. Jednak drugi próby, która ma zostać programowo przez klienta zakończy się pomyślnie. Ponieważ klient ponowione połączenie bez konieczności wprowadzania dodatkowych danych wejściowych od użytkownika, klient używa pojedynczego *telemetrii* komunikat z wieloma `Connection` wpisów do opisywania połączenia.

Inny przykład załóżmy, że użytkownik mówi trigger — słowo kluczowe, aby uruchamiały połączenie i ta próba połączenia nie powiedzie się po trzech ponownych prób. Klient zrezygnuje zatrzymuje próby nawiązania połączenia z usługą i informuje użytkownika, że coś poszło źle. Użytkownik mówi następnie wyzwalacza — słowo kluczowe ponownie. Teraz, załóżmy, że klient łączy się z usługą. Po nawiązaniu połączenia, natychmiast wysyła *telemetrii* komunikatu do usługi, który zawiera trzy `Connection` wpisów, które opisują błędów połączenia. Od momentu odebrania `turn.end` wiadomości, klient wysyła innego *telemetrii* komunikat, który opisuje udane połączenie.

## <a name="error-message-reference"></a>Dokumentacja komunikatów o błędach

### <a name="http-status-codes"></a>Kody stanu HTTP

| Kod stanu HTTP | Opis | Rozwiązywanie problemów |
| - | - | - |
| 400 Niewłaściwe żądanie | Klient wysłał żądanie połączenia obiektu WebSocket, który jest niepoprawne. | Sprawdź podane wszystkich wymaganych parametrów i nagłówków HTTP oraz czy wartości są prawidłowe. |
| 401 nieautoryzowane | Klient nie zawiera informacji o autoryzacji wymagane. | Sprawdź, czy wysyłasz *autoryzacji* nagłówka w połączenia obiektu WebSocket. |
| 403 Zabroniony | Klient wysłał informacji o autoryzacji, ale był nieprawidłowy. | Sprawdź, czy nie wysyłasz wygasły lub nieprawidłowa wartość *autoryzacji* nagłówka. |
| 404 — Nie odnaleziono | Klient próbuje uzyskać dostęp ścieżki adresu URL, który nie jest obsługiwany. | Sprawdź, czy używasz poprawny adres URL dla połączenia obiektu WebSocket. |
| 500 Błąd serwera | Usługa napotkała błąd wewnętrzny i nie można spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |
| 503 — usługa niedostępna | Usługa nie jest dostępny do obsługi żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

### <a name="websocket-error-codes"></a>Kody błędów protokołu WebSocket

| Kod WebSocketsStatus | Opis | Rozwiązywanie problemów |
| - | - | - |
| Normalne zamknięcia 1000 | Usługa zamknięcie połączenia protokołu WebSocket bez błędów. | Jeśli nieoczekiwanego zamknięcia protokołu WebSocket, odczytać dokumentacji, aby upewnić się, że rozumiesz, jak i kiedy usługa można zakończyć połączenia obiektu WebSocket. |
| Błąd protokołu 1002 | Klient nie może być zgodne z wymaganiami protokołu. | Sprawdź informacje w dokumentacji protokołu i wyraźnie o wymaganiach. Przeczytaj dokumentację poprzedniej o przyczyny błędu, aby zobaczyć, czy w przypadku naruszenia wymagania protokołu. |
| 1007 nieprawidłowy ładunek danych | Klient wysłał nieprawidłowy ładunek w komunikacie protokołu. | Sprawdź ostatnią wiadomością wysłaną z usługą błędów. Przeczytaj dokumentację poprzedniej o błędach ładunku. |
| Błąd serwera 1011 | Usługa napotkała błąd wewnętrzny i nie można spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

## <a name="related-topics"></a>Powiązane tematy

Zobacz [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) oznacza to implementacja protokołu WebSocket usług mowy.
