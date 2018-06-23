---
title: Szybki start API JavaScript rozpoznawania emocji — warstwa | Dokumentacja firmy Microsoft
description: Pobierz informacje i przykładowy kod w celu szybkiego Rozpoczynanie pracy przy użyciu interfejsu API rozpoznawania emocji — warstwa JavaScript w usługach kognitywnych.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 2578b0212f9b4a6483402074d7c9eff73e51ca6b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347552"
---
# <a name="emotion-api-javascript-quick-start"></a>Szybki Start API JavaScript rozpoznawania emocji — warstwa

> [!IMPORTANT]
> Interfejs API podglądu kończy się 30 października 2017 r. Testowanie nowego [wideo indeksatora interfejsu API w wersji zapoznawczej](https://azure.microsoft.com/services/cognitive-services/video-indexer/) można łatwo wyodrębnić szczegółowych informacji z wideo i celu ułatwienia pracy funkcję odnajdowania zawartości, takich jak wyniki wyszukiwania został określony poprzez wykrycie słowa rozmowy, kroje znaków i emocji. [Dowiedz się więcej](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ten artykuł zawiera informacje i przykłady kodu, aby szybko rozpocząć korzystanie z [metody rozpoznaje interfejsu API rozpoznawania emocji — warstwa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) JavaScript do rozpoznawania emocji wyrażonych przez osoby na obrazie.

## <a name="prerequisite"></a>Wymagania wstępne
* Uzyskaj swój klucz bezpłatnej subskrypcji [tutaj](https://azure.microsoft.com/try/cognitive-services/), lub jeśli masz subskrypcję platformy Azure tworzenie zasobu interfejsu API rozpoznawania emocji — warstwa i Uzyskaj klucz subskrypcji i punktu końcowego.

![Utwórz zasób interfejsu API rozpoznawania emocji — warstwa](../Images/create-resource.png)

## <a name="recognize-emotions-javascript-example-request"></a>Rozpoznaje emocji JavaScript przykładowe żądanie

Skopiuj następujące i zapisz go w pliku takich jak `test.html`. Żądanie zmiany `url` lokalizacji, w którym uzyskać klucze subskrypcji i zastąp wartość "Ocp-Apim-subskrypcji — klucz" po klucz ważnej subskrypcji. Te można znaleźć w portalu Azure w sekcji Przegląd i klucze interfejsu API rozpoznawania emocji — warstwa zasobu, odpowiednio. 

![Punkt końcowy interfejsu API](../Images/api-url.png)

![Klucz interfejsu API subskrypcji](../Images/keys.png)

i zmień treść żądania do lokalizacji obrazu, który ma być używany. Aby uruchomić przykładowe, przeciągnij i upuść plik w przeglądarce.

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

## <a name="recognize-emotions-sample-response"></a>Rozpoznaje emocji przykładowa odpowiedź
Pomyślnego połączenia zwraca tablicę wpisów krój oraz wyniki emocji skojarzony, uszeregowane według rozmiaru prostokąt krój w kolejności malejącej. Pustą odpowiedź wskazuje, że zostały wykryte nie kroje. Wpis emocji zawiera następujące pola:
* faceRectangle - lokalizacji prostokąta powierzchni w obrazie.
* wyniki - emocji wyniki dla każdej powierzchni w obrazie. 

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
