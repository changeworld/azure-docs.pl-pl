---
title: 'Szybki start: Tłumaczenie mowy, C# (platformy UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start utworzysz prostą aplikację platformy uniwersalnej systemu Windows (UWP) do przechwytywania mowy użytkownika, tłumaczenia jej na inny język i przekazywania tekstu do wiersza polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemu Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: 813edbea0548a5cac9532750a450de08bd238028
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640018"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Szybki start: Tłumaczenie mowy przy użyciu zestawu SDK usługi Mowa dla platformy uniwersalnej systemu Windows dla języka C# (UWP)

Przewodniki Szybki Start są również dostępne dla Asystenta [mowy na tekst](quickstart-csharp-uwp.md), [tekstu na mowę](quickstart-text-to-speech-csharp-uwp.md) i [głosu](quickstart-virtual-assistant-csharp-uwp.md).

W tym przewodniku Szybki start utworzysz prostą aplikację platformy uniwersalnej systemu Windows (UWP), która przechwytuje mowę użytkownika z mikrofonu komputera, tłumaczy tę mowę i transkrybuje przetłumaczony tekst do wiersza polecenia w czasie rzeczywistym. Ta aplikacja została zaprojektowana tak, aby działała w 64-bitowym systemie Windows i została skompilowana przy użyciu [pakietu NuGet zestawu mowy SDK](https://aka.ms/csspeech/nuget) i Microsoft Visual Studio 2017 lub nowszego.

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

> [!NOTE]
> Platforma UWP pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, w tym na komputerach PC, konsolach Xbox, urządzeniach Surface Hub i innych.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) lub nowszy
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodaj kod przykładowy

1. Interfejs użytkownika aplikacji jest definiowany przy użyciu języka XAML. Otwórz `MainPage.xaml` w Eksploratorze rozwiązań. W widoku języka XAML dla projektanta wstaw poniższy fragment kodu XAML między tagami `<Grid>` i `</Grid>`.

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otwórz plik źródłowy code-behind `MainPage.xaml.cs` (znajdź go w grupie w obszarze `MainPage.xaml`). Zastąp cały zawarty w nim kod poniższym kodem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. W procedurze obsługi `SpeechTranslationFromMicrophone_ButtonClicked` w tym pliku zastąp ciąg `YourSubscriptionKey` kluczem subskrypcji.

1. W procedurze obsługi `SpeechTranslationFromMicrophone_ButtonClicked` zastąp ciąg `YourServiceRegion` [regionem](regions.md) skojarzonym z subskrypcją (na przykład `westus` w przypadku subskrypcji bezpłatnej wersji próbnej).

1. Zapisz wszystkie zmiany w projekcie.

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

1. Skompiluj aplikację. Na pasku menu wybierz kolejno pozycje **Kompiluj** > **Kompiluj rozwiązanie**. Kod powinien teraz zostać skompilowany bez błędów.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Kompiluj rozwiązanie](media/sdk/qs-csharp-uwp-08-build.png "Kompilacja ukończona pomyślnie")

1. Uruchom aplikację. Na pasku menu wybierz kolejno pozycje **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.

    ![Zrzut ekranu aplikacji Visual Studio z wyróżnioną opcją Rozpocznij debugowanie](media/sdk/qs-csharp-uwp-09-start-debugging.png "Uruchamianie aplikacji do debugowania")

1. Zostanie wyświetlone okno podręczne. Wybierz opcję **Włącz mikrofon** i potwierdź żądanie uprawnień, które zostanie wyświetlone.

    ![Zrzut ekranu żądania uprawnień](media/sdk/qs-csharp-uwp-10-access-prompt.png "Uruchom aplikację do debugowania")

1. Wybierz opcję **Speech recognition with microphone input** (Rozpoznawanie mowy z użyciem danych wejściowych z mikrofonu) i wypowiedz zwrot lub zdanie w języku angielskim do mikrofonu urządzenia. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

    ![Zrzut ekranu interfejsu użytkownika rozpoznawania mowy](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przykładami dla języka C# w usłudze GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zobacz także

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
