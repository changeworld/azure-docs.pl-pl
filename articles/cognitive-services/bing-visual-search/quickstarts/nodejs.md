---
title: 'Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 23c3e167f2fd1544a42c98946f4ae95d3451dafe
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180623"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i platformy Node.js

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja JavaScript przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat. Chociaż ta aplikacja jest napisana w języku JavaScript, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Podczas przekazywania obrazu lokalnego dane formularza muszą zawierać nagłówek Content-Disposition. Jego parametr `name` musi mieć wartość "image", a parametr `filename` może być ustawiony na dowolny ciąg. Zawartością formularza jest plik binarny obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* Moduł żądania dla języka JavaScript
    * Ten moduł można zainstalować za pomocą polecenia `npm install request`
* Moduł danych formularza
    * Ten moduł można zainstalować za pomocą polecenia `npm install form-data`


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]


## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i ustaw następujące wymagania:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę obrazu.

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Utwórz funkcję o nazwie `requestCallback()`, która będzie drukować odpowiedź z interfejsu API.

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

1. Utwórz nowe dane formularza za pomocą funkcji `FormData()` i dodaj do nich ścieżkę obrazu za pomocą funkcji `fs.createReadStream()`.
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Przekaż obraz za pomocą biblioteki żądania, wywołując funkcję `requestCallback()` w celu wydrukowania odpowiedzi. Pamiętaj, aby dodać klucz subskrypcji do nagłówka żądania. 

    ```javascript
    form.getLength(function(err, length){
      if (err) {
        return requestCallback(err);
      }
      var r = request.post(baseUri, requestCallback);
      r._form = form; 
      r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
    });
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../tutorial-bing-visual-search-single-page-app.md)
