---
title: Wprowadzenie do kontrolki mapy sieci Web | Mapy Microsoft Azure
description: Dowiedz się, jak używać biblioteki Microsoft Azure Maps mapy JavaScript po stronie klienta do renderowania map i osadzonych funkcji Azure Maps w aplikacji sieci Web lub mobilnej.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 90cd752ddd46999627de538abe3ca6b1926005aa
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264133"
---
# <a name="use-the-azure-maps-map-control"></a>Korzystanie z kontrolki mapy Azure Maps

Biblioteka JavaScript po stronie klienta kontrolka mapy umożliwia renderowanie map i osadzonych funkcji Azure Maps w aplikacji sieci Web lub mobilnej.

## <a name="create-a-new-map-in-a-web-page"></a>Utwórz nową mapę na stronie sieci Web

Mapę można osadzić na stronie sieci Web przy użyciu kontrolka mapy biblioteki JavaScript po stronie klienta.

1. Utwórz nowy plik HTML.

2. Załaduj do Azure Maps Web SDK. Można wybrać jedną z dwóch opcji:

a. Użyj globalnie hostowanej wersji zestawu SDK Azure Maps sieci Web, dodając punkty końcowe URL do arkusza stylów i odwołania do skryptu w `<head>` elemencie pliku:

```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
```

b. Załaduj kod źródłowy Azure Maps Web SDK lokalnie przy użyciu pakietu [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm i hostowania go wraz z Twoją aplikacją. Ten pakiet zawiera również definicje języka TypeScript.

> **npm Install Azure-Maps-Control**

Następnie Dodaj odwołania do Azure Maps arkusza stylów i odwołania do źródła skryptów do elementu `<head>` pliku:

```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
    <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
```

    >[!Note]
    > Typescript definitions can be imported into your application by adding the following code:
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Aby renderować mapę w taki sposób, aby wypełniał całą treść strony, Dodaj następujący `<style>` elementu do elementu `<head>`.

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

4. W treści strony Dodaj element `<div>` i nadaj mu `id` z **myMap**.

```HTML
    <body>
        <div id="myMap"></div>
    </body>
```

5. Aby zainicjować formant mapy, Zdefiniuj nowy tag skryptu w treści HTML. Przekaż `id` mapy `<div>` lub `HTMLElement` (na przykład `document.getElementById('myMap')`) jako pierwszy parametr podczas tworzenia wystąpienia klasy `Map`. Użyj własnego klucza konta Azure Maps lub poświadczeń usługi Azure Active Directory (AAD), aby uwierzytelnić mapę przy użyciu [opcji uwierzytelniania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

Jeśli musisz utworzyć konto lub znaleźć klucz, postępuj zgodnie z instrukcjami w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i [Uzyskiwanie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

Opcja **Language** określa język, który ma być używany dla etykiet i kontrolek mapy. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane języki](supported-languages.md). Jeśli używasz klucza subskrypcji do uwierzytelniania, użyj następującego polecenia:

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

Jeśli używasz usługi Azure Active Directory (AAD) do uwierzytelniania, użyj następującego polecenia:

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

Listę przykładów pokazujących sposób integrowania usługi Azure Active Directory (AAD) z usługą Azure Maps można znaleźć [tutaj](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
Aby uzyskać więcej informacji, zobacz artykuł [uwierzytelnianie w programie Azure Maps](azure-maps-authentication.md) , a także [przykłady uwierzytelniania Azure Maps usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Opcjonalnie można dowiedzieć się, jak dodać następujące elementy meta tag do nagłówka strony przydatne:

```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
```

7. umieszczenie go razem z plikiem HTML powinno wyglądać podobnie do następującego kodu:

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

8. Otwórz plik w przeglądarce internetowej i Wyświetl renderowane mapowanie. Powinien wyglądać podobnie do poniższego obrazu:

![Obraz mapy pokazujący renderowany wynik](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps oferuje dwa różne sposoby ustawiania języka i widoku regionalnego dla renderowanej mapy. Pierwsza opcja polega na tym, aby dodać te informacje do globalnej przestrzeni nazw `atlas`, co spowoduje, że wszystkie wystąpienia formantów mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawia język francuski ("fr-FR") i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy w następujący sposób:

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
> Korzystając z zestawu SDK sieci Web, można załadować wiele wystąpień map na tej samej stronie z różnymi ustawieniami języka i regionu. Ponadto te ustawienia można zaktualizować po załadowaniu mapy przy użyciu funkcji `setStyle` mapy. 

Poniżej znajduje się przykład Azure Maps z językiem ustawionym na "fr-FR" i widokiem regionalnym ustawionym na "Auto".

![Obraz mapy przedstawiający etykiety w języku francuskim](./media/how-to-use-map-control/websdk-localization.png)

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Obsługa Azure Government w chmurze

Azure Maps Web SDK obsługuje chmurę Azure Government. Wszystkie adresy URL JavaScript i CSS używane do uzyskiwania dostępu do zestawu SDK sieci Web Azure Maps pozostają takie same. Aby nawiązać połączenie z wersją Azure Government w chmurze platformy Azure Maps, należy wykonać następujące zadania.

W przypadku używania kontrolki mapy interaktywnej Dodaj następujący wiersz kodu przed utworzeniem wystąpienia klasy `Map`. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Podczas uwierzytelniania mapy i usług upewnij się, że Azure Maps szczegóły uwierzytelniania na platformie Azure Government Cloud.

W przypadku korzystania z modułu usługi domena dla usług musi być ustawiona podczas tworzenia wystąpienia punktu końcowego adresu URL interfejsu API. Na przykład poniższy kod tworzy wystąpienie klasy `SearchURL` i wskazuje domenę na Azure Government chmurę.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

W przypadku bezpośredniego uzyskiwania dostępu do Azure Maps usług REST Zmień domenę adresu URL na `atlas.azure.us`. Na przykład jeśli korzystasz z usługi interfejsu API wyszukiwania, Zmień domenę adresu URL z `https://atlas.microsoft.com/search/` na `https://atlas.azure.us/search/`.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć mapę i korzystać z niej:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

Dowiedz się, jak stylować mapę:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](choose-map-style.md)

Aby dodać więcej danych do mapy:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Aby uzyskać listę przykładów pokazujących, jak zintegrować Azure Active Directory (AAD) z Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)