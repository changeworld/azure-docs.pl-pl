---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: af7c7d992db9afa0fb7affa0053bd89257a5a30e
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668364"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli jest to Twój pierwszy projekt w języku Python, użyj tego przewodnika, aby <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md" target="_blank">utworzyć <span class="docon docon-navigate-external x-hidden-focus"> </span>pusty przykładowy projekt </a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">Zainstaluj zestaw Speech SDK dla środowiska <span class="docon docon-navigate-external x-hidden-focus"> </span>deweloperskiego </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Tworzenie aplikacji LUIS na potrzeby rozpoznawania intencji

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Otwórz projekt

1. Otwórz preferowany IDE.
2. Utwórz nowy projekt i Utwórz plik o nazwie `quickstart.py`, a następnie otwórz go.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która używa klucza i lokalizacji dla zasobu przewidywania LUIS.

Wstaw ten kod w `quickstart.py`. Upewnij się, że te wartości są aktualizowane:

* Zastąp `"YourLanguageUnderstandingSubscriptionKey"` kluczem przewidywania LUIS.
* Zastąp `"YourLanguageUnderstandingServiceRegion"` lokalizacją LUIS. Użyj **identyfikatora regionu** z [regionu](https://aka.ms/speech/sdkregion)

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tych wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Ten przykład konstruuje obiekt `SpeechConfig` przy użyciu klucza i regionu LUIS. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Zestaw Speech SDK będzie domyślnie rozpoznawał użycie języka en-us w celu uzyskania informacji na temat wybierania [języka źródłowego.](../../../../how-to-specify-source-language.md)

## <a name="initialize-an-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer`. Wstaw ten kod bezpośrednio poniżej konfiguracji mowy.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Konieczne jest skojarzenie `LanguageUnderstandingModel` z aparatem zamierzania i dodaniem żądanych intencji. Będziemy używać intencji z prekompilowanej domeny dla automatyzacji domowej.

Wstaw ten kod poniżej `IntentRecognizer`. Upewnij się, że `"YourLanguageUnderstandingAppId"` jest zastępowany IDENTYFIKATORem aplikacji LUIS. 

>[!TIP]
> Jeśli potrzebujesz pomocy w znalezieniu tej wartości, zobacz [Tworzenie aplikacji Luis na potrzeby rozpoznawania intencji](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z obiektu `IntentRecognizer` nastąpi wywołanie metody `recognize_once()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy.

Wstaw ten kod pod modelem.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Poniżej Zadzwoń do `recognize_once()`Dodaj ten kod.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco.

> [!NOTE]
> Dodaliśmy Komentarze do tej wersji.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Uruchom próbkę z konsoli programu lub w środowisku IDE:

```
python quickstart.py
```

Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
