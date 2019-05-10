---
title: 'Szybki start: Rozpoznawanie mowy, C# (.NET Core) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak rozpoznawanie mowy w C# w ramach platformy .NET Core w Windows lub macOS przy użyciu zestawu SDK rozpoznawania mowy
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: f3dd4d20c6455e97b566ed435b14437d614e25b4
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466445"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Szybki start: Rozpoznawanie mowy przy użyciu zestawu Speech SDK dla platformy .NET Core

Przewodniki Szybki Start są również dostępne dla [tłumaczenia mowy](quickstart-translate-speech-dotnetcore-windows.md).

Jeśli to konieczne, należy wybrać inny język programowania i/lub środowiska:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule opisano tworzenie C# konsoli aplikacji dla platformy .NET Core w Windows lub macOS przy użyciu usług Cognitive Services [zestaw SDK rozpoznawania mowy](speech-sdk.md). Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym z mikrofonu komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

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

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Otwórz plik `Program.cs` i zastąp cały jego kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z monitem o wypowiedzenie słów. Wypowiedz zwrot lub zdanie w języku angielskim. Mowa jest przesyłane do usług przetwarzania mowy i przetłumaczone na tekst, który pojawia się w tym samym oknie.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe przykłady, dotyczące m.in. odczytywania mowy z pliku audio, są dostępne w serwisie GitHub.

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
