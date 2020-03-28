---
title: " Tworzenie jednostronicowej aplikacji sieci Web — wyszukiwanie wizualne Bing"
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zintegrować interfejs API wyszukiwania wizualnego Bing z jednostronicową aplikacją sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.openlocfilehash: 83cdaecfb819fb1f4677b051f87e23e0e03daef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370096"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji sieci Web wyszukiwania wizualnego

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje dotyczące obrazu. Możesz przesłać obraz lub podać adres URL do jednego z nich. Statystyki są wizualnie podobne obrazy, źródła zakupów, strony internetowe, które zawierają obraz, i więcej. Statystyki zwracane przez interfejs API wyszukiwania wizualnego Bing są podobne do tych wyświetlanych na Bing.com/images.

W tym samouczku wyjaśniono, jak rozszerzyć jednostronicową aplikację sieci Web dla interfejsu API wyszukiwania obrazów Bing. Aby wyświetlić ten samouczek lub uzyskać kod źródłowy używany tutaj, zobacz [Samouczek: Tworzenie aplikacji jednostronicowej dla interfejsu API wyszukiwania obrazów Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Pełny kod źródłowy dla tej aplikacji (po rozszerzeniu go do korzystania z interfejsu API wyszukiwania wizualnego Bing), jest dostępny na [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Wywołanie interfejsu API wyszukiwania wizualnego usługi Bing i obsługa odpowiedzi

Edytuj samouczek wyszukiwania obrazów Bing i dodaj `<script>` następujący kod na `</script>` końcu elementu (i przed tagiem zamykającym). Poniższy kod obsługuje wizualną odpowiedź wyszukiwania z interfejsu API, iteruje za pośrednictwem wyników i wyświetla je:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Poniższy kod wysyła żądanie wyszukiwania do interfejsu API, `handleVisualSearchResponse()`przy użyciu odbiornika zdarzeń do wywołania:

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Przechwytywanie tokenu szczegółowych informacji

Dodaj następujący kod `searchItemsRenderer` do obiektu. Ten kod dodaje link **znajdź podobne**, który po kliknięciu wywołuje funkcję `bingVisualSearch`. Funkcja odbiera `imageInsightsToken` jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Wyświetlenie podobnych obrazów

Dodaj następujący kod HTML w wierszu 601. Ten kod znaczników dodaje element do wyświetlania wyników wywołania interfejsu API wyszukiwania wizualnego Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Po umieszczeniu na miejscu całego nowego kodu JavaScript i wszystkich elementów HTML wyniki wyszukiwania są wyświetlane z linkiem **znajdź podobne**. Kliknij ten link, aby wypełnić sekcję **Podobne** obrazami podobnymi do obrazu wybranego przez Ciebie. W celu wyświetlenia tych obrazów może być konieczne rozwinięcie sekcji **Podobne**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Przycinanie obrazu za pomocą zestawu Bing Visual Search SDK dla języka C #](tutorial-visual-search-crop-area-results.md)
