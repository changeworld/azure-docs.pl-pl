---
title: Wiele tras w usłudze Azure Maps | Microsoft Docs
description: Znajdowanie tras dla różnych sposobów podróży za pomocą usługi Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b45394cedcb7bbcb46b9730b7e445b04c8cf4f18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Znajdowanie tras dla różnych sposobów podróży za pomocą usługi Azure Maps

W tym samouczku pokazano, jak używać konta usługi Azure Maps oraz usługi Route Service w celu znalezienia trasy do punktu orientacyjnego z uwzględnieniem priorytetów sposobu podróży. Na mapie zostaną wyświetlone dwie różne trasy: dla samochodów osobowych oraz dla ciężarówek. Druga z nich może podlegać ograniczeniom ze względu na wysokość lub wagę pojazdu albo przewożenie niebezpiecznego ładunku. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem wykonaj kroki pierwszego samouczka, aby [utworzyć konto usługi Azure Maps](./tutorial-search-location.md#createaccount), a następnie [pobierz klucz subskrypcji dla swojego konta](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Tworzenie nowej mapy 
Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy. 

1. Na komputerze lokalnym utwórz nowy plik i nadaj mu nazwę **MapTruckRoute.html**. 
2. Dodaj następujące składniki HTML do pliku:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
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
    W nagłówku HTML są osadzone lokalizacje zasobów dla plików CSS i JavaScript używanych przez bibliotekę usługi Azure Maps. Segment *script* w treści pliku HTML będzie zawierać śródwierszowy kod JavaScript mapy.
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

   ![Wyświetlanie podstawowej mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Wizualizowanie przepływu ruchu

1. Jeśli nie wybierzesz obszaru, pojawi się widok obejmujący cały świat. Aby wyświetlić dane ruchu drogowego, ustaw punkt centralny i poziom powiększenia mapy. Zastąp kod z deklaracją `new atlas.Map` następującym kodem JavaScript: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Ten kod umożliwia ustawienie punktu centralnego mapy i zadeklarowanie poziomu powiększenia, co pozwala domyślnie skupić się na danym obszarze. 

1. Dodaj widok przepływu ruchu do mapy:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Ten kod ustawia wartość przepływu ruchu na `relative`, czyli szybkość ruchu na drodze względem swobodnego przepływu ruchu. Tę wartość możesz również ustawić na `absolute` (względna szybkość ruchu na drodze) lub `relative-delay` (umożliwia wyświetlanie względnej szybkości, gdy różni się ona od swobodnego przepływu ruchu). 

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. Powinny pojawić się ulice Los Angeles z bieżącymi danymi ruchu ulicznego.

   ![Wyświetlanie mapy ruchu ulicznego](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Ustawianie punktu początkowego i punktu końcowego

W tym samouczku ustawimy punkt początkowy w lokalizacji fikcyjnej firmy Fabrikam (w Seattle), a punkt docelowy w lokalizacji biura firmy Microsoft. 

1. Dodaj następujący kod JavaScript, aby utworzyć pinezki dla punktu początkowego i punktu końcowego trasy:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Ten kod tworzy dwa [obiekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) reprezentujące punkt początkowy i punkt końcowy trasy. 

2. Dodaj następujący kod JavaScript, aby dodać punkt początkowy i punkt końcowy do mapy:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    Wywołanie **map.setCameraBounds** dostosowuje okno mapy według współrzędnych punktu początkowego i punktu końcowego. Wywołanie interfejsu API **map.addPins** dodaje punkty do kontrolki mapy jako składniki wizualne.

3. Zapisz plik i odśwież przeglądarkę, aby wyświetlić znaczniki na mapie. Mimo że mapę zadeklarowano z punktem centralnym w Los Angeles, wywołanie **map.setCameraBounds** spowodowało przeniesienie widoku w celu wyświetlenia punktu początkowego i punktu końcowego. 

   ![Wyświetlanie mapy z punktem początkowym i punktem końcowym](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderowanie tras z uwzględnieniem priorytetów na podstawie sposobu podróży

W tej sekcji pokazano, jak używać interfejsu API usługi Route Service w usłudze Maps do znajdowania wielu tras z danego punktu początkowego do punktu docelowego na podstawie sposobu podróży. Usługa Route Service udostępnia interfejsy API do planowania *najszybszej*, *najkrótszej*, *najciekawszej* lub *najbardziej ekologicznej* trasy między dwiema lokalizacjami z uwzględnieniem bieżących warunków drogowych. Umożliwia ona też użytkownikom planowanie tras w przyszłości, korzystając z obszernej historycznej bazy danych ruchu drogowego na platformie Azure i przewidując długość podróży trasami w dowolnym dniu i czasie. Aby uzyskać więcej informacji, zobacz [Get route directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. Najpierw dodaj nową warstwę na mapie, umożliwiającą wyświetlenie trasy — element *linestring*. W tym samouczku występują dwie różne trasy — **car-route** i **truck-route** — z własnymi stylami. Dodaj następujący kod JavaScript do bloku *script*:

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

2. Do bloku *script* dodaj następujący kod JavaScript, który umożliwia wysłanie żądania dotyczącego trasy dla ciężarówki i wyświetlenie wyników na mapie:

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Ten fragment kodu tworzy element [XMLHttpRequest](https://xhr.spec.whatwg.org/) i dodaje procedurę obsługi zdarzeń, która analizuje przychodzącą odpowiedź. W przypadku pomyślnej odpowiedzi tworzy on tablicę współrzędnych dla zwróconej trasy i dodaje ją do warstwy `truckRouteLayerName` mapy. 
    
    Ten fragment kodu tworzy również zapytanie dla usługi Route Service w usłudze Maps przy użyciu klucza konta. Zapytanie zawiera współrzędne punktu początkowego, współrzędne punktu końcowego oraz parametry opcjonalne, informujące o tym, że trasa jest przeznaczona dla ciężarówki o dużej masie.

2. Dodaj następujący kod JavaScript, który umożliwia wysłanie żądania dotyczącego trasy dla samochodu osobowego i wyświetlenie wyników:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Ten fragment kodu tworzy kolejny element [XMLHttpRequest](https://xhr.spec.whatwg.org/) i dodaje procedurę obsługi zdarzeń, która analizuje przychodzącą odpowiedź. W przypadku pomyślnej odpowiedzi tworzy on tablicę współrzędnych dla zwróconej trasy i dodaje ją do warstwy `carRouteLayerName` mapy. 
    
    Ten fragment kodu tworzy również zapytanie dla usługi Route Service w usłudze Maps przy użyciu klucza konta. Zapytanie zawiera współrzędne punktu początkowego i współrzędne punktu końcowego. Ponieważ nie podano dodatkowych parametrów, usługa Route Service domyślnie przyjmuje sposób podróży *car* — samochodem osobowym. 

3. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce, aby wyświetlić wynik. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej. 

    ![Określanie priorytetów tras za pomocą usługi Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa ciężarówki jest oznaczona grubą, niebieską linią, a trasa samochodu osobowego — cienką, purpurową linią. Trasa samochodu osobowego biegnie drogą I-90, przez Jezioro Waszyngtona, tunelami znajdującymi się pod dzielnicami mieszkaniowymi. Na tej trasie obowiązuje zakaz przewozu niebezpiecznych odpadów. Trasa ciężarówki uwzględnia ładunek należący do klasy 2 materiałów niebezpiecznych i została poprawnie poprowadzona inną autostradą. 

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

Aby dowiedzieć się więcej na temat zasięgu i możliwości usługi Azure Maps, zobacz [Poziomy powiększenia i siatka](zoom-levels-and-tile-grid.md) oraz inne artykuły dotyczące pojęć. 

Aby uzyskać dodatkowy przykładowy kod i zapoznać się z interaktywnym środowiskiem kodowania, zobacz [Jak używać kontrolki mapy](how-to-use-map-control.md) oraz inne przewodniki z instrukcjami. 
