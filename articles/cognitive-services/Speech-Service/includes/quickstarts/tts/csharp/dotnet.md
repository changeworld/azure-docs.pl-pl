---
title: 'Szybki start: synteza mowy, C# (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do tworzenia aplikacji konsoli zamiany tekstu na mowę przy użyciu platformy .NET framework dla systemu Windows i SDK mowy. Po zakończeniu można syntetyzować mowę z tekstu i słyszeć mowę na głośniku w czasie rzeczywistym.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: de3a81a66275b4f58ef0427c410046f2694471b1
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671384"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz **Program.cs** i zastąp automatycznie wygenerowany kod tym przykładem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji usługi Mowy.

1. Znajdź ciąg `YourServiceRegion`i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład, jeśli korzystasz z bezpłatnej subskrypcji `westus`próbnej, regionem jest .

1. Na pasku menu wybierz pozycję Zapisz**wszystkie** **pliki** > .

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz pozycję **Build** > **Build Solution** to build the application. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debugowanie** > **rozpocznij debugowanie** (lub wybierz **F5),** aby uruchomić aplikację **helloworld.**

1. Wprowadź angielską frazę lub zdanie. Aplikacja przesyła tekst do usługi Mowy, która wysyła syntetyzowaną mowę do aplikacji w celu odtworzenia na głośniku.

   ![Interfejs użytkownika syntezy mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
