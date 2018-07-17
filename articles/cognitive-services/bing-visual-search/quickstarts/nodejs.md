---
title: 'Szybki Start: JavaScript API wyszukiwania wizualnego Bing | Dokumentacja firmy Microsoft'
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Pokazuje, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskanie szczegółowych informacji o obrazie.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 60b1dc9b8ea9eda258e9776b8967df38c97d964e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071707"
---
# <a name="your-first-bing-visual-search-query-in-javascript"></a>Pierwsze zapytanie wyszukiwania wizualnego Bing w języku JavaScript

Interfejs API wyszukiwania wizualnego Bing zwraca informacje o obrazie, który należy podać. Aby przekazać obraz, przy użyciu adresu URL obrazu usługi insights tokenu, lub przekazanie obrazu. Aby uzyskać informacje o tych opcjach, zobacz [co to jest interfejs API wyszukiwania wizualnego Bing?](../overview.md) W tym artykule przedstawiono przekazywania obrazu. Przekazywanie obrazu może być przydatne w scenariuszach mobilnych, gdzie Zrób zdjęcie dobrze znanych charakterystycznych elementów krajobrazu i uzyskanie informacji na jego temat. Na przykład szczegółowe informacje zawiera elementy towarzyszące składni o charakterystycznych elementów krajobrazu. 

Jeśli załadujesz lokalny obraz, poniżej przedstawiono dane formularza należy uwzględnić w treści wpisu. Dane mogą zawierać nagłówek Content-Disposition. Jego `name` parametru musi być równa "image" i `filename` parametru może być ustawiona na dowolny ciąg. Zawartość formularza jest plik binarny obrazu. Rozmiar maksymalny obrazu, którą możesz przekazać to 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Ten artykuł zawiera prostą aplikację konsolową, która wysyła żądanie interfejsu API wyszukiwania wizualnego Bing i wyświetla wyniki wyszukiwania JSON. Podczas tej aplikacji został napisany w języku JavaScript, interfejs API jest zgodny z dowolnego języka programowania, który może wysyłać żądania HTTP i Przeanalizuj dane JSON usługi sieci Web typu RESTful. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz [Node.js 6](https://nodejs.org/en/download/) do uruchamiania tego kodu.

W tym przewodniku Szybki Start, możesz użyć [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klawisz płatna subskrypcja lub subskrypcja.

## <a name="running-the-application"></a>Uruchamianie aplikacji

Poniżej przedstawiono sposób wysyłania komunikatów przy użyciu pobranie w środowisku Node.js.

Aby uruchomić tę aplikację, wykonaj następujące kroki:

1. Utwórz folder dla projektu (lub użyj Twoim ulubionym środowiskiem IDE lub edytora).
2. W wierszu polecenia lub terminalu przejdź do folderu, który został utworzony.
3. Zainstaluj moduły żądania:  
  ```  
  npm install request  
  ```  
3. Zainstaluj moduły danych formularza:  
  ```  
  npm install form-data  
  ```  
4. Utwórz plik o nazwie GetVisualInsights.js i Dodaj następujący kod do niego.
5. Zastąp `subscriptionKey` wartość z kluczem subskrypcji.
6. Zastąp `imagePath` na ścieżkę obrazu do przekazania.
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

[Uzyskiwanie szczegółowych informacji o pliku obrazu przy użyciu tokenu usługi insights](../use-insights-token.md)  
[Samouczek przekazywania obrazów wyszukiwania wizualnego Bing](../tutorial-visual-search-image-upload.md)
[samouczek dotyczący aplikacji jednostronicowej wyszukiwania wizualnego Bing](../tutorial-bing-visual-search-single-page-app.md)  
[Przegląd wyszukiwania wizualnego Bing](../overview.md)  
[Wypróbuj](https://aka.ms/bingvisualsearchtryforfree)  
[Pobierz klucz bezpłatny dostęp próbny](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)
