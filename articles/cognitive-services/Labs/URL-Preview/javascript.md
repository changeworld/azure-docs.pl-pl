---
title: 'Szybki start: Laboratorium Project URL Preview, Java'
titlesuffix: Azure Cognitive Services
description: Przykładowy skrypt umożliwiający szybkie rozpoczęcie pracy z interfejsem Bing URL Preview API w języku JavaScript.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: ec8a7ceef8cd938984b8eedc8397f6f67294c990
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706927"
---
# <a name="quickstart-url-preview-in-javascript"></a>Szybki start: Laboratorium URL Preview w języku JavaScript 

Poniższa jednostronicowa aplikacja używa języka JavaScript w celu utworzenia podglądu adresu URL witryny SwiftKey: https://swiftkey.com/en. 

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz klucz dostępu dla bezpłatnej wersji próbnej zestawu [Cognitive Services Labs](https://labs.cognitive.microsoft.com/en-us/project-url-preview).

## <a name="code-scenario"></a>Scenariusz kodu
Poniższy przykład w języku JavaScript zawiera pole tekstowe obiektu wejściowego, w którym użytkownik wprowadza adres URL, dla którego ma zostać wyświetlony podgląd.  Gdy użytkownik kliknie przycisk **Preview** (Podgląd), metoda po kliknięciu (OnClick) kieruje do `getPreview`, gdzie kod generuje żądanie internetowe do punktu końcowego **UrlPreview**.

Kod tworzy żądanie *XMLHttpRequest*, dodaje nagłówek *Ocp-Apim-Subscription-Key* i klucz oraz wysyła żądanie.  Dodaje asynchroniczną obsługę zdarzeń na potrzeby przetwarzania odpowiedzi.

Jeśli odpowiedź zostanie pomyślnie zwrócona, program obsługi zdarzeń przypisze tekst odpowiedzi w formacie JSON do akapitu `demo` znajdującego się na stronie. Inne elementy odpowiedzi są wyświetlane w następujących akapitach.

**Nieprzetworzona odpowiedź w formacie JSON**

```
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

```

**Uruchomiona wersja demonstracyjna**

![Przykładowy podgląd adresu URL w języku JavaScript](./media/java-script-demo.png)

## <a name="running-the-application"></a>Uruchamianie aplikacji

Aby uruchomić aplikację:

1. Zastąp wartość `YOUR-SUBSCRIPTION-KEY` prawidłowym kluczem dostępu dla Twojej subskrypcji.
2. Zapisz plik HTML i skrypt w pliku o rozszerzeniu html.
3. Uruchom stronę internetową w przeglądarce.
4. Użyj istniejącego adresu URL lub wprowadź inny adres w polu tekstowym.
5. Kliknij przycisk **Preview** (Podgląd).

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
- [Przewodnik Szybki start dla języka C#](csharp.md)
- [Przewodnik Szybki start dla języka Java](java-quickstart.md)
- [Przewodnik Szybki start dla środowiska Node](node-quickstart.md)
- [Przewodnik Szybki start dla języka Python](python-quickstart.md)
