---
title: 'Szybki Start: Rozpoznawanie mowy, zamiar i jednostek, C++ usługa mowy'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b141fcdb2aacc8654999d023c4174c7eb9bc177f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761406"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli jest to Twój pierwszy C++ projekt, użyj tego przewodnika, aby <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">utworzyć pusty przykładowy projekt</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska deweloperskiego</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Otwórz projekt w programie Visual Studio

Następnie otwórz projekt w programie Visual Studio.

1. Uruchom program Visual Studio 2019.
2. Załaduj projekt i Otwórz `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu. Należy pamiętać, że utworzono metodę asynchroniczną o nazwie `recognizeIntent()`.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania LUIS.

> [!IMPORTANT]
> Klucz początkowy i klucze tworzenia nie będą działały. Musisz użyć utworzonego wcześniej klucza predykcyjnego i lokalizacji. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

Wstaw ten kod w metodzie `recognizeIntent()`. Upewnij się, że te wartości są aktualizowane:

* Zastąp `"YourLanguageUnderstandingSubscriptionKey"` kluczem przewidywania LUIS.
* Zastąp `"YourLanguageUnderstandingServiceRegion"` lokalizacją LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Ten przykład używa metody `FromSubscription()`, aby skompilować `SpeechConfig`. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer`. Wstaw ten kod w metodzie `recognizeIntent()`, bezpośrednio poniżej konfiguracji mowy.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Należy skojarzyć `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej.

Wstaw ten kod poniżej `IntentRecognizer`. Upewnij się, że `"YourLanguageUnderstandingAppId"` jest zastępowany IDENTYFIKATORem aplikacji LUIS.

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z obiektu `IntentRecognizer` nastąpi wywołanie metody `RecognizeOnceAsync()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy. Dla uproszczenia poczekamy na przyszłe zakończenie działania.

Wstaw ten kod pod modelem:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Wstaw następujący kod poniżej `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco:  

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wszystko jest gotowe do skompilowania aplikacji i przetestowania rozpoznawania mowy przy użyciu usługi mowy.

1. **Skompiluj kod** — na pasku menu programu Visual Studio wybierz polecenie **Build** > **Kompiluj rozwiązanie**.
2. **Uruchom aplikację** — z poziomu paska menu wybierz **Debuguj** > **Rozpocznij debugowanie** lub naciśnij klawisz **F5**.
3. **Rozpocznij rozpoznawanie** — spowoduje to wyświetlenie monitu o rozmowę w języku angielskim. Twoja mowa jest wysyłana do usługi mowy, uzyskanego jako tekst i renderowany w konsoli programu.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
