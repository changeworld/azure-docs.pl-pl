---
title: Wprowadzenie do sterowania mapami internetowymi | Mapy platformy Microsoft Azure
description: Dowiedz się, jak korzystać z biblioteki JavaScript po stronie klienta z kontrolą map Microsoft Azure Maps do renderowania map i osadzonych funkcji usługi Azure Maps w aplikacji sieci Web lub aplikacji mobilnej.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335249"
---
# <a name="use-the-azure-maps-map-control"></a>Korzystanie z kontrolki mapy usługi Azure Maps

Biblioteka JavaScript po stronie klienta kontroli map umożliwia renderowanie map i osadzonych funkcji usługi Azure Maps w aplikacji sieci Web lub aplikacji mobilnej.

## <a name="create-a-new-map-in-a-web-page"></a>Tworzenie nowej mapy na stronie internetowej

Mapę można osadzić na stronie internetowej za pomocą biblioteki JavaScript po stronie klienta kontroli mapy.

1. Utwórz nowy plik HTML.

2. Załaduj zestaw SDK usługi Azure Maps Web. Można wybrać jedną z dwóch opcji;

    * Użyj globalnie hostowanej wersji zestawu SDK sieci Web usługi Azure Maps, `<head>` dodając odwołania do języka JavaScript i arkusza stylów w elemencie pliku HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Załaduj kod źródłowy zestawu SDK sieci Web usługi Azure Maps lokalnie przy użyciu pakietu NPM [kontroli azure-maps](https://www.npmjs.com/package/azure-maps-control) i hostuj go za pomocą aplikacji. Ten pakiet zawiera również definicje TypeScript.

        > **npm zainstalować azure-maps-control**

       Następnie dodaj odwołania do arkusza stylów usługi Azure `<head>` Maps i odwołania do źródła skryptów do elementu pliku:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Definicje skryptów maszynowych można zaimportować do aplikacji, dodając następujący kod:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Aby renderować mapę tak, aby wypełniała całą treść `<style>` strony, `<head>` dodaj do elementu następujący element.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. W treści strony dodaj `<div>` element i nadaj mu `id` **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Aby zainicjować formant mapy, zdefiniuj nowy znacznik skryptu w treści html. Przekazywanie `id` w mapie `<div>` lub `HTMLElement` (na `document.getElementById('myMap')`przykład) jako pierwszy parametr podczas tworzenia `Map` wystąpienia klasy. Użyj własnego klucza konta usługi Azure Maps lub poświadczeń usługi Azure Active Directory (AAD) do uwierzytelniania mapy przy użyciu [opcji uwierzytelniania.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions) 

   Jeśli chcesz utworzyć konto lub znaleźć klucz, postępuj zgodnie z instrukcjami w [pozycję Utwórz konto](quick-demo-map-app.md#create-an-account-with-azure-maps) i [uzyskaj klucz podstawowy](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   Opcja **języka** określa język, który ma być używany dla etykiet map i formantów. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane języki](supported-languages.md). Jeśli do uwierzytelniania jest używany klucz subskrypcji, użyj następujących czynności:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   Jeśli używasz usługi Azure Active Directory (AAD) do uwierzytelniania, użyj następujących czynności:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   Listę przykładów przedstawiających integrowanie usługi Azure Active Directory (AAD) z usługą Azure Maps można znaleźć [tutaj.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples) 
    
   Aby uzyskać więcej informacji, zobacz dokument [Uwierzytelniania za pomocą usługi Azure Maps,](azure-maps-authentication.md) a także [przykłady uwierzytelniania usługi Azure Maps Azure AD.](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)

6. Opcjonalnie dodanie następujących elementów metatagu do głowicy strony może być pomocne:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Łącząc to wszystko razem plik HTML powinien wyglądać jak następujący kod:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Otwórz plik w przeglądarce internetowej i wyświetl renderowane mapy. Powinien wyglądać jak na poniższym obrazku:

   ![Obraz mapy przedstawiający renderowany wynik](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Usługa Azure Maps udostępnia dwa różne sposoby ustawiania widoku języka i widoku regionalnego dla renderowanego mapy. Pierwszą opcją jest dodanie tych `atlas` informacji do globalnej przestrzeni nazw, co spowoduje, że wszystkie wystąpienia kontroli mapy w aplikacji domyślnie do tych ustawień. Poniżej ustawia język francuski (fr-FR) i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Drugą opcją jest przekazanie tych informacji do opcji mapy podczas ładowania mapy w ten sposób:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Dzięki zestawowi Web SDK można załadować wiele wystąpień mapy na tej samej stronie z różnymi ustawieniami języka i regionu. Ponadto te ustawienia mogą być aktualizowane po `setStyle` załadowaniu mapy za pomocą funkcji mapy. 

Oto przykład usługi Azure Maps z językiem ustawionym na "fr-FR" i widokiem regionalnym ustawionym na "Auto".

![Obraz mapy z etykietami w języku francuskim](./media/how-to-use-map-control/websdk-localization.png)

Pełna lista obsługiwanych języków i widoków regionalnych jest udokumentowana [tutaj](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Pomoc techniczna w chmurze platformy Azure dla instytucji rządowych

Zestaw Azure Maps Web SDK obsługuje chmurę dla instytucji rządowych platformy Azure. Wszystkie adresy URL JavaScript i CSS używane do uzyskiwania dostępu do zestawu SDK sieci Web usługi Azure Maps pozostają takie same. Aby połączyć się z wersją platformy Azure Maps w chmurze platformy Azure Maps, należy wykonać następujące zadania.

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

Dowiedz się, jak tworzyć mapę i wchodzić w interakcje z nią:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

Dowiedz się, jak stylizować mapę:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](choose-map-style.md)

Aby dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Aby uzyskać listę przykładów przedstawiających sposób integrowania usługi Azure Active Directory (AAD) z usługą Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
