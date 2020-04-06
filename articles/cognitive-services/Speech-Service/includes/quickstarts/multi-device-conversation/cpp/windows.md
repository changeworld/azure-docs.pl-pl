---
services: cognitive-services
author: ralphe
manager: cpoulain
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: ralphe
ms.openlocfilehash: 015986ffa687142cf20ca18cff175ac6c5952a11
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671295"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. W programie Visual Studio otwórz plik źródłowy **helloworld.cpp**.

1. Zastąp cały kod następującym fragmentem kodu:

    ```cpp
    #include "pch.h"
    
    #define WIN32_LEAN_AND_MEAN
    #include <Windows.h>
    
    #include <iostream>
    #include <thread>
    #include <future>
    #include <string>
    #include <speechapi_cxx.h>
    
    using namespace std::chrono_literals;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Audio;
    using namespace Microsoft::CognitiveServices::Speech::Transcription;
    
    // Create a promise we will set when the user presses Ctrl-C
    std::promise<bool> promise;
    // Create the future we will use to wait for the promise to be fulfilled
    std::future<bool> future = promise.get_future();
    
    void StartNewConversation()
    {
        // Set these
        std::string subscriptionKey("YourSubscriptionKey");

        // Replace below with your own region identifier from here: https://aka.ms/speech/sdkregion
        std::string region("YourServiceRegion");
        
        std::string speechLanguage("en-US");
    
        // Create the conversation object you'll need to manage the conversation
        auto speechConfig = SpeechConfig::FromSubscription(subscriptionKey, region);
        speechConfig->SetSpeechRecognitionLanguage(speechLanguage);
        auto conversation = Conversation::CreateConversationAsync(speechConfig).get();
    
        // Start the conversation so you and others can join
        conversation->StartConversationAsync().get();
    
        // Get the conversation ID. It will be up to your scenario to determine how this is shared with other participants.
        std::cout << "Created conversation: " << conversation->GetConversationId() << std::endl;
    
        // You can now call various commands to manage the room. For example:
        conversation->MuteAllParticipantsAsync().get();
    
        // Create the conversation translator you'll need to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Add any event handlers
        conversationTranslator->SessionStarted += [](const SessionEventArgs& args)
        {
            std::cout << "Session started " << args.SessionId << std::endl;
        };
        conversationTranslator->SessionStopped += [](const SessionEventArgs& args)
        {
            std::cout << "Session stopped " << args.SessionId << std::endl;
        };
        conversationTranslator->Canceled += [](const ConversationTranslationCanceledEventArgs& args)
        {
            switch (args.Reason)
            {
                case CancellationReason::EndOfStream:
                    std::cout << "End of audio reached" << std::endl;
                    break;
    
                case CancellationReason::Error:
                    std::cout << "Canceled due to error. " << (long)args.ErrorCode << ": " << args.ErrorDetails << std::endl;
                    break;
            }
        };
        conversationTranslator->ConversationExpiration += [](const ConversationExpirationEventArgs& args)
        {
            std::cout << "Conversation will expire in " << args.ExpirationTime.count() << " minutes" << std::endl;
        };
        conversationTranslator->ParticipantsChanged += [](const ConversationParticipantsChangedEventArgs& args)
        {
            std::cout << "The following participant(s) have ";
            switch (args.Reason)
            {
                case ParticipantChangedReason::JoinedConversation:
                    std::cout << "joined";
                    break;
    
                case ParticipantChangedReason::LeftConversation:
                    std::cout << "left";
                    break;
    
                case ParticipantChangedReason::Updated:
                    std::cout << "been updated";
                    break;
            }
    
            std::cout << ":" << std::endl;
    
            for(std::shared_ptr<Participant> participant : args.Participants)
            {
                std::cout << "\t" << participant->DisplayName << std::endl;
            }
        };
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation so you can start receiving events
        conversationTranslator->JoinConversationAsync(conversation, "Test Host").get();
    
        // Send an instant message
        conversationTranslator->SendTextMessageAsync("This is a short test message").get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Leave the conversation. You will stop receiving events
        conversationTranslator->LeaveConversationAsync().get();
    
        // Once you are done, remember to delete the conversation.
        conversation->EndConversationAsync().get(); // You will not be able to rejoin after this
        conversation->DeleteConversationAsync().get(); // All participants still in the room will be ejected
    }
    
    int main()
    {
        // Register a handler for the Ctrl - C callback
        SetConsoleCtrlHandler(
            [](DWORD dwCtrlType) -> BOOL
            {
                if (dwCtrlType == CTRL_C_EVENT)
                {
                    // Signal that the user has pressed ctrl + C
                    promise.set_value(true);
                    return TRUE;
                }
    
                return FALSE;
            },
            TRUE);
    
        StartNewConversation();
    }
    ```

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz pozycję Zapisz**wszystkie** **pliki** > .

## <a name="build-and-run-the-application-to-create-a-new-conversation"></a>Tworzenie i uruchamianie aplikacji w celu utworzenia nowej konwersacji

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz <kbd>F5),</kbd>aby uruchomić aplikację **helloworld.**

1. Gdy pojawi `Started transcribing` się komunikat, możesz zacząć mówić. Zobaczysz transkrypcje pojawiają się podczas mówienia
    - Jeśli udostępnisz kod konwersacji innym osobom i dołączą do konwersacji, zobaczysz również ich transkrypcje.

1. Po zakończeniu mówienia naciśnij <kbd>klawisze Ctrl+C</kbd> na klawiaturze, aby zatrzymać przechwytywanie dźwięku.

    > [!NOTE]
    > Może zostać wyświetlony komunikat z programu Visual `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` Studio o wyjątku podobne do: Można bezpiecznie zignorować to.
    > <br/> <br/>
    > Naciśnij klawisz <kbd>F5</kbd>, aby kontynuować.

## <a name="build-and-run-the-application-to-join-an-existing-conversation"></a>Tworzenie i uruchamianie aplikacji w celu dołączenia do istniejącej konwersacji

1. Skopiuj i wklej następującą funkcję do `int main()` **helloworld.cpp** tuż przed funkcją:

    ```cpp
    void JoinExistingConversation(const std::string& conversationId)
    {
        std::string speechLanguage("en-US");
    
        // You'll now need to create a ConversationTranslator to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Attach event handlers here. For example:
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation
        conversationTranslator->JoinConversationAsync(conversationId, "participant", speechLanguage).get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Once you are done, leave the conversation
        conversationTranslator->LeaveConversationAsync().get();
    }
    ```

2. Zastąp `StartNewConversation();` w funkcji `int main()` na:

    ```cpp
    // Set this to the conversation you want to join
    JoinExistingConversation("YourConversationId");
    ```

[!INCLUDE [create-from-web](../create-from-web.md)]

4. Wróć do programu Visual `YourConversationId` Studio `int main()` i zastąp w funkcji identyfikator konwersacji z poprzedniego kroku.

5. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien zostać skompilowany bez błędów.

6. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub naciśnij klawisz <kbd>F5),</kbd>aby uruchomić aplikację **helloworld.**

7. Gdy pojawi `Started transcribing` się komunikat, możesz zacząć mówić. Zobaczysz transkrypcje pojawiają się podczas mówienia.
    - Jeśli wrócisz do przeglądarki, powinieneś zobaczyć, jak pojawiają się tam twoje transkrypcje, gdy mówisz.

8.  Po zakończeniu mówienia naciśnij <kbd>klawisze Ctrl+C,</kbd> aby zatrzymać przechwytywanie dźwięku, i zakończ konwersację.

    > [!NOTE]
    > Może zostać wyświetlony komunikat z programu Visual `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` Studio o wyjątku podobne do: Można bezpiecznie zignorować to.
    > <br/> <br/>
    > Naciśnij klawisz <kbd>F5</kbd>, aby kontynuować.

9. Wróć do przeglądarki i zakończ rozmowę za pomocą przycisku wyjścia w prawym górnym rogu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
