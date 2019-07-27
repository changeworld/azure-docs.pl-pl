---
title: Elementy webhook — usługa mowy
titleSuffix: Azure Cognitive Services
description: Elementy webhook to wywołania HTTP idealne dla optymalizacji rozwiązania podczas pracy z długotrwałymi procesami, takimi jak Importy, adaptacja, testy dokładności lub transkrypcje długotrwałych plików.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558802"
---
# <a name="webhooks-for-speech-services"></a>Elementy webhook dla usługi Speech Services

Elementy webhook przypominają wywołania zwrotne HTTP, które umożliwiają aplikacji akceptowanie danych z usług mowy, gdy staną się dostępne. Za pomocą elementów webhook można zoptymalizować korzystanie z interfejsów API REST, eliminując konieczność ciągłego sondowania odpowiedzi. W następnych kilku sekcjach dowiesz się, jak używać elementów webhook z usługami mowy.

## <a name="supported-operations"></a>Obsługiwane operacje

Usługi mowy obsługują elementy webhook dla wszystkich długotrwałych operacji. Każda z wymienionych poniżej operacji może wyzwolić wywołanie zwrotne HTTP po zakończeniu.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Następnie Utwórzmy element webhook.

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Utwórzmy element webhook dla transkrypcji w trybie offline. Scenariusz: użytkownik ma długi plik audio, który chce transkrypcja asynchronicznie z interfejsem API transkrypcji usługi Batch.

Elementy webhook można utworzyć, wysyłając żądanie post do https://\<region\>. CRIS.AI/API/speechtotext/v2.1/Transcriptions/Hooks.

Parametry konfiguracji żądania są podane jako dane JSON:

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
Wszystkie żądania POST do interfejsu API transkrypcji usługi Batch wymagają `name`elementu. Parametry `description` i`properties` są opcjonalne.

`Active` Właściwość służy do przełączania wywołania z powrotem do adresu URL i wyłączania bez konieczności usuwania i ponownego tworzenia rejestracji elementu webhook. Jeśli konieczne jest tylko wywołanie zwrotne po zakończeniu procesu, Usuń element webhook i Przełącz `Active` właściwość na wartość false.

Typ `TranscriptionCompletion` zdarzenia jest podany w tablicy Events. Nastąpi wywołanie zwrotne do punktu końcowego, gdy transkrypcja zostanie zaimportowana`Succeeded` do `Failed`stanu terminalu (lub). W przypadku wywołania zwrotnego do zarejestrowanego adresu URL żądanie będzie `X-MicrosoftSpeechServices-Event` zawierać nagłówek zawierający jeden z zarejestrowanych typów zdarzeń. Istnieje jedno żądanie dla zarejestrowanego typu zdarzenia.

Istnieje jeden typ zdarzenia, którego nie można subskrybować. Jest to typ `Ping` zdarzenia. Żądanie z tym typem jest wysyłane do adresu URL po zakończeniu tworzenia elementu webhook przy użyciu adresu URL polecenia ping (patrz poniżej).  

W konfiguracji `url` właściwość jest wymagana. Żądania POST są wysyłane do tego adresu URL. `secret` Służy do tworzenia skrótu SHA256 ładunku przy użyciu wpisu tajnego jako klucza HMAC. Skrót jest ustawiany jako `X-MicrosoftSpeechServices-Signature` nagłówek podczas wywoływania z powrotem do zarejestrowanego adresu URL. Ten nagłówek jest kodowany algorytmem Base64.

W tym przykładzie pokazano, jak sprawdzić poprawność ładunku przy użyciu C#:

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
W tym fragmencie `secret` kodu kod jest zdekodowany i sprawdzony. Zauważ również, że typ zdarzenia elementu webhook został przełączony. Obecnie istnieje jedno zdarzenie na zakończono transkrypcję. Kod ponawia próbę pięć razy dla każdego zdarzenia (z jednym drugim opóźnieniem) przed pokazaniem.

### <a name="other-webhook-operations"></a>Inne operacje elementu webhook

Aby uzyskać wszystkie zarejestrowane elementy webhook: POBIERZ https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Aby uzyskać jeden konkretny element webhook: POBIERZ https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Aby usunąć jeden z określonych elementów webhook: USUNIĘTY https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> W powyższym przykładzie region ma wartość "zachodnie". Powinno to zostać zastąpione przez region, w którym został utworzony zasób usługi Speech Services w Azure Portal.

Wpis https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping treści: pusty

Wysyła żądanie POST do zarejestrowanego adresu URL. Żądanie zawiera `X-MicrosoftSpeechServices-Event` nagłówek z wartością ping. Jeśli element webhook został zarejestrowany przy użyciu klucza tajnego, będzie zawierał `X-MicrosoftSpeechServices-Signature` nagłówek z SHA256 skrótem do ładunku z kluczem tajnym jako klucz HMAC. Skrót jest kodowany algorytmem Base64.

Wpis https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test treści: pusty

Wysyła żądanie POST do zarejestrowanego adresu URL, jeśli jednostka dla subskrybowanego typu zdarzenia (transkrypcja) jest obecna w systemie i jest w odpowiednim stanie. Ładunek zostanie wygenerowany z ostatniej jednostki, która spowodowałaby wywołanie elementu webhook. Jeśli żadna jednostka nie istnieje, wpis będzie odpowiadał z 204. Jeśli można wykonać żądanie testowe, będzie ono odpowiadać 200. Treść żądania ma ten sam kształt jak w przypadku żądania GET dla określonej jednostki element webhook jest subskrybowany (na potrzeby transkrypcji wystąpienia). Żądanie będzie zawierało `X-MicrosoftSpeechServices-Event` nagłówki i `X-MicrosoftSpeechServices-Signature` zgodnie z wcześniejszym opisem.

### <a name="run-a-test"></a>Uruchom test

Szybki test można wykonać przy użyciu witryny sieci Web https://bin.webhookrelay.com. Z tego miejsca możesz uzyskać zwrotne adresy URL do przekazania jako parametr do wpisu HTTP w celu utworzenia elementu webhook opisanego wcześniej w dokumencie.

Kliknij pozycję "Utwórz zasobnik" i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby uzyskać punkt zaczepienia. Następnie użyj informacji znajdujących się na tej stronie, aby zarejestrować punkt zaczepienia za pomocą usługi mowy. Ładunek komunikatu przekaźnika — w odpowiedzi na zakończenie transkrypcji — wygląda następująco:

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
Komunikat zawiera adres URL rejestrowania i modele używane do transkrypcja tego nagrania.

## <a name="next-steps"></a>Następne kroki

* [Pobierz subskrypcję usługi mowy w wersji próbnej](https://azure.microsoft.com/try/cognitive-services/)
