---
title: 'Szybki start: Tłumaczenie mowy, C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację platforma uniwersalna systemu Windows (platformy UWP), aby przechwycić mowę użytkownika, przetłumaczyć ją na inny język i wyprowadził tekst w wierszu polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemu Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382649"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Szybki start: Tłumaczenie mowy przy użyciu zestawu SDK usługi Mowa dla platformy uniwersalnej systemu Windows dla języka C# (UWP)

Przewodniki Szybki Start są również dostępne dla [funkcji rozpoznawania mowy](quickstart-csharp-uwp.md), [syntezy mowy](quickstart-text-to-speech-csharp-uwp.md)i [wirtualnego asystenta głosowego](quickstart-virtual-assistant-csharp-uwp.md).

W tym przewodniku szybki start utworzysz aplikację platforma uniwersalna systemu Windows (platformy UWP), która będzie przechwycić mowę użytkownika z mikrofonu komputera, przetłumaczy mowę i przekształca przetłumaczony tekst na wiersz polecenia w czasie rzeczywistym. Ta aplikacja została zaprojektowana tak, aby działała w 64-bitowym systemie Windows i została skompilowana przy użyciu [pakietu NuGet zestawu mowy SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2019.

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

> [!NOTE]
> Platforma UWP pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, w tym na komputerach PC, konsolach Xbox, urządzeniach Surface Hub i innych.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. W **Eksplorator rozwiązań**Otwórz plik `MainPage.xaml.cs`źródłowy związany z kodem. (Jest ono pogrupowane `MainPage.xaml`w.)

1. Zastąp cały kod w nim następującym fragmentem kodu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W obszarze `YourSubscriptionKey`obsługi w tym pliku Znajdź ciąg i zastąp go kluczem subskrypcji. `SpeechTranslationFromMicrophone_ButtonClicked`

1. W programie `YourServiceRegion`obsługi Znajdź ciąg i zastąp go regionem skojarzonym z subskrypcją. [](regions.md) `SpeechTranslationFromMicrophone_ButtonClicked` (Na przykład użyj `westus` subskrypcji bezpłatnej wersji próbnej).

1. Na pasku menu wybierz kolejno opcje **plik** > **Zapisz wszystko** , aby zapisać zmiany.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do kompilowania i testowania aplikacji.

1. Na pasku menu wybierz kolejno opcje **Kompiluj** > kompilacje**rozwiązanie** , aby skompilować aplikację. Kod powinien teraz zostać skompilowany bez błędów.

1. Wybierz **Debuguj** > **Rozpocznij debugowanie** (lub naciśnij klawisz **F5**), aby uruchomić aplikację. Zostanie wyświetlone okno **HelloWorld** .

   ![Przykładowa aplikacja tłumaczenia platformy UWP C# w — szybki start](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Wybierz pozycję **Włącz mikrofon**i po włączeniu żądania uprawnienia dostępu wybierz pozycję **tak**.

   ![Żądanie uprawnienia dostępu do mikrofonu](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz pozycję **Przekształć mowę z wejściowego mikrofonu**i zacznij od angielskiej frazy lub zdanie do mikrofonu Twojego urządzenia. Aplikacja przesyła mowę do usługi mowy, która tłumaczy mowę na tekst w innym języku (w tym przypadku w języku niemieckim). Usługa mowy wysyła przetłumaczony tekst z powrotem do aplikacji, co spowoduje wyświetlenie tłumaczenia w oknie.

   ![Interfejs użytkownika tłumaczenia mowy](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Uczenie modelu dla Custom Speech](how-to-custom-speech-train-model.md)
