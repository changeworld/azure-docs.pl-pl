---
title: Pobierz model z wywołaniem REST wC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: cc8f01d5608b36521185695cd5f7be531ba7aeaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503766"
---
## <a name="prerequisites"></a>Wymagania wstępne

* Klucz początkowy.
* Zaimportuj aplikację [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z repozytorium GitHub-Services-Language-zrozumieć.
* Identyfikator aplikacji LUIS dla zaimportowanej aplikacji TravelAgent. Identyfikator aplikacji jest wyświetlany na pulpicie nawigacyjnym aplikacji.
* Identyfikator wersji w aplikacji, która odbiera wyrażenia długości. Domyślny identyfikator to „0.1”.
* Język programowania [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Plik JSON z przykładowymi wypowiedziami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Pobieranie klucza usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Programowo Zmień model

Użyj języka go, aby dodać [interfejs API](https://aka.ms/luis-apim-v3-authoring) jednostki uczenia maszynowego do aplikacji. 

1. Utwórz nowy plik o nazwie `model.js`. Dodaj następujący kod:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const reponse = await requestpromise(options)
        console.log(reponse.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const reponse = await requestpromise(options)
        console.log(reponse.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Zastąp następujące wartości:

    * `YOUR-KEY` z kluczem początkowym
    * `YOUR-ENDPOINT` z punktem końcowym, na przykład `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` z IDENTYFIKATORem aplikacji

1. Za pomocą wiersza polecenia w tym samym katalogu, w którym został utworzony plik, wprowadź następujące polecenie, aby uruchomić plik:

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>Klucze usługi LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start Usuń plik z systemu plików. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dla aplikacji](../luis-concept-best-practices.md)