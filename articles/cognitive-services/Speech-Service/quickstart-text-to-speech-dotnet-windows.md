---
title: 'Szybki start: Synteza mowy, .NET Framework (Windows) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik służy do tworzenia aplikacji konsolowej zamiany tekstu na mowę przy użyciu programu .NET Framework dla systemu Windows i zestawu Speech SDK. Po zakończeniu można wypróbować mowę z tekstu i usłyszeć mowę w czasie rzeczywistym.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327446"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Wyszukiwać mowę przy użyciu zestawu Speech SDK dla .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-csharp-dotnet-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-dotnetframework-windows.md).

Ten przewodnik służy do tworzenia aplikacji konsolowej zamiany tekstu na mowę przy użyciu programu .NET Framework dla systemu Windows i zestawu Speech SDK. Po zakończeniu można wypróbować mowę z tekstu i usłyszeć mowę w czasie rzeczywistym.

Aby uzyskać szybką demonstrację (bez kompilowania projektu programu Visual Studio zgodnie z opisem w tym artykule), Pobierz najnowsze [przykłady zestawu SDK mowy Cognitive Services](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Dostępny jest głośnik (lub zestaw słuchawkowy).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz **program.cs** i Zastąp automatycznie wygenerowany kod tym przykładem:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź ciąg `YourSubscriptionKey` i zastąp go kluczem subskrypcji usługi Speech Services.

1. Znajdź ciąg `YourServiceRegion` i zastąp go [regionem](regions.md) skojarzonym z subskrypcją. Na przykład jeśli używasz bezpłatnej subskrypcji wersji próbnej, region jest `westus`.

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko**.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub wybierz **F5**), aby uruchomić aplikację **HelloWorld** .

1. Wprowadź frazę lub zdanie w języku angielskim. Aplikacja przesyła swój tekst do usług mowy, które wysyłają do aplikacji aplikację, która ma zostać przegrana przez prelegenta.

   ![Interfejs użytkownika syntezy mowy](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](record-custom-voice-samples.md)
