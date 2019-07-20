---
title: 'Szybki start: Uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu zestawu SDK wyszukiwania wizualnego Bing dla platformy Node.js'
titleSuffix: Azure Cognitive Services
description: Przekazywanie obrazu przy użyciu zestawu SDK wyszukiwania wizualnego Bing oraz uzyskiwanie szczegółowych informacji na jego temat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: f0dd0bb82f899146956b5671497d298c7ce076ef
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360522"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>Szybki start: Uzyskiwanie szczegółowych informacji dotyczących obrazu przy użyciu zestawu SDK wyszukiwania wizualnego Bing dla platformy Node.js

Użyj tego przewodnika Szybki start, aby rozpocząć uzyskiwanie szczegółowych danych obrazu w usłudze wyszukiwania wizualnego Bing przy użyciu zestawu SDK dla platformy Node.js. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, zestaw SDK umożliwia łatwe integrowanie usługi z Twoimi aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js). 

## <a name="prerequisites"></a>Wymagania wstępne
* [Node.js](https://www.nodejs.org/)
* Zestaw SDK wyszukiwania wizualnego Bing dla platformy Node.js
    * Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wizualnego Bing, uruchom następujące polecenia:
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-search-visualSearch`.


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku JavaScript w ulubionym środowisku IDE lub edytorze i dodaj następujące wymagania. Następnie utwórz zmienne dla klucza subskrypcji, identyfikatora konfiguracji niestandardowej i ścieżki do pliku obrazu, który chcesz przekazać. 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. Utwórz wystąpienie klienta.

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>Wyszukiwanie obrazów

1. Użyj metody `fs.createReadStream()` do odczytu w pliku obrazu i utwórz zmienne dla żądania wyszukiwania i wyników. Następnie użyj klienta do wyszukiwania obrazów.

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. Przeanalizuj wyniki poprzedniego zapytania:

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-visual-search-single-page-app.md)
