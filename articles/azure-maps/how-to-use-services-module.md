---
title: Korzystanie z modułu Usługi Azure Maps | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak korzystać z usług MICROSOFT Azure Maps REST przy użyciu modułu usług Usługi Usługi Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988740"
---
# <a name="use-the-azure-maps-services-module"></a>Korzystanie z modułu usług Usługi Azure Maps

Zestaw Azure Maps Web SDK udostępnia *moduł usług.* Ten moduł jest biblioteką pomocniczą, która ułatwia korzystanie z usług REST usługi Azure Maps w aplikacjach sieci Web lub Node.js przy użyciu języka JavaScript lub TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Korzystanie z modułu usług na stronie sieci Web

1. Utwórz nowy plik HTML.
1. Załaduj moduł usług Usługi Usługi Azure Maps. Można go załadować na jeden z dwóch sposobów:
    - Użyj globalnie hostowane, usługi Azure Content Delivery Network wersji modułu usług Usługi Usługi Usługi Azure Maps. Dodaj odwołanie do `<head>` skryptu do elementu pliku:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Alternatywnie należy załadować moduł usług dla kodu źródłowego zestawu SDK sieci Web usługi Azure Maps lokalnie przy użyciu pakietu npm [azure-maps-rest,](https://www.npmjs.com/package/azure-maps-rest) a następnie hostować go za pomocą aplikacji. Ten pakiet zawiera również definicje TypeScript. Użyj tego polecenia:
    
        > **npm install azure-maps-rest**
    
        Następnie dodaj odwołanie do `<head>` skryptu do elementu pliku:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Utwórz potok uwierzytelniania. Potok musi zostać utworzony, zanim będzie można zainicjować punkt końcowy klienta adresu URL usługi. Użyj własnego klucza konta usługi Azure Maps lub poświadczeń usługi Azure Active Directory (Azure AD) do uwierzytelniania klienta usługi Usługi Usługi Azure Maps. W tym przykładzie zostanie utworzony klient adresu URL usługi wyszukiwania. 

    Jeśli używasz klucza subskrypcji do uwierzytelniania:

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

    Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie za pomocą usługi Azure Maps](azure-maps-authentication.md).

1. Poniższy kod używa nowo utworzonego klienta adresu URL usługi Azure Maps Search do geokodowania adresu: "1 Microsoft Way, Redmond, WA". Kod używa `searchAddress` funkcji i wyświetla wyniki jako tabelę w treści strony.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
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

    Oto pełny, uruchomiony przykład kodu:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Korzystanie z modułu usług" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>przy użyciu modułu usług</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Pomoc techniczna w chmurze platformy Azure dla instytucji rządowych

Zestaw Azure Maps Web SDK obsługuje chmurę dla instytucji rządowych platformy Azure. Wszystkie adresy URL JavaScript i CSS używane do uzyskiwania dostępu do zestawu SDK sieci Web usługi Azure Maps pozostają takie same, jednak aby połączyć się z wersją chmury azure dla instytucji rządowych platformy Azure Maps, należy wykonać następujące zadania.

Korzystając z kontrolki mapy interaktywnej, dodaj następujący wiersz `Map` kodu przed utworzeniem wystąpienia klasy. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Pamiętaj, aby podczas uwierzytelniania mapy i usług używać szczegółów uwierzytelniania usługi Azure Maps z platformy chmurowej platformy Azure dla instytucji rządowych.

Podczas korzystania z modułu usług, domena dla usług musi być ustawiona podczas tworzenia wystąpienia punktu końcowego adresu URL interfejsu API. Na przykład poniższy kod tworzy `SearchURL` wystąpienie klasy i wskazuje domenę do chmury azure dla instytucji rządowych.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Jeśli dostęp do usług REST usługi Azure Maps `atlas.azure.us`ma bezpośredni dostęp, zmień domenę adresu URL na . Na przykład, jeśli korzystasz z usługi interfejsu `https://atlas.microsoft.com/search/` API `https://atlas.azure.us/search/`wyszukiwania, zmień domenę adresu URL na .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [MapuRL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Funkcja SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Routeurl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential (TokenCredential)](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Aby uzyskać więcej przykładów kodu, które używają modułu usług, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Pokazywale wyszukiwania na mapie](./map-search-location.md)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](./map-route.md)
