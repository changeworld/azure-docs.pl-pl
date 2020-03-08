---
title: 'Szybki Start: Tłumaczenie mowy na mowę, C# (platformy UWP) — usługa mowy'
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
ms.openlocfilehash: a0f62db319d54c2db71a86f621985a304dbbb4d2
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925171"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Skonfiguruj środowisko deweloperskie i Utwórz pusty projekt](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

Teraz Dodaj kod XAML, który definiuje interfejs użytkownika aplikacji, a następnie Dodaj implementację z C# kodem.

1. W **Eksplorator rozwiązań**Otwórz `MainPage.xaml`.

1. W widoku XAML projektanta Wstaw następujący fragment kodu XAML do tagu **Grid** (między `<Grid>` i `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksplorator rozwiązań**Otwórz plik źródłowy związany z kodem `MainPage.xaml.cs`. (Jest ono pogrupowane w obszarze `MainPage.xaml`).

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. W obszarze obsługi `SpeechTranslationFromMicrophone_ButtonClicked` w tym pliku Znajdź ciąg `YourSubscriptionKey`i zastąp go kluczem subskrypcji.

1. W obsłudze `SpeechTranslationFromMicrophone_ButtonClicked` Znajdź ciąg `YourServiceRegion`i zastąp go [regionem](~/articles/cognitive-services/Speech-Service/regions.md) skojarzonym z subskrypcją. (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz kolejno opcje **kompiluj** > **Kompiluj rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja tłumaczenia platformy UWP C# w — szybki start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz pozycję **Przekształć mowę z wejściowego mikrofonu**i zacznij od angielskiej frazy lub zdanie do mikrofonu Twojego urządzenia. Aplikacja przesyła mowę do usługi mowy, która tłumaczy mowę na tekst w innym języku (w tym przypadku w języku niemieckim). Usługa mowy wysyła przetłumaczony tekst z powrotem do aplikacji, co spowoduje wyświetlenie tłumaczenia w oknie.

   ![Interfejs użytkownika tłumaczenia mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
