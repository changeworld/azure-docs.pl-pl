---
title: 'Szybki start: synteza mowy, C# (.NET Core) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak syntetyzować mowę w języku C# w obszarze .NET Core w systemie Windows przy użyciu sdk mowy
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yinhew
ms.openlocfilehash: 91e06805b687e66c147b0904175ae20d01387acf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275010"
---
> [!NOTE]
> .NET Core jest międzyplatformową platformą .NET typu open source, która wdraża specyfikację [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=dotnetcore&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Otwórz plik `Program.cs` i zastąp cały jego kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnetcore/text-to-speech/helloworld/Program.cs#code)]

1. W tym samym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. Zastąp także ciąg `YourServiceRegion`[regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz zmiany projektu.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz pozycję **Build** > **Build Solution**. Kod powinien zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Udana kompilacja")

1. Uruchom aplikację. Na pasku menu wybierz polecenie **Debugowanie** > **start debugowania**lub klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Uruchom aplikację do debugowania")

1. Zostanie wyświetlenie okna konsoli z monitem o wpisanie tekstu. Wpisz kilka słów lub zdanie. Wpisany tekst jest przesyłany do usługi Mowa i syntetyzowany do mowy, która jest odtwarzana na głośniku.

    ![Zrzut ekranu przedstawiający wyjście konsoli po udanej syntezie](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Wyjście konsoli po udanej syntezie")

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
