---
title: 'Szybki Start: synteza mowy w pliku audio C# (.NET) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 17e5f35e83f5cb6e8115a9d344f93abf84e5b38a
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156065"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Otwórz projekt w programie Visual Studio

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i Otwórz `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `SynthesisToAudioFileAsync()`.

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

Przed zainicjowaniem obiektu `SpeechSynthesizer` należy utworzyć konfigurację korzystającą z klucza subskrypcji i regionu subskrypcji. Wstaw ten kod w metodzie `SynthesisToAudioFileAsync()`.

````C#
// Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
// from here: https://aka.ms/speech/sdkregion).
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Tworzenie konfiguracji audio

Teraz musisz utworzyć obiekt ````AudioConfig````, który wskazuje na plik audio. Ten obiekt jest tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w metodzie `SynthesisToAudioFileAsync()`, bezpośrednio poniżej konfiguracji mowy.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inicjowanie elementu SpeechSynthesizer

Teraz Utwórzmy obiekt `SpeechSynthesizer` przy użyciu utworzonych wcześniej obiektów `SpeechConfig` i `AudioConfig`. Ten obiekt jest również tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w metodzie `SynthesisToAudioFileAsync()`, wewnątrz instrukcji using, która otacza obiekt ````AudioConfig````.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Synteza tekstu przy użyciu SpeakTextAsync

Z obiektu `SpeechSynthesizer` nastąpi wywołanie metody `SpeakTextAsync()`. Ta metoda wysyła tekst do usługi mowy, która konwertuje ją na dźwięk. `SpeechSynthesizer` będzie używać domyślnego głosu, jeśli `config.VoiceName` nie został jawnie określony.

Wewnątrz instrukcji using Dodaj następujący kod:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Sprawdź pod kątem błędów

Gdy wynik syntezy jest zwracany przez usługę mowy, należy upewnić się, że tekst został pomyślnie wyszukany.

Wewnątrz instrukcji using poniżej `SpeakTextAsync()`Dodaj następujący kod:
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

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania syntezy mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Build** > **Kompiluj rozwiązanie**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.
3. **Rozpocznij syntezę** — tekst jest konwertowany na mowę i zapisywany w określonych danych audio.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
