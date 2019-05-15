---
title: Transkrypcja wielu uczestników rozmowy z zestawem SDK mowy — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji z zestawem SDK rozpoznawania mowy. Dostępne dla C++, C#i Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: 80ec606fee30c239d47bca94188d3b9cbb7c82d5
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604415"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transkrypcja wielu uczestników rozmowy z zestawem SDK rozpoznawania mowy

Zestaw SDK rozpoznawania mowy **ConversationTranscriber** interfejs API umożliwia także spotkań/konwersacje możliwość dodawania, usuwania i identyfikowanie uczestników przez przesyłanie strumieniowe audio do korzystania z usług przetwarzania mowy `PullStream` lub `PushStream`.

## <a name="limitations"></a>Ograniczenia

* Konwersacja programu transcriber jest obsługiwana w przypadku C++, C#i języka Java na Windows, Linux i Android.
* Mxchip ROOBO to środowisko obsługiwanego sprzętu, do tworzenia transkrypcje konwersacji, ponieważ zapewniający cykliczne tablica wielu mikrofonu, która może być wykorzystywany efektywnie Identyfikacja osoby mówiącej. [Aby uzyskać więcej informacji, zobacz zestawu Speech Devices SDK](speech-devices-sdk.md).
* Obsługa transkrypcji konwersacji w zestawie SDK rozpoznawania mowy jest ograniczona do używania audio ściągania i wypychania strumienie trybu z ośmiu kanały audio PCM 16 kHz 16-bitowych.
* Transkrypcja konwersacji jest obecnie dostępna w językach "en US" i "zh-CN" w następujących regionach: centralus i Azja Wschodnia.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dowiedz się, jak używać mowy na tekst z zestawem SDK rozpoznawania mowy.](quickstart-csharp-dotnet-windows.md)
* [Pobierz wersję próbną subskrypcji mowy.](https://azure.microsoft.com/try/cognitive-services/)
* Zestaw SDK w wersji 1.5.1 mowy lub nowszy jest wymagany.

## <a name="create-voice-signatures-for-participants"></a>Tworzenie podpisów głosu dla uczestników

Pierwszym krokiem jest tworzenie podpisów głosu dla uczestników konwersacji. Tworzenie podpisów głos jest wymagane do identyfikacji osoby mówiącej wydajne.

### <a name="requirements-for-input-wave-file"></a>Wymagania dotyczące pliku wejściowego wave

* Plik wave audio w wejściowych do tworzenia podpisów głos jest w przykłady 16-bitowych, częstotliwość próbkowania 16 kHz i format pojedynczy kanał (Mono).
* Zalecana długość dla każdej próbki audio jest od 30 sekund do dwóch minut.

Poniższy przykład przedstawia dwa różne sposoby tworzenia podpisu głosowych przez [przy użyciu interfejsu API REST.] (https://aka.ms/cts/signaturegenservice) z C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Przepisywania konwersacje

Transkrypcja rozmów z wielu uczestników, należy utworzyć `ConversationTranscriber` obiekt, który jest skojarzony z `AudioConfig` obiekt utworzony dla sesji konwersacji i strumień audio przy użyciu `PullAudioInputStream` lub `PushAudioInputStream`.

Załóżmy, że masz ConversationTranscriber klasę o nazwie `MyConversationTranscriber`. Twój kod może wyglądać następująco:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
