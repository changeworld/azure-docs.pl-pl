---
title: Dokumentacja interfejsu API zamiany mowy na tekst (REST) — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać interfejsu API REST mowy na tekst. W tym artykule dowiesz się o opcjach autoryzacji, opcje zapytania, jak struktury żądania i odpowiedzi.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487235"
---
# <a name="speech-to-text-rest-api"></a>Interfejs API REST mowy na tekst

Jako alternatywę dla [zestaw SDK rozpoznawania mowy](speech-sdk.md), usług przetwarzania mowy umożliwiają konwertowanie za pomocą interfejsu API REST mowy na tekst. Każdy punkt końcowy, dostępny jest skojarzone z regionem. Aplikacja wymaga klucza subskrypcji dla punktu końcowego, który ma być używany.

Przed rozpoczęciem korzystania z interfejsu API REST mowy na tekst, należy zrozumieć:
* Żądania, które używają interfejsu API REST może zawierać tylko 10 sekund nagrania audio.
* Interfejs API REST mowy na tekst zwraca tylko wyniki końcowe. Wyniki częściowe nie są dostarczane.

Jeśli wysyłanie dłużej audio jest wymagana dla aplikacji, należy rozważyć użycie [zestaw SDK rozpoznawania mowy](speech-sdk.md) lub [batch transkrypcji](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Regiony i punktów końcowych

Te regiony są obsługiwane w przypadku przekształcania mowy na tekst przy użyciu interfejsu API REST. Upewnij się, wybierz pozycję punkt końcowy, który odpowiada Twoim regionie subskrypcji.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Parametry zapytania

Te parametry mogą być zawarte w ciągu zapytania żądania REST.

| Parametr | Opis | Wymagane / opcjonalne |
|-----------|-------------|---------------------|
| `language` | Określa język mówiony, który jest rozpoznawany. Zobacz [obsługiwane języki](language-support.md#speech-to-text). | Wymagane |
| `format` | Określa format wyniku. Akceptowane wartości to `simple` i `detailed`. Proste wyniki obejmują `RecognitionStatus`, `DisplayText`, `Offset`, i `Duration`. Precyzyjne reakcje obejmują wiele wyników z wartościami zaufania i cztery różne reprezentacje. Ustawieniem domyślnym jest `simple`. | Optional (Opcjonalność) |
| `profanity` | Określa sposób obsługi wulgaryzmów w wyniki rozpoznawania. Akceptowane wartości to `masked`, gwiazdek, która zastępuje wulgaryzmów `removed`, który Usuń wszystkie wulgaryzmów z wyników, lub `raw`, w tym wulgaryzmów w wyniku. Ustawieniem domyślnym jest `masked`. | Optional (Opcjonalność) |

## <a name="request-headers"></a>Nagłówki żądań

Ta tabela zawiera wymagane i opcjonalne nagłówki dla żądania zamiany mowy na tekst.

|Nagłówek| Opis | Wymagane / opcjonalne |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Klucz subskrypcji usług przetwarzania mowy. | Albo tego pliku nagłówkowego lub `Authorization` jest wymagana. |
| `Authorization` | Token autoryzacji poprzedzone wyrazem `Bearer`. Aby uzyskać więcej informacji, zobacz [Authentication](#authentication) (Uwierzytelnianie). | Albo tego pliku nagłówkowego lub `Ocp-Apim-Subscription-Key` jest wymagana. |
| `Content-type` | W tym artykule opisano format i kodera-dekodera audio podanych danych. Akceptowane wartości to `audio/wav; codecs=audio/pcm; samplerate=16000` i `audio/ogg; codecs=opus`. | Wymagane |
| `Transfer-Encoding` | Określa, czy fragmentaryczne dane audio są wysyłane, zamiast pojedynczego pliku. Ten nagłówek należy używać tylko, jeśli dane audio. | Optional (Opcjonalność) |
| `Expect` | Jeśli używasz fragmentaryczne transferu, Wyślij `Expect: 100-continue`. Usług przetwarzania mowy, użytkownik potwierdza żądanie początkowe i czeka na dodatkowe dane.| Wymagany, jeśli wysyłanie danych audio podzielonego. |
| `Accept` | Jeśli podano, musi on być `application/json`. Usługi mowy oferują wyniki w formacie JSON. Niektóre środowiska żądania sieci Web Podaj wartość domyślną niezgodne, jeśli nie zostanie określony, dzięki czemu jest dobrym rozwiązaniem jest zawsze zawierać `Accept`. | Opcjonalne, ale zalecane. |

## <a name="audio-formats"></a>Formaty audio

Dźwięku w treści HTTP `POST` żądania. Musi być w jednym z formatów w tej tabeli:

| Format | Koder-dekoder | Szybkość transmisji bitów | Częstotliwość próbkowania |
|--------|-------|---------|-------------|
| WAV | MODUŁU PCM | 16-bitowych | 16 kHz, narzędzie mono |
| OGG | DZIELE | 16-bitowych | 16 kHz, narzędzie mono |

>[!NOTE]
>Powyższe formaty są obsługiwane za pośrednictwem interfejsu API REST i WebSocket w usług przetwarzania mowy. [Zestaw SDK rozpoznawania mowy](speech-sdk.md) aktualnie obsługuje tylko WAV Formatuj przy użyciu kodera-dekodera PCM.

## <a name="sample-request"></a>Przykładowe żądanie

Jest to typowy żądania HTTP. Poniższy przykład obejmuje nazwę hosta i wymagane nagłówki. Należy zauważyć, że usługa oczekuje również dane audio, która nie znajduje się w tym przykładzie. Jak wspomniano wcześniej, segmentu jest zalecane, jednak nie jest wymagane.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Kody stanu HTTP

Kod stanu HTTP dla każdej odpowiedzi wskazuje sukces lub typowych błędów.

| Kod stanu HTTP | Opis | Możliwa przyczyna |
|------------------|-------------|-----------------|
| 100 | Kontynuuj | Wstępne żądanie zostało zaakceptowane. Czy kontynuować wysyłanie pozostałe dane. (Używany przy użyciu transferu pakietowego). |
| 200 | OK | Żądanie powiodło się; treść odpowiedzi jest obiekt JSON. |
| 400 | Nieprawidłowe żądanie | Kod języka podany lub nie jest obsługiwany język; Nieprawidłowy plik audio. |
| 401 | Brak autoryzacji | Klucz subskrypcji lub autoryzacji token jest nieprawidłowy w określonym regionie lub nieprawidłowy punkt końcowy. |
| 403 | Zabroniony | Brak klucz subskrypcji lub autoryzacji tokenu. |

## <a name="chunked-transfer"></a>Fragmentaryczne transferu

Transferu pakietowego (`Transfer-Encoding: chunked`) może pomóc zmniejszyć opóźnienie rozpoznawania, ponieważ zezwala ona na usług przetwarzania mowy rozpocząć przetwarzanie plik dźwiękowy, gdy są przesyłane. Interfejs API REST nie zapewnia tymczasowe lub częściowe wyniki. Ta opcja jest przeznaczona wyłącznie do zwiększyć szybkość reakcji.

Ten przykładowy kod przedstawia sposób wysłania audio we fragmentach. Tylko pierwszy fragment może zawierać nagłówek pliku audio. `request` Obiekt HTTPWebRequest podłączonego do odpowiedniego punktu końcowego REST. `audioFile` jest to ścieżka do pliku audio na dysku.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Parametrów odpowiedzi

Wyniki są dostarczane w formacie JSON. `simple` Formatu zawiera następujące pola najwyższego poziomu.

| Parametr | Opis  |
|-----------|--------------|
|`RecognitionStatus`|Stan, takich jak `Success` rozpoznawania się pomyślnie. Zobacz następną tabelę.|
|`DisplayText`|Rozpoznany tekst po wielkość liter, znaki interpunkcyjne, tekst odwrotny normalizacji (Konwersja tekstu mówionego na krótszą formularzy, takich jak 200, "200" lub "odzyskiwania po awarii. Smith""lekarzem Smith"), a wulgaryzmów maskowania. Istnieje tylko w przypadku powodzenia.|
|`Offset`|Czas (w jednostkach 100-nanosekundowych), jaką rozpoznawanym mowy rozpoczyna się w strumienia audio.|
|`Duration`|Czas trwania (w jednostkach 100-nanosekundowych) rozpoznawaną mowy w strumienia audio.|

`RecognitionStatus` Pole może zawierać następujące wartości:

| Stan | Opis |
|--------|-------------|
| `Success` | Rozpoznawanie zakończyło się pomyślnie i `DisplayText` pole jest obecny. |
| `NoMatch` | Wykryto mowy strumienia audio, ale bez wyrazów z języka docelowego zostały dopasowane. Zwykle oznacza, że rozpoznawanie jest inny język niż te, które użytkownik mówiącego Prezydenta. |
| `InitialSilenceTimeout` | Początek strumienia audio zawiera tylko wyciszenia, a usługa upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `BabbleTimeout` | Początek strumienia audio zawiera tylko hałasu i usługi, upłynął limit czasu oczekiwania podczas rozpoznawania mowy. |
| `Error` | Usługa rozpoznawania napotkał błąd wewnętrzny i nie może kontynuować działania. Spróbuj ponownie, jeśli to możliwe. |

> [!NOTE]
> Jeśli audio składa się tylko z wulgaryzmów i `profanity` parametr zapytania ma wartość `remove`, usługa nie zwróciła wynik mowy.

`detailed` Format obejmuje te same dane co `simple` formacie wraz z `NBest`, listę alternatywnych interpretacji ten sam wynik rozpoznawania. Te wyniki są oceniane od najbardziej prawdopodobne najmniej prawdopodobnie pierwszy wpis jest taki sam jak wynik rozpoznawania głównego.  Korzystając z `detailed` formacie `DisplayText` jest dostarczana jako `Display` dla każdego wyniku `NBest` listy.

Każdy obiekt w `NBest` lista zawiera:

| Parametr | Opis |
|-----------|-------------|
| `Confidence` | Współczynnik ufności wpisu od 0,0 (nie zaufania) 1.0 (pełne zaufanie) |
| `Lexical` | Leksykalne formularza rozpoznany tekst: rzeczywistymi słowami został rozpoznany. |
| `ITN` | Znormalizowane tekstu odwrotność ("") forma kanoniczna rozpoznany, za pomocą telefonu liczb, liczby, skróty ("lekarzem smith" do "odzyskiwania po awarii smith") i inne zastosowane przekształcenia. |
| `MaskedITN` | Formularz podczas przy użyciu maskowania wulgaryzmów stosowane, jeśli jest to wymagane. |
| `Display` | Formularz wyświetlania rozpoznany tekst, znaki interpunkcyjne i dodaje wielkie litery. Ten parametr jest taka sama jak `DisplayText` pod warunkiem, gdy skonfigurowano format `simple`. |

## <a name="sample-responses"></a>Przykład odpowiedzi

Jest to typowa odpowiedź dla `simple` rozpoznawania.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Jest to typowa odpowiedź dla `detailed` rozpoznawania.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

- [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
