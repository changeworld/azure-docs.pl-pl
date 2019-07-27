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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 3c925e0160a4c57f7e43c3463fc1db7098a6e606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553601"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Wyszukiwać mowę przy użyciu zestawu Speech SDK dla .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne do [rozpoznawania mowy](quickstart-csharp-dotnet-windows.md) i [translacji mowy](quickstart-translate-speech-dotnetframework-windows.md).

Ten przewodnik służy do tworzenia aplikacji konsolowej zamiany tekstu na mowę przy użyciu programu .NET Framework dla systemu Windows i zestawu Speech SDK. Po zakończeniu można wypróbować mowę z tekstu i usłyszeć mowę w czasie rzeczywistym.

Aby uzyskać szybką demonstrację (bez kompilowania projektu programu Visual Studio, jak pokazano poniżej):

Pobierz najnowsze [przykłady Cognitive Services mowy SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Dostępny jest głośnik (lub zestaw słuchawkowy).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik `Program.cs` i zastąp automatycznie wygenerowany kod następującym przykładem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź i Zamień ciąg `YourSubscriptionKey` na klucz subskrypcji usługi Speech Services.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Na pasku menu wybierz pozycję **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**, aby uruchomić aplikację.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z monitem o wpisanie tekstu. Wpisz kilka wyrazów lub zdania. Tekst, który wpisano, jest przesyłany do usługi mowy i można go wyrównać do mowy, który jest odtwarzany na głośniku.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosuj czcionki głosowe](how-to-customize-voice-font.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
