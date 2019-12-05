---
title: 'Szybki Start: synteza mowy C# , (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu Speech SDK usługi Cognitive Services. Wykorzystasz mowę z tekstu w czasie rzeczywistym do głośnika Twojego urządzenia. Aplikacja została skompilowana z pakietem NuGet zestawu mowy SDK i Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 276de42f2a37a0e48cd323e285760c00e6db9f32
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818147"
---
> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj implementację z C# kodem.

1. W **Eksplorator rozwiązań**Otwórz `MainPage.xaml`.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do tagu **Grid** (między `<Grid>` i `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. (Jest ono pogrupowane w obszarze `MainPage.xaml`).

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. W programie obsługi `Speak_ButtonClicked` pliku źródłowego Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji.

1. W obsłudze `Speak_ButtonClicked` Znajdź ciąg `YourServiceRegion`i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja syntezy mowy C# platformy UWP w — szybki start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Wprowadź tekst w polu tekstowym, a następnie kliknij przycisk **mów**. Twój tekst jest przesyłany do usługi mowy i można go wyrównać do mowy, który jest odtwarzany na głośniku.

    ![Interfejs użytkownika syntezy mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz także

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Rejestruj niestandardowe przykłady głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
