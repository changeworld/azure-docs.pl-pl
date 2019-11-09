---
title: 'Szybki Start: Rozpoznawanie mowy, intencji i jednostek, usługa Python-Speech'
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
ms.openlocfilehash: 9a226c17dd72d0dcd6403277054cf264f0094e65
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73850446"
---
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

> [!div class="checklist"]
> * [Tworzenie zasobu usługi Azure Speech](../../../../get-started.md)
> * [Tworzenie aplikacji LUIS i pobieranie klucza punktu końcowego](../../../../quickstarts/create-luis.md)
> * [Konfigurowanie środowiska deweloperskiego](../../../../quickstarts/setup-platform.md)
> * [Tworzenie pustego przykładowego projektu](../../../../quickstarts/create-project.md)

## <a name="open-your-project"></a>Otwórz projekt

Otwórz Quickstart.py w edytorze języka Python.

## <a name="start-with-some-boilerplate-code"></a>Zacznij od pewnego kodu standardowego

Dodajmy kod, który działa jako szkielet dla projektu.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Tworzenie konfiguracji mowy

Przed zainicjowaniem obiektu `IntentRecognizer` należy utworzyć konfigurację, która korzysta z klucza i regionu LUIS Endpoing. Wstaw ten kod dalej.

Ten przykład konstruuje obiekt `SpeechConfig` przy użyciu klucza i regionu LUIS. Aby uzyskać pełną listę dostępnych metod, zobacz [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

> [!NOTE]
> Ważne jest, aby użyć klucza punktu końcowego LUIS, a nie kluczy początkowych lub autorskich, ponieważ tylko klucz punktu końcowego jest prawidłowy dla rozpoznawania mowy dla konwersji. Zobacz [Tworzenie aplikacji Luis i Uzyskiwanie klucza punktu końcowego,](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) Aby uzyskać instrukcje dotyczące pobierania poprawnego klucza.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

## <a name="initialize-a-intentrecognizer"></a>Inicjowanie elementu IntentRecognizer

Teraz Utwórzmy `IntentRecognizer`. Wstaw ten kod bezpośrednio poniżej konfiguracji mowy.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Dodaj LanguageUnderstandingModel i intencje

Teraz musisz skojarzyć `LanguageUnderstandingModel` z aparatem zamierzania i dodać żądane intencje.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Rozpoznawanie zamiaru

Z obiektu `IntentRecognizer` nastąpi wywołanie metody `recognize_once()`. Ta metoda pozwala usłudze rozpoznawania mowy wysyłać pojedyncze frazy do rozpoznawania, a po zidentyfikowaniu frazy do zatrzymania reconizing mowy.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Wyświetlanie wyników rozpoznawania (lub błędów)

Gdy usługa mowy zwróci wynik rozpoznawania, należy wykonać coś z nim. Zajmiemy się tym, że będzie on prosty i będzie drukował wynik do konsoli.

Wewnątrz instrukcji using poniżej wywołania `recognize_once()`Dodaj następujący kod: [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Sprawdź swój kod

W tym momencie kod powinien wyglądać następująco: (dodaliśmy Komentarze do tej wersji) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Kompilowanie i uruchamianie aplikacji

Uruchom próbkę z konsoli programu lub w środowisku IDE:

    ````
    python quickstart.py
    ````

Następne 15 sekund mowy z mikrofonu zostanie rozpoznane i zarejestrowane w oknie konsoli.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [footer](./footer.md)]
