---
title: JavaScript — Szybki Start dla Visual wyszukiwania usługi Bing interfejsu API | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz Visual API wyszukiwania usługi Bing i wrócić szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: dd28c829d8d24980a746244dc6aca880d2d69224
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349345"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>W języku JavaScript pierwszego zapytania Visual wyszukiwania usługi Bing

Visual API wyszukiwania usługi Bing zwraca informacje dotyczące obrazu, który podasz. Musisz podać obrazu przy użyciu adresu URL obrazu, insights token, lub przekazując obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest Visual API wyszukiwania usługi Bing?](../overview.md) W tym artykule przedstawiono przekazywanie obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie obrazu dzielnicę dobrze znanych i odzyskać informacje o nim. Na przykład szczegółowych danych może zawierać elementy towarzyszące składni o punkty. 

Jeśli Przekaż obraz lokalnych, poniżej przedstawiono dane formularza należy uwzględnić w treści POST. Dane formularza musi zawierać nagłówek Content-Disposition. Jego `name` parametr musi być ustawiony na "obrazu" i `filename` parametr może być ustawiony na dowolny ciąg. Zawartość formularza jest plikiem binarnym obrazu. Rozmiar maksymalny obrazu, które mogą przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostej aplikacji konsolowej wysyła żądanie Visual API wyszukiwania usługi Bing i wyświetla wyniki wyszukiwania JSON. Podczas tej aplikacji został napisany w języku JavaScript, interfejsu API jest zgodny z żadnego języka programowania, które mogą wysyłać żądania HTTP i przeanalizować składni JSON usługi sieci RESTful Web. 

## <a name="prerequisites"></a>Wymagania wstępne

Należy [Node.js 6](https://nodejs.org/en/download/) do uruchomienia tego kodu.

Dla tego przewodnika Szybki Start, możesz użyć [bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatną subskrypcję lub subskrypcji.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób wysyłania wiadomości za pomocą FormData w środowisku Node.js.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz folder dla projektu (lub użyć ulubionego IDE lub edytora).
2. Z wiersza polecenia lub terminalu przejdź do folderu utworzonego.
3. Zainstaluj moduły żądania:  
  ```  
  npm install request  
  ```  
3. Zainstaluj moduły danych formularza:  
  ```  
  npm install form-data  
  ```  
4. Utwórz plik o nazwie GetVisualInsights.js i Dodaj następujący kod do niego.
5. Zastąp `subscriptionKey` wartości z klucza subskrypcji.
6. Zastąp `imagePath` wartości o ścieżce obrazu do przekazania.
7. Uruchom program.  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');
var fs = require('fs');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';
var imagePath = "<pathtoyourimagegoeshere>";

var form = new FormData();
form.append("image", fs.createReadStream(imagePath));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


## <a name="next-steps"></a>Kolejne kroki

[Uzyskiwanie szczegółowych informacji o obrazie przy użyciu tokenu insights](../use-insights-token.md)  
[Samouczek aplikacji jednej strony Visual wyszukiwania usługi Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Omówienie Visual wyszukiwania usługi Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatnej wersji próbnej dostępu](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Odwołanie do Visual API wyszukiwania usługi Bing](https://aka.ms/bingvisualsearchreferencedoc)
