---
title: 'Szybki Start: Rozpoznawanie cyfrowego pisma odręcznego za pomocą interfejsu API REST rozpoznawania atramentu iC#'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API rozpoznawania pisma odręcznego, aby rozpocząć rozpoznawanie cyfrowych pociągnięć atramentu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 6ef58d1444eb0ec7b2b8891f944b428a613edf3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515613"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Szybki Start: Rozpoznawanie cyfrowego pisma odręcznego za pomocą interfejsu API REST rozpoznawania atramentu iC#

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wysyłanie pociągnięć cyfrowego atramentu do interfejsu API rozpoznawania atramentu. Ta C# aplikacja wysyła żądanie interfejsu API zawierające dane pociągnięcia odręcznego w formacie JSON i pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji cyfrowego odręcznego. Ten przewodnik Szybki Start wysyła dane pociągnięć odręcznych dla poniższego przykładu napisanego ręcznie z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft. JSON](https://www.newtonsoft.com/json)
    - Aby zainstalować plik Newtonsoft. JSON jako pakiet NuGet w programie Visual Studio:
        1. Kliknij prawym przyciskiem myszy **Menedżera rozwiązań**
        2. Kliknij pozycję **Zarządzaj pakietami NuGet...**
        3. Wyszukaj `Newtonsoft.Json` i zainstaluj pakiet
- Jeśli używasz systemu Linux/MacOS, ta aplikacja może zostać uruchomiona przy użyciu narzędzia [mono](https://www.mono-project.com/).

- Przykładowe dane pociągnięcia farbą dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania atramentu

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio Utwórz nowe rozwiązanie konsoli i Dodaj następujące pakiety. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Utwórz zmienne dla klucza subskrypcji i punktu końcowego oraz przykładowy plik JSON. Punkt końcowy zostanie później połączony z `inkRecognitionUrl` w celu uzyskania dostępu do interfejsu API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Utwórz funkcję do wysyłania żądań

1. Utwórz nową funkcję asynchroniczną o nazwie `Request`, która przyjmuje zmienne utworzone powyżej.

2. Ustaw informacje o protokole i nagłówku zabezpieczeń klienta przy użyciu obiektu `HttpClient`. Pamiętaj, aby dodać klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`. Następnie Utwórz obiekt `StringContent` dla żądania.
 
3. Wyślij żądanie z `PutAsync()`. Jeśli żądanie zakończy się pomyślnie, zwróć odpowiedź.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Wyślij żądanie rozpoznawania pisma odręcznego

1. Utwórz nową funkcję o nazwie `recognizeInk()`. Utwórz żądanie i wyślij je, wywołując funkcję `Request()` z punktem końcowym, kluczem subskrypcji, adresem URL interfejsu API i cyfrowym pociągnięcia atramentu.

2. Deserializacji obiektu JSON i Zapisz go w konsoli. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Ładowanie danych cyfrowego atramentu

Utwórz funkcję o nazwie `LoadJson()`, aby załadować plik JSON danych pisma odręcznego. Użyj `StreamReader` i `JsonTextReader`, aby utworzyć `JObject` i zwrócić go.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Wyślij żądanie interfejsu API

1. W głównej metodzie aplikacji Załaduj dane JSON przy użyciu funkcji utworzonej powyżej. 

2. Wywołaj funkcję `recognizeInk()` utworzoną powyżej. Użyj `System.Console.ReadKey()`, aby zachować otwarte okno konsoli po uruchomieniu aplikacji.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację i Wyświetl odpowiedź

Uruchom aplikację. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź na kod JSON można również znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w aplikacji do cyfrowego odkróla, zapoznaj się z następującymi przykładowymi aplikacjami w witrynie GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
