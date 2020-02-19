---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 7faf4a029168c12a3213bc9a06891200e9ac6142
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445539"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli jest to Twój pierwszy C# projekt, użyj tego przewodnika, aby <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=dotnet" target="_blank">utworzyć pusty przykładowy <span class="docon docon-navigate-external x-hidden-focus"> </span>projekt </a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska <span class="docon docon-navigate-external x-hidden-focus"> </span>deweloperskiego </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwórz projekt w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i Otwórz `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `RecognizeIntentAsync()`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania LUIS.

> [!IMPORTANT]
> Klucz początkowy i klucze tworzenia nie będą działały. Musisz użyć utworzonego wcześniej klucza predykcyjnego i lokalizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten kod w metodzie `RecognizeIntentAsync()`. Upewnij się, że te wartości są aktualizowane:

* Zastąp `"YourLanguageUnderstandingSubscriptionKey"` kluczem przewidywania LUIS.
* Zastąp `"YourLanguageUnderstandingServiceRegion"` lokalizacją LUIS. Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion).

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

Ten przykład używa metody `FromSubscription()`, aby skompilować `SpeechConfig`. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer`. Ten obiekt jest tworzony wewnątrz instrukcji using, aby zapewnić odpowiednią wersję niezarządzanych zasobów. Wstaw ten kod w metodzie `RecognizeIntentAsync()`, bezpośrednio poniżej konfiguracji mowy.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Należy skojarzyć `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej. Wstaw ten kod w instrukcji using z poprzedniej sekcji. Upewnij się, że `"YourLanguageUnderstandingAppId"` jest zastępowany IDENTYFIKATORem aplikacji LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z obiektu `IntentRecognizer` nastąpi wywołanie metody `RecognizeOnceAsync()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wewnątrz instrukcji using Dodaj następujący kod pod modelem: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Wyświetl wyniki (lub błędy) rozpoznawania

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie to proste i będzie można drukować wyniki w konsoli.

Wewnątrz instrukcji using poniżej `RecognizeOnceAsync()`Dodaj następujący kod:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Build** > **Kompiluj rozwiązanie**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz <kbd>F5</kbd>.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
