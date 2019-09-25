---
title: 'Szybki start: Rozpoznawanie cyfrowego pisma odręcznego za pomocą interfejsu API REST aparatu rozpoznawania atramentu i środowiska Node. js'
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
ms.openlocfilehash: 5e3b97faaed84f2c07ea70ddb73bd8e8c9efa71d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212646"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Szybki start: Rozpoznawanie cyfrowego pisma odręcznego za pomocą interfejsu API REST aparatu rozpoznawania atramentu i języka JavaScript

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z interfejsu API rozpoznawania atramentu na potrzeby pociągnięć atramentów cyfrowych Ta aplikacja JavaScript wysyła żądanie interfejsu API zawierające dane pociągnięcia odręcznego w formacie JSON i wyświetla odpowiedź.

Gdy aplikacja jest zapisywana w języku JavaScript i jest uruchamiana w przeglądarce sieci Web, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Zazwyczaj można wywołać interfejs API z aplikacji cyfrowego odręcznego. Ten przewodnik Szybki Start wysyła dane pociągnięć odręcznych dla poniższego przykładu napisanego ręcznie z pliku JSON.

![obraz tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarka sieci Web
- Przykładowe dane pociągnięcia farbą dla tego przewodnika Szybki Start można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W ulubionym środowisku IDE lub edytorze Utwórz nowy `.html` plik. Następnie Dodaj do niego podstawowy kod HTML, który zostanie dodany później.
    
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

2. `<body>` W tagu Dodaj następujący kod HTML:
    1. Dwa obszary tekstu do wyświetlania żądania i odpowiedzi JSON.
    2. Przycisk służący do wywoływania `recognizeInk()` funkcji, która zostanie utworzona później.
    
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

1. `<script>` W tagu Utwórz zmienną dla sampleJson. Następnie Utwórz funkcję języka JavaScript o `openFile()` nazwie otwierającej Eksploratora plików, aby można było wybrać plik JSON. `Recognize ink` Gdy przycisk zostanie kliknięty, wywoła tę funkcję i rozpocznie odczytywanie pliku.
2. `FileReader` Użyj funkcji`onload()` obiektu, aby przetworzyć plik asynchronicznie. 
    1. Zastąp `\n` wszystkie `\r` znaki lub w pliku pustym ciągiem. 
    2. Użyj `JSON.parse()` do przekonwertowania tekstu na prawidłowy kod JSON
    3. Zaktualizuj pole `request` tekstowe w aplikacji. Służy `JSON.stringify()` do formatowania ciągu JSON. 
    
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

## <a name="send-a-request-to-the-ink-recognizer-api"></a>Wyślij żądanie do interfejsu API rozpoznawania pisma odręcznego

1. W tagu Utwórz funkcję o nazwie `recognizeInk()`. `<script>` Ta funkcja spowoduje późniejsze wywołanie interfejsu API i zaktualizowanie strony z odpowiedzią. Dodaj kod z następujących kroków w tej funkcji. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Utwórz zmienne dla adresu URL punktu końcowego, klucza subskrypcji i przykładowego pliku JSON. Następnie Utwórz `XMLHttpRequest` obiekt, aby wysłać żądanie interfejsu API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: "https://<your-custom-subdomain>.cognitiveservices.azure.com";
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Utwórz funkcję Return dla `XMLHttpRequest` obiektu. Ta funkcja przeanalizuje odpowiedź interfejsu API z pomyślnego żądania i wyświetli ją w aplikacji. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Utwórz funkcję Error dla obiektu request. Ta funkcja rejestruje błąd w konsoli programu. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Utwórz funkcję dla `onreadystatechange` właściwości obiektu żądania. Gdy stan gotowości obiektu żądania ulegnie zmianie, zostaną zastosowane powyższe funkcje Return i Error.
            
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
    
    5. Wyślij żądanie interfejsu API. Dodaj klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka i `content-type` ustaw wartość na`application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację i Wyświetl odpowiedź

Ta aplikacja może być uruchamiana w przeglądarce internetowej. Pomyślna odpowiedź jest zwracana w formacie JSON. Odpowiedź na kod JSON można również znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w aplikacji do cyfrowego odkróla, zapoznaj się z następującymi przykładowymi aplikacjami w witrynie GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
