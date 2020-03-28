---
title: 'Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu pisma odczłowego i pliku Node.js'
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
ms.openlocfilehash: a37f2b7044fcba04ca18093aa73563961e9e35de
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448145"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Szybki start: rozpoznawanie atramentu cyfrowego za pomocą interfejsu API REST aparatu odurzającego i języka JavaScript

Ten przewodnik Szybki start umożliwia rozpoczęcie korzystania z interfejsu API aparatu rozpoznawania pisma odręcznego na cyfrowych pociągnięciach odręcznych. Ta aplikacja JavaScript wysyła żądanie interfejsu API zawierającego dane obrysu atramentu w formacie JSON i wyświetla odpowiedź.

Podczas gdy ta aplikacja jest napisana w javascript i działa w przeglądarce internetowej, API jest restful usługi internetowej zgodne z większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji pisma odustniającego cyfrowego. Ten przewodnik Szybki start wysyła dane obrysu odręcznego dla następującego przykładu odręcznego z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy tego przewodnika Szybki start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka internetowa
- Przykładowe dane obrysu pisma odręcznego dla tego przewodnika Szybki start można znaleźć w [usłudze GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).

### <a name="create-an-ink-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania pisma odurzacza

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W ulubionym IDE lub edytorze `.html` utwórz nowy plik. Następnie dodaj do niego podstawowy kod HTML dla kodu, który dodamy później.
    
    ```html
    <!DOCTYPE html>
    <html>
    
        <head>
            <script type="text/javascript">
            </script>
        </head>
        
        <body>
        </body>
    
    </html>
    ```

2. W `<body>` tagu dodaj następujący html:
    1. Dwa obszary tekstowe do wyświetlania żądania JSON i odpowiedzi.
    2. Przycisk do wywoływania `recognizeInk()` funkcji, która zostanie utworzona później.
    
    ```HTML
    <!-- <body>-->
        <h2>Send a request to the Ink Recognition API</h2>
        <p>Request:</p>
        <textarea id="request" style="width:800px;height:300px"></textarea>
        <p>Response:</p>
        <textarea id="response" style="width:800px;height:300px"></textarea>
        <br>
        <button type="button" onclick="recognizeInk()">Recognize Ink</button>
    <!--</body>-->
    ```

## <a name="load-the-example-json-data"></a>Ładowanie przykładowych danych JSON

1. W `<script>` obrębie tagu utwórz zmienną dla przykładuSon. Następnie utwórz funkcję `openFile()` JavaScript o nazwie, która otwiera eksploratora plików, aby można było wybrać plik JSON. Po `Recognize ink` kliknięciu przycisku wywoła tę funkcję i rozpocznie czytanie pliku.
2. Funkcja `FileReader` obiektu `onload()` służy do asynchronicznie przetwarzania pliku. 
    1. Zastąp dowolny `\n` lub `\r` dowolny znak w pliku pustym ciągiem. 
    2. Służy `JSON.parse()` do konwertowania tekstu na prawidłowy JSON
    3. Zaktualizuj `request` pole tekstowe w aplikacji. Służy `JSON.stringify()` do formatowania ciągu JSON. 
    
    ```javascript
    var sampleJson = "";
    function openFile(event) {
        var input = event.target;
    
        var reader = new FileReader();
        reader.onload = function(){
            sampleJson = reader.result.replace(/(\\r\\n|\\n|\\r)/gm, "");
            sampleJson = JSON.parse(sampleJson);
            document.getElementById('request').innerHTML = JSON.stringify(sampleJson, null, 2);
        };
        reader.readAsText(input.files[0]);
    };
    ```

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Wysyłanie żądania do interfejsu API aparatu rozpoznawania pisma odurzacza

1. W `<script>` obrębie znacznika utwórz funkcję o nazwie `recognizeInk()`. Ta funkcja spowoduje późniejsze wywołanie interfejsu API i zaktualizowanie strony za pomocą odpowiedzi. Dodaj kod z następujących kroków w ramach tej funkcji. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Tworzenie zmiennych dla adresu URL punktu końcowego, klucza subskrypcji i przykładowego JSON. Następnie utwórz obiekt `XMLHttpRequest` do wysłania żądania interfejsu API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = process.env["INK_RECOGNITION_ENDPOINT"];
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        var SUBSCRIPTION_KEY = process.env["INK_RECOGNITION_SUBSCRIPTION_KEY"];
        var xhttp = new XMLHttpRequest();
        ```
    2. Utwórz funkcję zwracania `XMLHttpRequest` obiektu. Ta funkcja będzie analizować odpowiedź interfejsu API z pomyślnego żądania i wyświetlić go w aplikacji. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Utwórz funkcję błędu dla obiektu żądania. Ta funkcja rejestruje błąd w konsoli. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Utwórz funkcję właściwości obiektu `onreadystatechange` żądania. Po zmianie stanu gotowości obiektu żądania zostaną zastosowane powyższe funkcje zwracania i błędu.
            
        ```javascript
        xhttp.onreadystatechange = function () {
            if (this.readyState === 4) {
                if (this.status === 200) {
                    returnFunction(xhttp);
                } else {
                    errorFunction(xhttp);
                }
            }
        };
        ```
    
    5. Wyślij żądanie interfejsu API. Dodaj klucz subskrypcji `Ocp-Apim-Subscription-Key` do nagłówka `content-type` i ustaw`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Uruchamianie aplikacji i wyświetlanie odpowiedzi

Ta aplikacja może być uruchamiana w przeglądarce internetowej. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź JSON można również znaleźć na [GitHub:](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Odwołanie do interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odurzającego w aplikacji do cyfrowego pisma odurzającego, zapoznaj się z następującymi przykładowymi aplikacjami w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
