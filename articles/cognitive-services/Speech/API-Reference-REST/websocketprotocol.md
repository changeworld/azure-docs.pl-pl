---
title: rozpoznawanie mowy Bing protokół WebSocket | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Dokumentacja protokołu dla rozpoznawanie mowy Bing w oparciu o usługi WebSockets
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e7f51d49624d5019bec058a2d12f6ca2f1366938
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966886"
---
# <a name="bing-speech-websocket-protocol"></a>rozpoznawanie mowy Bing protokół WebSocket

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozpoznawanie mowy Bing to oparta na chmurze platforma, która oferuje najbardziej zaawansowane algorytmy umożliwiające konwersję dźwięku wypowiadanego na tekst. Protokół rozpoznawanie mowy Bing definiuje [konfigurację połączenia](#connection-establishment) między aplikacjami klienckimi a usługą i komunikaty rozpoznawania mowy wymieniane między odpowiednikami ([komunikaty pochodzące z klienta](#client-originated-messages) i [komunikaty pochodzące z usługi](#service-originated-messages) ). Ponadto opisano [komunikaty telemetryczne](#telemetry-schema) i [Obsługa błędów](#error-handling) .

## <a name="connection-establishment"></a>Ustanowienie połączenia

Protokół usługi mowy jest zgodny ze standardem WebSocket [RFC 6455](https://tools.ietf.org/html/rfc6455). Połączenie protokołu WebSocket jest uruchamiane jako żądanie HTTP zawierające nagłówki HTTP wskazujące, że klient chce uaktualnić połączenie do protokołu WebSocket zamiast korzystać z semantyki protokołu HTTP. Serwer wskazuje swoją gotowość do uczestnictwa w połączeniu z protokołem WebSocket, zwracając `101 Switching Protocols` odpowiedź HTTP. Po wymianie tego uzgodnienia zarówno klient, jak i usługa przechowują gniazdo otwarte i zaczynają korzystać z protokołu opartego na komunikatach w celu wysyłania i odbierania informacji.

Aby rozpocząć uzgadnianie protokołu WebSocket, aplikacja kliencka wysyła żądanie HTTPS GET do usługi. Zawiera standardowe nagłówki aktualizacji protokołu WebSocket wraz z innymi nagłówkami, które są specyficzne dla mowy.

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

Usługa reaguje na:

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

Wszystkie żądania mowy wymagają szyfrowania [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) . Korzystanie z nieszyfrowanych żądań mowy nie jest obsługiwane. Obsługiwana jest następująca wersja protokołu TLS:

* TLS 1.2

### <a name="connection-identifier"></a>Identyfikator połączenia

Usługa mowy wymaga, aby wszyscy klienci z unikatowym IDENTYFIKATORem identyfikować połączenie. Klienci *muszą* zawierać nagłówek *X-ConnectionID* podczas uruchamiania uzgadniania protokołu WebSocket. Nagłówek *X-ConnectionID* musi być wartością [uniwersalnie unikatowym identyfikatorem](https://en.wikipedia.org/wiki/Universally_unique_identifier) (UUID). Żądania uaktualnienia protokołu WebSocket, które nie obejmują *x-ConnectionID*, nie określają wartości dla nagłówka *x-ConnectionID* lub nie zawierają prawidłowej wartości UUID są odrzucane przez usługę z odpowiedzią http `400 Bad Request` .

### <a name="authorization"></a>Authorization

Oprócz standardowych nagłówków uzgadniania protokołu WebSocket żądania mowy wymagają nagłówka *autoryzacji* . Żądania połączenia bez tego nagłówka są odrzucane przez usługę z odpowiedzią http `403 Forbidden` .

Nagłówek *autoryzacji* musi zawierać token dostępu tokenu sieci Web JSON (JWT).

Informacje o sposobie subskrybowania i uzyskiwania kluczy interfejsu API, które są używane do pobierania prawidłowych tokenów dostępu JWT, można znaleźć na stronie [subskrypcji Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) .

Klucz interfejsu API jest przesyłany do usługi tokenu. Na przykład:

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

W celu uzyskania dostępu do tokenu wymagane są następujące informacje nagłówka.

| Name | Format | Opis |
|----|----|----|
| OCP-Apim-Subscription-Key | ASCII | Twój klucz subskrypcji |

Usługa tokenu zwraca token dostępu JWT jako `text/plain`. Następnie token JWT jest przesyłany `Base64 access_token` do uzgadniania jako nagłówek *autoryzacji* poprzedzony ciągiem. `Bearer` Na przykład:

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>Pliki cookie

Klienci *muszą* obsługiwać pliki cookie protokołu HTTP zgodnie z opisem w [dokumencie RFC 6265](https://tools.ietf.org/html/rfc6265).

### <a name="http-redirection"></a>Przekierowywanie HTTP

Klienci *muszą* obsługiwać standardowe mechanizmy przekierowania określone przez [specyfikację protokołu HTTP](https://www.w3.org/Protocols/rfc2616/rfc2616.html).

### <a name="speech-endpoints"></a>Punkty końcowe mowy

Klienci *muszą* używać odpowiedniego punktu końcowego usługi mowy. Punkt końcowy jest oparty na trybie rozpoznawania i języku. W tabeli przedstawiono kilka przykładów.

| Tryb | Path | Identyfikator URI usługi |
| -----|-----|-----|
| Interaktywne | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| Konwersacja | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| Dyktowanie | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

Aby uzyskać więcej informacji, zobacz stronę [URI usługi](../GetStarted/GetStartedREST.md#service-uri) .

### <a name="report-connection-problems"></a>Zgłoś problemy z połączeniem

Klienci powinni natychmiast zgłosić wszystkie problemy napotkane podczas nawiązywania połączenia. Protokół komunikatów dla raportowania nieudanych połączeń został opisany w polu [Telemetria błędu połączenia](#connection-failure-telemetry).

### <a name="connection-duration-limitations"></a>Ograniczenia czasu trwania połączenia

W porównaniu z typowymi połączeniami HTTP usługi sieci Web, połączenia protokołu WebSocket są nawiązywane *po raz ostatni* . Usługa mowy umieszcza ograniczenia dotyczące czasu trwania połączeń protokołu WebSocket z usługą:

 * Maksymalny czas trwania aktywnego połączenia WebSocket wynosi 10 minut. Połączenie jest aktywne, jeśli usługa lub klient wysyła komunikaty protokołu WebSocket za pośrednictwem tego połączenia. Usługa przerywa połączenie bez ostrzeżenia po osiągnięciu limitu. Klienci powinni opracowywać scenariusze użytkowników, które nie wymagają, aby połączenie pozostało aktywne w trakcie maksymalnego okresu istnienia połączenia lub jego bliskość.

 * Maksymalny czas trwania dowolnego nieaktywnego połączenia z użyciem protokołu WebSocket to 180 sekund. Połączenie jest nieaktywne, jeśli żadna usługa ani klient nie wysłał komunikatu protokołu WebSocket przez połączenie. Po osiągnięciu maksymalnego nieaktywnego okresu istnienia usługa przerywa połączenie z nieaktywnym obiektem WebSocket.

## <a name="message-types"></a>Typy komunikatów

Po ustanowieniu połączenia protokołu WebSocket między klientem a usługą Klient i usługa mogą wysyłać komunikaty. W tej sekcji opisano format tych komunikatów protokołu WebSocket.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455) określa, że komunikaty protokołu WebSocket mogą przesyłać dane przy użyciu tekstu lub kodowania binarnego. Dwa kodowania są używane w różnych formatach w sieci szkieletowej. Każdy format jest zoptymalizowany pod kątem wydajnego kodowania, przesyłania i dekodowania ładunku komunikatów.

### <a name="text-websocket-messages"></a>Komunikaty protokołu WebSocket tekstu

Komunikaty protokołu WebSocket tekstowych zawierają ładunek informacji tekstowych, które składają się z sekcji nagłówków i treści oddzielonej przez znaną parę wiersza podwójnego powrotu karetki, używanej na potrzeby komunikatów HTTP. I, podobnie jak komunikaty HTTP, tekstowe komunikaty protokołu WebSocket określają nagłówki w *nazwie: format wartości* rozdzielone przez parę nowego wiersza powrotu karetki. Każdy tekst zawarty w komunikacie protokołu WebSocket tekstu *musi* używać kodowania [UTF-8](https://tools.ietf.org/html/rfc3629) .

Komunikaty protokołu WebSocket tekstu muszą określać ścieżkę wiadomości w *ścieżce*nagłówka. Wartość tego nagłówka musi być jednym z typów komunikatów protokołu mowy zdefiniowanych w dalszej części tego dokumentu.

### <a name="binary-websocket-messages"></a>Binarne komunikaty protokołu WebSocket

Binarne komunikaty protokołu WebSocket zawierają ładunek binarny. W protokole usługi mowy usługa audio jest przesyłana do usługi i odbierana z niej przy użyciu binarnych komunikatów WebSocket. Wszystkie inne komunikaty są tekstowymi komunikatami protokołu WebSocket.

Podobnie jak komunikaty protokołu WebSocket tekstu, binarne komunikaty protokołu WebSocket składają się z nagłówka i sekcji treści. Pierwsze 2 bajty binarnego komunikatu protokołu WebSocket określają w kolejności [big-endian](https://en.wikipedia.org/wiki/Endianness) 16-bitową liczbę całkowitą sekcji nagłówka. Minimalny rozmiar sekcji nagłówka to 0 bajtów. Maksymalny rozmiar to 8 192 bajtów. Tekst w nagłówkach binarnych komunikatów WebSocket *musi* używać kodowania [US-ASCII](https://tools.ietf.org/html/rfc20) .

Nagłówki w binarnym komunikacie WebSocket są kodowane w tym samym formacie co w komunikatach protokołu WebSocket tekstu. *Nazwa: format wartości* jest oddzielona przez parę jednowierszowego powrotu karetki. Binarne komunikaty WebSocket muszą określać ścieżkę wiadomości w *ścieżce*nagłówka. Wartość tego nagłówka musi być jednym z typów komunikatów protokołu mowy zdefiniowanych w dalszej części tego dokumentu.

W protokole usługi Speech Service są używane wiadomości tekstowych i binarnych obiektów WebSocket.

## <a name="client-originated-messages"></a>Komunikaty pochodzące od klienta

Po nawiązaniu połączenia zarówno klient, jak i usługa mogą zacząć wysyłać wiadomości. W tej sekcji opisano format i ładunek komunikatów wysyłanych przez aplikacje klienckie do usługi mowy. W sekcji [komunikaty pochodzące z usługi](#service-originated-messages) znajdują się komunikaty, które pochodzą z usługi mowy i są wysyłane do aplikacji klienckich.

Główne wiadomości wysyłane przez klienta do usług to `speech.config`, `audio`i `telemetry` . Przed przekazaniem szczegółowych informacji o poszczególnych komunikatach są opisane typowe wymagane nagłówki komunikatów dla wszystkich tych komunikatów.

### <a name="required-message-headers"></a>Wymagane nagłówki komunikatów

Następujące nagłówki są wymagane dla wszystkich komunikatów pochodzących od klienta.

| nagłówek | Value |
|----|----|
| Path | Ścieżka wiadomości określona w tym dokumencie |
| X-RequestId | Identyfikator UUID w formacie "No-kreskowany" |
| X-Timestamp | Sygnatura czasowa zegara klienta w formacie ISO 8601 |

#### <a name="x-requestid-header"></a>Nagłówek X-IdentyfikatorŻądania

Żądania pochodzące od klientów są jednoznacznie identyfikowane przez nagłówek komunikatu *X-IdentyfikatorŻądania* . Ten nagłówek jest wymagany dla wszystkich komunikatów pochodzących od klienta. Wartość nagłówka *X-IdentyfikatorŻądania* musi być identyfikatorem UUID w postaci "No-kreskowany", na przykład *123e4567e89b12d3a456426655440000*. *Nie może* być w postaci kanonicznej *123e4567-e89b-12d3-A456-426655440000*. Żądania bez nagłówka *X-IdentyfikatorŻądania* lub z wartością nagłówka używającą niewłaściwego formatu identyfikatorów UUID powodują, że usługa przerywa połączenie z użyciem protokołu WebSocket.

#### <a name="x-timestamp-header"></a>Nagłówek sygnatury czasowej X

Każdy komunikat wysyłany do usługi mowy przez aplikację kliencką *musi* zawierać nagłówek z *sygnaturą czasową X* . Wartość tego nagłówka to czas, po którym klient wysyła komunikat. Żądania bez nagłówka *znacznika czasu X* lub z wartością nagłówka, która używa niewłaściwego formatu, powodują przerwanie połączenia z użyciem protokołu WebSocket przez usługę.

Wartość nagłówka *sygnatury czasowej X* musi mieć postać "YYYY'-'MM'-'dd'T'HH": "mm": "SS". " fffffffZ "gdzie" fffffff "jest częścią sekundy. Na przykład "12,5" oznacza "12 + 5/10 sekund" i "12,526" oznacza "12 Plus 526/1000 s". Ten format jest zgodny z [normą ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i, w przeciwieństwie do standardowego nagłówka *daty* http, może zapewnić rozdzielczooć milisekund. Aplikacje klienckie mogą zaokrąglić sygnatury czasowe do najbliższej milisekundy. Aplikacje klienckie muszą mieć pewność, że zegar urządzenia dokładnie śledzi czas przy użyciu [serwera NTP (Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol).

### <a name="message-speechconfig"></a>Komunikat`speech.config`

Usługa mowy musi znać charakterystykę aplikacji, aby zapewnić najlepszą możliwą funkcję rozpoznawania mowy. Wymagane dane charakterystyki zawierają informacje o urządzeniu i systemie operacyjnym, które mają uprawnienia do Twojej aplikacji. Te informacje są `speech.config` podawane w komunikacie.

Klienci *muszą* wysłać `speech.config` komunikat natychmiast po nawiązaniu połączenia z usługą mowy i przed wysłaniem jakichkolwiek `audio` komunikatów. `speech.config` Komunikat należy wysłać tylko raz dla każdego połączenia.

| Pole | Opis |
|----|----|
| Kodowanie komunikatów WebSocket | Text |
| Treść | Ładunek jako struktura JSON |

#### <a name="required-message-headers"></a>Wymagane nagłówki komunikatów

| Nazwa nagłówka | Value |
|----|----|
| Path | `speech.config` |
| X-Timestamp | Sygnatura czasowa zegara klienta w formacie ISO 8601 |
| Content-Type | application/json; charset=utf-8 |

Podobnie jak w przypadku wszystkich komunikatów uzyskanych przez klienta w protokole usługi mowy `speech.config` , komunikat *musi* zawierać nagłówek *sygnatury czasowej* , który rejestruje czas zegara klienta, gdy wiadomość została wysłana do usługi. Komunikat `speech.config` nie *wymaga nagłówka* *X-IdentyfikatorŻądania* , ponieważ ten komunikat nie jest skojarzony z określonym żądaniem mowy.

#### <a name="message-payload"></a>Ładunek komunikatu
Ładunek `speech.config` wiadomości jest strukturą JSON, która zawiera informacje o aplikacji. W poniższym przykładzie przedstawiono te informacje. Informacje kontekstowe klienta i urządzenia są zawarte w elemencie *kontekstu* struktury JSON.

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

##### <a name="system-element"></a>Element systemowy

Element System. Version `speech.config` komunikatu zawiera wersję oprogramowania zestawu Speech SDK używanego przez aplikację kliencką lub urządzenie. Wartość ma postać *główna. pomocnicza. kompilacja. gałąź*. Możesz pominąć składnik *gałęzi* , jeśli nie ma zastosowania.

##### <a name="os-element"></a>Element systemu operacyjnego

| Pole | Opis | Użycie |
|-|-|-|
| system operacyjny. platforma | Platforma systemu operacyjnego, która obsługuje aplikację, na przykład Windows, Android, iOS lub Linux |Wymagane |
| os.name | Nazwa produktu systemu operacyjnego, na przykład Debian lub Windows 10 | Wymagane |
| os.version | Wersja systemu operacyjnego w postaci *główna. pomocnicza. kompilacja. rozgałęzienie* | Wymagane |

##### <a name="device-element"></a>Element urządzenia

| Pole | Opis | Użycie |
|-|-|-|
| device.manufacturer | Producent sprzętu urządzenia | Wymagane |
| device.model | Model urządzenia | Wymagane |
| device.version | Wersja oprogramowania urządzenia dostarczana przez producenta urządzenia. Ta wartość określa wersję urządzenia, które może być śledzone przez producenta. | Wymagane |

### <a name="message-audio"></a>Komunikat`audio`

Aplikacje klienckie z obsługą mowy wysyłają dźwięk do usługi mowy przez konwersję strumienia audio na serię fragmentów audio. Każdy fragment audio jest segmentem wymawianego dźwięku, który ma być uzyskanego przez usługę. Maksymalny rozmiar pojedynczego fragmentu audio to 8 192 bajtów. Komunikaty strumieni audio to *binarne komunikaty protokołu WebSocket*.

Klienci wysyłają `audio` fragmenty audio do usługi za pomocą wiadomości. Klienci odczytują dźwięk z mikrofonu w fragmentach i wysyłają te fragmenty do usługi mowy w celu transkrypcji. Pierwszy `audio` komunikat musi zawierać poprawnie sformułowany nagłówek, który prawidłowo określa, że dźwięk jest zgodny z jednym z formatów kodowania obsługiwanych przez usługę. Dodatkowe `audio` komunikaty zawierają tylko dane binarne strumienia audio odczytywane z mikrofonu.

Klienci mogą opcjonalnie wysłać `audio` komunikat z treścią o zerowej długości. Ten komunikat informuje usługę, że klient wie, że użytkownik zatrzymał czytanie, wypowiedź jest zakończona, a mikrofon jest wyłączony.

Usługa Speech używa pierwszej `audio` wiadomości zawierającej unikatowy identyfikator żądania, aby sygnalizować początek nowego cyklu żądania/odpowiedzi lub *włączyć*. Gdy usługa odbierze `audio` komunikat z nowym identyfikatorem żądania, odrzuci wszystkie wiadomości w kolejce lub niewysłane, które są skojarzone z poprzednim ustawieniem.

| Pole | Opis |
|-------------|----------------|
| Kodowanie komunikatów WebSocket | Binary |
| Treść | Dane binarne dla fragmentu audio. Maksymalny rozmiar to 8 192 bajtów. |

#### <a name="required-message-headers"></a>Wymagane nagłówki komunikatów

Poniższe nagłówki są wymagane dla wszystkich `audio` komunikatów.

| nagłówek         |  Value     |
| ------------- | ---------------- |
| Path | `audio` |
| X-RequestId | Identyfikator UUID w formacie "No-kreskowany" |
| X-Timestamp | Sygnatura czasowa zegara klienta w formacie ISO 8601 |
| Content-Type | Typ zawartości audio. Typem musi być *audio/x-WAV* (PCM) lub *audio/jedwab* (jedwab). |

#### <a name="supported-audio-encodings"></a>Obsługiwane kodowania audio

W tej sekcji opisano kodery-dekoder audio obsługiwane przez usługę mowy.

##### <a name="pcm"></a>MODUŁU PCM

Usługa mowy akceptuje nieskompresowane audio pulsu modulacji kodu (PCM). Dźwięk jest wysyłany do usługi w formacie [WAV](https://en.wikipedia.org/wiki/WAV) , więc pierwszy fragment audio *musi* zawierać prawidłowy nagłówek [pliku Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format) (RIFF). Jeśli klient inicjuje działanie z fragmentem audio, który *nie zawiera prawidłowego* nagłówka RIFF, usługa odrzuca żądanie i kończy połączenie protokołu WebSocket.

Audio PCM *należy* próbkować o 16 kHz z 16 bitami na próbkę i jeden kanał (*RIFF-16khz-16bit-mono-PCM*). Usługa mowy nie obsługuje strumieni audio stereo i odrzuca strumienie audio, które nie używają określonej szybkości transmisji bitów, szybkości próbkowania ani liczby kanałów.

##### <a name="opus"></a>Opus

Opus to otwarty, bezpłatny, niewszechstronny koder-dekoder audio. Usługa Speech obsługuje Opus z stałą szybkością `32000` bitową lub. `16000` Tylko kontener dla Opus jest obecnie obsługiwany `audio/ogg` przez typ MIME. `OGG`

Aby użyć Opus, zmodyfikuj [przykład JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101) i Zmień `RecognizerSetup` metodę na Return.

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

#### <a name="detect-end-of-speech"></a>Wykrywanie końca mowy

Ludzie nie sygnalizują jawnie, gdy skończysz mówić. Każda aplikacja akceptująca mowę jako dane wejściowe ma dwie opcje obsługi końca mowy w strumieniu audio: wykrycie punktu końcowego usługi i wykrywanie końców mowy przez klienta. Z tych dwóch opcji funkcja wykrywania punktu końcowego na mowę usługi zwykle zapewnia lepszy komfort pracy użytkowników.

##### <a name="service-end-of-speech-detection"></a>Wykrywanie końca mowy usługi

Aby zbudować idealne, bezpłatne środowisko mowy, aplikacje umożliwiają wykrywanie usługi po zakończeniu mówienia przez użytkownika. Klienci wysyłają dźwięk z mikrofonu jako fragmenty *audio* , dopóki usługa nie wykryje ciszi i odpowie z powrotem `speech.endDetected` z komunikatem.

##### <a name="client-end-of-speech-detection"></a>Wykrywanie końca mowy klienta

Aplikacje klienckie, które umożliwiają użytkownikowi sygnalizowanie końca mowy w jakiś sposób, mogą również dać usłudze ten sygnał. Na przykład aplikacja kliencka może mieć przycisk "Zatrzymaj" lub "Wycisz", który użytkownik może nacisnąć. Aby sygnalizować koniec mowy, aplikacje klienckie wysyłają komunikat fragmentu *audio* o zerowej długości. Usługa Speech interpretuje ten komunikat jako koniec przychodzącego strumienia audio.

### <a name="message-telemetry"></a>Komunikat`telemetry`

Aplikacje klienckie *muszą* potwierdzić koniec każdego z nich, wysyłając dane telemetryczne dotyczące usługi zamiany na mowę. Potwierdzenie z kolei umożliwia usłudze mowy, aby upewnić się, że wszystkie komunikaty niezbędne do ukończenia żądania i jego odpowiedź zostały prawidłowo odebrane przez klienta. Potwierdzenie wyłączania umożliwia również usłudze mowy sprawdzenie, czy aplikacje klienckie działają zgodnie z oczekiwaniami. Te informacje są niecenne, jeśli potrzebujesz pomocy w rozwiązywaniu problemów z aplikacją obsługującą mowę.

Klienci muszą potwierdzić koniec z kolei, wysyłając `telemetry` komunikat wkrótce po `turn.end` odebraniu komunikatu. Klienci powinni próbować potwierdzić `turn.end` jak najszybciej, jak to możliwe. Jeśli aplikacja kliencka nie powiedzie się, usługa rozpoznawania mowy może przerwać połączenie z błędem. Klienci muszą wysyłać tylko jeden `telemetry` komunikat dla każdego żądania i odpowiedzi identyfikowanego przez wartość *X-IdentyfikatorŻądania* .

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `telemetry` |
| X-Timestamp | Sygnatura czasowa zegara klienta w formacie ISO 8601 |
| Content-Type | `application/json` |
| Treść | Struktura JSON, która zawiera informacje o kliencie o przewróceniu |

Schemat treści `telemetry` wiadomości jest zdefiniowany w sekcji [schemat telemetrii](#telemetry-schema) .

#### <a name="telemetry-for-interrupted-connections"></a>Dane telemetryczne dla przerwanych połączeń

Jeśli połączenie sieciowe z jakiegokolwiek powodu zakończy się niepowodzeniem, a klient `turn.end` nie *otrzyma komunikatu* z usługi, klient wysyła `telemetry` komunikat. Ten komunikat opisuje żądanie zakończone niepowodzeniem przy następnym nawiązaniu połączenia z usługą przez klienta. Klienci nie muszą natychmiast próbować nawiązać połączenia w celu wysłania `telemetry` wiadomości. Komunikat może być zbuforowany na kliencie i wysyłany w przyszłym połączeniu żądanym przez użytkownika. Komunikat dla żądania zakończonego niepowodzeniem musi używać wartości *X-IdentyfikatorŻądania* z żądania zakończonego niepowodzeniem. `telemetry` Może być wysyłany do usługi zaraz po nawiązaniu połączenia, bez czekania na wysyłanie lub otrzymywanie innych komunikatów.

## <a name="service-originated-messages"></a>Komunikaty pochodzące z usługi

W tej sekcji opisano komunikaty, które pochodzą z usługi mowy i są wysyłane do klienta programu. Usługa Speech obsługuje rejestr możliwości klienta i generuje komunikaty wymagane przez każdego klienta, więc nie wszyscy klienci odbierają wszystkie komunikaty opisane w tym miejscu. W przypadku zwięzłości do wiadomości jest przywoływana wartość nagłówka *Path* . Na przykład odwołujemy się do wiadomości tekstowej protokołu WebSocket z wartością `speech.hypothesis` ścieżki jako komunikatem o hipotezie.

### <a name="message-speechstartdetected"></a>Komunikat`speech.startDetected`

`speech.startDetected` Komunikat wskazuje, że usługa mowy wykryła mowę w strumieniu audio.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `speech.startDetected` |
| Content-Type | application/json; charset=utf-8 |
| Treść | Struktura JSON, która zawiera informacje o warunkach po wykryciu uruchomienia mowy. Pole *przesunięcia* w tej strukturze Określa przesunięcie (w jednostkach 100-nanosekund) w przypadku wykrycia mowy w strumieniu audio względem początku strumienia. |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>Komunikat`speech.hypothesis`

Podczas rozpoznawania mowy usługa mowy okresowo generuje w ten sposób informacje o wyrazach rozpoznawanych przez usługę. Usługa mowy wysyła te te same do klienta co około 300 milisekund. Jest to odpowiednie tylko w celu zwiększenia możliwości mowy użytkownika. `speech.hypothesis` Nie trzeba podejmować żadnych zależności od zawartości lub dokładności tekstu w tych komunikatach.

 `speech.hypothesis` Komunikat ma zastosowanie do klientów, którzy mają pewną możliwość renderowania tekstu, i chcą zapewnić użytkownikom niemal w czasie rzeczywistym informacje o rozpoznawaniu w toku.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `speech.hypothesis` |
| X-RequestId | Identyfikator UUID w formacie "No-kreskowany" |
| Content-Type | application/json |
| Treść | Struktura JSON zahipotezy mowy |

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

Element *offset* Określa przesunięcie (w jednostkach 100-nanosekund), gdy fraza została rozpoznana względem początku strumienia audio.

Element *Duration* określa czas trwania tej frazy mowy (w jednostkach 100-nanosekund).

Klienci nie mogą wprowadzać żadnych założeń dotyczących częstotliwości, chronometrażu lub tekstu zawartego w hipotezie mowy lub spójności tekstu we wszystkich założeniach mowy. Te same są tylko migawkami w procesie transkrypcji w usłudze. Nie reprezentują one stabilnego akumulacji transkrypcji. Na przykład pierwsza hipoteza mowy może zawierać słowa "drobnoziarniste", a druga hipoteza może zawierać słowa "Znajdź zabawne". Usługa mowy nie wykonuje żadnych operacji przetwarzania końcowego (na przykład wielkich liter, interpunkcji) w tekście w hipotezie mowy.

### <a name="message-speechphrase"></a>Komunikat`speech.phrase`

Gdy usługa mowy określi, że ma wystarczającą ilość informacji, aby utworzyć wynik rozpoznawania, który nie ulegnie zmianie, usługa `speech.phrase` tworzy komunikat. Usługa mowy generuje te wyniki po wykryciu, że użytkownik zakończył zdanie lub frazę.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `speech.phrase` |
| Content-Type | application/json |
| Treść | Struktura JSON frazy mowy |

Schemat JSON `RecognitionStatus`frazy mowy zawiera następujące pola:, `DisplayText`, `Offset`i `Duration`. Aby uzyskać więcej informacji na temat tych pól, zobacz [odpowiedzi transkrypcji](../concepts.md#transcription-responses).

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

### <a name="message-speechenddetected"></a>Komunikat`speech.endDetected`

`speech.endDetected` Komunikat określa, że aplikacja kliencka powinna zatrzymać przesyłanie strumieniowe audio do usługi.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `speech.endDetected` |
| Treść | Struktura JSON, która zawiera przesunięcie po wykryciu końca mowy. Przesunięcie jest reprezentowane w jednostkach 100-nanosekund przesunięcie od początku dźwięku, który jest używany do rozpoznawania. |
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

Element *offset* Określa przesunięcie (w jednostkach 100-nanosekund), gdy fraza została rozpoznana względem początku strumienia audio.

### <a name="message-turnstart"></a>Komunikat`turn.start`

`turn.start` Sygnalizuje początek z perspektywy usługi. `turn.start` Komunikat to zawsze pierwszy komunikat odpowiedzi otrzymany dla każdego żądania. Jeśli nie otrzymasz `turn.start` komunikatu, załóż, że stan połączenia z usługą jest nieprawidłowy.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
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

Treść `turn.start` wiadomości jest strukturą JSON, która zawiera kontekst dla początku obrotu. Element *Context* zawiera właściwość *serviceTag* . Ta właściwość określa wartość tagu, z którą usługa została skojarzona. Ta wartość może być używana przez firmę Microsoft, jeśli potrzebujesz pomocy w rozwiązywaniu problemów z błędami w aplikacji.

### <a name="message-turnend"></a>Komunikat`turn.end`

`turn.end` Sygnalizuje koniec z perspektywy usługi. `turn.end` Komunikat jest zawsze ostatnim komunikatem odpowiedzi odbieranym dla każdego żądania. Klienci mogą używać otrzymania tego komunikatu jako sygnału do czyszczenia działań i przejścia do stanu bezczynności. Jeśli nie otrzymasz `turn.end` komunikatu, załóż, że stan połączenia z usługą jest nieprawidłowy. W takich przypadkach należy zamknąć istniejące połączenie z usługą i ponownie nawiązać połączenie.

| Pole | Opis |
| ------------- | ---------------- |
| Kodowanie komunikatów WebSocket | Text |
| Path | `turn.end` |
| Treść | Brak |

#### <a name="sample-message"></a>Przykładowy komunikat

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>Schemat telemetrii

Treść komunikatu *telemetrii* jest strukturą JSON, która zawiera informacje o kliencie z włączonym lub próbnym połączeniem. Struktura składa się z sygnatur czasowych klienta, które są rejestrowane w przypadku wystąpienia zdarzeń klienta. Każda sygnatura czasowa musi być w formacie ISO 8601, zgodnie z opisem w sekcji "nagłówek znacznika czasu X". Komunikaty *telemetryczne* , które nie określają wszystkich wymaganych pól w strukturze JSON lub nie używają poprawnego formatu sygnatury czasowej, mogą spowodować przerwanie połączenia z klientem przez usługę. Klienci *muszą* podawać prawidłowe wartości dla wszystkich wymaganych pól. Klienci *powinni* podawać wartości pól opcjonalnych w miarę potrzeb. Wartości podane w przykładach w tej sekcji dotyczą tylko ilustracji.

Schemat telemetrii jest podzielony na następujące części: odebrane sygnatury czasowe komunikatów i metryki. Format i użycie każdej części jest określony w poniższych sekcjach.

### <a name="received-message-time-stamps"></a>Sygnatury czasowe odebranych komunikatów

Klienci muszą uwzględniać wartości czasu odbioru dla wszystkich odebranych komunikatów po pomyślnym nawiązaniu połączenia z usługą. Te wartości muszą rejestrować czas, po którym klient *otrzymał* każdy komunikat z sieci. Wartość nie powinna rejestrować żadnych innych godzin. Na przykład klient nie powinien rejestrować czasu, kiedy *działał* on na wiadomości. Sygnatury czasowe odebranych komunikatów są określone w tablicy *name: value* Pars. Nazwa pary określa wartość *ścieżki* wiadomości. Wartość pary określa czas klienta, kiedy wiadomość została odebrana. Lub, jeśli otrzymano więcej niż jeden komunikat o podanej nazwie, wartość pary jest tablicą sygnatur czasowych, która wskazuje, kiedy te komunikaty zostały odebrane.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

Klienci *muszą* potwierdzić otrzymanie wszystkich komunikatów wysyłanych przez usługę, dołączając sygnatury czasowe tych komunikatów w treści JSON. Jeśli klient nie będzie mógł potwierdzić otrzymania komunikatu, usługa może przerwać połączenie.

### <a name="metrics"></a>Metryki

Klienci muszą zawierać informacje o zdarzeniach, które wystąpiły w okresie istnienia żądania. Obsługiwane są następujące metryki: `Connection`, `Microphone`, i `ListeningTrigger`.

### <a name="metric-connection"></a>Metryki`Connection`

`Connection` Metryka określa szczegółowe informacje o próbach połączenia przez klienta. Metryka musi obejmować sygnatury czasowe, gdy połączenie WebSocket zostało uruchomione i zakończone. Metryka jest wymagana *tylko w przypadku pierwszego włączenia połączenia.* `Connection` Kolejne operacje nie są wymagane do uwzględnienia tych informacji. Jeśli klient wykonuje wiele prób połączenia przed nawiązaniem połączenia, należy uwzględnić informacje o *wszystkich* próbach połączenia. Aby uzyskać więcej informacji, zobacz [telemetrię błędu połączenia](#connection-failure-telemetry).

| Pole | Opis | Użycie |
| ----- | ----------- | ----- |
| Name | `Connection` | Wymagane |
| Id | Wartość identyfikatora połączenia użyta w nagłówku *X-ConnectionID* dla tego żądania połączenia | Wymagane |
| Start | Czas wysłania żądania połączenia przez klienta | Wymagane |
| End | Czas, po którym klient otrzymał powiadomienie, że połączenie zostało nawiązane pomyślnie, lub w przypadkach błędów, odrzuconych, odrzuconych lub zakończonych niepowodzeniem | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli połączenie zakończyło się pomyślnie, klienci powinny pominąć to pole. Maksymalna długość tego pola to 50 znaków. | Wymagane dla przypadków błędów, pominięte w przeciwnym razie |

Opis błędu powinien mieć co najwyżej 50 znaków i najlepiej być jedną z wartości wymienionych w poniższej tabeli. Jeśli warunek błędu nie jest zgodny z jedną z tych wartości, klienci mogą użyć zwięzłego opisu warunku błędu, używając [CamelCasing](https://en.wikipedia.org/wiki/Camel_case) bez odstępów. Możliwość wysłania komunikatu *telemetrii* wymaga połączenia z usługą, dlatego w komunikacie *telemetrii* mogą być zgłaszane tylko przejściowe lub tymczasowe warunki błędu. Warunki błędów, które *trwale* blokują klientowi nawiązywanie połączenia z usługą, uniemożliwiają klientowi wysłanie do usługi komunikatu, w tym komunikatów *telemetrycznych* .

| Błąd | Użycie |
| ----- | ----- |
| DNSfailure | Klient nie mógł nawiązać połączenia z usługą z powodu błędu DNS w stosie sieciowym. |
| Nonetwork | Klient podjął próbę nawiązania połączenia, ale stos sieciowy zgłosił, że żadna sieć fizyczna nie była dostępna. |
| Noauthorization | Połączenie klienta nie powiodło się podczas próby uzyskania tokenu autoryzacji dla połączenia. |
| Brak zasobów | Klient wystąpił poza pewien zasób lokalny (na przykład pamięć) podczas próby nawiązania połączenia. |
| Zabroniony | Klient nie mógł nawiązać połączenia z usługą, ponieważ usługa zwróciła kod stanu `403 Forbidden` http w żądaniu uaktualnienia protokołu WebSocket. |
| Brak autoryzacji | Klient nie mógł nawiązać połączenia z usługą, ponieważ usługa zwróciła kod stanu `401 Unauthorized` http w żądaniu uaktualnienia protokołu WebSocket. |
| BadRequest | Klient nie mógł nawiązać połączenia z usługą, ponieważ usługa zwróciła kod stanu `400 Bad Request` http w żądaniu uaktualnienia protokołu WebSocket. |
| ServerUnavailable | Klient nie mógł nawiązać połączenia z usługą, ponieważ usługa zwróciła kod stanu `503 Server Unavailable` http w żądaniu uaktualnienia protokołu WebSocket. |
| Błąd servererror | Klient nie mógł nawiązać połączenia z usługą, ponieważ usługa zwróciła `HTTP 500` kod stanu błędu wewnętrznego w żądaniu uaktualnienia protokołu WebSocket. |
| limit czasu | Upłynął limit czasu żądania połączenia klienta bez odpowiedzi z usługi. Pole *koniec* zawiera czas, po upływie którego upłynął limit czasu klienta i czas oczekiwania na połączenie. |
| Błąd clienterror | Klient przerwał połączenie z powodu błędu wewnętrznego klienta. |

### <a name="metric-microphone"></a>Metryki`Microphone`

Ta `Microphone` Metryka jest wymagana przez wszystkie zamiany mowy. Ta Metryka mierzy czas na kliencie, w którym dane wejściowe audio są aktywnie używane dla żądania mowy.

Skorzystaj z poniższych przykładów jako wytycznych dotyczących rejestrowania wartości czasu *rozpoczęcia* dla `Microphone` metryki w aplikacji klienckiej:

* Aplikacja kliencka wymaga, aby użytkownik musiał nacisnąć przycisk fizyczny, aby uruchomić mikrofon. Po naciśnięciu przycisku aplikacja kliencka odczytuje dane wejściowe z mikrofonu i wysyła je do usługi Speech. Wartość`Microphone` *początkowa* metryki rejestruje czas po wypchnięciu przycisku, gdy mikrofon jest zainicjowany i gotowy do zapewnienia danych wejściowych. `speech.endDetected` Wartość *końcowa* metryki rejestruje czas, w którym aplikacja kliencka zatrzymała przesyłanie strumieniowe audio do usługi po odebraniu komunikatu z usługi. `Microphone`

* Aplikacja kliencka używa słowa kluczowego Spotter, które jest "zawsze" nasłuchuje. Tylko po Spotter słowa kluczowego wymawiane jest, że aplikacja kliencka zbiera dane wejściowe z mikrofonu i wysyła je do usługi mowy. Wartość *początkowa* dla `Microphone` metryki rejestruje czas, przez który słowo kluczowe Spotter powiadomiło klienta o konieczności rozpoczęcia korzystania z mikrofonu. `speech.endDetected` Wartość *końcowa* metryki rejestruje czas, w którym aplikacja kliencka zatrzymała przesyłanie strumieniowe audio do usługi po odebraniu komunikatu z usługi. `Microphone`

* Aplikacja kliencka ma dostęp do stałego strumienia audio i wykonuje wyciszenie/wykrycie mowy dla tego strumienia audio w *module wykrywania mowy*. Wartość *początkowa* dla `Microphone` metryki rejestruje czas, przez który *moduł wykrywania mowy* powiadamia klienta o konieczności rozpoczęcia korzystania ze strumienia audio. `speech.endDetected` Wartość *końcowa* metryki rejestruje czas, w którym aplikacja kliencka zatrzymała przesyłanie strumieniowe audio do usługi po odebraniu komunikatu z usługi. `Microphone`

* Aplikacja kliencka przetwarza drugi przekształcenie żądania i jest informowany przez komunikat odpowiedzi usługi, aby włączyć mikrofon w celu zebrania danych wejściowych dla drugiego ruchu. Wartość`Microphone` *początkowa* metryki rejestruje czas, w którym aplikacja kliencka włącza mikrofon i zaczyna używać danych wejściowych z tego źródła audio. `speech.endDetected` Wartość *końcowa* metryki rejestruje czas, w którym aplikacja kliencka zatrzymała przesyłanie strumieniowe audio do usługi po odebraniu komunikatu z usługi. `Microphone`

Wartość czasu *zakończenia* dla `Microphone` metryki rejestruje czas zatrzymania przesyłania strumieniowego audio przez aplikację kliencką. W większości sytuacji to zdarzenie występuje wkrótce po odebraniu `speech.endDetected` przez klienta komunikatu z usługi. Aplikacje klienckie mogą sprawdzić, czy są prawidłowo zgodne z protokołem, upewniając się, że wartość czasu *zakończenia* dla `Microphone` tej metryki jest późniejsza niż wartość `speech.endDetected` czasu odbioru wiadomości. Z tego powodu zwykle opóźnienie między końcem jednego z kolei a rozpoczęciem kolejnego działania powoduje, że klienci mogą weryfikować zgodność protokołów, upewniając się, że `Microphone` czas *rozpoczęcia* metryki dla każdego kolejnego powraca czas, gdy Klient *rozpoczął* używanie mikrofonu do przesyłania strumieniowego wejścia audio do usługi.

| Pole | Opis | Użycie |
| ----- | ----------- | ----- |
| Name | Gniazdo | Wymagane |
| Start | Czas rozpoczęcia pracy przez klienta przy użyciu wejścia audio z mikrofonu lub innego strumienia audio lub odebrania wyzwalacza ze słowa kluczowego Spotter | Wymagane |
| End | Czas zatrzymania klienta przy użyciu mikrofonu lub strumienia audio | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli operacja mikrofonu zakończyła się pomyślnie, klienci powinni pominąć to pole. Maksymalna długość tego pola to 50 znaków. | Wymagane dla przypadków błędów, pominięte w przeciwnym razie |

### <a name="metric-listeningtrigger"></a>Metryki`ListeningTrigger`
`ListeningTrigger` Metryka mierzy czas, kiedy użytkownik wykonuje akcję inicjującą wprowadzanie mowy. `ListeningTrigger` Metryka jest opcjonalna, ale klienci, którzy mogą zapewnić tę metrykę, są zachęcani do tego.

W poniższych przykładach przedstawiono wskazówki dotyczące rejestrowania `ListeningTrigger` wartości czasu *rozpoczęcia* i *zakończenia* dla metryki w aplikacji klienckiej.

* Aplikacja kliencka wymaga, aby użytkownik musiał nacisnąć przycisk fizyczny, aby uruchomić mikrofon. Wartość *początkowa* tej metryki rejestruje czas wypchnięcia przycisku. Wartość *końcowa* rejestruje czas zakończenia wypychania przycisku.

* Aplikacja kliencka używa słowa kluczowego Spotter, które jest "zawsze" nasłuchuje. Gdy słowo kluczowe Spotter wykrywa wymawiane frazy wyzwalacza, aplikacja kliencka odczytuje dane wejściowe z mikrofonu i wysyła je do usługi mowy. Wartość *początkowa* tej metryki rejestruje czas, w którym słowo kluczowe Spotter otrzymało dźwięk, który został wykryty jako frazę wyzwalacza. Wartość *końcowa* rejestruje czas, w którym ostatni wyraz frazy wyzwalacza był wymawiany przez użytkownika.

* Aplikacja kliencka ma dostęp do stałego strumienia audio i wykonuje wyciszenie/wykrycie mowy dla tego strumienia audio w *module wykrywania mowy*. Wartość *początkowa* tej metryki rejestruje czas, przez jaki *moduł wykrywania mowy* otrzymał dźwięk, który został wykryty jako mowę. Wartość *końcowa* rejestruje czas, w którym *moduł wykrywania mowy* wykrył mowę.

* Aplikacja kliencka przetwarza drugi przekształcenie żądania i jest informowany przez komunikat odpowiedzi usługi, aby włączyć mikrofon w celu zebrania danych wejściowych dla drugiego ruchu. Aplikacja kliencka *nie* powinna uwzględniać `ListeningTrigger` metryki dla tej usługi.

| Pole | Opis | Użycie |
| ----- | ----------- | ----- |
| Name | ListeningTrigger | Optional |
| Start | Czas uruchomienia wyzwalacza nasłuchiwania klienta | Wymagane |
| End | Czas zakończenia wyzwalacza nasłuchiwania klienta | Wymagane |
| Błąd | Opis błędu, który wystąpił, jeśli istnieje. Jeśli operacja wyzwalacza zakończyła się pomyślnie, klienci powinni pominąć to pole. Maksymalna długość tego pola to 50 znaków. | Wymagane dla przypadków błędów, pominięte w przeciwnym razie |

#### <a name="sample-message"></a>Przykładowy komunikat

Poniższy przykład pokazuje komunikat telemetrii z elementami ReceivedMessages i Metrics:

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

W tej sekcji opisano rodzaje komunikatów o błędach i warunki, które aplikacja musi obsłużyć.

### <a name="http-status-codes"></a>Kody stanu HTTP

W trakcie żądania uaktualnienia do protokołu WebSocket usługa mowy może zwrócić dowolny ze standardowych kodów stanu HTTP, takich jak `400 Bad Request`itd. Aplikacja musi prawidłowo obsłużyć te warunki błędu.

#### <a name="authorization-errors"></a>Błędy autoryzacji

Jeśli podczas uaktualniania protokołu WebSocket podano nieprawidłową autoryzację, usługa mowy zwraca `403 Forbidden` kod stanu HTTP. Wśród warunków, które mogą wyzwolić ten kod błędu, są:

* Brak nagłówka *autoryzacji*

* Nieprawidłowy token autoryzacji

* Wygasły Token autoryzacji

Komunikat `403 Forbidden` o błędzie nie wskazuje na problem z usługą mowy. Ten komunikat o błędzie wskazuje na problem z aplikacją kliencką.

### <a name="protocol-violation-errors"></a>Błędy naruszenia protokołu

Jeśli usługa mowy wykrywa naruszenia protokołu z klienta, usługa przerywa połączenie z użyciem protokołu WebSocket po powrocie *kodu stanu* i *przyczynie* zakończenia. Aplikacje klienckie mogą używać tych informacji do rozwiązywania problemów i naprawiania naruszeń.

#### <a name="incorrect-message-format"></a>Niepoprawny format komunikatu

Jeśli klient wyśle wiadomość tekstową lub binarną do usługi, która nie jest zakodowana w poprawnym formacie podanym w tej specyfikacji, usługa zamknie połączenie z *1007 nieprawidłowym kodem stanu danych ładunku* .

Usługa zwraca ten kod stanu z różnych powodów, jak pokazano w następujących przykładach:

* "Nieprawidłowy format komunikatu. Komunikat binarny ma nieprawidłowy prefiks rozmiaru nagłówka. " Klient wysłał komunikat binarny, który ma nieprawidłowy prefiks rozmiaru nagłówka.

* "Nieprawidłowy format komunikatu. Komunikat binarny ma nieprawidłowy rozmiar nagłówka. " Klient wysłał komunikat binarny, który określił nieprawidłowy rozmiar nagłówka.

* "Nieprawidłowy format komunikatu. Dekodowanie nagłówków komunikatów binarnych w kodzie UTF-8 nie powiodło się. " Klient wysłał komunikat binarny zawierający nagłówki, które nie zostały poprawnie zakodowane w formacie UTF-8.

* "Nieprawidłowy format komunikatu. Wiadomość tekstowa nie zawiera żadnych danych ". Klient wysłał wiadomość tekstową, która nie zawiera danych treści.

* "Nieprawidłowy format komunikatu. Dekodowanie wiadomości tekstowej do UTF-8 nie powiodło się. " Klient wysłał wiadomość tekstową, która nie została poprawnie zakodowana w formacie UTF-8.

* "Nieprawidłowy format komunikatu. Wiadomość tekstowa nie zawiera separatora nagłówka ". Klient wysłał wiadomość tekstową, która nie zawiera separatora nagłówka lub użyła nieprawidłowego separatora nagłówka.

#### <a name="missing-or-empty-headers"></a>Brakujące lub puste nagłówki

Jeśli klient wysyła komunikat, który nie ma wymaganych nagłówków *X-numer_id_żądania* lub *Path*, Usługa zamyka połączenie z kodem stanu *błędu protokołu 1002* . Komunikat ma wartość "Brak/pusty nagłówek". {Nazwa nagłówka} ".

#### <a name="requestid-values"></a>Wartości żądania

Jeśli klient wysyła komunikat, który określa nagłówek *X-numer_id_żądania* z nieprawidłowym formatem, usługa zamknie połączenie i zwróci stan *błędu protokołu 1002* . Komunikat ma wartość "Nieprawidłowe żądanie. Nie określono wartości nagłówka X-Numer_id_żądania w formacie identyfikatora UUID bez kreski "".

#### <a name="audio-encoding-errors"></a>Błędy kodowania audio

Jeśli klient wysyła fragment audio, który inicjuje przekształcenie, a format dźwięku lub kodowanie nie jest zgodne z wymaganą specyfikacją, usługa zamknie połączenie i zwróci *1007 nieprawidłowy kod stanu danych ładunku* . Komunikat wskazuje Źródło błędu kodowania formatowania.

#### <a name="requestid-reuse"></a>Ponowne użycie żądania

Po zakończeniu gdy klient wysyła komunikat, który ponownie używa identyfikatora żądania z tego powodu, usługa zamknie połączenie i zwróci kod stanu *błędu protokołu 1002* . Komunikat ma wartość "Nieprawidłowe żądanie. Ponowne użycie identyfikatorów żądań jest niedozwolone. "

## <a name="connection-failure-telemetry"></a>Dane telemetryczne błędu połączenia

Aby zapewnić najlepsze możliwe środowisko użytkownika, klienci muszą poinformować usługę mowy o sygnaturach czasowych dla ważnych punktów kontrolnych w ramach połączenia przy użyciu komunikatu *telemetrii* . Równie ważne jest, aby klienci powiadamiali usługę połączeń, które zostały podjęte, ale nie powiodły się.

Dla każdej próby połączenia, która się nie powiodła, klienci tworzą komunikat *telemetrii* z unikatową wartością nagłówka *X-IdentyfikatorŻądania* . Ponieważ klient nie mógł nawiązać połączenia, pole *ReceivedMessages* w treści JSON można pominąć. Uwzględniony jest tylko wpiswpolumetryki`Connection` . Ten wpis obejmuje sygnatury czasowe rozpoczęcia i zakończenia oraz warunek błędu, który wystąpił.

### <a name="connection-retries-in-telemetry"></a>Ponawianie próby połączenia w telemetrii

Klienci powinni rozróżnić *ponowną próbę* od *wielu prób połączenia* przez zdarzenie wyzwalające próbę połączenia. Próby połączenia, które są wykonywane programowo, bez ponowień wprowadzonych przez użytkownika. Wiele prób połączenia, które są wykonywane w odpowiedzi na dane wejściowe użytkownika, to wiele prób połączenia. Klienci mogą nawiązać każde połączenie wyzwolone przez użytkownika, próbując wykonać unikatowy komunikat *X-numer_id_żądania* i *telemetrię* . Klienci ponownie użyją *żądania X-IdentyfikatorŻądania* w celu programowego ponawiania prób. Jeśli wykonano wiele ponownych prób nawiązania połączenia, każda próba ponowienia zostanie uwzględniona `Connection` jako wpis w komunikacie *telemetrii* .

Na przykład załóżmy, że użytkownik mówi wyzwalacza słowa kluczowego, aby rozpocząć połączenie, a pierwsze próba połączenia nie powiedzie się z powodu błędów DNS. Jednak druga próba programistyczna wykonywana przez klienta powiedzie się. Ponieważ klient próbuje nawiązać połączenie bez konieczności wprowadzania dodatkowych danych przez użytkownika, klient korzysta z pojedynczego komunikatu *telemetrii* z `Connection` wieloma wpisami w celu opisania połączenia.

Innym przykładem jest to, że użytkownik mówi wyzwalacza słowa kluczowego, aby rozpocząć połączenie, a próba nawiązania połączenia kończy się niepowodzeniem po trzech ponownych próbach. Klient zostanie następnie zakończył próbę nawiązania połączenia z usługą i informuje użytkownika o niepowodzeniu. Użytkownik będzie następnie ponownie wypowiadał wyzwalacz słowa kluczowego. Tym razem klient nawiąże połączenie z usługą. Po nawiązaniu połączenia klient natychmiast wysyła do usługi komunikat *telemetrii* , który zawiera `Connection` trzy wpisy opisujące błędy połączenia. Po odebraniu `turn.end` komunikatu klient wysyła kolejny komunikat *telemetrii* , który opisuje pomyślne połączenie.

## <a name="error-message-reference"></a>Odwołanie do komunikatu o błędzie

### <a name="http-status-codes"></a>Kody stanu HTTP

| Kod stanu HTTP | Opis | Rozwiązywanie problemów |
| - | - | - |
| 400 Nieprawidłowe żądanie | Klient wysłał żądanie połączenia WebSocket, które było nieprawidłowe. | Sprawdź, czy zostały podane wszystkie wymagane parametry i nagłówki HTTP oraz czy wartości są poprawne. |
| 401 — nieautoryzowane | Klient nie dołączył wymaganych informacji o autoryzacji. | Sprawdź, czy do połączenia z użyciem protokołu WebSocket jest wysyłany nagłówek *autoryzacji* . |
| 403 Zabronione | Klient wysłał informacje o autoryzacji, ale jest nieprawidłowy. | Sprawdź, czy w nagłówku *autoryzacji* nie jest wysyłana wartość wygasła lub nieprawidłowa. |
| 404 Nie znaleziono | Klient próbował uzyskać dostęp do nieobsługiwanej ścieżki URL. | Sprawdź, czy używasz poprawnego adresu URL dla połączenia z użyciem protokołu WebSocket. |
| Błąd serwera 500 | Usługa napotkała błąd wewnętrzny i nie może spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |
| 503 — usługa niedostępna | Usługa była niedostępna do obsługi żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

### <a name="websocket-error-codes"></a>Kody błędów protokołu WebSocket

| Kod WebSocketsStatus | Opis | Rozwiązywanie problemów |
| - | - | - |
| 1000 normalne zamknięcie | Usługa zamknęła połączenie z użyciem protokołu WebSocket bez błędu. | W przypadku nieoczekiwanego zamknięcia protokołu WebSocket Przeczytaj dokumentację, aby dowiedzieć się, jak i kiedy usługa może zakończyć połączenie z użyciem protokołu WebSocket. |
| Błąd protokołu 1002 | Klient nie przestrzegał wymagań protokołu. | Zapoznaj się z dokumentacją protokołu i Wyczyść informacje o wymaganiach. Zapoznaj się z poprzednią dokumentacją dotyczącą przyczyn błędów, aby sprawdzić, czy naruszasz wymagania dotyczące protokołu. |
| 1007 nieprawidłowe dane ładunku | Klient wysłał nieprawidłowy ładunek w komunikacie protokołu. | Sprawdź ostatni komunikat wysłany do usługi pod kątem błędów. Zapoznaj się z poprzednią dokumentacją dotyczącą błędów ładunku. |
| Błąd serwera 1011 | Usługa napotkała błąd wewnętrzny i nie może spełnić żądania. | W większości przypadków ten błąd jest przejściowy. Ponów żądanie. |

## <a name="related-topics"></a>Tematy pokrewne

Zobacz [zestaw SDK języka JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) , który jest implementacją protokołu usługi mowy opartego na protokole WebSocket.
