---
title: Transkrypcja konwersacji wielouczestnikowych przy użyciu zestawu Speech SDK — Speech Service
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji przy użyciu zestawu Speech SDK. Dostępne dla C++języków C#, i Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066431"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transkrypcja konwersacji wielouczestnikowych przy użyciu zestawu Speech SDK

Interfejs API **ConversationTranscriber** zestawu mowy SDK umożliwia transkrypcja spotkań/konwersacji z możliwością dodawania, usuwania i identyfikowania uczestników za pomocą przesyłania strumieniowego audio do usług mowy przy użyciu `PullStream` lub `PushStream`.

## <a name="limitations"></a>Ograniczenia

* Transcriber konwersacji jest obsługiwana dla C++systemów C#, i Java w systemach Windows, Linux i Android.
* ROOBO DevKit to obsługiwane środowisko sprzętowe do tworzenia transkrypcji konwersacji, co zapewnia cykliczną tablicę z wielomikrofonem, którą można wydajnie wykorzystać do identyfikacji osoby mówiącej. [Aby uzyskać więcej informacji, zobacz zestaw SDK urządzeń usługi Speech](speech-devices-sdk.md).
* Obsługa zestawu Speech SDK na potrzeby transkrypcji konwersacji jest ograniczona do strumieni trybu ściągania i wypychania audio z 8 kanałów dla 16-bitowego 16 kHz audio PCM. Obecnie tylko te zestawy są obsługiwane w przypadku 8-kanałowego przechwytywania audio:
   * [ROOBO inteligentny dźwięk cykliczny 7-MIC DK](https://ddk.roobo.com/)
   * [Azure urządzenia Kinect DK](https://azure.microsoft.com/en-in/services/kinect-dk/)
* Transkrypcja konwersacji jest obecnie dostępna w językach "en-US" i "zh-CN" w następujących regionach: środkowe i eastasia.

## <a name="prerequisites"></a>Wymagania wstępne

* [Dowiedz się, jak używać zamiany mowy na tekst za pomocą zestawu Speech SDK.](quickstart-csharp-dotnet-windows.md)
* [Pobierz subskrypcję wersji próbnej usługi Speech.](https://azure.microsoft.com/try/cognitive-services/)
* Wymagany jest pakiet Speech SDK w wersji 1.5.1 lub nowszej.

## <a name="create-voice-signatures-for-participants"></a>Tworzenie podpisów głosowych dla uczestników

Pierwszym krokiem jest utworzenie podpisów głosowych dla uczestników konwersacji. Tworzenie podpisów głosowych jest wymagane do wydajnej identyfikacji głośników.

### <a name="requirements-for-input-wave-file"></a>Wymagania dotyczące wejściowego pliku Wave

* Wejściowy plik Wave audio służący do tworzenia podpisów głosowych powinien znajdować się w 16-bitowych próbkach, szybkości próbkowania 16 kHz i formacie pojedynczego kanału (mono).
* Zalecana długość poszczególnych próbek audio to 30 sekund i dwie minuty.

W poniższym przykładzie przedstawiono dwa różne sposoby tworzenia sygnatury głosowej przy [użyciu interfejsu API REST](https://aka.ms/cts/signaturegenservice) z C#:

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

## <a name="transcribing-conversations"></a>Jego przepisywania konwersacje

Aby transkrypcja konwersacje z wieloma uczestnikami, `ConversationTranscriber` Utwórz obiekt, który jest skojarzony `AudioConfig` z obiektem utworzonym dla sesji konwersacji i strumieniowego `PushAudioInputStream`audio przy użyciu `PullAudioInputStream` lub.

Załóżmy, że masz klasę ConversationTranscriber o nazwie `MyConversationTranscriber`. Twój kod może wyglądać następująco:

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
