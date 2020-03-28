---
title: 'Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu pisma odczłowego i języka Java'
titleSuffix: Azure Cognitive Services
description: Użyj interfejsu API aparatu rozpoznawania pisma odręcznego, aby rozpocząć rozpoznawanie cyfrowych pociągnięć odręcznych w tym przewodniku Szybki start.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d2cd4e56477ea39587ce318538c9ddd84c51b03b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448119"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu pisma odczłowego i języka Java

Ten przewodnik Szybki start umożliwia rozpoczęcie korzystania z interfejsu API aparatu rozpoznawania pisma odręcznego na cyfrowych pociągnięciach odręcznych. Ta aplikacja Java wysyła żądanie interfejsu API zawierającego dane obrysu pisma odręcznego w formacie JSON i pobiera odpowiedź.

Chociaż ta aplikacja jest napisana w języku Java, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji pisma odustniającego cyfrowego. Ten przewodnik Szybki start wysyła dane obrysu odręcznego dla następującego przykładu odręcznego z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy tego przewodnika Szybki start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089904).

## <a name="prerequisites"></a>Wymagania wstępne

- [Zestaw&trade; Java Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) lub nowszy.

- Importowanie tych bibliotek z repozytorium Maven
    - [JSON w](https://mvnrepository.com/artifact/org.json/json) pakiecie Java
    - Pakiet [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient)

- Przykładowe dane obrysu pisma odręcznego dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania pisma odurzacza

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. Tworzenie zmiennych dla klucza subskrypcji, punktu końcowego i pliku JSON. Punkt końcowy zostanie później dołączyć do identyfikatora rozpoznawania rozpoznawania pisma URI pisma od ink.

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Tworzenie funkcji do wysyłania żądań

1. Utwórz nową `sendRequest()` funkcję o nazwie, która przyjmuje zmienne utworzone powyżej. Następnie wykonaj poniższe czynności.

2. Utwórz `CloseableHttpClient` obiekt, który może wysyłać żądania do interfejsu API. Wyślij żądanie do `HttpPut` obiektu żądania, łącząc punkt końcowy i adres URL aparatu rozpoznawania pisma odurzającego.

3. Użyj `setHeader()` funkcji żądania, aby `Content-Type` ustawić `application/json`nagłówek na , i `Ocp-Apim-Subscription-Key` dodać klucz subskrypcji do nagłówka.

4. Użyj `setEntity()` funkcji żądania do danych, które mają być wysyłane.   

5. Użyj `execute()` funkcji klienta, aby wysłać żądanie i `CloseableHttpResponse` zapisać je w obiekcie. 

6. Utwórz `HttpEntity` obiekt do przechowywania zawartości odpowiedzi. Pobierz zawartość `getEntity()`z . Jeśli odpowiedź nie jest pusta, zwróć ją.
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>Wysyłanie żądania rozpoznawania pisma odusianego

Utwórz metodę `recognizeInk()` wywoływaną w celu rozpoznawania danych obrysu odręcznego. Wywołanie `sendRequest()` metody utworzonej powyżej z punktu końcowego, adres URL, klucz subskrypcji i json danych. Pobierz wynik i wydrukuj go na konsoli.

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>Załaduj dane atramentu cyfrowego i wyślij żądanie

1. W głównej metodzie aplikacji przeczytaj w pliku JSON zawierający dane, które zostaną dodane do żądań.

2. Wywołanie funkcji rozpoznawania pisma odurzonego utworzonej powyżej.
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>Uruchamianie aplikacji i wyświetlanie odpowiedzi

Uruchom aplikację. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź JSON można również znaleźć w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odurzającego w aplikacji do cyfrowego pisma odurzającego, zapoznaj się z następującymi przykładowymi aplikacjami w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
