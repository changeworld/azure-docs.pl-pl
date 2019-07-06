---
title: Elementy Webhook — usługi mowy
titlesuffix: Azure Cognitive Services
description: Elementy Webhook są HTTP oddzwanianie idealne rozwiązanie w przypadku optymalizacji rozwiązania podczas pracy z długich uruchomionych procesów, takich jak importów, adaptacja, testy dokładności lub transkrypcje długotrwałe plików.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a100049ddfc9d4859e303546c1b10e814cf96ebb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606220"
---
# <a name="webhooks-for-speech-services"></a>Elementy Webhook dla usług przetwarzania mowy

Elementy Webhook są podobne wywołania zwrotne HTTP, które umożliwiają aplikacji na akceptowanie danych z usług przetwarzania mowy, gdy stanie się dostępna. Przy użyciu elementów webhook, można zoptymalizować korzystanie z interfejsów API REST, eliminując konieczność ciągłego sondowania dla odpowiedzi. W kolejnych sekcjach dowiesz się, jak używać elementów webhook z usług przetwarzania mowy.

## <a name="supported-operations"></a>Obsługiwane operacje

Usługi mowy obsługują elementy webhook dla wszystkich operacji długotrwałej. Każdej z czynności wymienionych poniżej możesz wyzwolić wywołanie zwrotne HTTP, po jego ukończeniu.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Następnie utwórz element webhook.

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Utwórz element webhook dla transkrypcji w trybie offline. Scenariusz: użytkownik ma plik długo działa audio, który chciałby transkrypcja asynchronicznie z interfejsem API usługi Batch transkrypcji.

Elementy Webhook można utworzyć, wprowadzając żądania POST do https://\<region\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks.

Parametry konfiguracji dla żądania są dostarczane jako dane JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Wszystkie żądania POST do interfejsu API usługi Batch transkrypcji wymagają `name`. `description` i `properties` parametry są opcjonalne.

`Active` Właściwość jest używana do przełączania wywołań zwrotnych do adresu URL włączać i wyłączać bez konieczności usunięcie i ponowne utworzenie rejestracji elementu webhook. Jeśli potrzebujesz tylko wywołania zwrotnego raz po proces składa się z pełną, następnie usuń element webhook i przełącznik `Active` wartość false dla właściwości.

Typ zdarzenia `TranscriptionCompletion` znajduje się w tablicy zdarzenia. Jej będzie wywołania zwrotnego do punktu końcowego podczas transkrypcji pobiera na stan końcowy (`Succeeded` lub `Failed`). W przypadku wywołań zwrotnych do zarejestrowanego adresu URL, żądanie będzie zawierać `X-MicrosoftSpeechServices-Event` nagłówka zawierające jeden z typów zarejestrowanych zdarzeń. Istnieje jedno żądanie wg typu zdarzenia zarejestrowane.

Istnieje jeden typ zdarzenia, który nie może subskrybować. Jest `Ping` typu zdarzenia. Żądania z tego typu są wysyłane do adresu URL po zakończeniu tworzenia elementu webhook, korzystając z polecenia ping adresu URL (patrz poniżej).  

W konfiguracji `url` właściwość jest wymagana. Żądania POST są wysyłane do tego adresu URL. `secret` Służy do tworzenia skrótu SHA256 ładunek, z kluczem tajnym jako klucz HMAC. Wartość skrótu jest ustawiony jako `X-MicrosoftSpeechServices-Signature` nagłówka podczas wywołań zwrotnych do zarejestrowanego adresu URL. Tego pliku nagłówkowego jest zakodowany w formacie Base64.

W tym przykładzie przedstawiono sposób sprawdzania poprawności ładunku przy użyciu C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
W poniższym przykładzie `secret` jest dekodowana i zweryfikowane. Zauważysz również, czy typ zdarzenia elementu webhook została przełączona. Obecnie ma jedno zdarzenie na ukończone transkrypcji. Kod ponowi pięć razy dla każdego zdarzenia (z jednym półsekundowym opóźnieniu) przed rezygnacją.

### <a name="other-webhook-operations"></a>Inne operacje elementów webhook

Aby wyświetlić wszystkie zarejestrowane elementy webhook: POBIERZ https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Aby wyświetlić jeden określonego elementu webhook: POBIERZ https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Aby usunąć jednego określonego elementu webhook: USUŃ https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> W powyższym przykładzie region jest 'westus'. Powinny zostać zastąpione region, w której utworzono zasób usługi mowy w witrynie Azure portal.

WPIS https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping treść: pusty

Wysyła żądanie POST na adres URL zarejestrowane. Żądanie zawiera `X-MicrosoftSpeechServices-Event` nagłówka przy użyciu polecenia ping wartość. Jeśli element webhook został zarejestrowany za pomocą klucza tajnego, będzie ona zawierać `X-MicrosoftSpeechServices-Signature` nagłówek o skrót SHA256 ładunku z kluczem tajnym jako klucz HMAC. Wartość skrótu jest zakodowany w formacie Base64.

WPIS https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test treść: pusty

Wysyła żądanie POST na adres URL zarejestrowanego, jeśli jednostki typu subskrybowanego zdarzenia (tekst) znajduje się w systemie i jest w odpowiednim stanie. Obciążenie zostanie wygenerowane z ostatni obiekt, który będzie wywoływany punktu zaczepienia sieci web. Jeśli jednostka nie jest obecny, WPIS będzie odpowiadać za pomocą 204. Jeśli żądanie testowe można zapewnić, odpowiada za 200. Treść żądania jest ten sam kształt, tak jak żądanie GET dla określonej jednostki punktu zaczepienia sieci web ma subskrybuje (na przykład transkrypcja). Żądanie będzie miał `X-MicrosoftSpeechServices-Event` i `X-MicrosoftSpeechServices-Signature` nagłówki zgodnie z opisem przed.

### <a name="run-a-test"></a>Uruchamianie testu

Może odbywać się przeprowadzić szybki test przy użyciu witryny sieci Web https://bin.webhookrelay.com. Z tego miejsca można uzyskać wywołanie kopii adresy URL do przekazania jako parametr do metody POST protokołu HTTP do tworzenia elementu webhook z opisem we wcześniejszej części dokumentu.

Kliknij pozycję "Utwórz zasobnika" i postępuj zgodnie z wyświetlanymi instrukcjami, aby uzyskać zaczepienia. Następnie użyć informacje podane na tej stronie, aby zarejestrować punkt zaczepienia z usługa mowy. Ładunek komunikatu przekazywania — w odpowiedzi na ukończenie transkrypcji — wygląda następująco:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Komunikat zawiera adres URL rejestrowania i modeli, które umożliwia także tego nagrania.

## <a name="next-steps"></a>Kolejne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
