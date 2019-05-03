---
title: Transkrypcja wielu uczestników rozmowy z zestawem SDK mowy — usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać usługi transkrypcji konwersacji z zestawem SDK rozpoznawania mowy. Dostępne dla C++, C#i Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jhakulin
ms.openlocfilehash: 73ab4cfa92a1efc49dea16ba2941cf16b7a1cf3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025794"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transkrypcja wielu uczestników rozmowy z zestawem SDK rozpoznawania mowy

Zestaw SDK rozpoznawania mowy **ConversationTranscriber** interfejs API umożliwia także spotkań/konwersacje możliwość dodawania, usuwania i identyfikowanie uczestników przez przesyłanie strumieniowe audio do korzystania z usług przetwarzania mowy `PullStream` lub `PushStream`.

## <a name="limitations"></a>Ograniczenia

* Konwersacja programu transcriber jest obsługiwana w przypadku C++, C#i języka Java na Windows, Linux i Android.
* Mxchip ROOBO to środowisko obsługiwanego sprzętu, do tworzenia konwersacji, ponieważ zapewniający cykliczne tablica wielu mikrofonu, która może być wykorzystana efektywnie przez usługę transkrypcji konwersacji identyfikacji osoby mówiącej. [Aby uzyskać więcej informacji, zobacz zestawu Speech Devices SDK](speech-devices-sdk.md). 
* Obsługa zestawu SDK rozpoznawania mowy jest ograniczona do użytkowania audio ściągania i wypychania strumienie trybu z ośmiu kanałach audio PCM.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dowiedz się, jak używać mowy na tekst z zestawem SDK rozpoznawania mowy.](quickstart-csharp-dotnet-windows.md)
* [Pobierz wersję próbną subskrypcji mowy.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Tworzenie podpisów głosu dla uczestników

Pierwszym krokiem jest tworzenie podpisów głosu dla uczestników konwersacji. Tworzenie podpisów głos jest wymagane do identyfikacji osoby mówiącej wydajne.
W poniższym przykładzie będziemy [Uzyskaj podpis głosowych za pomocą interfejsu API REST.](https://aka.ms/cts/signaturegenservice)

W poniższym przykładzie pokazano dwa różne sposoby tworzenia podpisów głosu:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
