---
title: Szybki start JavaScript interfejs API rozpoznawania emocji | Dokumentacja firmy Microsoft
description: Pobierz informacje oraz przykłady kodu w celu szybkiego Rozpocznij pracę przy użyciu interfejsu API rozpoznawania emocji, za pomocą języka JavaScript w usługach Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: fb9cc2335582c4ec75ec45635e519346d65d7e08
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072096"
---
# <a name="emotion-api-javascript-quick-start"></a>JavaScript interfejs API rozpoznawania emocji — Szybki Start

> [!IMPORTANT]
> Wersja zapoznawcza interfejsu API wideo zakończy się 30 października 2017 r. Wypróbuj nowy [wersji zapoznawczej interfejsu API indeksatora wideo](https://azure.microsoft.com/services/cognitive-services/video-indexer/) łatwe prowadzenie analiz materiałów wideo i ulepszanie środowiska odnajdywania zawartości, takie jak wyniki wyszukiwania oparte na wykrywaniu wypowiedzianych słów, twarzy, znaki i emocje. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ten artykuł zawiera informacje i przykłady kodu, aby pomóc Ci szybko rozpocząć pracę, przy użyciu [metoda rozpoznaje interfejsu API rozpoznawania emocji](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) za pomocą języka JavaScript do rozpoznawania emocji, wyrażonych przez co najmniej jedną osobę w obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Pobierz klucz subskrypcji bezpłatnych [tutaj](https://azure.microsoft.com/try/cognitive-services/), lub jeśli masz subskrypcję platformy Azure Utwórz zasób usługi interfejsu API rozpoznawania emocji i dostać się tam Twoje klucz subskrypcji i punktu końcowego.

![Tworzenie zasobu interfejsu API rozpoznawania emocji](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Rozpoznawanie emocji JavaScript przykładowe żądanie

Skopiuj następujące i takie jak zapisać do pliku `test.html`. Żądanie zmiany `url` Użyj lokalizacji, gdzie uzyskać klucze subskrypcji i zastąp wartość symbolu "Ocp-Apim-Subscription-Key" klucz ważnej subskrypcji. Te można znaleźć w witrynie Azure portal, w sekcjach Przegląd i klucze zasobu interfejsu API rozpoznawania emocji, odpowiednio. 

![Punkt końcowy interfejsu API](../Images/api-url.png)

![Klucz subskrypcji interfejsu API](../Images/keys.png)

Zmień treść żądania do lokalizacji pliku obrazu, którego chcesz użyć. Aby uruchomić próbki, a następnie przeciągnij i upuść plik w przeglądarce.

```html
<!DOCTYPE html>
<html>
<head>
    <title>JSSample</title>
    <script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>

<h2>Face Rectangle</h2>
<ul id="faceRectangle">
<!-- Will populate list with response content -->
</ul>

<h2>Emotions</h2>
<ul id="scores">
<!-- Will populate list with response content -->
</ul>

<body>

<script type="text/javascript">
    $(function() {
        // No query string parameters for this API call.
        var params = { };
      
        $.ajax({
            // NOTE: You must use the same location in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            url: "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?" + $.param(params),
            beforeSend: function(xhrObj){
                // Request headers, also supports "application/octet-stream"
                xhrObj.setRequestHeader("Content-Type","application/json");

                // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
                xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key","<your subscription key>");
            },
            type: "POST",
            // Request body
            data: '{"url": "<your image url>"}',
        }).done(function(data) {
            // Get face rectangle dimensions
            var faceRectangle = data[0].faceRectangle;
            var faceRectangleList = $('#faceRectangle');

            // Append to DOM
            for (var prop in faceRectangle) {
                faceRectangleList.append("<li> " + prop + ": " + faceRectangle[prop] + "</li>");
            }
            
            // Get emotion confidence scores
            var scores = data[0].scores;
            var scoresList = $('#scores');

            // Append to DOM
            for(var prop in scores) {
                scoresList.append("<li> " + prop + ": " + scores[prop] + "</li>")
            }
        }).fail(function(err) {
            alert("Error: " + JSON.stringify(err));
        });
    });
</script>
</body>
</html>
```

## <a name="recognize-emotions-sample-response"></a>Rozpoznawanie emocji przykładowa odpowiedź
Pomyślne wywołanie zwraca tablicę wpisy twarzy oraz wyniki skojarzone rozpoznawania emocji, uporządkowanych według rozmiaru prostokąt twarzy w kolejności malejącej. Pustą odpowiedź wskazuje, czy nie wykryto żadnych twarzy. Wpis rozpoznawania emocji zawiera następujące pola:
* faceRectangle — lokalizacja prostokąt twarzy na obrazie.
* wyniki — wyniki rozpoznawania emocji dla każdej twarzy na obrazie. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
