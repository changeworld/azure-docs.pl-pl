---
title: Transkrypcja konwersacji w czasie rzeczywistym (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji w czasie rzeczywistym za pomocą zestawu Speech SDK. Dostępne dla C++języków C#, i Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 93f0117096a5601632ccced6b698e84a0714bbd4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805811"
---
# <a name="real-time-conversation-transcription-preview"></a>Transkrypcja konwersacji w czasie rzeczywistym (wersja zapoznawcza)

Interfejs API **ConversationTranscriber** zestawu mowy SDK umożliwia transkrypcja spotkań i innych konwersacji z możliwością dodawania, usuwania i identyfikowania wielu uczestników przez przesyłanie strumieniowe plików audio do usługi mowy przy użyciu `PullStream` lub `PushStream`. Ten temat wymaga, aby dowiedzieć się, jak używać zamiany mowy na tekst za pomocą zestawu Speech SDK (wersja 1.8.0 lub nowsza). Aby uzyskać więcej informacji, zobacz [co to jest usługa Speech Services](overview.md).

## <a name="limitations"></a>Ograniczenia

- Interfejs API ConversationTranscriber jest obsługiwany dla C++systemów C#, i Java w systemach Windows, Linux i Android.
- Obecnie dostępne w językach "en-US" i "zh-CN" w następujących regionach: _środkowe_ i _eastasia_.
- Wymaga cyklicznej macierzy z obsługą wielomikrofonową z użyciem 7 mikrofonów z strumieniem odwołania odtwarzania. Tablica mikrofonów powinna być zgodna z [naszą specyfikacją](https://aka.ms/sdsdk-microphone).
- [Zestaw SDK urządzeń mowy](speech-devices-sdk.md) udostępnia odpowiednie urządzenia i przykładową aplikację pokazującą transkrypcję konwersacji.

## <a name="optional-sample-code-resources"></a>Opcjonalne przykładowe zasoby kodu

Zestaw SDK urządzenia mowy zawiera przykładowy kod w języku Java do przechwytywania audio w czasie rzeczywistym przy użyciu 8 kanałów.

- [Przykładowy kod urządzenia ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Przykładowy kod usługi Azure urządzenia Kinect dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja usługi mowy. Jeśli go nie masz, możesz [uzyskać subskrypcję wersji próbnej](https://azure.microsoft.com/try/cognitive-services/) .

## <a name="create-voice-signatures"></a>Tworzenie podpisów głosowych

Pierwszym krokiem jest utworzenie podpisów głosowych dla uczestników konwersacji na potrzeby efektywnej identyfikacji głośników.

### <a name="audio-input-requirements"></a>Wymagania wejściowe audio

- Wejściowy plik Wave audio służący do tworzenia podpisów głosowych powinien znajdować się w 16-bitowych próbkach, szybkości próbkowania 16 kHz i formacie pojedynczego kanału (mono).
- Zalecana długość dla każdego przykładu audio to 30 sekund i dwie minuty.

### <a name="sample-code"></a>Przykładowy kod

W poniższym przykładzie przedstawiono dwa różne sposoby tworzenia sygnatury głosowej przy [użyciu interfejsu API REST](https://aka.ms/cts/signaturegenservice) w programie C#. Należy pamiętać, że należy zastąpić prawdziwe informacje dla "YourSubscriptionKey", nazwę pliku Wave dla "speakerVoice. wav" oraz swój region dla `{region}` i "YourServiceRegion" (_środkowe_ lub _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
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

## <a name="transcribe-conversations"></a>Transkrypcja konwersacje

Poniższy przykładowy kod demonstruje, jak transkrypcja konwersacje w czasie rzeczywistym dla trzech głośników. Przyjęto założenie, że sygnatury głosowe zostały już utworzone dla każdego głośnika, jak pokazano powyżej. Podstaw rzeczywiste informacje dotyczące "YourSubscriptionKey" i "YourServiceRegion" podczas tworzenia obiektu SpeechConfig.

Przykładowe przykłady kodu obejmują:

- Tworzenie obiektu `Conversation` z obiektu `SpeechConfig` przy użyciu identyfikatora spotkania wygenerowanego przy użyciu `Guid.NewGuid()`
- Tworzenie obiektu `ConversationTranscriber` i dołączanie do konwersacji przy użyciu `JoinConversationAsync()` do rozpoczęcia transkrypcji
- Rejestrowanie interesujących Cię zdarzeń
- Dodawanie lub usuwanie uczestników konwersacji przy użyciu obiektu konwersacja
- Przesyłanie strumieniowe audio

Identyfikator transkrypcji i prelegenta wracają do zarejestrowanych zdarzeń.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = new Conversation(config, meetingId))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
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

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Transkrypcja konwersacji asynchronicznej](how-to-async-conversation-transcription.md)
