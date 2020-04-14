---
title: 'Szybki start: synteza mowy w pliku audio, C# (.NET) - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: d3d0a807e8742419ee321f38daebf6f70d6b0be1
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274741"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj `Program.cs`projekt i otwórz .

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu. Należy pamiętać, że utworzono metodę `SynthesisToAudioFileAsync()`asynchronizową o nazwie .

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem `SpeechSynthesizer` obiektu należy utworzyć konfigurację, która używa klucza subskrypcji i regionu subskrypcji. Wstaw ten `SynthesisToAudioFileAsync()` kod w metodzie.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Tworzenie konfiguracji audio

Teraz musisz utworzyć obiekt, ````AudioConfig```` który wskazuje plik audio. Ten obiekt jest tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `SynthesisToAudioFileAsync()` kod w metodzie, tuż poniżej konfiguracji mowy.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inicjowanie programu SpeechSynthesizer

Teraz utwórzmy obiekt `SpeechSynthesizer` przy `SpeechConfig` użyciu `AudioConfig` obiektów i obiektów utworzonych wcześniej. Ten obiekt jest również tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `SynthesisToAudioFileAsync()` kod w metodzie, wewnątrz ````AudioConfig```` using instrukcji, która zawija obiekt.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Syntetyzowanie tekstu za pomocą SpeakTextAsync

Z `SpeechSynthesizer` obiektu, masz zamiar wywołać `SpeakTextAsync()` metodę. Ta metoda wysyła tekst do usługi Mowy, która konwertuje go na dźwięk. Użyje `SpeechSynthesizer` domyślnego głosu, jeśli `config.VoiceName` nie jest jawnie określony.

Wewnątrz using instrukcji, dodaj ten kod:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Sprawdzanie błędów

Po zwrócony wynik syntezy przez usługę mowy, należy sprawdzić, aby upewnić się, że tekst został pomyślnie zsyntetyzowany.

Wewnątrz using instrukcji, `SpeakTextAsync()`poniżej, dodaj ten kod:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Tworzenie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia aplikacji i testowania naszej syntezy mowy za pomocą usługi Mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz pozycję **Build** > **Build Solution**.
2. **Uruchom aplikację** — na pasku menu wybierz pozycję **Debugowanie** > **rozpocznij debugowanie** lub naciśnij **klawisz F5**.
3. **Rozpocznij syntezę** — tekst jest konwertowany na mowę i zapisywany w określonych danych audio.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
