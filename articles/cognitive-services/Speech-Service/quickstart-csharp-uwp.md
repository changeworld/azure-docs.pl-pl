---
title: 'Szybki start: Rozpoznawanie mowy, C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu Speech SDK usługi Cognitive Services. Transkrypcja mowy na tekst będzie się odbywać w czasie rzeczywistym podczas korzystania z mikrofonu urządzenia. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382264"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Szybki start: Rozpoznawanie mowy w aplikacji platformy UWP przy użyciu zestawu Speech SDK

Przewodniki Szybki Start są również dostępne dla [syntezy mowy](quickstart-text-to-speech-csharp-uwp.md), [tłumaczenia mowy](quickstart-translate-speech-uwp.md)i [wirtualnego asystenta głosowego](quickstart-virtual-assistant-csharp-uwp.md).

Jeśli chcesz, wybierz inny język programowania i/lub środowisko:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

W tym artykule opisano tworzenie aplikacji C# platforma uniwersalna systemu Windows (platformy UWP) przy użyciu [zestawu SDK Cognitive Services Speech](speech-sdk.md). Program przekształca mowę na tekst w czasie rzeczywistym z mikrofonu Twojego urządzenia. Aplikacja jest zbudowana z [pakietem NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019 (w dowolnej wersji).

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksplorator rozwiązań**Otwórz plik `MainPage.xaml.cs`źródłowy związany z kodem. (Jest ono pogrupowane `MainPage.xaml`w.)

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W programie `SpeechRecognitionFromMicrophone_ButtonClicked` obsługi pliku źródłowego Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji.

1. W programie `YourServiceRegion`obsługi Znajdź ciąg i zastąp go regionem skojarzonym z subskrypcją. [](regions.md) `SpeechRecognitionFromMicrophone_ButtonClicked` (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz polecenie **Kompiluj** > **kompilację rozwiązania** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja do C# rozpoznawania mowy platformy UWP — Szybki Start](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz opcję **Speech recognition with microphone input** (Rozpoznawanie mowy z użyciem danych wejściowych z mikrofonu) i wypowiedz zwrot lub zdanie w języku angielskim do mikrofonu urządzenia. Twoja Zamiana jest przekazywana do usługi mowy i uzyskanego do tekstu, który pojawia się w oknie.

   ![Interfejs użytkownika rozpoznawania mowy](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Szybki start: Tłumaczenie mowy na mowę przy użyciu zestawu C# Speech SDK dla (platformy UWP)](quickstart-translate-speech-uwp.md)
- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
