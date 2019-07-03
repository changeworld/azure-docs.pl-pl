---
title: Protokół WebSocket rozpoznawania mowy Bing | Dokumentacja firmy Microsoft
titlesuffix: Azure Cognitive Services
description: Dokumentacja protokołu rozpoznawania mowy Bing, w oparciu o protokół WebSockets
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515317"
---
# <a name="bing-speech-websocket-protocol"></a>Protokół WebSocket rozpoznawania mowy Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozpoznawanie mowy Bing jest oparte na chmurze platformy, która oferuje najbardziej zaawansowanych algorytmów do konwertowania dźwięk mówiony na tekst. Protokół rozpoznawania mowy Bing definiuje [ustawienia połączenia](#connection-establishment) między aplikacjami klienta i usługę i komunikaty rozpoznawania mowy, które są wymieniane między odpowiedniki ([komunikatów pochodzących od klientów](#client-originated-messages) i [pochodzi z usługi wiadomości](#service-originated-messages)). Ponadto [komunikaty telemetryczne](#telemetry-schema) i [obsługi błędów](#error-handling) są opisane.

## <a name="connection-establishment"></a>Ustanawianie połączenia

Protokół usługi mowy zgodna ze specyfikacją standardowego protokołu WebSocket [IETF RFC 6455](https://tools.ietf.org/html/rfc6455). Połączenie WebSocket rozpoczyna się jako żądanie HTTP, który zawiera nagłówki HTTP, które wskazują klienta chęci uaktualnienia połączenia do WebSocket, zamiast korzystać z semantyką HTTP. Serwer wskazuje chęć uczestnictwa w połączeniem WebSocket, zwracając HTTP `101 Switching Protocols` odpowiedzi. Po wymianie to uzgadniania zarówno klienta, jak i usługi nie zamykaj gniazda i rozpocząć korzystanie opartego na komunikat protokołu do wysyłania i odbierania informacji.

Aby rozpocząć uzgadnianie protokołu WebSocket, aplikacja kliencka wysyła żądanie GET protokołu HTTPS do usługi. Obejmuje ona standardowych nagłówków uaktualnienia WebSocket wraz z innych nagłówków, które są specyficzne dla funkcji rozpoznawania mowy.

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

Usługa odpowiada za pomocą:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Wszystkie żądania mowy wymagają [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) szyfrowania. Korzystanie z żądaniami funkcji rozpoznawania mowy niezaszyfrowane nie jest obsługiwana. Następującą wersję protokołu TLS są obsługiwane:

* TLS 1.2

### <a name="connection-identifier"></a>Identyfikator połączenia

Usługa rozpoznawania mowy wymaga, że wszyscy klienci obejmują unikatowy identyfikator połączenia. Klienci *musi* obejmują *X ConnectionId* nagłówka podczas uruchamiania uzgadniania protokołu WebSocket. *X ConnectionId* nagłówek może mieć [unikatowym identyfikatorem](https://en.wikipedia.org/wiki/Universally_unique_identifier) wartość (UUID). Żądania uaktualnienia WebSocket, które nie zawierają *X ConnectionId*, nie określaj wartości *X ConnectionId* nagłówka, lub nie zawiera prawidłowej wartości identyfikatora UUID są odrzucane przez usługę za pośrednictwem protokołu HTTP `400 Bad Request` odpowiedzi.

### <a name="authorization"></a>Autoryzacja

Oprócz standardowych nagłówków uzgadnianie protokołu WebSocket wymagają żądaniami funkcji rozpoznawania mowy *autoryzacji* nagłówka. Bez tego pliku nagłówkowego są odrzucane przez usługę za pośrednictwem protokołu HTTP, żądania połączeń `403 Forbidden` odpowiedzi.

*Autoryzacji* nagłówek musi zawierać token dostępu tokenu Web JSON (JWT).

Aby dowiedzieć się, jak subskrybować i uzyskać klucze interfejsu API, które są używane do pobierania tokenów dostępu w usłudze prawidłowy token JWT, zobacz [subskrypcji usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) strony.

Klucz interfejsu API jest przekazywany do usługi tokenu. Na przykład:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Następujące informacje nagłówka są wymagane do uzyskania tokenu dostępu.

| Name (Nazwa) | Format | Opis |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Klucz subskrypcji |

Zwraca wartość usługi tokenu JWT token dostępu jako `text/plain`. Następnie tokenu JWT jest przekazywany jako `Base64 access_token` do uzgadniania jako *autoryzacji* nagłówków z prefiksem ciągu `Bearer`. Na przykład:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Pliki cookie

Klienci *musi* obsługujących pliki cookie HTTP, jak to określono w [RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Przekierowywanie HTTP

Klienci *musi* obsługi przekierowania standardowych mechanizmów, określony przez [specyfikacji protokołu HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Punktów końcowych rozpoznawania mowy

Klienci *musi* użyć odpowiednich punktów końcowych usługi mowy. Punkt końcowy zależy od trybu rozpoznawania i język. W tabeli przedstawiono kilka przykładów.

| Tryb | Path | Identyfikator URI usługi |
| -----|-----|-----|
| Interaktywne | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| konwersacji | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dyktowanie | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Aby uzyskać więcej informacji, zobacz [identyfikator URI usługi](../GetStarted/GetStartedREST.md#service-uri) strony.

### <a name="report-connection-problems"></a>Problemy z połączeniem raportu

Klienci natychmiast powinien wysyłać raporty problemów napotkanych podczas nawiązywania połączenia. Message protocol dla raportowania połączenia zakończone niepowodzeniem jest opisana w [telemetrii awarii połączenia](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Ograniczenia dotyczące czasu trwania połączenia

W porównaniu z połączenia HTTP usługi typowej sieci web, ostatnie połączeń protokołu WebSocket *długie* czasu. Usługa rozpoznawania mowy umieszcza ograniczenia na czas trwania połączeń protokołu WebSocket z usługą:

 * Maksymalny czas trwania dla dowolnego aktywne połączenia protokołu WebSocket to 10 minut. Połączenie jest aktywne, jeśli usługa lub klient wysyła komunikaty protokołu WebSocket za pośrednictwem tego połączenia. Usługa kończy połączenie bez ostrzeżenia, po osiągnięciu limitu. Klienci opracować scenariuszy użytkowników, które nie wymagają połączenia pozostaną aktywne po lub w pobliżu połączenia maksymalny okres istnienia.

 * Maksymalny czas trwania dla dowolnego nieaktywnego połączenia protokołu WebSocket to 180 sekund. Połączenie jest aktywne, jeśli usługa ani klient wysłano komunikat protokołu WebSocket za pośrednictwem połączenia. Po osiągnięciu maksymalnego nieaktywne okresu istnienia, usługa kończy nieaktywnego połączenia protokołu WebSocket.

## <a name="message-types"></a>Typy komunikatu

Po nawiązaniu połączenia protokołu WebSocket między klientem a usługą zarówno klient, jak i usługa może wysyłać wiadomości. W tej sekcji opisano format te komunikaty protokołu WebSocket.

[6455 RFC organizacji IETF](https://tools.ietf.org/html/rfc6455) Określa komunikaty protokołu WebSocket mogą przesyłać dane przy użyciu tekstu lub kodowanie binarne. Dwa kodowania używają różnych formatów w locie. Każdy format jest zoptymalizowany pod kątem efektywne kodowanie, transmisji i dekodowanie ładunek komunikatu.

### <a name="text-websocket-messages"></a>Komunikaty protokołu WebSocket tekstu

Komunikaty protokołu WebSocket tekst przenoszenia ładunek informacji tekstowych, która składa się z części nagłówki i treść rozdzielone pary znanych nowego wiersza powrotu karetki podwójnej precyzji dla wiadomości HTTP. I, takich jak wiadomości HTTP wiadomości protokołu WebSocket tekstu określ nagłówki w *Nazwa: wartość* format rozdzielone parę pojedynczego karetki znakami nowego wiersza. Dowolny tekst, zawarte w wiadomości SMS WebSocket *musi* użyj [UTF-8](https://tools.ietf.org/html/rfc3629) kodowania.

Komunikaty protokołu WebSocket tekstu, należy określić ścieżkę wiadomości w nagłówku *ścieżki*. Wartość tego nagłówka stanowi musi być jeden z typów wiadomości protokołu rozpoznawania mowy, zdefiniowane w dalszej części tego dokumentu.

### <a name="binary-websocket-messages"></a>Binarny komunikaty protokołu WebSocket

Binarny komunikaty protokołu WebSocket przenoszenia ładunek danych binarnych. Za pomocą protokołu rozpoznawania mowy usługi audio jest przesyłane do i odbierane z usługi za pomocą binarne komunikatów protokołu WebSocket. Wszystkie inne komunikaty są komunikaty protokołu WebSocket tekstu.

Takie jak tekst wiadomości protokołu WebSocket binarne wiadomości protokołu WebSocket składają się nagłówek i treść sekcji. Określ pierwsze 2 bajty obiektu komunikatu binarnego WebSocket, [big-endian](https://en.wikipedia.org/wiki/Endianness) kolejność sekcji nagłówka rozmiar 16-bitową liczbę całkowitą. Minimalna nagłówek o rozmiarze sekcji to 0 bajtów. Maksymalny rozmiar to 8192 bajtów. Tekst w nagłówkach binarne komunikaty protokołu WebSocket *musi* użyj [US-ASCII](https://tools.ietf.org/html/rfc20) kodowania.

Nagłówków binarne komunikatu protokołu WebSocket są kodowane w tym samym formacie jak wiadomości protokołu WebSocket tekstu. *Nazwa: wartość* format jest oddzielona parę pojedynczego karetki znakami nowego wiersza. Binarny komunikaty protokołu WebSocket, należy określić ścieżkę wiadomości w nagłówku *ścieżki*. Wartość tego nagłówka stanowi musi być jeden z typów wiadomości protokołu rozpoznawania mowy, zdefiniowane w dalszej części tego dokumentu.

Tekst i binarny komunikaty protokołu WebSocket są używane w protokole usługa rozpoznawania mowy.

## <a name="client-originated-messages"></a>Komunikatów pochodzących od klientów

Po nawiązaniu połączenia zarówno klient, jak i usługi można uruchomić do wysyłania wiadomości. W tej sekcji opisano format i ładunku komunikatów, które aplikacje klienckie wysyłają do usługi rozpoznawania mowy. Sekcja [pochodzi z usługi wiadomości](#service-originated-messages) przedstawia informacje o wiadomości, które pochodzą z usługi rozpoznawania mowy i są wysyłane do aplikacji klienckich.

Główne wiadomości wysłane przez klienta do usług są `speech.config`, `audio`, i `telemetry` wiadomości. Zanim firma Microsoft uważa, każdy komunikat szczegółowy, typowe wymagane nagłówki komunikatów te komunikaty są opisane.

### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

Następujące nagłówki są wymagane dla wszystkich komunikatów pochodzących od klientów.

| nagłówek | Wartość |
|----|----|
| Path | Ścieżka podana w tym dokumencie |
| X-RequestId | Identyfikator UUID w formacie "nie-dash" |
| X-Timestamp | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |

#### <a name="x-requestid-header"></a>Nagłówek X-RequestId

Żądań pochodzących od klientów unikatowo zidentyfikować za pomocą *X RequestId* nagłówka komunikatu. Tego pliku nagłówkowego jest wymagana dla wszystkich komunikatów pochodzących od klientów. *X RequestId* wartość nagłówka musi być to identyfikator UUID w formie "nie kreska", na przykład *123e4567e89b12d3a456426655440000*. Jego *nie* w postaci kanonicznej *123e4567-e89b-12d3-a456-426655440000*. Żądania bez *X RequestId* nagłówka przy użyciu wartości nagłówka, w formacie nieprawidłowe identyfikatory UUID nie są przyczyną usługę, aby zakończyć połączenie protokołu WebSocket.

#### <a name="x-timestamp-header"></a>Nagłówek X-znacznik czasu:

Każdy komunikat wysyłany do usługi rozpoznawania mowy przez aplikację kliencką *musi* obejmują *sygnatura czasowa X* nagłówka. Wartość dla tego pliku nagłówkowego jest czas, kiedy klient wysyła komunikat. Żądania bez *sygnatura czasowa X* nagłówek lub z wartością nagłówek, który ma niewłaściwy format spowodować, że usługa przerywania połączenia protokołu WebSocket.

*Sygnatura czasowa X* wartość nagłówka musi mieć postać "yyyy'-'MM'-'dd'T' HH': 'mm':'ss '." fffffffZ "gdzie"fffffff"to część 1 sekunda. Na przykład "12,5" oznacza "12 + 5/10 sekundy i"12.526"oznacza" 12 oraz sekund 526/1000". Ten format jest zgodny z [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i, w przeciwieństwie do standardowego protokołu HTTP *data* nagłówka, funkcja ta może zapewnić rozpoznawanie milisekund. Aplikacje klienckie mogą okrągłe sygnatury czasowe do najbliższej milisekundy. Aplikacje klienckie, należy upewnić się, że zegara urządzenia śledzi dokładnego czasu za pomocą [serwera protokołu NTP (Network Time)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Komunikat `speech.config`

Usługa rozpoznawania mowy musi znać cechy aplikacji w taki sposób, aby zapewnić najlepsze możliwe mowę. Dane wymagane właściwości obejmują informacje o urządzeniu i systemu operacyjnego, która napędza aplikację. Możesz podać tę informację w `speech.config` wiadomości.

Klienci *musi* wysyłania `speech.config` komunikatów bezpośrednio w przypadku, po nawiązaniu połączenia usługa rozpoznawania mowy i przed dowolnego wysyłają `audio` wiadomości. Trzeba będzie wysłać `speech.config` tylko raz komunikatu dla połączenia.

| Pole | Opis |
|----|----|
| Kodowanie komunikatu protokołu WebSocket | Text |
| Treść | Ładunek w strukturze JSON |

#### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

| Nazwa nagłówka | Wartość |
|----|----|
| Path | `speech.config` |
| X-Timestamp | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

Podobnie jak w przypadku wszystkich wiadomości pochodzi klienta za pomocą protokołu rozpoznawania mowy usługi `speech.config` komunikat *musi* obejmują *sygnatura czasowa X* nagłówek, który rejestruje czas zegara klienta UTC, kiedy wiadomość została wysłana w usłudze. `speech.config` Komunikat *nie* wymagają *X RequestId* nagłówka ponieważ ten komunikat nie jest skojarzona z żądaniem określonego mowy.

#### <a name="message-payload"></a>Ładunek komunikatu
Ładunek `speech.config` komunikat jest strukturę JSON, który zawiera informacje o aplikacji. Te informacje można znaleźć w poniższym przykładzie. Zawiera informacje o kontekście dla klienta i urządzenia *kontekstu* element strukturze JSON.

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

Elementem system.version `speech.config` wiadomość zawiera wersję mowy SDK oprogramowanie używane przez aplikację klienta lub urządzenia. Wartość ma postać *major.minor.build.branch*. Możesz pominąć *gałęzi* składnik, jeśli nie ma zastosowania.

##### <a name="os-element"></a>OS element

| Pole | Opis | Sposób użycia |
|-|-|-|
| os.platform | Systemu operacyjnego platformy, który jest hostem aplikacji, na przykład, Windows, Android, iOS i Linux |Wymagane |
| os.name | Nazwa produktu systemu operacyjnego, na przykład Debian lub Windows 10 | Wymagane |
| OS.Version | Wersja systemu operacyjnego w formie *major.minor.build.branch* | Wymagane |

##### <a name="device-element"></a>Element urządzenia

| Pole | Opis | Sposób użycia |
|-|-|-|
| device.manufacturer | Producent sprzętu urządzenia | Wymagane |
| device.model | Model urządzenia | Wymagane |
| device.version | Wersja oprogramowania urządzenia, dostarczone przez producenta urządzenia. Ta wartość określa wersję urządzenia, które mogą być śledzone przez producenta. | Wymagane |

### <a name="message-audio"></a>Komunikat `audio`

Aplikacje klienckie przeznaczone dla komputerów z obsługą mowy wysyłać audio usługa rozpoznawania mowy za pomocą konwersji strumienia audio na serię fragmenty audio. Każdy fragment audio niesie ze sobą segment dźwięk mówiony, który ma być przetłumaczone przez usługę. Maksymalny rozmiar jednego fragmentu audio jest 8192 bajtów. Komunikaty strumienia audio są *komunikatów binarnych WebSocket*.

Klienci używają `audio` wiadomości do wysłania fragmentu audio do usługi. Klienci wysyłają tych fragmentów mowy usługi dla transkrypcji i przeczytaj dźwięku z mikrofonu we fragmentach. Pierwszy `audio` wiadomości musi zawierać sformułowany nagłówek, który prawidłowo Określa, że audio są zgodne w jednym z formatów kodowania, obsługiwane przez usługę. Dodatkowe `audio` wiadomości zawierają tylko binarne audio strumienia danych odczytanych z mikrofonu.

Klienci mogą opcjonalnie wysyłają `audio` wiadomość z treścią o zerowej długości. Ta wiadomość informuje usługę, że klient zna użytkownik zatrzymał mówić, wypowiedź została zakończona i mikrofonu jest wyłączony.

Usługa rozpoznawania mowy używa pierwszego `audio` wiadomość, która zawiera identyfikator unikatowy żądania w celu sygnalizowania, że początku nowego cyklu żądania/odpowiedzi lub *Włącz*. Gdy usługa otrzyma `audio` komunikatów za pomocą nowego identyfikatora żądania, odrzuca on umieszczonych w kolejce lub niewysłanych wiadomości, które są skojarzone z dowolnego poprzedniego Włącz.

| Pole | Opis |
|-------------|----------------|
| Kodowanie komunikatu protokołu WebSocket | Binarny |
| Treść | Dane binarne dla fragmentu audio. Maksymalny rozmiar to 8192 bajtów. |

#### <a name="required-message-headers"></a>Nagłówki komunikatów wymagane

Następujące nagłówki są wymagane w przypadku wszystkich `audio` wiadomości.

| nagłówek         |  Wartość     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | Identyfikator UUID w formacie "nie-dash" |
| X-Timestamp | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | Typ zawartości audio. Typ musi być albo *audio/x-wav* (PCM) lub *audio/silk* (SILK). |

#### <a name="supported-audio-encodings"></a>Obsługiwane są audio

W tej sekcji opisano audio kodery-dekodery obsługiwane przez usługę rozpoznawania mowy.

##### <a name="pcm"></a>MODUŁU PCM

Usługa rozpoznawania mowy akceptuje nieskompresowanych pulse kodu modulacji (PCM) audio. Audio są wysyłane do usługi w [WAV](https://en.wikipedia.org/wiki/WAV) formatowania, więc pierwszym dźwięk, Podziel *musi* zawierać prawidłową [Format pliku wymiany zasobów](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) nagłówka (RIFF). Jeśli klient inicjuje Włącz z fragmentu audio, który wykonuje *nie* obejmują prawidłowego nagłówka RIFF, usługa odrzuca żądanie i przerywa połączenie protokołu WebSocket.

Dźwięk PCM *musi* będą próbkowane w 16 kHz z 16 bitów na przykład i jednego kanału (*riff-16khz-16-bitowych-mono-pcm*). Usługa rozpoznawania mowy nie obsługuje stereo strumieni audio i odrzuca strumieni audio, które nie korzystają z określona szybkość transmisji bitów, częstotliwość próbkowania lub liczby kanałów.

##### <a name="opus"></a>Dziele

Dziele jest otwarty, niepodlegającej opłatom tantiemowym, wszechstronny kodera-dekodera audio. Usługa rozpoznawania mowy obsługuje dziele przy stałej szybkości `32000` lub `16000`. Tylko `OGG` kontener dziele jest obecnie obsługiwany określoną przez `audio/ogg` typ mime.

Aby użyć dziele, zmodyfikuj [próbki kodu JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) i zmień `RecognizerSetup` metody do zwrócenia.

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

#### <a name="detect-end-of-speech"></a>Wykrywanie zakończenia mowy

Ludzie jawnie sygnalizuje, gdy będą gotowe mówić. Każda aplikacja, która akceptuje mowy jako dane wejściowe ma jedną z dwóch opcji obsługi koniec mowy w strumień audio: wykrywanie zakończenia mowy i wykrywanie zakończenia mowy klienta usługi. Z tych dwóch opcji wykrywanie zakończenia mowy usługi zwykle zapewnia lepsze środowisko użytkownika.

##### <a name="service-end-of-speech-detection"></a>Wykrywanie zakończenia mowy usługi

Aby utworzyć środowisko idealne bezobsługowego mowy, aplikacje umożliwiają usługę, aby wykryć, kiedy użytkownik zakończył mówić. Klienci wysyłają dźwięku z mikrofonu jako *audio* chunks, dopóki usługa wykrywa wyciszenia i odpowiada z powrotem `speech.endDetected` wiadomości.

##### <a name="client-end-of-speech-detection"></a>Wykrywanie zakończenia mowy klienta

Aplikacje klienckie, które umożliwiają użytkownikom w celu sygnalizowania, że koniec mowy w jakiś sposób również umożliwiają usłudze, sygnału. Na przykład aplikacja kliencka może być "Stop" lub "Wycisz" przycisk, który użytkownik może nacisnąć. O końcu mowy, Wyślij aplikacje klienckie *audio* fragmentów wiadomość z treścią o zerowej długości. Usługa rozpoznawania mowy interpretuje ten komunikat, jako koniec przychodzącego strumienia audio.

### <a name="message-telemetry"></a>Komunikat `telemetry`

Aplikacje klienckie *musi* potwierdzić końca każdego Włącz poprzez wysyłanie telemetrii dotyczącej ruch do usługi rozpoznawania mowy. Włącz end potwierdzenia umożliwia usługa rozpoznawania mowy upewnić się, że wszystkie komunikaty niezbędne do ukończenia żądania i odpowiedzi przez punkt końcowy prawidłowo zostały odebrane przez klienta. Włącz end potwierdzenia umożliwia także usługa rozpoznawania mowy sprawdzić, czy aplikacje klienckie działają zgodnie z oczekiwaniami. Te informacje są nieocenione, jeśli potrzebujesz pomocy w rozwiązywaniu problemów aplikacji z obsługą mowy.

Klienci muszą potwierdzić koniec Włącz, wysyłając `telemetry` wiadomości wkrótce od momentu odebrania `turn.end` wiadomości. Klienci próbują należy potwierdzić `turn.end` tak szybko, jak to możliwe. Jeśli aplikacja kliencka nie powiedzie się potwierdzić koniec Włącz, usługa rozpoznawania mowy może przerwać połączenie z powodu błędu. Klienci muszą wysyłać tylko jeden `telemetry` komunikat dla każdego żądania i odpowiedzi identyfikowane przez *X RequestId* wartość.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `telemetry` |
| X-Timestamp | Sygnatura czasowa zegara klienta UTC w formacie ISO 8601 |
| Content-Type | `application/json` |
| Treść | Strukturę JSON, która zawiera informacje o kliencie o Włącz |

Schemat dla treści `telemetry` wiadomości jest zdefiniowany w [schematu danych Telemetrycznych](#telemetry-schema) sekcji.

#### <a name="telemetry-for-interrupted-connections"></a>Dane telemetryczne dla przerwanego połączenia

Jeśli połączenie sieciowe nie powiedzie się z jakiegokolwiek powodu w kolejce, a klient ma *nie* otrzymywać `turn.end` komunikatów z usługi, klient wysyła `telemetry` wiadomości. Ta wiadomość opisuje nieudanych żądań następnym razem, gdy klient wysyła połączenia z usługą. Klienci nie muszą podejmować natychmiastowe połączenie do wysłania `telemetry` wiadomości. Komunikat może być buforowane na kliencie i przesyłane za pośrednictwem przyszłych połączenia użytkownik zażądał. `telemetry` Komunikat dla żądań zakończonych niepowodzeniem *musi* użyj *X RequestId* wartość z żądania nie powiodło się. Mogą być wysyłane do usługi, gdy tylko jest nawiązywane połączenie, nie trzeba czekać do wysyłania czy odbierania dla innych wiadomości.

## <a name="service-originated-messages"></a>Komunikatów pochodzących od usługi

W tej sekcji opisano wiadomości, które pochodzą z usługi rozpoznawania mowy i są wysyłane do klienta. Usługa rozpoznawania mowy przechowuje rejestr możliwości klienta i generuje komunikaty wymagane przez każdego klienta, więc nie wszyscy klienci otrzymają wszystkie komunikaty, które są opisane w tym miejscu. Celu skrócenia programu, komunikaty są przywoływane przez wartość *ścieżki* nagłówka. Na przykład nazywamy WebSocket wiadomość SMS z *ścieżki* wartość `speech.hypothesis` jako speech.hypothesis wiadomości.

### <a name="message-speechstartdetected"></a>Komunikat `speech.startDetected`

`speech.startDetected` Komunikat oznacza, że usługa rozpoznawania mowy mowy w strumienia audio.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Treść | Struktura JSON, która zawiera informacje o warunkach, gdy wykryte początek mowy. *Przesunięcie* pola w tej strukturze określa przesunięcie (w jednostkach 100-nanosekundowych) czas wykrycia mowy w usłudze stream audio względem początku strumienia. |

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

Podczas rozpoznawania mowy usługa rozpoznawania mowy okresowo generuje hipotezę o wyrazy usługi został rozpoznany. Usługa rozpoznawania mowy wysyła te hipotezy do klienta, co około 300 milisekund. `speech.hypothesis` Nadaje *tylko* na zwiększenie komfortu mowy. Nie należy wykonać wszelkie zależności od zawartości lub dokładności tekstu w tych komunikatach.

 `speech.hypothesis` Komunikatu ma zastosowanie do tych klientów, którzy mają możliwość renderowania tekstu i aby przekazać opinię niemal w czasie rzeczywistym, rozpoznawania w toku do osoby, która mówiącego Prezydenta.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `speech.hypothesis` |
| X-RequestId | Identyfikator UUID w formacie "nie-dash" |
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

*Przesunięcie* element określa przesunięcie (w jednostkach 100-nanosekundowych) kiedy został rozpoznany frazy, względem początku strumienia audio.

*Czas trwania* element określa czas trwania (w jednostkach 100-nanosekundowych) Ta fraza mowy.

Klienci nie należy czynić żadnych założeń o częstotliwości, czasu lub tekst zawarty w hipotezę mowy lub spójność tekst w dowolnej hipotezy dwóch mowy. Hipotezy to po prostu migawki z procesem transkrypcji w usłudze. Przedstawiają one stabilny akumulacja transkrypcji. Na przykład pierwsza hipoteza mowy może zawierać słowa "Zabawa poprawnie", a drugi hipotezę może zawierać słowa "Rady find." Usługa rozpoznawania mowy nie wykonuje żadnych przetwarzanie końcowe (na przykład wielkość liter, znaki interpunkcyjne) na podstawie tekstu w hipotezę mowy.

### <a name="message-speechphrase"></a>Komunikat `speech.phrase`

Gdy usługa rozpoznawania mowy Określa, że ma on wystarczających informacji do uzyskania wyniku rozpoznawania, który nie ulegnie zmianie, tworzy usługi `speech.phrase` wiadomości. Usługa rozpoznawania mowy tworzy następujące wyniki po wykryciu, że użytkownik zakończył zdania lub frazy.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `speech.phrase` |
| Content-Type | application/json |
| Treść | Fraza mowy strukturze JSON |

Schemat JSON frazy mowy zawiera następujące pola: `RecognitionStatus`, `DisplayText`, `Offset`, i `Duration`. Aby uzyskać więcej informacji na temat tych pól, zobacz [odpowiedzi transkrypcji](../concepts.md#transcription-responses).

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

`speech.endDetected` Komunikat informuje, że aplikacja kliencka powinna zostać przerwana strumieniowe przesyłanie audio do usługi.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `speech.endDetected` |
| Treść | Struktura JSON, która zawiera przesunięcie, gdy wykryto koniec mowy. Przesunięcie jest reprezentowany w jednostkach 100-nanosekundowych przesunięcie od początku dźwięk, który jest używany do rozpoznawania. |
| Content-Type | application/json; charset=utf-8 |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*Przesunięcie* element określa przesunięcie (w jednostkach 100-nanosekundowych) kiedy został rozpoznany frazy, względem początku strumienia audio.

### <a name="message-turnstart"></a>Komunikat `turn.start`

`turn.start` Sygnalizuje początek Włącz z punktu widzenia usługi. `turn.start` Wiadomości, zawsze jest pierwszy komunikat odpowiedzi, pojawi się do każdego żądania. Jeśli nie otrzymasz `turn.start` komunikatu, założono, że stan połączenia usługi jest nieprawidłowy.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `turn.start` |
| Content-Type | application/json; charset=utf-8 |
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

Treść `turn.start` komunikat jest strukturą JSON, która zawiera kontekst na początku Włącz. *Kontekstu* element zawiera *serviceTag* właściwości. Ta właściwość określa wartość tagu, usługi skojarzone z Włącz. Ta wartość może być używana przez firmę Microsoft, jeśli potrzebujesz pomocy, rozwiązywanie problemów z błędami w aplikacji.

### <a name="message-turnend"></a>Komunikat `turn.end`

`turn.end` Sygnalizuje koniec Włącz z punktu widzenia usługi. `turn.end` Wiadomości, zawsze jest ostatni komunikat odpowiedzi, pojawi się do każdego żądania. Klienci mogą używać odebranie tej wiadomości jako sygnał dla działań oczyszczania i przejście do stanu bezczynności. Jeśli nie otrzymasz `turn.end` komunikatu, założono, że stan połączenia usługi jest nieprawidłowy. W takich przypadkach Zamknij istniejące połączenie z usługą i ponownie.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatu protokołu WebSocket | Text |
| Path | `turn.end` |
| Treść | Brak |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schemat danych telemetrycznych

Treść *telemetrii* komunikat jest strukturę JSON, która zawiera informacje o kliencie o Wyłącz lub próby połączenia. Struktura składa się z klienta sygnatury czasowe, służące do rejestrowania po wystąpieniu zdarzenia klienta. Każdy sygnatura czasowa musi być w formacie ISO 8601, zgodnie z opisem w sekcji "Header sygnatura czasowa X". *Dane telemetryczne* wiadomości, który nie należy określać wszystkie wymagane pola w strukturze JSON lub nie używaj format sygnatury czasowej poprawne może spowodować, że usługa zakończyć połączenie z klientem. Klienci *musi* podać prawidłowe wartości dla wszystkich wymaganych pól. Klienci *powinien* Podaj wartości dla pól opcjonalnych zawsze, gdy jest to odpowiednie. Wartości wyświetlane w przykładach w tej sekcji są wyłącznie do celów informacyjnych.

Schemat danych telemetrycznych jest podzielona na następujące elementy: Odebrano komunikat sygnatury czasowe i metryki. Format i użycia każdej części jest określona w poniższych sekcjach.

### <a name="received-message-time-stamps"></a>Sygnatury czasowe odebranego komunikatu

Klienci mogą zawierać godziny od otrzymania wartości dla wszystkich wiadomości, które otrzyma po pomyślnym nawiązaniu połączenia z usługą. Te wartości należy zanotować czas podczas klienta *Odebrano* każdy komunikat z sieci. Wartość nie należy rejestrować dowolnym innym czasie. Na przykład klient nie powinien zanotować czas podczas jego *działał* wiadomości. Sygnatury czasowe odebranego komunikatu są określone w tablicy *Nazwa: wartość* pary. Nazwa pary Określa *ścieżki* wartość komunikatu. Wartość pary określa czas klienta, kiedy wiadomość została odebrana. Lub, jeśli odebrano więcej niż jeden komunikat o określonej nazwie, wartość pary jest tablica sygnatury czasowe, która wskazuje, kiedy te komunikaty zostały odebrane.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienci *musi* otrzymanie wszystkich komunikatów wysłanych przez usługę, umieszczając sygnatury czasowe dla tych wiadomości w treść kodu JSON. Jeśli klient nie otrzymanie wiadomości, usługa może przerwać połączenie.

### <a name="metrics"></a>Metryki

Klienci mogą zawierać informacje o zdarzeniach, które wystąpiły w okresie istnienia żądania. Obsługiwane są następujące metryki: `Connection`, `Microphone`, i `ListeningTrigger`.

### <a name="metric-connection"></a>Metryki `Connection`

`Connection` Metryka określa szczegółowe informacje o próby nawiązania połączenia przez klienta. Metryka musi zawierać sygnaturą czasową, gdy połączenie WebSocket zostało uruchomione i zakończone. `Connection` Metryka jest wymagana *tylko w przypadku pierwszego Włącz połączenia*. Włącza kolejnych nie trzeba dołączyć tę informację. Jeśli klient wysyła wiele prób połączenia, zanim połączenie zostanie nawiązane, informacje o *wszystkich* próby nawiązania połączenia mają zostać uwzględnione. Aby uzyskać więcej informacji, zobacz [telemetrii awarii połączenia](#connection-failure-telemetry).

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | `Connection` | Wymagane |
| Identyfikator | Wartość identyfikatora połączenia, który był używany w *X ConnectionId* nagłówek dla tego żądania połączenia | Wymagane |
| Uruchamianie | Czas, kiedy klient wysyłał żądania połączenia | Wymagane |
| End | Podczas gdy klient otrzymał powiadomienie, że połączenie zostało nawiązane pomyślnie, lub w przypadku błędów, odrzucona, odrzuconych lub nie powiodło się | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Połączenie zakończyło się pomyślnie, należy pominąć znak tego pola przez klientów. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadkach, błąd, w przeciwnym razie pominięcia |

Opis błędu nie powinna przekraczać 50 znaków i najlepiej musi mieć jedną z wartości wymienione w poniższej tabeli. Jeśli warunek błędu nie odpowiada jednej z tych wartości, klienci mogą używać zwięzły opis warunku błędu przy użyciu [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez biały znak. Możliwość wysyłania *telemetrii* komunikat wymaga połączenia z usługą, więc tylko przejściowy lub tymczasowe błędy mogą być zgłaszane w *telemetrii* wiadomości. Błędów, które *trwale* bloku przez klienta podczas nawiązywania połączenia z usługą zapobiec wysyła wszystkie komunikaty do usługi, w tym *telemetrii* wiadomości.

| Błąd | Sposób użycia |
| ----- | ----- |
| DNSfailure | Klient nie może połączyć się z usługą z powodu błędu DNS w stosie sieci. |
| NoNetwork | Klient próbował połączenie, ale stos sieciowy zgłosił, że nie sieci fizycznej była dostępna. |
| NoAuthorization | Połączenie klienta nie powiodło się podczas próby uzyskania tokenu autoryzacji dla połączenia. |
| NoResources | Klient zabrakło niektórych lokalnych zasobów (np. pamięci), podczas próby nawiązania połączenia. |
| Zabroniony | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `403 Forbidden` kod stanu na żądanie uaktualnienia protokołu WebSocket. |
| Brak autoryzacji | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `401 Unauthorized` kod stanu na żądanie uaktualnienia protokołu WebSocket. |
| BadRequest | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `400 Bad Request` kod stanu na żądanie uaktualnienia protokołu WebSocket. |
| ServerUnavailable | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła HTTP `503 Server Unavailable` kod stanu na żądanie uaktualnienia protokołu WebSocket. |
| Błąd ServerError | Klient nie może połączyć się z usługą, ponieważ Usługa zwróciła `HTTP 500` kod stanu błędu wewnętrznego na żądanie uaktualnienia protokołu WebSocket. |
| limit czasu | Upłynął limit czasu bez odpowiedzi z usługi żądanie połączenia klienta. *Zakończenia* pole zawiera godzinę, kiedy klient przekroczyło limit czasu i zatrzymanie oczekiwanie na połączenie. |
| Błąd ClientError | Klient zakończył połączenie ze względu na błąd wewnętrznego klienta. |

### <a name="metric-microphone"></a>Metryki `Microphone`

`Microphone` Metryka jest wymagana dla wszystkich włącza mowy. Ta metryka mierzy czas na komputerze klienckim, podczas którego wejścia audio jest aktywnie używana dla żądania mowy.

Można użyć następujących przykładów jak wskazówki for rejestrujące *Start* wartości dla czasu `Microphone` metryki w aplikacji klienckiej:

* Aplikacja kliencka wymaga, że wymaga naciśnięcia przycisku fizycznego, można uruchomić z mikrofonu. Po naciśnięcie przycisku aplikacja kliencka odczytuje dane wejściowe z mikrofonu i wysyła je do usługi rozpoznawania mowy. *Start* wartość `Microphone` metryki rejestruje czas po naciśnięcie przycisku, gdy mikrofon jest inicjowany i przygotuj dane wejściowe. *Zakończenia* wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka zatrzymana, strumieniowe przesyłanie audio do usługi, po otrzymaniu jej `speech.endDetected` wiadomości z usługi.

* Aplikacja kliencka używa spotter — słowo kluczowe, który nasłuchuje "zawsze". Tylko wtedy, gdy spotter — słowo kluczowe wykrywa fraz wypowiadanych wyzwalacz aplikacja kliencka zbierania danych wejściowych z mikrofonu i wysyłać je do usługi rozpoznawania mowy. *Start* wartość `Microphone` metryki rejestruje czas, kiedy spotter — słowo kluczowe powiadomienia klienta, aby rozpocząć korzystanie z danych wejściowych z mikrofonu. *Zakończenia* wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka zatrzymana, strumieniowe przesyłanie audio do usługi, po otrzymaniu jej `speech.endDetected` wiadomości z usługi.

* Aplikacja kliencka ma dostęp do stały strumień audio i przeprowadza wykrywanie wyciszenia/mowy w tym strumienia audio w *modułu wykrywania mowy*. *Start* wartość `Microphone` metryki rejestruje czas podczas *modułu wykrywania mowy* powiadomienia klienta, aby rozpocząć korzystanie z danych wejściowych ze strumienia audio. *Zakończenia* wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka zatrzymana, strumieniowe przesyłanie audio do usługi, po otrzymaniu jej `speech.endDetected` wiadomości z usługi.

* Aplikacja kliencka przetwarza drugi Włącz żądania Włącz wielu i jest informowany za pomocą usługi wiadomości odpowiedzi, aby włączyć mikrofonu w celu zbierania danych wejściowych dla drugiego Włącz. *Start* wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka umożliwia mikrofon i rozpoczyna się przy użyciu danych wejściowych z tego źródła audio. *Zakończenia* wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka zatrzymana, strumieniowe przesyłanie audio do usługi, po otrzymaniu jej `speech.endDetected` wiadomości z usługi.

*Zakończenia* czasu wartość `Microphone` metryki rejestruje czas, kiedy aplikacja kliencka zatrzymana, przesyłanie strumieniowe wejścia audio. W większości sytuacji to zdarzenie występuje zaraz po klient otrzymał `speech.endDetected` wiadomości z usługi. Aplikacje klienckie mogą sprawdzić, czy są one prawidłowo odpowiadających protokołu poprzez zapewnienie, że *zakończenia* czasu wartość `Microphone` metryki występuje później niż wartość czasu odebrania `speech.endDetected` wiadomości. I ponieważ jest zwykle opóźnienia między końcem jednego Włącz a początek Włącz innego, klienci mogą weryfikują zgodności protokołów poprzez zapewnienie, że *Start* czasem `Microphone` metryki dla wszelkich kolejnych Wyłącz poprawnie rejestruje czas podczas klienta *pracę* przy użyciu mikrofonu wejściem strumienia audio w usłudze.

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | Mikrofon | Wymagane |
| Uruchamianie | Czas, kiedy klient uruchomiony przy użyciu audio dane wejściowe z mikrofonu lub innego strumienia audio lub odebrane wyzwalacza z spotter — słowo kluczowe | Wymagane |
| End | Czas, kiedy klient zatrzymana, za pomocą usługi stream mikrofon lub nagrania dźwiękowego | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Operacje mikrofon zakończyły się pomyślnie, należy pominąć znak tego pola przez klientów. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadkach, błąd, w przeciwnym razie pominięcia |

### <a name="metric-listeningtrigger"></a>Metryki `ListeningTrigger`
`ListeningTrigger` Metryka mierzy czas, gdy użytkownik wykonuje akcję, która inicjuje wejście mowy. `ListeningTrigger` Metryka jest opcjonalne, ale zachęcamy klientów, którzy mogą zapewnić tę metrykę, aby to zrobić.

Można użyć następujących przykładów jak wskazówki for rejestrujące *Start* i *zakończenia* wartości dla czasu `ListeningTrigger` metryki w aplikacji klienckiej.

* Aplikacja kliencka wymaga, że wymaga naciśnięcia przycisku fizycznego, można uruchomić z mikrofonu. *Start* wartość ta metryka rejestruje czas wypychania przycisku. *Zakończenia* wartość rejestruje czas, po zakończeniu wypychania przycisku.

* Aplikacja kliencka używa spotter — słowo kluczowe, który nasłuchuje "zawsze". Po słowie kluczowym spotter wykrywa frazy prowadzone wyzwalacza, aplikacja kliencka odczytuje dane wejściowe z mikrofonu i wysyła je do usługi rozpoznawania mowy. *Start* wartość ta metryka rejestruje czasu otrzymania dźwięk, który następnie wykryto frazy wyzwalacza spotter — słowo kluczowe. *Zakończenia* wartość rejestruje czas po ostatni wyraz frazy wyzwalacz był używany przez użytkownika.

* Aplikacja kliencka ma dostęp do stały strumień audio i przeprowadza wykrywanie wyciszenia/mowy w tym strumienia audio w *modułu wykrywania mowy*. *Start* wartość ta metryka rejestruje czas, *modułu wykrywania mowy* Odebrano dźwięk, który następnie wykryto mowy. *Zakończenia* wartość rejestruje czas podczas *modułu wykrywania mowy* wykryto mowy.

* Aplikacja kliencka przetwarza drugi Włącz żądania Włącz wielu i jest informowany za pomocą usługi wiadomości odpowiedzi, aby włączyć mikrofonu w celu zbierania danych wejściowych dla drugiego Włącz. Aplikacja kliencka powinna *nie* obejmują `ListeningTrigger` metryk dla tego ruchu.

| Pole | Opis | Sposób użycia |
| ----- | ----------- | ----- |
| Name (Nazwa) | ListeningTrigger | Optional (Opcjonalność) |
| Uruchamianie | Czas rozpoczęcia nasłuchiwania wyzwalacz klient | Wymagane |
| End | Czas, po zakończeniu nasłuchiwania wyzwalacz klient | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Operacja wyzwalacza zakończyło się pomyślnie, należy pominąć znak tego pola przez klientów. Maksymalna długość tego pola wynosi 50 znaków. | Wymagane w przypadkach, błąd, w przeciwnym razie pominięcia |

#### <a name="sample-message"></a>Przykładowy komunikat

Poniższy przykład pokazuje komunikaty telemetryczne z częściami ReceivedMessages i metryki:

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

W tej sekcji opisano rodzaje komunikaty o błędach i warunki, które Twoja aplikacja potrzebuje do obsługi.

### <a name="http-status-codes"></a>Kody stanu HTTP

Podczas uaktualniania żądanie protokołu WebSocket, usługa rozpoznawania mowy może zwrócić wszystkich standardowych kodów stanu HTTP, takich jak `400 Bad Request`itp. Aplikacja musi poprawnie obsługiwać tych warunków błędu.

#### <a name="authorization-errors"></a>Błędy autoryzacji

Jeśli niepoprawne autoryzacja została podana podczas uaktualniania WebSocket Speech Service zwraca HTTP `403 Forbidden` kod stanu. Wśród ze zdarzeniami wyzwalającymi tego kodu błędu to:

* Brak *autoryzacji* nagłówka

* Token autoryzacji nieprawidłowy

* Token autoryzacji wygasły

`403 Forbidden` Komunikat o błędzie nie wskazują na problem z usługą mowy. Ten komunikat o błędzie wskazuje na problem z aplikacją klienta.

### <a name="protocol-violation-errors"></a>Błędów naruszenia protokołu

Jeśli usługa rozpoznawania mowy wykryje naruszenie protokołu od klienta, usługa przerywa połączenie WebSocket po zwróceniu *kod stanu* i *Przyczyna* zakończenia. Aplikacje klienckie, można użyć tych informacji, rozwiązywanie problemów i naprawy naruszenia.

#### <a name="incorrect-message-format"></a>Format niepoprawny komunikat

Jeśli klient wysyła tekst lub komunikatu binarnego do usługi, która nie jest zakodowana w prawidłowym formacie podane w tej specyfikacji, Usługa zamyka połączenie z *1007 nieprawidłowe dane ładunku* kod stanu.

Usługa zwraca ten kod stanu różnych powodów, jak pokazano w poniższych przykładach:

* "Komunikat niepoprawny format. Komunikatu binarnego ma nieprawidłowy nagłówek rozmiar prefiksu". Klient wysłał komunikatu binarnego, który ma nieprawidłowy nagłówek rozmiar prefiksu.

* "Komunikat niepoprawny format. Komunikatu binarnego ma nieprawidłowy nagłówek rozmiar". Klient wysłał komunikatu binarnego, że określony rozmiar nieprawidłowy nagłówek.

* "Komunikat niepoprawny format. Nagłówki komunikatu binarnego dekodowanie do UTF-8 nie powiodło się." Klient wysłał komunikatu binarnego, która zawiera nagłówki, które nie zostały poprawnie zakodowany w formacie UTF-8.

* "Komunikat niepoprawny format. Wiadomości SMS nie zawiera danych." Klient wysyłał wiadomości SMS, który nie zawiera żadnych danych treści.

* "Komunikat niepoprawny format. Wiadomość SMS dekodowanie do UTF-8 nie powiodło się." Klient wysyłał wiadomości SMS, który nie został poprawnie zakodowany w formacie UTF-8.

* "Komunikat niepoprawny format. Wiadomość SMS zawiera separator nie nagłówka". Klient wysyłał wiadomości SMS, który nie zawiera separator nagłówek lub użyć separatora nieprawidłowy nagłówek.

#### <a name="missing-or-empty-headers"></a>Nagłówki lub jest pusty

Jeśli klient wysyła komunikat, który nie ma wymaganych nagłówki *X RequestId* lub *ścieżki*, Usługa zamyka połączenie z *błąd protokołu 1002* kod stanu. Komunikat jest "header brakujące lub są puste. {Nazwa nagłówka}."

#### <a name="requestid-values"></a>Identyfikator żądania wartości

Jeśli klient wysyła komunikat, który określa *X RequestId* nagłówka w niepoprawnym formacie, Usługa zamyka połączenie i zwraca *błąd protokołu 1002* stanu. Komunikat jest "nieprawidłowe żądanie. Wartość nagłówka X-RequestId nie została określona w formacie UUID nie-dash."

#### <a name="audio-encoding-errors"></a>Błędy kodowania audio

Jeśli klient wysyła fragmentu audio, który inicjuje Włącz i audio format lub kodowanie nie jest zgodny z wymaganą specyfikacją, Usługa zamyka połączenie i zwraca *1007 nieprawidłowe dane ładunku* kod stanu. Komunikat wskazuje format kodowania źródło błędu.

#### <a name="requestid-reuse"></a>Identyfikator żądania ponownego wykorzystania

Po zakończeniu Włącz, jeśli klient wysyła komunikat, który używa identyfikatora żądania z tego Wyłącz, Usługa zamyka połączenie i zwraca *błąd protokołu 1002* kod stanu. Komunikat jest "nieprawidłowe żądanie. Ponowne użycie identyfikatorów żądania nie jest dozwolona."

## <a name="connection-failure-telemetry"></a>Dane telemetryczne błąd połączenia

Aby zapewnić najlepsze możliwe środowisko korzystania, klienci musi powiadomić Speech Service sygnatury czasowe dla ważnych punktów kontrolnych w ramach połączenia przy użyciu *telemetrii* wiadomości. Jest równie ważne poinformować usługi, połączeń, które zostały podjęto, ale nie powiodło się, że klienci.

Dla każdej próbie połączenia, który uległ awarii, klienci tworzą *telemetrii* wiadomości przy użyciu unikatowego *X RequestId* wartość nagłówka. Ponieważ klient nie może nawiązać połączenie, *ReceivedMessages* pola w treść kodu JSON można pominąć. Tylko `Connection` wpis *metryki* pole jest uwzględniane. Ten wpis zawiera początek i koniec sygnatury czasowe, a także warunek błędu, który wystąpił.

### <a name="connection-retries-in-telemetry"></a>Próby połączenia w danych telemetrycznych

Klienci powinna wskazywać *ponownych prób* z *próby nawiązania połączenia z wieloma* przez zdarzenie, które wyzwala próba połączenia. Próby nawiązania połączenia, które są wykonywane programowo bez żadnych danych wejściowych użytkownika są ponownych prób. Wiele prób połączenia, które są wykonywane w odpowiedzi na dane wejściowe użytkownika są wielokrotne próby połączenia. Klienci Nadaj każdej próbie połączenia użytkownik wyzwolił unikatową *X RequestId* i *telemetrii* wiadomości. Użyj klientów ponownie *X RequestId* programowe ponownych prób. Jeśli wprowadzono wiele ponownych prób dla pojedynczego połączenia, każda próba ponowienia jest uwzględniona jako `Connection` wpis *telemetrii* wiadomości.

Na przykład, załóżmy, że użytkownik mówi wyzwalacz — słowo kluczowe, aby uruchomić połączenia, a pierwsza próba połączenia kończy się niepowodzeniem z powodu błędów DNS. Natomiast druga próba, programowo wykonanym przez klienta zakończy się pomyślnie. Ponieważ klient próba połączenia bez konieczności dodatkowe dane wejściowe od użytkownika, klient używa jednej *telemetrii* komunikat z wieloma `Connection` wpisów, aby opisać połączenie.

Inny przykład załóżmy, że użytkownik mówi wyzwalacz — słowo kluczowe, aby uruchomić połączenia i próba połączenia nie powiedzie się po trzech ponownych prób. Klient zrezygnuje zatrzymuje próby połączenia z usługą i informuje użytkownika, że wystąpił problem. Użytkownik mówi następnie wyzwalacz — słowo kluczowe ponownie. Tym razem Załóżmy, że klient łączy się z usługą. Po nawiązaniu połączenia, klient natychmiast wysyła *telemetrii* komunikatu do usługi, która zawiera trzy `Connection` wpisów, które opisują błędy połączenia. Od momentu odebrania `turn.end` wiadomości, klient wysyła innego *telemetrii* komunikat, który opisuje pomyślne połączenie.

## <a name="error-message-reference"></a>Informacje o komunikat o błędzie

### <a name="http-status-codes"></a>Kody stanu HTTP

| Kod stanu HTTP | Opis | Rozwiązywanie problemów |
| - | - | - |
| 400 Niewłaściwe żądanie | Klient wysłał żądanie połączenia protokołu WebSocket, która była nieprawidłowa. | Sprawdź podano wszystkich wymaganych parametrów i nagłówków HTTP i czy wartości są poprawne. |
| 401 Brak autoryzacji | Klient nie zawiera informacji o autoryzacji wymagane. | Upewnij się, że wysyłasz *autoryzacji* nagłówka w ramach połączenia protokołu WebSocket. |
| 403 Zabroniony | Klient wysyłał informacje dotyczące autoryzacji, ale była nieprawidłowa. | Sprawdź, czy nie wysyłasz wartością wygasła lub jest nieprawidłowy *autoryzacji* nagłówka. |
| 404 — Nie odnaleziono | Klient, próbowało uzyskać dostęp do ścieżki adresu URL, który nie jest obsługiwany. | Sprawdź, że używasz prawidłowego adresu URL dla połączenia protokołu WebSocket. |
| Błąd serwera 500 | Usługa napotkała błąd wewnętrzny i nie może spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |
| 503 — usługa niedostępna | Usługa nie mogła obsłużyć żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

### <a name="websocket-error-codes"></a>Kody błędów protokołu WebSocket

| Kod WebSocketsStatus | Opis | Rozwiązywanie problemów |
| - | - | - |
| 1000 normalne zamknięcia | Usługa zamknął połączenie WebSocket bez błędów. | W przypadku zamknięcia protokołu WebSocket był nieoczekiwany, należy przeczytać dokumentację, aby upewnić się, że rozumiesz, jak i kiedy usługa może zakończyć połączenie protokołu WebSocket. |
| Błąd protokołu 1002 | Klient nie może być zgodne z wymaganiami protokołu. | Sprawdź, czy poznać dokumentacji protokołu i są jasno o wymaganiach. Przeczytaj dokumentację poprzedniej o przyczyny błędu, aby zobaczyć, jeśli masz naruszenie wymagania protokołu. |
| 1007 nieprawidłowy ładunek danych | Klient wysłał nieprawidłowy ładunek komunikatu protokołu. | Sprawdź ostatni komunikat, który wysyłane do usługi w przypadku błędów. Przeczytaj dokumentację poprzedniej o błędach ładunku. |
| Błąd serwera 1011 | Usługa napotkała błąd wewnętrzny i nie może spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

## <a name="related-topics"></a>Tematy pokrewne

Zobacz [zestaw JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) oznacza to implementacja protokołu WebSocket usługi mowy.
