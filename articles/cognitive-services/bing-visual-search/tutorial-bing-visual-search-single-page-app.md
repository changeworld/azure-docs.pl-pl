---
title: 'Samouczek: tworzenie jednostronicowej aplikacji internetowej — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Pokazuje sposób użycia interfejsu API wyszukiwania wizualnego Bing w jednostronicowej aplikacji internetowej.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 10/04/2017
ms.author: aahi
ms.openlocfilehash: fe7159e88bd70ba8af23909559264fa5f210ef10
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443902"
---
# <a name="tutorial-visual-search-single-page-web-app"></a>Samouczek: jednostronicowa aplikacja internetowa wyszukiwania wizualnego

Interfejs API wyszukiwania wizualnego Bing udostępnia środowisko podobne do szczegółów obrazu pokazanego w witrynie Bing.com/images. Za pomocą wyszukiwania wizualnego można określić obraz i uzyskać szczegółowe informacje dotyczące tego obrazu, takie jak podobne obrazy, źródła zakupów, strony internetowe zawierające ten obraz i nie tylko. 

Ten samouczek wymaga rozpoczęcia subskrypcji w warstwie cenowej S9, jak pokazano w temacie [Cennik usług Cognitive Services — interfejs API wyszukiwania Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Aby rozpocząć subskrypcję w witrynie Azure Portal:
1. Wprowadź tekst „BingSearchV7” w polu tekstowym w górnej części witryny Azure Portal, w którym wyświetlany jest komunikat `Search resources, services, and docs`.  
2. W witrynie Marketplace z listy rozwijanej wybierz pozycję `Bing Search v7`.
3. Wprowadź wartość `Name` dla nowego zasobu.
4. Wybierz subskrypcję `Pay-As-You-Go`.
5. Wybierz warstwę cenową `S9`.
6. Kliknij pozycję `Enable`, aby rozpocząć subskrypcję.

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