---
title: Jak używać kontrolki mapy platformy Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać biblioteki Javascript po stronie klienta usługi Azure Maps — formant mapa.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1d8d78ceb9cdb5e5dd205306eac00c8ca89e8d4a
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587943"
---
# <a name="use-the-azure-maps-map-control"></a>Korzystanie z kontrolki mapy usługi Azure Maps

Biblioteka języka Javascript po stronie klienta — formant mapa programu umożliwia renderowanie mapy i funkcji usługi Azure Maps osadzone w sieci web lub aplikacji mobilnej.

## <a name="create-a-new-map-in-a-web-page"></a>Tworzenie nowej mapy na stronie sieci web

Mapę można osadzić na stronie sieci web przy użyciu biblioteki Javascript po stronie klienta formantu mapy.

1. Utwórz nowy plik HTML.

2. Obciążenia w sieci Web usługi Azure Maps SDK. Można to zrobić przy użyciu jednej z dwóch opcji;
    
    a. Używanie sieci CDN globalnie hostowanej wersję zestawu SDK usługi sieci Web Azure Maps przez dodanie punkty końcowe adresu URL do arkusza stylów i skrypt odwołań w `<head>` element pliku:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. Alternatywnie obciążenia kod źródłowy zestawu SDK sieci Web mapy platformy Azure lokalnie przy użyciu [azure maps kontroli](https://www.npmjs.com/package/azure-maps-control) NPM pakietu, a następnie Hostuj go z aplikacją. Ten pakiet zawiera również definicjach TypeScript.

    > npm install azure maps — kontroli

    Następnie dodaj odwołania do usługi Azure Maps arkusza stylów i skrypt źródło odwołania do `<head>` element pliku:

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Aby renderować na mapie, tak aby wypełnił cała treść strony, Dodaj następujący element `<style>` elementu `<head>` elementu.

    ```html
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

4. W treści strony Dodaj `<div>` elementu i nadaj mu `id` z **myMap**. 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Aby zainicjować formantu mapy, zdefiniować nową sekcję w treści html i utworzyć skrypt. Użyj własnego klucza konta usługi Azure Maps lub poświadczeń usługi Azure Active Directory (AAD) do uwierzytelniania za pomocą mapy [opcje uwierzytelniania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Jeśli musisz utworzyć konto usługi lub znaleźć Twojego klucza, zobacz [jak zarządzać swoim kontem usługi Azure Maps i klucze](how-to-manage-account-keys.md). **Języka** opcja określa język do zastosowania etykiety mapy i kontrolek. Aby uzyskać więcej informacji na temat obsługiwanych języków, zobacz [obsługiwane języki](supported-languages.md). Jeśli do uwierzytelniania, przy użyciu klucza subskrypcji.

    ```html
    <script type='text/javascript'>
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

    Jeśli do uwierzytelniania, przy użyciu usługi Azure Active Directory (AAD):

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

6. Opcjonalnie może się okazać, dodając następujące elementy tag meta do Nagłówek strony przydatne:

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Łączenie wszystkiego razem plik HTML powinien wyglądać następująco:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
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
        
        <script type='text/javascript'>
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

8. Otwórz plik w przeglądarce sieci web i wyświetlić mapę renderowany. Jego powinien wyglądać następująco:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Jak używać kontrolki mapy" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>jak używać kontrolki mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
    </iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć i korzystać z mapy:

> [!div class="nextstepaction"]
> [Tworzenie mapy](map-create.md)

Dowiedz się, jak do nadawania stylu mapy:

> [!div class="nextstepaction"]
> [Wybierz style mapy](choose-map-style.md)
