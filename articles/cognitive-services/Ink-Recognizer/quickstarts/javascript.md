---
title: 'Szybki start: Rozpoznaje cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego i środowiska Node.js'
description: Użyj interfejsu API rozpoznawania pisma odręcznego, aby rozpocząć, rozpoznawaniu pociągnięć odręcznych cyfrowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 1785faf718b940794aebc045a3491be45eea03f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435215"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-javascript"></a>Szybki start: Rozpoznaje cyfrowy atrament za pomocą interfejsu API REST rozpoznawania pisma odręcznego i JavaScript

Aby rozpocząć korzystanie z interfejsu API rozpoznawania pisma odręcznego na pociągnięć odręcznych cyfrowych, należy użyć tego przewodnika Szybki Start. Ta aplikacja JavaScript wysyła żądanie interfejsu API, zawierające dane pociągnięcia odręczne w formacie JSON i wyświetli odpowiedzi.

Ta aplikacja jest napisany w języku Javascript i działa w przeglądarce sieci web, interfejs API jest zgodny z większość języków programowania usługi sieci web typu RESTful.

Zwykle będzie wywołać interfejs API z aplikacją pisma odręcznego cyfrowych. Ten przewodnik Szybki Start wysyła danych pociągnięcia odręczne na potrzeby poniższego przykładu pisma odręcznego z pliku JSON.

![Obraz przedstawiający tekstu odręcznego](../media/handwriting-sample.jpg)

Kod źródłowy dla tego przewodnika Szybki Start można znaleźć na [GitHub](https://go.microsoft.com/fwlink/?linkid=2089905).

## <a name="prerequisites"></a>Wymagania wstępne

- Przeglądarki sieci web
- Pociągnięcia odręczne przykładowych danych w tym przewodniku Szybki znajduje się na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-ink-strokes.json).


[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

1. W Twoim ulubionym środowiskiem IDE lub edytora, Utwórz nową `.html` pliku. Następnie należy dodać podstawowe HTML do niego, dla kodu, który zostanie dodany później.
    
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

2. W ramach `<body>` tag, Dodaj poniższy kod html:
    1. Dwa obszary tekst do wyświetlania JSON żądania i odpowiedzi.
    2. Przycisk do wywoływania `recognizeInk()` funkcja, która zostanie utworzona później.
    
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

## <a name="load-the-example-json-data"></a>Załadować przykładowe dane JSON

1. W ramach `<script>` tag, Utwórz zmienną dla sampleJson. Następnie Utwórz funkcję języka JavaScript o nazwie `openFile()` które otwiera Eksploratora plików, dzięki czemu można wybrać pliku JSON. Gdy `Recognize ink` przycisku, wywołaj tę funkcję i rozpocząć odczytywanie pliku.
2. Użyj `FileReader` obiektu `onload()` funkcji do asynchronicznego przetwarzania pliku. 
    1. Zastąp dowolny `\n` lub `\r` znaki w pliku z pustym ciągiem. 
    2. Użyj `JSON.parse()` przekonwertować tekst na prawidłowym kodem JSON
    3. Aktualizacja `request` pole tekstowe w aplikacji. Użyj `JSON.stringify()` formatującej ciąg JSON. 
    
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

1. W ramach `<script>` tagów, należy utworzyć funkcję o nazwie `recognizeInk()`. Ta funkcja zostanie później wywołania interfejsu API i aktualizacji strony z odpowiedzią. Dodawanie kodu za pomocą poniższych kroków w tej funkcji. 
        
    ```javascript
    function recognizeInk() {
    // add the code from the below steps here 
    }
    ```

    1. Utwórz zmienne dla adresu URL punktu końcowego, klucz subskrypcji i przykładowym danym JSON. Następnie utwórz `XMLHttpRequest` obiekt do wysyłania żądań do interfejsu API. 
        
        ```javascript
        // Replace the below URL with the correct one for your subscription. 
        // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
        var SERVER_ADDRESS = "YOUR-SUBSCRIPTION-URL";
        var ENDPOINT_URL = SERVER_ADDRESS + "/inkrecognizer/v1.0-preview/recognize";
        // Replace the subscriptionKey string value with your valid subscription key.
        var SUBSCRIPTION_KEY = "YOUR-SUBSCRIPTION-KEY";
        var xhttp = new XMLHttpRequest();
        ```
    2. Tworzenie zwracaną funkcji dla `XMLHttpRequest` obiektu. Ta funkcja analizy odpowiedzi interfejsu API z żądania zakończonego powodzeniem i wyświetl ją w aplikacji. 
            
        ```javascript
        function returnFunction(xhttp) {
            var response = JSON.parse(xhttp.responseText);
            console.log("Response: %s ", response);
            document.getElementById('response').innerHTML = JSON.stringify(response, null, 2);
        }
        ```
    3. Utwórz funkcję błędu dla obiektu żądania. Ta funkcja jest rejestruje błąd w konsoli. 
            
        ```javascript
        function errorFunction() {
            console.log("Error: %s, Detail: %s", xhttp.status, xhttp.responseText);
        }
        ```

    4. Tworzenie funkcji dla obiektu żądania `onreadystatechange` właściwości. Gdy zmienia się stan gotowości żądanie obiektu, zostaną zastosowane powyższe funkcje zwracają i błąd.
            
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
    
    5. Wyślij żądanie interfejsu API. Dodaj klucz subskrypcji, aby `Ocp-Apim-Subscription-Key` nagłówka, a następnie ustaw `content-type` do `application/json`
    
        ```javascript
        xhttp.open("PUT", ENDPOINT_URL, true);
        xhttp.setRequestHeader("Ocp-Apim-Subscription-Key", SUBSCRIPTION_KEY);
        xhttp.setRequestHeader("content-type", "application/json");
        xhttp.send(JSON.stringify(sampleJson));
        };
        ```

## <a name="run-the-application-and-view-the-response"></a>Uruchom aplikację, a następnie zobacz odpowiedź

Ta aplikacja może działać w przeglądarce sieci web. Odpowiedź oznaczająca Powodzenie są zwracane w formacie JSON. Możesz również znaleźć odpowiedź w formacie JSON na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/javascript/InkRecognition/quickstart/example-response.json):

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST](https://go.microsoft.com/fwlink/?linkid=2089907)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w cyfrowych aplikacji pisma odręcznego, spójrz na następujące przykładowe aplikacje w usłudze GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
