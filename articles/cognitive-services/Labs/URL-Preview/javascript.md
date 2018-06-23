---
title: JavaScript — Szybki Start dla podglądu URL projektu - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Przykład skryptu, aby szybko rozpocząć korzystanie z interfejsem API Bing adres URL w wersji zapoznawczej w kognitywnych usług Microsoft Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 9041a88a292fb2dabead69195ebc3074e2ecf486
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348633"
---
# <a name="url-preview-in-javascript"></a>Adres URL w wersji zapoznawczej w języku JavaScript 

Następującej aplikacji jednostronicowej Tworzenie podglądu adres URL witryny SwiftKey za pomocą kodu JavaScript: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej [kognitywnych Labs usług](https://aka.ms/answersearchsubscription)

## <a name="code-scenario"></a>Scenariusz kodu
W poniższym przykładzie javascript zawiera pole tekstowe obiektu wejścia, gdy użytkownik musi wprowadzić adres URL, aby wyświetlić podgląd.  Po kliknięciu przez użytkownika **Podgląd** przycisk tras metody onclick `getPreview` gdzie kodu generuje żądania sieci Web w taki sposób, aby **UrlPreview** punktu końcowego.

Kod tworzy *XMLHttpRequest*, dodaje *Ocp-Apim-subskrypcji — klucz* nagłówka i klucz i wysyła żądanie.  Dodaje Zdarzenie asynchroniczne program obsługi przetwarzał odpowiedzi.

Jeśli odpowiedź zwróci pomyślnie, program obsługi przypisuje tekst JSON odpowiedzi `demo` akapitu na stronie. Inne elementy odpowiedzi są ustawione na następuje do wyświetlenia.

**Nieprzetworzona odpowiedź w formacie JSON**

````
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https://swiftkey.com/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

````

**Uruchomiona wersja demonstracyjna**

![Przykład JavaScript adres Url w wersji zapoznawczej](./media/java-script-demo.png)

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację:

1. Zastąp `YOUR-SUBSCRIPTION-KEY` wartość kluczem dostępu prawidłową dla Twojej subskrypcji.
2. Zapisz plik z rozszerzeniem .html HTML i skryptu.
3. Uruchom strony sieci Web w przeglądarce.
4. Użyj istniejącego adresu URL lub inną nazwę w polu tekstowym.
5. Kliknij przycisk **Podgląd** przycisku.

**Kod źródłowy:**

```
<!DOCTYPE html>

<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="utf-8" />
    <title>urlPreview Demo</title>
</head>
<body>
    <h3>URL Preview Demo</h3>

    Page to preview: <input type="url" id="myURL" value="https://swiftkey.com/en">
    <button onclick="getPreview()">Preview</button>

    <p id="demo"></p>
    <br />
    <p id="jsonDesc"></p>
    <p><a id="familyFriendly"></a></p>
    <a id="contentUrl"></a>
    <p />
    <img id="jsonImage" />

    <script>

        var BING_ENDPOINT = "https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search"; 
        var key = "YOUR-SUBSCRIPTION-KEY";
        var xhr;

        function getPreview() {
            xhr = new XMLHttpRequest();

            var queryUrl = BING_ENDPOINT + "?q=" +
                encodeURIComponent(document.getElementById("myURL").value);
            xhr.open('GET', queryUrl, true);
            xhr.setRequestHeader("Ocp-Apim-Subscription-Key", key);

            xhr.send();

            xhr.addEventListener("readystatechange", processRequest, false);
        }

        function processRequest(e) {

            if (xhr.readyState == 4 && xhr.status == 200) {
                document.getElementById("demo").innerHTML = xhr.responseText;
                var obj = JSON.parse(xhr.responseText);
                document.getElementById("jsonDesc").innerHTML = obj.description;
                document.getElementById("familyFriendly").innerHTML = "Family Friendly: " + obj.isFamilyFriendly;
                document.getElementById("contentUrl").innerHTML = obj.url;
                document.getElementById("contentUrl").href = obj.url;
                document.getElementById("jsonImage").width = 350;
                document.getElementById("jsonImage").src = obj.primaryImageOfPage.contentUrl;

            }

        }

    </script>

</body>
</html>

```

## <a name="next-steps"></a>Kolejne kroki
- [C# — Szybki Start](csharp.md)
- [Szybki Start Java](java-quickstart.md)
- [Węzeł Szybki Start](node-quickstart.md)
- [Krótkie wprowadzenie do języka Python](python-quickstart.md)