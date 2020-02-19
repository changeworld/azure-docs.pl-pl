---
title: 'Szybki Start: Rozpoznawanie mowy z mikrofonu, C# (.NET) — usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 9c16e2e1726fcbf1e0b55ee0e9808a607b611ea6
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446000"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=dotnet)
> * Upewnij się, że masz dostęp do mikrofonu do przechwytywania audio

## <a name="open-your-project-in-visual-studio"></a>Otwórz projekt w programie Visual Studio

Pierwszym krokiem jest upewnienie się, że projekt jest otwarty w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i Otwórz `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `RecognizeSpeechAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `SpeechRecognizer` należy utworzyć konfigurację, która korzysta z klucza subskrypcji i regionu subskrypcji (wybierz **Identyfikator regionu** z [regionu](https://aka.ms/speech/sdkregion). Wstaw ten kod w metodzie `RecognizeSpeechAsync()`.

> [!NOTE]
> Ten przykład używa metody `FromSubscription()`, aby skompilować `SpeechConfig`. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-a-speechrecognizer"></a>Inicjowanie elementu SpeechRecognizer

Teraz Utwórzmy `SpeechRecognizer`. Ten obiekt jest tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w metodzie `RecognizeSpeechAsync()`, bezpośrednio poniżej konfiguracji mowy.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Rozpoznawanie frazy

Z obiektu `SpeechRecognizer` nastąpi wywołanie metody `RecognizeOnceAsync()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wewnątrz instrukcji using Dodaj następujący kod: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Wewnątrz instrukcji using, poniżej `RecognizeOnceAsync()`, Dodaj następujący kod: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Build** > **Kompiluj rozwiązanie**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
