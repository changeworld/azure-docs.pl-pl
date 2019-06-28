---
title: 'Szybki start: Syntetyzowania mowy, C# (.NET Core) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak syntetyzowania mowy w C# w ramach platformy .NET Core w Windows przy użyciu zestawu SDK rozpoznawania mowy
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467425"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Szybki start: Syntetyzowania mowy z zestawem SDK mowy dla platformy .NET Core

Przewodniki Szybki Start są również dostępne dla [rozpoznawania mowy](quickstart-csharp-dotnetcore-windows.md) i [tłumaczenia mowy](quickstart-translate-speech-dotnetcore-windows.md).

W tym artykule utworzysz aplikację konsolową w języku C# dla platformy .NET Core w systemie Windows za pomocą zestawu [Speech SDK](speech-sdk.md) usługi Cognitive Services. Możesz syntetyzowania mowę na podstawie tekstu w czasie rzeczywistym do osoby mówiącej Twojego komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

> [!NOTE]
> .NET Core jest międzyplatformową platformą .NET typu open source, która wdraża specyfikację [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Należy klucz subskrypcji usług przetwarzania mowy, aby ukończyć ten przewodnik Szybki Start. Możesz go uzyskać bezpłatnie. Zobacz [bezpłatnego wypróbowania usługi mowy](get-started.md) Aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Zestaw SDK dla platformy .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp cały jego kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli monit wpisz jakiś tekst. Wpisz kilka słów lub zdanie. Wpisany tekst jest przesyłane do usług przetwarzania mowy i przekształcony na mowę, na którym odtwarzany na głośników.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym syntezy](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "dane wyjściowe konsoli po pomyślnym syntezy")

## <a name="next-steps"></a>Kolejne kroki

Więcej przykładów, takie jak sposób syntetyzowania zamiana mowy na plik audio są dostępne w witrynie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosowywanie czcionek głosowych](how-to-customize-voice-font.md)
- [Próbki głosu rekord](record-custom-voice-samples.md)
