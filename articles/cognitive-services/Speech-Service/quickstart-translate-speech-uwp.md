---
title: 'Szybki start: tłumaczenie mowy, C# (UWP) — usługi mowy'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start utworzysz prostą aplikację platformy uniwersalnej systemu Windows (UWP) do przechwytywania mowy użytkownika, tłumaczenia jej na inny język i przekazywania tekstu do wiersza polecenia. Ten przewodnik jest przeznaczony dla użytkowników systemu Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: bb0296098d0717e95e9aa1d73229d59709d13766
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106090"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Szybki start: Tłumaczenie mowy przy użyciu zestawu SDK usługi Mowa dla platformy uniwersalnej systemu Windows dla języka C# (UWP)

W tym przewodniku Szybki start utworzysz prostą aplikację platformy uniwersalnej systemu Windows (UWP), która przechwytuje mowę użytkownika z mikrofonu komputera, tłumaczy tę mowę i transkrybuje przetłumaczony tekst do wiersza polecenia w czasie rzeczywistym. Ta aplikacja jest przeznaczona do uruchamiania w 64-bitowych systemach Windows i jest kompilowana przy użyciu [pakietu NuGet zestawu SDK usługi Mowa](https://aka.ms/csspeech/nuget) i programu Microsoft Visual Studio 2017.

Aby uzyskać pełną listę języków dostępnych na potrzeby tłumaczenia mowy, zapoznaj się z [listą obsługiwanych języków](language-support.md).

> [!NOTE]
> Platforma UWP pozwala tworzyć aplikacje, które działają na dowolnym urządzeniu z systemem Windows 10, w tym na komputerach PC, konsolach Xbox, urządzeniach Surface Hub i innych.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start wymaga następujących elementów:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klucz subskrypcji platformy Azure dla usługi Mowa. [Uzyskaj bezpłatnie](get-started.md).

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Dodawanie przykładowego kodu

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

## <a name="see-also"></a>Zobacz też

- [Samouczek: tworzenie niestandardowego modelu akustycznego](how-to-customize-acoustic-models.md)
- [Samouczek: tworzenie niestandardowego modelu językowego](how-to-customize-language-model.md)
