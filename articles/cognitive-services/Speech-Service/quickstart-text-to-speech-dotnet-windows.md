---
title: 'Szybki start: Syntetyzowania mowy, .NET Framework (Windows) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika, aby utworzyć aplikację konsoli zamiany tekstu na mowę przy użyciu programu .NET framework dla Windows i zestaw SDK rozpoznawania mowy. Po zakończeniu możesz syntetyzowania mowę na podstawie tekstu i posłuchaj mowy na głośników w czasie rzeczywistym.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: 3b7addd369d5e26554dd4300e65bee66b8a62768
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465577"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Szybki start: Syntetyzowania mowy z zestawem SDK mowy dla programu .NET Framework (Windows)

Przewodniki Szybki Start są również dostępne dla [rozpoznawania mowy](quickstart-csharp-dotnet-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-dotnetframework-windows.md).

Użyj tego przewodnika, aby utworzyć aplikację konsoli zamiany tekstu na mowę przy użyciu programu .NET framework dla Windows i zestaw SDK rozpoznawania mowy. Po zakończeniu możesz syntetyzowania mowę na podstawie tekstu i posłuchaj mowy na głośników w czasie rzeczywistym.

Szybkie demonstracyjne (bez kompilowania projektu programu Visual Studio, samodzielnie, jak pokazano poniżej):

Dostęp do najnowszych [mowy Cognitive Services — przykłady zestawu SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z usługi GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten projekt, będą potrzebne następujące elementy:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).
* Osoby mówiącej (lub zestaw nagłowny) dostępne.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik `Program.cs` i zastąp automatycznie wygenerowany kod następującym przykładem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Znajdź i Zastąp ciąg `YourSubscriptionKey` z kluczem subskrypcji usług przetwarzania mowy.

1. Znajdź i zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z Twoją subskrypcją. Jeśli na przykład używasz bezpłatnej wersji próbnej, region to `westus`.

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Na pasku menu wybierz pozycję **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**, aby uruchomić aplikację.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Okno konsoli zostanie wyświetlony monit wpisz jakiś tekst. Wpisz kilka słów lub zdanie. Wpisany tekst jest przesyłane do usług przetwarzania mowy i przekształcony na mowę, na którym odtwarzany na głośników.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosowywanie czcionek głosowych](how-to-customize-voice-font.md)
- [Próbki głosu rekord](record-custom-voice-samples.md)
