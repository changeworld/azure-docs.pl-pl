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
ms.openlocfilehash: 89f491aae91787ec6b67b9aa2daee262f3bab62e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275093"
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

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
