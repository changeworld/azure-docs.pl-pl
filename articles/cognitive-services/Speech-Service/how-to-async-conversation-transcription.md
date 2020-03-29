---
title: Transkrypcja konwersacji asynchroniiowej (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać transkrypcji konwersacji asynchroniiowej za pomocą usługi Mowy. Dostępne tylko dla języka Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384300"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transkrypcja konwersacji asynchroniisty (wersja zapoznawcza)

W tym artykule transkrypcji konwersacji asynchroniiowej jest zademonstrowany przy użyciu interfejsu API **RemoteConversationTranscriptionClient.** Jeśli skonfigurowano transkrypcję konwersacji do transkrypcji asynchroniiowej i mają `conversationId`, `conversationId` można uzyskać transkrypcji skojarzone z tym za pomocą **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroniczne vs. w czasie rzeczywistym + asynchroniczne

Dzięki transkrypcji asynchroniiowej można przesyłać strumieniowo dźwięk konwersacji, ale nie potrzebujesz transkrypcji zwróconej w czasie rzeczywistym. Zamiast tego po wysłaniu dźwięku `conversationId` użyj `Conversation` do kwerendy o stan transkrypcji asynchronicznej. Gdy transkrypcja asynchroniza jest gotowa, otrzymasz . `RemoteConversationTranscriptionResult`

Z w czasie rzeczywistym plus asynchroniczne, można uzyskać transkrypcji w czasie rzeczywistym, `conversationId` ale również uzyskać transkrypcji przez zapytanie z (podobny do scenariusza asynchronicznego).

Dwa kroki są wymagane do wykonania transkrypcji asynchronii. Pierwszym krokiem jest przesłanie dźwięku, wybierając tylko asynchroniczne lub w czasie rzeczywistym plus asynchroniczne. Drugim krokiem jest uzyskanie wyników transkrypcji.

## <a name="upload-the-audio"></a>Prześlij dźwięk

Przed transkrypcji asynchronicznego można wykonać, należy wysłać audio do usługi transkrypcji konwersacji przy użyciu microsoft cognitive speech client SDK (wersja 1.8.0 lub powyżej).

Ten przykładowy kod pokazuje, jak utworzyć transkrypcję konwersacji dla trybu tylko asynchroniza. Aby przesyłać strumieniowo dźwięk do transcribera, należy dodać kod przesyłania strumieniowego audio pochodzący z [transkrypcji konwersacji w czasie rzeczywistym za pomocą SDK mowy](how-to-use-conversation-transcription-service.md). Zapoznaj się z sekcji **Ograniczenia** tego tematu, aby zobaczyć obsługiwane platformy i języki interfejsów API.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Jeśli chcesz w czasie rzeczywistym _plus_ asynchroniczne, komentarz i uncomment odpowiednie wiersze kodu w następujący sposób:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Uzyskaj wyniki transkrypcji

Ten krok pobiera wyniki transkrypcji asynchroniiowej, ale zakłada, że wszelkie przetwarzanie w czasie rzeczywistym może być wymagane odbywa się w innym miejscu. Aby uzyskać więcej informacji, zobacz [Transkrypcja konwersacji w czasie rzeczywistym za pomocą SDK mowy](how-to-use-conversation-transcription-service.md).

W przypadku kodu pokazanego tutaj potrzebujesz **zdalnej konwersacji w wersji 1.8.0,** obsługiwanej tylko dla języka Java (1.8.0 lub nowszy) w systemach Windows, Linux i Android (tylko poziom interfejsu API 26 lub wyższy).

### <a name="obtaining-the-client-sdk"></a>Uzyskiwanie sdk klienta

Można uzyskać **zdalną konwersację,** edytując plik pom.xml w następujący sposób.

1. Na końcu pliku przed tagiem `</project>`zamknięcia należy `repositories` utworzyć element z odwołaniem do repozytorium Maven dla SDK mowy:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Dodaj również `dependencies` element, z remoteconversation-client-sdk 1.8.0 jako zależność:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Zapisywanie zmian

### <a name="sample-transcription-code"></a>Przykładowy kod transkrypcji

Po utworzeniu `conversationId`klienta transkrypcji zdalnej konwersacji **RemoteConversationTranscriptionClient** w aplikacji klienckiej, aby zbadać stan transkrypcji asynchronicznej. Użyj **metody getTranscriptionOperation** w **RemoteConversationTranscriptionClient,** aby uzyskać obiekt [PollerFlux.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) Obiekt PollerFlux będzie miał informacje o stanie zdalnej operacji **RemoteConversationTranscriptionOperation** i końcowym wyniku **RemoteConversationTranscriptionResult**. Po zakończeniu operacji pobierz **remoteconversationTranscriptionResult,** wywołując **getFinalResult** na [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). W tym kodzie po prostu drukujemy zawartość wyniku na wyjściu systemowym.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
