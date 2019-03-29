---
title: Za pomocą modułu usług — usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z modułu usługi Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e614758a91cb3ff02822eeeeb8ae7e80d2123e5d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578734"
---
# <a name="using-the-azure-maps-services-module"></a>Za pomocą modułu usług mapy platformy Azure

Zestaw SDK usługi Azure Maps w sieci Web udostępnia moduł usług, który jest Biblioteka pomocnicza, która ułatwia korzystanie z usług REST mapy platformy Azure w sieci web lub aplikacji w technologii Node.js przy użyciu języka JavaScript lub TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>Za pomocą modułu usług na stronie sieci web

1. Utwórz nowy plik HTML.
2. Ładowanie w module usług mapy platformy Azure. Można to zrobić przy użyciu jednej z dwóch opcji;

    a. Używanie sieci CDN globalnie hostowanej wersję modułu usługi Azure Maps przez dodanie odwołania do skryptu w taki sposób, aby <head> element pliku:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. Alternatywnie obciążenia kod źródłowy zestawu SDK sieci Web mapy platformy Azure lokalnie przy użyciu [azure maps stałej](https://www.npmjs.com/package/azure-maps-rest) NPM pakietu, a następnie Hostuj go z aplikacją. Ten pakiet zawiera również definicjach TypeScript.
    
    > npm install azure maps — stałej
    
    Następnie dodaj odwołania do skryptu w taki sposób, aby `<head>` element pliku:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Aby zainicjować klienta adres URL punktu końcowego, należy najpierw utworzyć potok usługi uwierzytelniania. Użyj własnego klucza konta usługi Azure Maps lub poświadczeń usługi Azure Active Directory (AAD) do uwierzytelniania klienta usługi wyszukiwania. W tym przykładzie zostanie utworzony klient adres URL usługi wyszukiwania. Jeśli do uwierzytelniania, przy użyciu klucza subskrypcji:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Jeśli do uwierzytelniania, przy użyciu usługi Azure Active Directory (AAD):

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
        try {
            console.log("Renewing token");
            var token = await getAadToken();
            tokenCredential.token = token;
            tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
        } catch (error) {
            console.log("Caught error when renewing token");
            clearTimeout(tokenRenewalTimer);
            throw error;
        }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Aby uzyskać więcej informacji, zobacz [uwierzytelniania za pomocą usługi Azure Maps](azure-maps-authentication.md).

4. W poniższym kodzie użyto klientowi adres URL usługi wyszukiwania nowo utworzony geocode na adres, "1 Microsoft sposób, Redmond, WA" przy użyciu `searchAddress` funkcji i wyświetlić wyniki w postaci tabeli w treści strony. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');
      
      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
                    response.results[i].address.freeformAddress, 
                    '</td><td>', 
                    response.results[i].position.lat,
                    '</td><td>', 
                    response.results[i].position.lon,
                    '</td></tr>');
      }
      
      html.push('</table>');
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Poniżej przedstawiono przykładowy kod w pełni uruchomiona:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Za pomocą modułu usług" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>za pomocą modułu usług</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, korzystających z modułu usług:

> [!div class="nextstepaction"]
> [Pokaż wyniki wyszukiwania na mapie](./map-search-location.md)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)