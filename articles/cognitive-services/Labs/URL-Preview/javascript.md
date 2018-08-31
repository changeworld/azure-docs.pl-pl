---
title: JavaScript Przewodnik Szybki start dotyczący Podgląd adresu URL projektu — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
description: Przykładowy skrypt, aby szybko rozpocząć pracę przy użyciu interfejsu API usługi Bing adresu URL (wersja zapoznawcza) w usługach Microsoft Cognitive Services na platformie Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: dda6f7c105dfbadc3c22f0c008aa8759fe12fa03
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301356"
---
# <a name="url-preview-in-javascript"></a>Podgląd adresu URL w języku JavaScript 

Korzysta z następującej aplikacji jednostronicowej JavaScript do tworzenia adresu URL w wersji zapoznawczej witryny SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Wymagania wstępne

Uzyskiwanie klucza dostępu dla bezpłatnej wersji próbnej [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview)

## <a name="code-scenario"></a>Scenariusz kodu
Poniższy przykład kodu javascript zawiera pole tekstowe obiektu wejściowego, gdy użytkownik wprowadzi adres URL, aby wyświetlić podgląd.  Kiedy użytkownik kliknie **(wersja zapoznawcza)** przycisk trasy metoda onclick `getPreview` gdzie kod generuje żądanie sieci Web w taki sposób, aby **UrlPreview** punktu końcowego.

Ten kod tworzy *XMLHttpRequest*, dodaje *Ocp-Apim-Subscription-Key* nagłówka i klucz i wysyła żądanie.  Dodaje procedurę obsługi zdarzeń asynchronicznych do przetwarzania odpowiedzi.

Jeśli odpowiedź zwróci się pomyślnie, program obsługi przypisuje tekst kodu JSON odpowiedzi `demo` akapitu na stronie. Inne elementy odpowiedzi są ustawione na następuje do wyświetlenia.

**Nieprzetworzoną odpowiedź JSON**

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

**Uruchamianie demonstracyjnego**

![Przykład JavaScript adresu Url w wersji zapoznawczej](./media/java-script-demo.png)

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację:

1. Zastąp `YOUR-SUBSCRIPTION-KEY` wartością prawidłowy klucz dostępu dla Twojej subskrypcji.
2. Zapisz plik z rozszerzeniem .html HTML i skryptu.
3. Uruchom stronę sieci Web w przeglądarce.
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
- [Przewodnik Szybki Start języka C#](csharp.md)
- [Przewodnik Szybki Start języka Java](java-quickstart.md)
- [Przewodnik Szybki Start węzła](node-quickstart.md)
- [Przewodnik Szybki Start języka Python](python-quickstart.md)
