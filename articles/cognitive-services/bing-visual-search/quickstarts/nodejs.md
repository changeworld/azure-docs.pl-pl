---
title: 'Szybki start: uzyskaj szczegółowe informacje o obrazie przy użyciu interfejsu API REST i pliku Node.js — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: 373d6fa5402ba703cbebe88ad562974ba97f3391
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75379712"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-nodejs"></a>Szybki start: uchodź szczegółowe informacje o obrazie za pomocą interfejsu API REST wyszukiwania wizualnego Bing i pliku Node.js

Ten przewodnik Szybki start umożliwia utworzenie pierwszego wywołania interfejsu API wyszukiwania wizualnego Bing i wyświetlenie wyników wyszukiwania. Ta prosta aplikacja JavaScript przekazuje obraz do interfejsu API i wyświetla zwrócone informacje na jego temat. Aplikacja jest napisana w języku JavaScript, natomiast interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Node.js](https://nodejs.org/en/download/)
* Moduł Żądanie dla języka JavaScript. Można użyć `npm install request` polecenia, aby zainstalować moduł.
* Moduł danych formularza. Można użyć `npm install form-data` polecenia, aby zainstalować moduł. 

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz plik JavaScript w ulubionym interfejsie IDE lub edytorze i ustaw następujące wymagania:

    ```javascript
    var request = require('request');
    var FormData = require('form-data');
    var fs = require('fs');
    ```

2. Utwórz zmienne dla punktu końcowego interfejsu API, klucz subskrypcji oraz ścieżkę obrazu. `baseUri`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlany w witrynie Azure portal dla zasobu:

    ```javascript
    var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
    var subscriptionKey = 'your-api-key';
    var imagePath = "path-to-your-image";
    ```

3. Utwórz funkcję `requestCallback()` o nazwie, aby wydrukować odpowiedź z interfejsu API:

    ```javascript
    function requestCallback(err, res, body) {
        console.log(JSON.stringify(JSON.parse(body), null, '  '))
    }
    ```

## <a name="construct-and-send-the-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

Podczas przekazywania obrazu lokalnego dane formularza `Content-Disposition` muszą zawierać nagłówek. Należy ustawić `name` jego parametr na "image", a `filename` parametr można ustawić na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, wynosi 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

1. Utwórz nowy obiekt **FormularzaData** za pomocą `FormData()`programu , `fs.createReadStream()`i dołącz do niego ścieżkę obrazu, używając :
    
    ```javascript
    var form = new FormData();
    form.append("image", fs.createReadStream(imagePath));
    ```

2. Użyj biblioteki żądań, aby `requestCallback()` przekazać obraz i wywołać, aby wydrukować odpowiedź. Pamiętaj, aby dodać klucz subskrypcji do nagłówka żądania:

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
> [Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego](../tutorial-bing-visual-search-single-page-app.md)
