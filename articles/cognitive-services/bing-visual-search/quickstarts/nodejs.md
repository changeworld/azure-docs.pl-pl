---
title: 'Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 9414bac220d928618b403aa2f7df7748772e0e9a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047572"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Szybki start: uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu interfejsu API REST wyszukiwania wizualnego Bing i platformy Node.js

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania do interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja JavaScript przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat. Aplikacja jest napisana w języku JavaScript, natomiast interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Podczas przekazywania obrazu lokalnego, mogą zawierać dane formularza `Content-Disposition` nagłówka. Należy ustawić jego `name` parametr "image", a `filename` parametr może być ustawiony na dowolny ciąg. Zawartość formularza obejmują dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* Moduł żądania dla języka JavaScript. Możesz użyć `npm install request` polecenie, aby zainstalować moduł.
* Moduł danych formularza. Możesz użyć `npm install form-data` polecenie, aby zainstalować moduł. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz plik JavaScript w Twoim ulubionym środowiskiem IDE lub edytora i ustaw następujące wymagania:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę do obrazu:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Tworzenie funkcji o nazwie `requestCallback()` do drukowania odpowiedzi z interfejsu API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

1. Utwórz nową **pobranie** przy użyciu `FormData()`, obiektów blob i uzupełnialnych Twojej ścieżki obrazu, za pomocą `fs.createReadStream()`:
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Przekaż obraz, za pomocą biblioteki żądania i wywołać `requestCallback()` do drukowania w odpowiedzi. Pamiętaj dodać klucz subskrypcji do nagłówka żądania:

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z jednej strony wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
