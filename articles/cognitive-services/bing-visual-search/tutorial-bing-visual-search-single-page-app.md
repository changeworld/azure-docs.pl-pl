---
title: 'Samouczek: tworzenie jednostronicowej aplikacji internetowej — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Pokazuje sposób użycia interfejsu API wyszukiwania wizualnego Bing w jednostronicowej aplikacji internetowej.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: bking
ms.openlocfilehash: a63107b86e82bf0bcd89523588414b45e6e21d5a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465201"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Samouczek: jednostronicowa aplikacja internetowa wyszukiwania wizualnego

Interfejs API wyszukiwania wizualnego Bing udostępnia środowisko podobne do szczegółów obrazu pokazanego w witrynie Bing.com/images. Za pomocą wyszukiwania wizualnego można określić obraz i uzyskać szczegółowe informacje dotyczące tego obrazu, takie jak podobne obrazy, źródła zakupów, strony internetowe zawierające ten obraz i nie tylko. 

W tym samouczku rozbudujemy jednostronicową aplikację internetową z samouczka wyszukiwania obrazów Bing (zobacz [Jednostronicowa aplikacja internetowa](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)). Aby uzyskać pełny kod źródłowy potrzebny do rozpoczęcia tego samouczka, zobacz [Jednostronicowa aplikacja internetowa (kod źródłowy)](../Bing-Image-Search/tutorial-bing-image-search-single-page-app-source.md). Aby uzyskać końcowy kod źródłowy tego samouczka, zobacz [Jednostronicowa aplikacja internetowa wyszukiwania wizualnego](tutorial-bing-visual-search-single-page-app-source.md).

Wykonane zadania:

> [!div class="checklist"]
> * Wywołanie interfejsu API wyszukiwania wizualnego Bing za pomocą tokenu szczegółowych informacji obrazu
> * Wyświetlenie podobnych obrazów

## <a name="call-bing-visual-search"></a>Wywołanie wyszukiwania wizualnego Bing
Edytuj samouczek wyszukiwania obrazów Bing i dodaj następujący kod na końcu elementu skryptu w wierszu 409. Ten kod wywołuje interfejs API wyszukiwania wizualnego Bing i wyświetla wyniki.

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

## <a name="capture-insights-token"></a>Przechwytywanie tokenu szczegółowych informacji
Dodaj następujący kod do obiektu `searchItemsRenderer` w wierszu 151. Ten kod dodaje link **znajdź podobne**, który po kliknięciu wywołuje funkcję `bingVisualSearch`. Funkcja otrzymuje token imageInsightsToken jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Wyświetlenie podobnych obrazów
Dodaj następujący kod HTML w wierszu 601. Ten kod narzutu dodaje element używany do wyświetlania wyników wywołania interfejsu API wyszukiwania wizualnego Bing.

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Po umieszczeniu na miejscu całego nowego kodu JavaScript i wszystkich elementów HTML wyniki wyszukiwania są wyświetlane z linkiem **znajdź podobne**. Kliknij ten link, aby wypełnić sekcję **Podobne** obrazami podobnymi do obrazu wybranego przez Ciebie. W celu wyświetlenia tych obrazów może być konieczne rozwinięcie sekcji **Podobne**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Źródło jednostronicowej aplikacji internetowej wyszukiwania wizualnego](tutorial-bing-visual-search-single-page-app-source.md)
> [Dokumentacja interfejsu API wyszukiwania wizualnego Bing](https://aka.ms/bingvisualsearchreferencedoc)