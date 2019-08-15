---
title: Wprowadzenie do kontrolki mapy sieci Web w Azure Maps | Microsoft Docs
description: Dowiedz się, jak używać biblioteki kodu JavaScript po stronie klienta w usłudze Azure Maps map.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a152f32ef7d68686539b7d6edee57db8a5da9553
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976240"
---
# <a name="use-the-azure-maps-map-control"></a>Korzystanie z kontrolki mapy Azure Maps

Biblioteka JavaScript po stronie klienta kontrolka mapy umożliwia renderowanie map i osadzonych funkcji Azure Maps w aplikacji sieci Web lub mobilnej.

## <a name="create-a-new-map-in-a-web-page"></a>Utwórz nową mapę na stronie sieci Web

Mapę można osadzić na stronie sieci Web przy użyciu kontrolka mapy biblioteki JavaScript po stronie klienta.

1. Utwórz nowy plik HTML.

2. Załaduj do Azure Maps Web SDK. Można to zrobić przy użyciu jednej z dwóch opcji:

    a. Użyj globalnej hostowanej wersji usługi CDN Azure Maps Web SDK, dodając punkty końcowe URL do arkusza stylów i odwołania do skryptu w `<head>` elemencie pliku:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Alternatywnie Załaduj kod źródłowy zestawu SDK sieci Web Azure Maps lokalnie przy użyciu pakietu [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm i hostowania go wraz z Twoją aplikacją. Ten pakiet zawiera również definicje języka TypeScript.

    > npm Install Azure-Maps-Control

    Następnie Dodaj odwołania do arkusza stylów Azure Maps i odwołania do źródła skryptów do `<head>` elementu pliku:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Aby renderować mapę tak, aby wypełniał całą treść strony, Dodaj następujący `<style>` element `<head>` do elementu.

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

4. W treści strony Dodaj `<div>` element i nadaj `id` mu **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Aby zainicjować formant mapy, zdefiniuj nową sekcję w treści HTML i Utwórz skrypt. `<div>` `document.getElementById('myMap')`Przekaż do mapy lub`HTMLElement` (na przykład`Map` ) jako pierwszy parametr podczas tworzenia wystąpienia klasy. `id` Użyj własnego klucza konta Azure Maps lub poświadczeń usługi Azure Active Directory (AAD), aby uwierzytelnić mapę przy użyciu [opcji uwierzytelniania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Jeśli musisz utworzyć konto lub znaleźć klucz, zobacz [jak zarządzać kontem i kluczami Azure Maps](how-to-manage-account-keys.md). Opcja **Language** określa język, który ma być używany dla etykiet i kontrolek mapy. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane języki](supported-languages.md). W przypadku korzystania z klucza subskrypcji na potrzeby uwierzytelniania.

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

    W przypadku korzystania z Azure Active Directory (AAD) do uwierzytelniania:

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
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Aby uzyskać więcej informacji, zobacz artykuł [uwierzytelnianie w programie Azure Maps](azure-maps-authentication.md) .

6. Opcjonalnie można dowiedzieć się, jak dodać następujące elementy meta tag do nagłówka strony przydatne:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Umieszczenie go wszystkie razem plik HTML powinien wyglądać podobnie do następującego kodu:

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

8. Otwórz plik w przeglądarce internetowej i Wyświetl renderowane mapowanie. Powinien wyglądać podobnie do następującego kodu:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Jak używać kontrolki mapy" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>jak używać kontrolki mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Azure Maps oferuje dwa różne sposoby ustawiania języka i widoku regionalnego mapy. Pierwsza opcja polega na dodaniu tych informacji do globalnej `atlas` przestrzeni nazw, co spowoduje, że wszystkie wystąpienia formantów mapy w aplikacji domyślnie przeprowadzą do tych ustawień. Poniżej ustawia język francuski ("fr-FR") i widok regionalny na "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druga opcja polega na przejściu tych informacji do opcji mapy podczas ładowania mapy, jak:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: 'msft.ccsctp.net'
    }
});
```

> [!Note]
> Korzystając z zestawu SDK sieci Web, można załadować wiele wystąpień map na tej samej stronie z różnymi ustawieniami języka i regionu. Ponadto te ustawienia można aktualizować po załadowaniu mapy przy użyciu `setStyle` funkcji mapy. 

Poniżej znajduje się przykład Azure Maps z językiem ustawionym na "fr-FR" i widokiem regionalnym ustawionym na "Auto".

![Obraz mapy przedstawiający etykiety w języku francuskim](./media/how-to-use-map-control/websdk-localization.png)

Pełna lista obsługiwanych języków i widoków regionalnych została udokumentowana [tutaj](supported-languages.md).

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