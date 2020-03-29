---
title: Transkrypcja konwersacji w czasie rzeczywistym (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji w czasie rzeczywistym za pomocą SDK mowy. Dostępne dla języka C++, C#i Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263731"
---
# <a name="real-time-conversation-transcription-preview"></a>Transkrypcja konwersacji w czasie rzeczywistym (wersja zapoznawcza)

Interfejs API **konwersacji konwersatora** SDK umożliwia transkrypcję spotkań i innych konwersacji z możliwością dodawania, `PullStream` usuwania i identyfikowania wielu uczestników przez przesyłanie strumieniowe dźwięku do usługi Mowy za pomocą lub `PushStream`. W tym temacie należy wiedzieć, jak używać zamiany mowy na tekst przy użyciu sdk mowy (wersja 1.8.0 lub nowsza). Aby uzyskać więcej informacji, zobacz [Co to są usługi mowy](overview.md).

## <a name="limitations"></a>Ograniczenia

- Interfejs API konwersatora jest obsługiwany dla języka C++, C#i Java w systemach Windows, Linux i Android.
- Obecnie dostępne w językach "en-US" i "zh-CN" w następujących regionach: _centralus_ i _eastasia_.
- Wymaga 7-mikrofonowej tablicy wielomiażdżycowej z strumieniem referencyjnym odtwarzania. Zestaw mikrofonów powinien spełniać [naszą specyfikację.](https://aka.ms/sdsdk-microphone)
- Zestaw [SDK urządzeń mowy](speech-devices-sdk.md) udostępnia odpowiednie urządzenia i przykładową aplikację demonstrującą transkrypcję konwersacji.

## <a name="optional-sample-code-resources"></a>Opcjonalne przykładowe zasoby kodu

Zestaw SDK urządzenia mowy udostępnia przykładowy kod w języku Java do przechwytywania dźwięku w czasie rzeczywistym przy użyciu 8 kanałów.

- [Kod przykładowego urządzenia ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Przykładowy kod zestawu Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Wymagania wstępne

Subskrypcja usługi mowy. Jeśli jej nie masz, możesz [uzyskać subskrypcję wersji próbnej mowy.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures"></a>Tworzenie podpisów głosowych

Pierwszym krokiem jest stworzenie podpisów głosowych dla uczestników konwersacji w celu sprawnego identyfikacji prelegentów.

### <a name="audio-input-requirements"></a>Wymagania dotyczące wejścia audio

- Wejściowy plik fali audio do tworzenia podpisów głosowych powinien znajdować się w próbkach 16-bitowych, częstotliwości próbkowania 16 kHz i formacie jednokanałowym (mono).
- Zalecana długość dla każdej próbki audio wynosi od trzydziestu sekund do dwóch minut.

### <a name="sample-code"></a>Przykładowy kod

W poniższym przykładzie przedstawiono dwa różne sposoby tworzenia [podpisu głosowego przy użyciu interfejsu API REST](https://aka.ms/cts/signaturegenservice) w języku C#. Należy pamiętać, że musisz zastąpić prawdziwe informacje dla "YourSubscriptionKey", nazwa pliku wave dla "speakerVoice.wav", a twój region `{region}` i "YourServiceRegion"_(centralus_ lub _eastasia_).

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
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
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
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Transkrypcja konwersacji

Poniższy przykładowy kod pokazuje, jak transkrypcji konwersacji w czasie rzeczywistym dla trzech głośników. Zakłada się, że dla każdego głośnika zostały już utworzone podpisy głosowe, jak pokazano powyżej. Zastąp prawdziwe informacje dla "YourSubscriptionKey" i "YourServiceRegion" podczas tworzenia SpeechConfig obiektu.

Przykładowe najważniejsze informacje o kodzie obejmują:

- Tworzenie `Conversation` obiektu z `SpeechConfig` obiektu przy użyciu identyfikatora spotkania wygenerowanego przy użyciu`Guid.NewGuid()`
- Tworzenie `ConversationTranscriber` obiektu i dołączenie `JoinConversationAsync()` do konwersacji, aby rozpocząć transkrypcję
- Rejestrowanie interesujących wydarzeń
- Dodawanie lub usuwanie uczestników do konwersacji przy użyciu obiektu Konwersacja
- Przesyłanie strumieniowe dźwięku
- W polu wersji sygnatury głosowej w wersji `int` 1.9.0 i 1.9.0 i później obsługiwane są zarówno typy `string` i typy wartości.

Transkrypcja i identyfikator prelegenta powracają w zarejestrowanych zdarzeniach.

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
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
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
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

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
> [Transkrypcja konwersacji asynchroniisty](how-to-async-conversation-transcription.md)
