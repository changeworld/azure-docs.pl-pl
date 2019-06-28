---
title: 'Szybki start: Syntetyzowania mowy, C# systemu Windows (UWP) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu Speech SDK usługi Cognitive Services. Możesz syntetyzowania mowę na podstawie tekstu w czasie rzeczywistym do osoby mówiącej na urządzeniu. Aplikacja będzie kompilowana przy użyciu pakietu NuGet zestawu Speech SDK i programu Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: c11fbff883d2699bdd0a107fc462524d92ee410d
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467446"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Szybki start: Syntetyzowania mowy w aplikacji platformy uniwersalnej systemu Windows przy użyciu zestawu SDK rozpoznawania mowy

Przewodniki Szybki Start są również dostępne dla [rozpoznawania mowy](quickstart-csharp-uwp.md), [tłumaczenia mowy](quickstart-translate-speech-uwp.md) i [głosu pierwszego wirtualnego Asystenta](quickstart-virtual-assistant-csharp-uwp.md).

W tym artykule napiszesz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP; system Windows w wersji 1709 lub nowszej) przy użyciu zestawu [Speech SDK](speech-sdk.md) usługi Cognitive Services. Program będzie syntetyzowania mowę na podstawie tekstu w czasie rzeczywistym do osoby mówiącej na urządzeniu. Aplikacja będzie kompilowana przy użyciu [pakietu NuGet zestawu Speech SDK](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017 (w dowolnej wersji).

> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

1. Interfejs użytkownika aplikacji jest definiowany przy użyciu języka XAML. Otwórz `MainPage.xaml` w Eksploratorze rozwiązań. W widoku języka XAML dla projektanta wstaw poniższy fragment kodu XAML do tagu siatki (między `<Grid>` i `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otwórz plik źródłowy code-behind `MainPage.xaml.cs` (znajdź go w grupie w obszarze `MainPage.xaml`). Zastąp cały zawarty w nim kod poniższym kodem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W procedurze obsługi `Speak_ButtonClicked` w tym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. W procedurze obsługi `Speak_ButtonClicked` zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz wszystkie zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-uwp-08-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-uwp-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Wprowadź tekst w polu tekstowym, a następnie kliknij przycisk **Speak**. Tekst jest przesyłane do usług przetwarzania mowy i przekształcony na mowę, na którym odtwarzany na głośników.

    ![Zrzut ekranu przedstawiający interfejs użytkownika synteza mowy](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Dostosowywanie czcionek głosowych](how-to-customize-voice-font.md)
- [Próbki głosu rekord](record-custom-voice-samples.md)
