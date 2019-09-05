---
title: 'Szybki start: Synteza mowy, C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu Speech SDK usługi Cognitive Services. Wykorzystasz mowę z tekstu w czasie rzeczywistym do głośnika Twojego urządzenia. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 65b65c9af377b6a9951f9f328e0732850d3b9c1d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382206"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Szybki start: Wykorzystaj mowę w aplikacji platformy UWP przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-csharp-uwp.md), [tłumaczenia mowy](quickstart-translate-speech-uwp.md)i [wirtualnego asystenta głosowego](quickstart-virtual-assistant-csharp-uwp.md).

W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) przy użyciu [zestawu SDK Cognitive Services Speech](speech-sdk.md). Program umożliwia przemówienie zamiany mowy z tekstu w czasie rzeczywistym na prelegenta Twojego urządzenia. Aplikację można skompilować przy użyciu [pakietu NuGet zestawu mowy SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj implementację z C# kodem.

1. W **Eksplorator rozwiązań**Otwórz `MainPage.xaml`program.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do tagu **Grid** (między `<Grid>` i `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksplorator rozwiązań**Otwórz plik `MainPage.xaml.cs`źródłowy związany z kodem. (Jest ono pogrupowane `MainPage.xaml`w.)

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W programie `Speak_ButtonClicked` obsługi pliku źródłowego Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji.

1. W programie `YourServiceRegion`obsługi Znajdź ciąg i zastąp go regionem skojarzonym z subskrypcją. [](regions.md) `Speak_ButtonClicked` (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz polecenie **Kompiluj** > **kompilację rozwiązania** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja syntezy mowy C# platformy UWP w — szybki start](media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Wprowadź tekst w polu tekstowym, a następnie kliknij przycisk **mów**. Twój tekst jest przesyłany do usługi Speech Services i jest on wydawany na mowę, który jest odtwarzany na głośniku.

    ![Interfejs użytkownika syntezy mowy](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Tworzenie i używanie niestandardowych modeli głosu](how-to-custom-voice-create-voice.md)
- [Rejestruj przykłady głosu](record-custom-voice-samples.md)
