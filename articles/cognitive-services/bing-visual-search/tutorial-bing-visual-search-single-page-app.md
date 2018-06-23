---
title: Wyszukiwania usługi Bing obrazu jednostronicowej aplikacji sieci Web | Dokumentacja firmy Microsoft
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Przedstawia sposób użycia interfejsu API wyszukiwania usługi Bing obrazu w aplikacji jednej strony sieci Web.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-brapel
ms.openlocfilehash: 303d7745167d2ea25fda083ed99881ac4e0a7ec7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348709"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Samouczek: Visual wyszukiwania jednostronicowej aplikacji sieci Web

Visual API wyszukiwania usługi Bing udostępnia środowisko podobne do szczegółów obraz wyświetlany na Bing.com/images. Z Visual wyszukiwania można określić obrazu i wrócić szczegółowych informacji o obrazie, takich jak obrazy wizualnie podobne, zakupów źródeł, stron sieci Web, który obejmuje obraz i inne. 

W tym samouczku rozszerza jednostronicowej aplikacji sieci web z samouczka wyszukiwania usługi Bing obrazu (zobacz [aplikacji jednej strony sieci Web](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Aby uzyskać pełny kod źródłowy można uruchomić w tym samouczku, zobacz [aplikacji jednej strony sieci Web (kodu źródłowego)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Aby kod źródłowy końcowego tego samouczka, zobacz [aplikacji sieci Web jednej strony wyszukiwania Visual](tutorial-bing-visual-search-single-page-app-source.md).

Zadania objęte są:

> [!div class="checklist"]
> * Wywołania z tokenem insights obrazu Visual API wyszukiwania usługi Bing
> * Wyświetlanie obrazów podobne

## <a name="call-bing-visual-search"></a>Wywołaj Visual wyszukiwania usługi Bing
Edytuj samouczek wyszukiwania usługi Bing obraz i Dodaj następujący kod na końcu elementu skryptu w wierszu 409. Ten kod wywołuje interfejs API Bing Visual wyszukiwania i wyświetla wyniki.

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

## <a name="capture-insights-token"></a>Przechwyć insights token
Dodaj następujący kod do `searchItemsRenderer` obiektu w wierszu 151. Ten kod dodaje **Znajdź podobne** link, który wywołuje `bingVisualSearch` działać po kliknięciu. Funkcja odbiera imageInsightsToken jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Wyświetlanie obrazów podobne
Dodaj następujący kod HTML w wierszu 601. Ten kod znaczników dodaje element używany do wyświetlania wyników wywołania Visual API wyszukiwania usługi Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Ze wszystkimi nowy kod JavaScript i elementów HTML w miejscu, wyniki wyszukiwania są wyświetlane z **Znajdź podobne** łącza. Kliknij łącze, aby wypełnić **podobny** sekcji z obrazami podobny do Ciebie. Może być konieczne rozwiń **podobny** sekcję, aby wyświetlić obrazy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Program Visual source aplikacji jednej strony wyszukiwania w sieci Web](tutorial-bing-visual-search-single-page-app-source.md)
> [odwołanie Visual API wyszukiwania usługi Bing](https://aka.ms/bingvisualsearchreferencedoc)