---
title: 'Szybki start: rozpoznawanie mowy w języku C# na platformie .NET Framework w systemie Windows za pomocą zestawu Speech SDK usługi Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Dowiedz się, jak rozpoznawać mowę w języku C# na platformie .NET Framework w systemie Windows za pomocą zestawu Speech SDK usługi Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 32b484451c4ee2264c25cca92b1d03d91b955a29
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054000"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>Szybki start: rozpoznawanie mowy w języku C# na platformie .NET Framework w systemie Windows za pomocą zestawu Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule utworzysz aplikację konsolową języka C# dla platformy .NET Framework w systemie Windows za pomocą [zestawu Speech SDK](speech-sdk.md). Transkrypcja mowy na tekst będzie odbywać się w czasie rzeczywistym z mikrofonu komputera. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia kroków tego przewodnika Szybki start potrzebujesz klucza subskrypcji usługi rozpoznawania mowy. Możesz uzyskać go bezpłatnie. Zobacz [Wypróbuj bezpłatnie usługę rozpoznawania mowy](get-started.md), aby uzyskać szczegółowe informacje.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp cały jego kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji usługi rozpoznawania mowy.

1. Zastąp także ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany projektu.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji programu Visual Studio aplikacji z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-dotnet-windows-08-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji programu Visual Studio aplikacji z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno konsoli z monitem o wypowiedzenie słów. Wymów frazę lub zdanie w języku angielskim. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym samym oknie.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Dane wyjściowe konsoli po pomyślnym ukończeniu rozpoznawania")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Poszukaj tego przykładu w folderze `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Recognize intents from speech by using the Speech SDK for C#](how-to-recognize-intents-from-speech-csharp.md) (Rozpoznawanie intencji z mowy przy użyciu zestawu Speech SDK mowy dla języka C#)

## <a name="see-also"></a>Zobacz też

- [Translate speech](how-to-translate-speech-csharp.md) (Tłumaczenie mowy)
- [Customize acoustic models](how-to-customize-acoustic-models.md) (Dostosowywanie modeli akustycznych)
- [Dostosowywanie modeli językowych](how-to-customize-language-model.md)
