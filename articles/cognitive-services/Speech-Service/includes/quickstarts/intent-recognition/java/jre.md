---
title: 'Szybki Start: Rozpoznawanie mowy, intencji i jednostek, usługa Java-Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 441c938e9ab74eafecaf0f9188c0884696eccfae
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503990"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Tworzenie aplikacji LUIS i pobieranie klucza punktu końcowego](../../../../quickstarts/create-luis.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Otwórz projekt

Załaduj projekt i Otwórz `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która korzysta z klucza i regionu LUIS Endpoing. Wstaw ten kod w bloku try/catch w obszarze głównym

Ten przykład używa metody `FromSubscription()`, aby skompilować `SpeechConfig`. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

> [!NOTE]
> Ważne jest, aby użyć klucza punktu końcowego LUIS, a nie kluczy początkowych lub Authroing, ponieważ tylko klucz punktu końcowego jest prawidłowy na potrzeby rozpoznawania mowy. Zobacz [Tworzenie aplikacji Luis i Uzyskiwanie klucza punktu końcowego,](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) Aby uzyskać instrukcje dotyczące pobierania poprawnego klucza.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-a-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer`. Wstaw ten kod bezpośrednio poniżej konfiguracji mowy.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Teraz musisz skojarzyć `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z obiektu `IntentRecognizer` nastąpi wywołanie metody `recognizeOnceAsync()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania reconizing mowy.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Poniżej Zadzwoń do `recognizeOnceAsync()`Dodaj następujący kod: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Zasoby wydania

Ważne jest, aby zwolnić zasoby mowy po ich zakończeniu. Wstaw ten kod na końcu bloku try/catch: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Naciśnij klawisz F11 lub wybierz kolejno pozycje **Uruchom** > **Debugowanie**.
Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]