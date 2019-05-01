---
title: Użyj modułu services — Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z modułu usługi Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: f3650d4db06a763308939e9fb1a98fddb0eaa04a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725910"
---
# <a name="use-the-azure-maps-services-module"></a>Użyj modułu usługi Azure Maps

Zawiera zestaw SDK usługi sieci Web Azure map *moduł usług*. Ten moduł jest Biblioteka pomocnicza, który można łatwo korzystać z usług REST mapy platformy Azure, w sieci web lub aplikacji w technologii Node.js przy użyciu języka JavaScript lub TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Używaj modułu usług na stronie sieci Web

1. Utwórz nowy plik HTML.
1. Załaduj moduł usługi Azure Maps. Możesz załadować je w jeden z dwóch sposobów:
    - Użyj usługi Azure Content Delivery Network globalnie hostowanej wersję modułu usługi Azure Maps. Dodaj odwołanie do skryptu `<head>` element pliku:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - Alternatywnie obciążenia kod źródłowy zestawu SDK sieci Web mapy platformy Azure lokalnie, używając [azure maps stałej](https://www.npmjs.com/package/azure-maps-rest) npm pakietu, a następnie Hostuj je z aplikacją. Ten pakiet zawiera również definicjach TypeScript. Użyj tego polecenia:
    
        > **npm install azure maps — stałej**
    
        Następnie należy dodać odwołania do skryptu w taki sposób, aby `<head>` element pliku:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Instrukcje tworzenia potoku usługi uwierzytelniania. Przed można zainicjować klienta adres URL punktu końcowego, należy utworzyć potok. Użyj własnego klucza konta usługi Azure Maps lub poświadczeń usługi Azure Active Directory (Azure AD) do uwierzytelniania klienta usługi Azure Search mapy. W tym przykładzie zostanie utworzony klient adres URL usługi wyszukiwania. 

    Jeśli klucz subskrypcji używany do uwierzytelniania:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Jeśli używasz usługi Azure AD do uwierzytelniania:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
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

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
    retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        // Use the signed-in auth context to get a token.
        return new Promise((resolve, reject) => {
            // The resource should always be https://atlas.microsoft.com/.
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
        // Decode the JSON Web Token (JWT) to get the expiration time stamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        // Return the milliseconds remaining until the token must be renewed.
        // Reduce the time until renewal by 5 minutes to avoid using an expired token.
        // The exp property is the time stamp of the expiration, in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Aby uzyskać więcej informacji, zobacz [uwierzytelniania za pomocą usługi Azure Maps](azure-maps-authentication.md).

1. W poniższym kodzie użyto nowo utworzonego klienta usługi Azure Search adres URL do geokodowania adres: "1 Microsoft sposób, Redmond, WA". Kod używa `searchAddress` działać, a następnie wyświetla wyniki w postaci tabeli w treści strony.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];

      // Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');

      // Create a table of the results.
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

      // Add the resulting HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Oto pełny uruchamianie przykładowego kodu:

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

Aby uzyskać więcej przykładów kodu, korzystających z modułu usług zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Pokaż wyniki wyszukiwania na mapie](./map-search-location.md)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)