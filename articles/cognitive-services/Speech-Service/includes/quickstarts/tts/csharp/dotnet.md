---
title: 'Szybki Start: synteza mowy C# , (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do tworzenia aplikacji konsolowej zamiany tekstu na mowę przy użyciu programu .NET Framework dla systemu Windows i zestawu Speech SDK. Po zakończeniu można wypróbować mowę z tekstu i usłyszeć mowę w czasie rzeczywistym.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 38dac73d253245fce86ac419c7a4eb3a106df3f0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818322"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz **program.cs** i Zastąp automatycznie wygenerowany kod tym przykładem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji usługi rozpoznawania mowy.

1. Znajdź ciąg `YourServiceRegion`i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. Na przykład jeśli używasz bezpłatnej subskrypcji wersji próbnej, region jest `westus`.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wprowadź frazę lub zdanie w języku angielskim. Aplikacja przesyła swój tekst do usługi mowy, która wysyła do aplikacji aplikację, która ma być odtwarzana na głos.

   ![Interfejs użytkownika syntezy mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
