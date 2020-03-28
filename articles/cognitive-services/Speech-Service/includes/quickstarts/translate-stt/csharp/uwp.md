---
title: 'Szybki start: tłumaczenie mowy na tekst, C# (UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: e84c7423e0f061004a827ac45c211d7db9c556df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924881"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz, upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

Teraz dodaj kod XAML, który definiuje interfejs użytkownika aplikacji i dodaj implementację w języku C# bez kodu.

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml`.

1. W widoku XAML projektanta wstaw następujący fragment kodu XAML do `<Grid>` znacznika **Siatka** (między i): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml.cs`plik źródłowy związany z kodem . (Jest zgrupowany `MainPage.xaml`w obszarze .)

1. Zastąp cały kod w nim z następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. W `SpeechTranslationFromMicrophone_ButtonClicked` programie obsługi w tym `YourSubscriptionKey`pliku znajdź ciąg i zastąp go kluczem subskrypcji.

1. W `SpeechTranslationFromMicrophone_ButtonClicked` programie obsługi znajdź `YourServiceRegion`ciąg i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. (Na przykład `westus` użyj bezpłatnej subskrypcji próbnej).

1. Na pasku menu wybierz **pozycję Zapisz** > **wszystko,** aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia i testowania aplikacji.

1. Na pasku menu **wybierz** > build**build solution,** aby utworzyć aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Aby uruchomić aplikację, wybierz **opcję Debugowanie** > **rozpocznij debugowanie** (lub naciśnij **klawisz F5).** Zostanie **wyświetlene okno helloworld.**

   ![Przykładowa aplikacja do tłumaczenia platformy uniwersalnej systemu w sieciowej w języku C# — szybki start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Wybierz **włącz mikrofon**, a gdy pojawi się żądanie uprawnień dostępu, wybierz pozycję **Tak**.

   ![Żądanie uprawnień dostępu do mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz **opcję Przetłumacz mowę z wejścia mikrofonu**i mów w języku angielskim do mikrofonu urządzenia. Aplikacja przesyła mowę do usługi Mowy, która tłumaczy mowę na tekst w innym języku (w tym przypadku niemieckim). Usługa mowy wysyła przetłumaczony tekst z powrotem do aplikacji, która wyświetla tłumaczenie w oknie.

   ![Interfejs użytkownika tłumaczenia mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
