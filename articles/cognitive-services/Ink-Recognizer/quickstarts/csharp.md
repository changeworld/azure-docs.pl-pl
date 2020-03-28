---
title: 'Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu pisma odczłowego i C #'
titleSuffix: Azure Cognitive Services
description: Ten przewodnik Szybki start pokazuje, jak za pomocą interfejsu API aparatu rozpoznawania pisma odręcznego, aby rozpocząć rozpoznawanie cyfrowych pociągnięć odręcznych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: c24d055f1904453d2f512a278f00e23c6fea1d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371377"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu pisma odczłowego i C #

Ten przewodnik Szybki start umożliwia rozpoczęcie wysyłania cyfrowych pociągnięć odręcznych do interfejsu API aparatu rozpoznawania pisma odręcznego. Ta aplikacja języka C# wysyła żądanie interfejsu API zawierające dane obrysu pisma odręcznego w formacie JSON i pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku C#, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji pisma odustniającego cyfrowego. Ten przewodnik Szybki start wysyła dane obrysu odręcznego dla następującego przykładu odręcznego z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy tego przewodnika Szybki start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Wymagania wstępne

- Dowolna wersja programu [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Aby zainstalować Newtonsoft.Json jako pakiet NuGet w programie Visual Studio:
        1. Kliknij prawym przyciskiem myszy **menedżera rozwiązań**
        2. Kliknij **pozycję Zarządzaj pakietami NuGet...**
        3. Wyszukiwanie `Newtonsoft.Json` i instalowanie pakietu
- Jeśli używasz Linuksa / MacOS, ta aplikacja może być uruchomiony przy użyciu [Mono](https://www.mono-project.com/).

- Przykładowe dane obrysu pisma odręcznego dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania pisma odurzacza

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W programie Visual Studio utwórz nowe rozwiązanie konsoli i dodaj następujące pakiety. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. Tworzenie zmiennych dla klucza subskrypcji i punktu końcowego oraz przykładowego pliku JSON. Punkt końcowy zostanie później `inkRecognitionUrl` połączony z dostępem do interfejsu API. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji do wysyłania żądań

1. Utwórz nową funkcję asynchronizową o nazwie, `Request` która przyjmuje zmienne utworzone powyżej.

2. Ustaw protokół zabezpieczeń klienta i informacje `HttpClient` nagłówka przy użyciu obiektu. Pamiętaj, aby dodać klucz `Ocp-Apim-Subscription-Key` subskrypcji do nagłówka. Następnie utwórz `StringContent` obiekt dla żądania.
 
3. Wyślij żądanie `PutAsync()`z . Jeśli żądanie zakończy się pomyślnie, zwróć odpowiedź.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>Wysyłanie żądania rozpoznawania pisma odusianego

1. Utwórz nową `recognizeInk()`funkcję o nazwie . Skonstruuj żądanie i `Request()` wyślij go, wywołując funkcję z punktem końcowym, kluczem subskrypcji, adresem URL interfejsu API i danymi cyfrowego obrysu pisma odręcznego.

2. Deserialize obiektu JSON i zapisać go do konsoli. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>Ładowanie danych atramentu cyfrowego

Utwórz funkcję `LoadJson()` wywoływaną w celu załadowania pliku JSON danych pisma oduwczłowego. Użyj `StreamReader` i `JsonTextReader` utworzyć `JObject` i zwrócić go.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>Wyślij żądanie interfejsu API

1. W głównej metodzie aplikacji należy załadować dane JSON z funkcją utworzoną powyżej. 

2. Wywołanie `recognizeInk()` funkcji utworzonej powyżej. Służy `System.Console.ReadKey()` do utrzymywanie otwartego okna konsoli po uruchomieniu aplikacji.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uruchamianie aplikacji i wyświetlanie odpowiedzi

Uruchom aplikację. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź JSON można również znaleźć w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odurzającego w aplikacji do cyfrowego pisma odurzającego, zapoznaj się z następującymi przykładowymi aplikacjami w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
