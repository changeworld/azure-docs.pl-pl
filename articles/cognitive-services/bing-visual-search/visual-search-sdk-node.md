---
title: 'Szybki start: zestaw SDK wyszukiwania wizualnego Bing, Node'
titleSuffix: Azure Cognitive Services
description: Konfigurowanie aplikacji konsolowej zestawu SDK wyszukiwania wizualnego na platformie Node.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: v-gedod
ms.openlocfilehash: 77b8f956ca2a6985121cb5dbfebc2755fa73a35c
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685977"
---
# <a name="quickstart-bing-visual-search-sdk-node"></a>Szybki start: zestaw SDK wyszukiwania wizualnego Bing na platformie Node 

Zestaw SDK wyszukiwania wizualnego Bing używa funkcjonalności interfejsu API REST na potrzeby żądań internetowych i analizowania wyników.
[Kod źródłowy przykładów zestawów SDK wyszukiwania wizualnego Bing na platformie Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) jest dostępny w serwisie Git Hub.

Scenariusze kodu są udokumentowane w następujących sekcjach:
* [Klient wyszukiwania wizualnego](#client)
* [Kompletna aplikacja konsolowa](#complete)

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz interfejsu API usług Cognitive Services jest wymagany do uwierzytelniania wywołań zestawu SDK. Utwórz konto, aby otrzymać [klucz bezpłatnej wersji próbnej](https://azure.microsoft.com/try/cognitive-services/?api=search-api-v7). Klucz wersji próbnej obowiązuje przez siedem dni i oferuje jedno wywołanie na sekundę. W przypadku scenariuszy produkcyjnych [kup klucz dostępu](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7). Zobacz również [informacje o cenach](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/visual/).

## <a name="application-dependencies"></a>Zależności aplikacji

Aby skonfigurować aplikację konsolową przy użyciu zestawu SDK wyszukiwania wizualnego Bing:
* Uruchom polecenie `npm install ms-rest-azure`
* Uruchom polecenie `npm install azure-cognitiveservices-search-visualSearch`.

<a name="client"></a>
## <a name="visual-search-client"></a>Klient wyszukiwania wizualnego
Aby utworzyć wystąpienie klienta `VisualSearchAPI`, dodaj dyrektywy using:
```
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;

```
Następnie utwórz wystąpienie klienta:
```
let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);

```
Użyj klienta do wyszukiwania obrazów:
```
let fileStream = fs.createReadStream('../Data/image.jpg');
let visualSearchRequest = JSON.stringify({});
let visualSearchResults;
try {
    visualSearchResults = await visualSearchApiClient.images.visualSearch({
        image: fileStream,
        knowledgeRequest: visualSearchRequest
    });
    console.log("Search visual search request with binary of image");
} catch (err) {
    console.log("Encountered exception. " + err.message);
}

```
Przeanalizuj wyniki poprzedniego zapytania:
```
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
<a name="complete"></a>
## <a name="complete-console-application"></a>Kompletna aplikacja konsolowa

Następująca aplikacja konsolowa wykonuje wcześniejszy kod, inne scenariusze i analizuje wyniki:
```
/*
 * Copyright (c) Microsoft Corporation. All rights reserved.
 * Licensed under the MIT License. See License.txt in the project root for
 * license information.
 */
'use strict';

const os = require("os");
const async = require('async');
const fs = require('fs');
const Search = require('azure-cognitiveservices-visualsearch');
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;


let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';

let credentials = new CognitiveServicesCredentials(keyVar);
let visualSearchApiClient = new Search.VisualSearchAPIClient(credentials);
let visualModels = visualSearchApiClient.models;
sample();

function sample() {
    async.series([
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let visualSearchRequest = JSON.stringify({});
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("Visual search request with binary of dog image");
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
            if (!visualSearchResults) {
                console.log("No visual search result data. ");
            } else {
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
            }
        },
        async function () {
            let fileStream = fs.createReadStream('../Data/image.jpg');
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    image: fileStream,
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with binary of image with JSON info of cropArea");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
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
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            let imageInfo = { url: "https://images.unsplash.com/photo-1512546148165-e50d714a565a?w=600&q=80" };
            let filters = { site: "pinterest.com" };
            let knowledgeRequest = { filters: filters };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo, knowledgeRequest: knowledgeRequest });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nSearch visual search request with imageInfo and filters in JSON text");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
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
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        async function () {
            var imageInsightsToken = "bcid_CA6BDBEA28D57D52E0B9D4B254F1DF0D*ccid_6J+8V1zi*thid_R.CA6BDBEA28D57D52E0B9D4B254F1DF0D";
            let cropArea = { top: 0.1, bottom: 0.5, left: 0.1, right: 0.9 };
            let imageInfo = { imageInsightsToken: imageInsightsToken, cropArea: cropArea };
            let visualSearchRequest = JSON.stringify({ imageInfo: imageInfo });
            let visualSearchResults;
            try {
                visualSearchResults = await visualSearchApiClient.images.visualSearch({
                    knowledgeRequest: visualSearchRequest
                });
                console.log("\r\nVisual search request with ImageInsightsToken and crop area of image");
                if (!visualSearchResults) {
                    console.log("No visual search result data.");
                }
                else {
                    // Visual Search results
                    if (visualSearchResults.image.imageInsightsToken) {
                        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
                    }
                    else {
                        console.log("Couldn't find image insights token!");
                    }

                    // List of tags
                    if (visualSearchResults.tags.length > 0) {
                        var firstTagResult = visualSearchResults.tags[0];
                        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);

                        // List of actions in first tag
                        if (firstTagResult.actions.length > 0) {
                            var firstActionResult = firstTagResult.actions[0];
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
                }
            } catch (err) {
                console.log("Encountered exception. " + err.message);
            }
        },
        function () {
            return new Promise((resolve) => {
                console.log(os.EOL);
                console.log("Finished running Visual-Search sample.");
                resolve();
            })
        }
    ], (err) => {
        throw (err);
        });
}

exports.sample = sample;
```

## <a name="next-steps"></a>Następne kroki

[Przykłady zastosowania zestawu SDK usług Cognitive Services na platformie .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
