---
title: Znajdowanie trasy przy użyciu usługi Azure Maps | Microsoft Docs
description: Znajdowanie trasy do punktu orientacyjnego przy użyciu usługi Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 09/04/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1ef4467862f47a833e0592c94c662170ca2946d8
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781453"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Znajdowanie trasy do punktu orientacyjnego przy użyciu usługi Azure Maps

W tym samouczku pokazano, jak używać konta usługi Azure Maps i zestawu Route Service SDK w celu znalezienia trasy do punktu orientacyjnego. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Ustawianie współrzędnych adresu
> * Wysyłanie do usługi Route Service zapytania dotyczącego wskazówek dojazdu do punktu orientacyjnego

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem wykonaj kroki poprzedniego samouczka, aby [utworzyć konto usługi Azure Maps](./tutorial-search-location.md#createaccount), a następnie [pobierz klucz subskrypcji dla swojego konta](./tutorial-search-location.md#getkey). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Tworzenie nowej mapy 
Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy. 

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapRoute.html**. 
2. Dodaj następujące składniki HTML do pliku:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css"/> 
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script> 
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.min.js?api-version=1"></script> 
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>

    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    W nagłówku HTML są osadzone lokalizacje zasobów dla plików CSS i JavaScript używanych przez bibliotekę usługi Azure Maps. Segment *script* w treści pliku HTML będzie zawierał śródwierszowy kod JavaScript uzyskujący dostęp do interfejsów API usługi Azure Maps.

3. Dodaj następujący kod JavaScript do bloku *script* w pliku HTML. Zastąp ciąg **\<your account key\>** kluczem podstawowym, skopiowanym z konta usługi Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Element **atlas.Map** zapewnia kontrolkę dla wizualnej interakcyjnej mapy internetowej i jest składnikiem interfejsu API kontrolki mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać. 

   ![Wyświetlanie podstawowej mapy](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Ustawianie punktu początkowego i punktu końcowego

W tym samouczku ustawimy punkt początkowy w siedzibie firmy Microsoft, a punkt docelowy na stacji paliw w Seattle. 

1. W tym samym bloku *script* w pliku **MapRoute.html** dodaj następujący kod JavaScript, aby utworzyć punkt początkowy i punkt końcowy trasy:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Ten kod tworzy dwa [obiekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) reprezentujące punkt początkowy i punkt końcowy trasy. 

2. Dodaj następujący kod JavaScript, aby dodać pinezki punktu początkowego i punktu końcowego do mapy:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Wywołanie **map.setCameraBounds** dostosowuje okno mapy według współrzędnych punktu początkowego i punktu końcowego. Wywołanie interfejsu API **map.addPins** dodaje punkty do kontrolki mapy jako składniki wizualne.

7. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. Mapa jest teraz wyśrodkowana na Seattle. Punkt początkowy jest oznaczony niebieskim kółkiem, a punkt końcowy — niebieskim znacznikiem.

   ![Wyświetlanie mapy z zaznaczonym punktem początkowym i punktem końcowym](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Uzyskiwanie wskazówek dojazdu

W tej sekcji pokazano, jak używać interfejsu API usługi Route Service w usłudze Maps do znajdowania trasy z danego punktu początkowego do punktu docelowego. Usługa Route Service udostępnia interfejsy API do planowania *najszybszej*, *najkrótszej*, *najciekawszej* lub *najbardziej ekologicznej* trasy między dwiema lokalizacjami. Umożliwia ona też użytkownikom planowanie tras w przyszłości, korzystając z obszernej historycznej bazy danych ruchu drogowego na platformie Azure i przewidując długość podróży trasami w dowolnym dniu i czasie. Aby uzyskać więcej informacji, zobacz [Get route directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Najpierw dodaj nową warstwę na mapie, umożliwiającą wyświetlenie trasy — element *linestring*. Dodaj następujący kod JavaScript do bloku *script*.

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2.  Utwórz wystąpienie usługi klienta, dodając następujący kod Javascript do bloku skryptu.
    ```JavaScript
    var client = new atlas.service.Client(subscriptionKey);
    ```

3. Dodaj następujący blok kodu, aby skonstruować ciąg zapytania trasy.
    ```JavaScript
    // Construct the route query string 
        var routeQuery = startPoint.coordinates[1] + 
            "," + 
            startPoint.coordinates[0] + 
            ":" + 
            destinationPoint.coordinates[1] + 
            "," + 
            destinationPoint.coordinates[0];     
    ```

4. Aby uzyskać trasę, dodaj następujący blok kodu do skryptu. Wysyła on zapytania do usługi trasowania Azure Maps za pośrednictwem metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections), a następnie analizuje odpowiedź w formacie GeoJSON przy użyciu metody [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes). Następnie dodaje wszystkie wiersze odpowiedzi do mapy w celu przedstawienia trasy. Zobacz artykuł poświęcony [dodawaniu wiersza na mapie](./map-add-shape.md#addALine), aby uzyskać więcej informacji.

    ```JavaScript
    // Execute the query then add the route to the map once a response is received  
    client.route.getRouteDirections(routeQuery).then(response => { 
         // Parse the response into GeoJSON 
         var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response); 
 
         // Get the first in the array of routes and add it to the map 
         map.addLinestrings([geoJsonResponse.getGeoJsonRoutes().features[0]], { 
             name: routeLinesLayerName 
         }); 
    }); 
    ```

5. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Kontrolka mapy platformy Azure i usługa Route Service](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Ustawianie współrzędnych adresu
> * Wysyłanie do usługi Route Service zapytania dotyczącego wskazówek dojazdu do punktu orientacyjnego

Następny samouczek przedstawia tworzenie zapytania o trasę z ograniczeniami dotyczącymi np. sposobu podróży lub rodzaju ładunku, a następnie wyświetlanie wielu tras na tej samej mapie. 

> [!div class="nextstepaction"]
> [Znajdowanie tras dla różnych sposobów podróży](./tutorial-prioritized-routes.md)
