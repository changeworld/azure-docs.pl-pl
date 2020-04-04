---
title: 'Szybki start: rozpoznawanie mowy z mikrofonu, C# (UWP) — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 3e7cd96b979fb97d7b50b84907881fe59d03e295
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658333"
---
## <a name="prerequisites"></a>Wymagania wstępne

Zanim zaczniesz:

> [!div class="checklist"]
> * [Tworzenie zasobu mowy platformy Azure](../../../../get-started.md)
> * [Konfigurowanie środowiska programistycznego i tworzenie pustego projektu](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania dźwięku

Jeśli już to zrobiłeś, świetnie. Chodźmy dalej.

## <a name="open-your-project-in-visual-studio"></a>Otwieranie projektu w programie Visual Studio

Pierwszym krokiem jest, aby upewnić się, że projekt jest otwarty w programie Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od kodu standardowego

Dodajmy kod, który działa jako szkielet dla naszego projektu.

1. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml`.

2. W widoku XAML projektanta wstaw następujący fragment kodu XAML do `<Grid>` znacznika **Siatka** (między i): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. W **Eksploratorze rozwiązań**otwórz `MainPage.xaml.cs`plik źródłowy związany z kodem . (Jest zgrupowany `MainPage.xaml`w obszarze .)

4. Zastąp kod następującym kodem podstawowym:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem `SpeechRecognizer` obiektu należy utworzyć konfigurację, która używa klucza subskrypcji i regionu subskrypcji. Wstaw ten `RecognizeSpeechAsync()` kod w metodzie.

> [!NOTE]
> W tym przykładzie `FromSubscription()` użyto `SpeechConfig`metody do utworzenia pliku . Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/)[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inicjowanie funkcji SpeechRecognizer

Teraz utwórzmy plik `SpeechRecognizer`. Ten obiekt jest tworzony wewnątrz using instrukcji, aby zapewnić prawidłowe uwalnianie zasobów niezarządzanych. Wstaw ten `RecognizeSpeechAsync()` kod w metodzie, tuż poniżej konfiguracji mowy.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Rozpoznawanie frazy

Z `SpeechRecognizer` obiektu, masz zamiar wywołać `RecognizeOnceAsync()` metodę. Ta metoda pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy.

Wewnątrz using instrukcji, dodaj ten kod.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy wynik rozpoznawania jest zwracany przez usługę mowy, należy coś z nim zrobić. Będziemy to proste i wydrukować wynik na panelu stanu.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

Teraz możesz przystąpić do tworzenia i testowania aplikacji.

1. Na pasku menu wybierz pozycję **Build** > **Build Solution** to build the application. Kod powinien teraz zostać skompilowany bez błędów.

1. Aby uruchomić aplikację, wybierz **opcję Debugowanie** > **rozpocznij debugowanie** (lub naciśnij **klawisz F5).** Zostanie **wyświetlene okno helloworld.**

   ![Przykładowa aplikacja do rozpoznawania mowy platformy uniwersalnej systemu Windows w języku C# — szybki start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Wybierz **włącz mikrofon**, a gdy pojawi się żądanie uprawnień dostępu, wybierz pozycję **Tak**.

   ![Żądanie uprawnień dostępu do mikrofonu](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Wybierz opcję **Speech recognition with microphone input** (Rozpoznawanie mowy z użyciem danych wejściowych z mikrofonu) i wypowiedz zwrot lub zdanie w języku angielskim do mikrofonu urządzenia. Mowa zostanie przesłana do usługi rozpoznawania mowy i transkrybowana na tekst, który zostanie wyświetlony w tym oknie.

   ![Interfejs użytkownika rozpoznawania mowy](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](../footer.md)]
