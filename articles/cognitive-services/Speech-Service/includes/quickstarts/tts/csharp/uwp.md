---
title: 'Szybki start: Synteza mowy, C# (UWP) - Usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule utworzysz aplikację w języku C# dla platformy uniwersalnej systemu Windows (UWP) przy użyciu zestawu Speech SDK usługi Cognitive Services. Syntetyzujesz mowę z tekstu w czasie rzeczywistym do głośnika urządzenia. Aplikacja jest zbudowana z pakietem NuGet zestawu SDK mowy i programem Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 8ca97be2863bd8e45ac7937c49c464fa2f216b11
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925121"
---
> [!NOTE]
> Platforma uniwersalna systemu Windows pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, m.in. na komputerach PC, konsolach Xbox, urządzeniach Surface Hub itp.

## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

Teraz dodaj kod XAML, który definiuje interfejs użytkownika aplikacji i dodaj implementację w języku C# bez kodu.

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml`.

1. W widoku XAML projektanta wstaw następujący fragment kodu XAML do `<Grid>` znacznika **Siatka** (między i): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml.cs`plik źródłowy związany z kodem . (Jest zgrupowany `MainPage.xaml`w obszarze .)

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. W programie obsługi `Speak_ButtonClicked` pliku źródłowego `YourSubscriptionKey`znajdź ciąg i zastąp go kluczem subskrypcji.

1. W `Speak_ButtonClicked` programie obsługi znajdź `YourServiceRegion`ciąg i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. (Na przykład `westus` użyj bezpłatnej subskrypcji próbnej).

1. Na pasku menu wybierz **pozycję Zapisz** > **wszystko,** aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia i testowania aplikacji.

1. Na pasku menu wybierz pozycję **Build** > **Build Solution** to build the application. Kod powinien teraz zostać skompilowany bez błędów.

1. Aby uruchomić aplikację, wybierz **opcję Debugowanie** > **rozpocznij debugowanie** (lub naciśnij **klawisz F5).** Zostanie **wyświetlene okno helloworld.**

   ![Przykładowa aplikacja do syntezy mowy platformy uniwersalnej systemu w języku C# — szybki start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Wprowadź tekst w tokwaku tekstu i kliknij przycisk **Mów**. Tekst jest przesyłany do usługi Mowy i syntetyzowany do mowy, która jest odtwarzana na głośniku.

    ![Interfejs użytkownika syntezy mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zobacz też

- [Tworzenie niestandardowego głosu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Nagrywanie niestandardowych próbek głosu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
