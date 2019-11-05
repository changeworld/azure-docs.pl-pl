---
title: Transkrypcja konwersacji asynchronicznej (wersja zapoznawcza) — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać asynchronicznego transkrypcji konwersacji przy użyciu usługi mowy. Dostępne tylko dla języka Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506979"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Transkrypcja konwersacji asynchronicznej (wersja zapoznawcza)

W tym artykule, transkrypcja konwersacji asynchronicznej jest wykazana przy użyciu interfejsu API **RemoteConversationTranscriptionClient** . Jeśli skonfigurowano transkrypcję konwersacji do wykonywania asynchronicznego transkrypcji i ma `conversationId`, można uzyskać transkrypcję skojarzoną z tym `conversationId` przy użyciu interfejsu API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroniczny a w czasie rzeczywistym + asynchronicznie

Dzięki operacji transkrypcji asynchronicznej można przesyłać strumieniowo konwersacje audio, ale nie musi istnieć transkrypcja zwrócona w czasie rzeczywistym. Zamiast tego, po wysłaniu dźwięku, użyj `conversationId` `ConversationTranscriber`, aby wykonać zapytanie o stan asynchronicznego transkrypcji. Gdy asynchroniczne transkrypcje jest gotowe, uzyskasz `RemoteConversationTranscriptionResult`.

W czasie rzeczywistym i asynchronicznie można uzyskać transkrypcję w czasie rzeczywistym, ale również uzyskać transkrypcję, wykonując zapytania o `conversationId` (podobnie jak w scenariuszu asynchronicznym).

Do wykonania asynchronicznego transkrypcji wymagane są dwa kroki. Pierwszym krokiem jest przekazanie dźwięku, wybranie opcji tylko asynchroniczne lub w czasie rzeczywistym, a asynchronicznie. Drugim krokiem jest uzyskanie wyników transkrypcji.

## <a name="upload-the-audio"></a>Przekaż dźwięk

Przed wykonaniem asynchronicznej transkrypcji należy wysłać dźwięk do transkrypcji konwersacji przy użyciu zestawu SDK programu Microsoft poznawczego klienta (wersja 1.8.0 lub nowsza).

Ten przykładowy kod pokazuje, jak utworzyć konwersację Transcriber w trybie tylko asynchronicznie. Aby przesłać strumieniowo dźwięk do Transcriber, musisz dodać kod strumieniowego audio pochodzący z [konwersacji transkrypcja w czasie rzeczywistym za pomocą zestawu Speech SDK](how-to-use-conversation-transcription-service.md). Zapoznaj się z sekcją **ograniczenia** tego tematu, aby wyświetlić obsługiwane interfejsy API platform i języków.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
...
```

Jeśli chcesz, aby w czasie rzeczywistym _Plus_ asynchroniczny, komentarz i Usuń komentarz z odpowiednich wierszy kodu w następujący sposób:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Pobierz wyniki transkrypcji

Ten krok umożliwia pobranie asynchronicznych wyników transkrypcji, ale założenie, że wymagane jest przetwarzanie w czasie rzeczywistym w innym miejscu. Aby uzyskać więcej informacji, zobacz [transkrypcja konwersacje w czasie rzeczywistym za pomocą zestawu Speech SDK](how-to-use-conversation-transcription-service.md).

W przypadku kodu pokazanego w tym miejscu potrzebna jest **wersja remoteconversation-Client-SDK 1.0.0**obsługiwana tylko przez język Java (1,8 lub nowszy) w systemach Windows, Linux i Android (poziom interfejsu API 26 lub nowszy).

### <a name="obtaining-the-client-sdk"></a>Uzyskiwanie zestawu SDK klienta

Możesz uzyskać **remoteconversation-Client-SDK** , edytując plik pliku pom. XML w następujący sposób.

- Na końcu pliku przed `</project>`tagu zamykającego Utwórz element `repositories` z odwołaniem do repozytorium Maven dla zestawu Speech SDK:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Dodaj również element `dependencies` z remoteconversation-Client-SDK 1.0.0 jako zależność:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Zapisz zmiany

### <a name="sample-transcription-code"></a>Przykładowy kod transkrypcji

Po utworzeniu `conversationId`Utwórz obiekt operacji zdalnej **RemoteConversationTranscriptionOperation** na kliencie, aby wykonać zapytanie o stan asynchronicznego transkrypcji. **RemoteConversationTranscriptionOperation** został rozszerzony z [sondy](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). Po zakończeniu sondowania Pobierz **RemoteConversationTranscriptionResult** przez zasubskrybowanie sondcy i zbadanie obiektu. W tym kodzie po prostu wydrukujesz zawartość wyniku do danych wyjściowych systemu.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami w usłudze GitHub](https://aka.ms/csspeech/samples)
