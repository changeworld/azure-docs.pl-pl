---
title: Wiele tras w usłudze Azure Maps | Microsoft Docs
description: Znajdowanie tras dla różnych sposobów podróży za pomocą usługi Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 10/29/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 67b68489f2e06b9149f842f293a769fa7f688be0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412707"
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
    <html>
    <head>
        <title>Multiple routes by mode of travel</title>
        
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                position: relative;
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```
    
    Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zwróć uwagę na zdarzenie `onload` w treści strony, które spowoduje wywołanie funkcji `GetMap` po załadowaniu treści strony. Ta funkcja będzie zawierać śródwierszowy kod JavaScript umożliwiający dostęp do interfejsów API usługi Azure Maps.

3. Dodaj następujący kod JavaScript do funkcji `GetMap`. Zastąp ciąg **\<Your Azure Maps Key\>** kluczem podstawowym, skopiowanym z konta usługi Maps.

    ```JavaScript
    //Add your Azure Maps subscription key to the map SDK. 
    atlas.setSubscriptionKey('<Your Azure Maps Key>');

    //Initialize a map instance.
    map = new atlas.Map('myMap');
    ```

    Element **atlas.Map** zapewnia kontrolkę dla wizualnej interakcyjnej mapy internetowej i jest składnikiem interfejsu API kontrolki mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Wizualizowanie przepływu ruchu

1. Dodaj widok przepływu ruchu do mapy. Wywołanie `map.events.add` zapewnia, że wszystkie funkcje map dodane do mapy zostaną załadowane po pełnym załadowaniu mapy.

    ```JavaScript
    map.events.add("load", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```
    
     Zdarzenie ładowania jest dodawane do mapy. Zostanie ono wyzwolone po całkowitym załadowaniu zasobów mapy. W obsłudze zdarzeń ładowania mapy ustawienie przepływu ruchu na mapie ma wartość `relative`, co odpowiada szybkości ruchu na drodze względem swobodnego przepływu ruchu. Tę wartość możesz również ustawić na `absolute` (względna szybkość ruchu na drodze) lub `relative-delay` (umożliwia wyświetlanie względnej szybkości, gdy różni się ona od swobodnego przepływu ruchu).

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. Powinny pojawić się ulice Los Angeles z bieżącymi danymi ruchu ulicznego.

   ![Wyświetlanie mapy ruchu ulicznego](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku na mapie zostaną obliczone i wyrenderowane dwie trasy. Jedna trasa będzie prowadziła po drogach dostępnych dla samochodów osobowych, a druga dla samochodów ciężarowych. Po wyrenderowaniu zostanie wyświetlona ikona symbolu początku i końca trasy. Każda trasa zostanie oznaczona liniami w innych kolorach.

1. W funkcji GetMap po zainicjowaniu mapy dodaj następujący kod JavaScript.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round',
            filter: ['==', '$type', 'LineString']
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['==', '$type', 'Point']
        }));
    });
    ```
    
    Zdarzenie ładowania jest dodawane do mapy. Zostanie ono wyzwolone po całkowitym załadowaniu zasobów mapy. W obsłudze zdarzeń ładowania mapy tworzone jest źródło danych do przechowywania linii tras oraz punktów początkowych i końcowych. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Za pomocą wyrażeń są pobierane szerokość i kolor linii z właściwości funkcji linii trasy. Dodawany jest filtr, aby się upewnić, że ta warstwa będzie renderowała tylko dane LineString formatu GeoJSON. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Dzięki temu linia trasy nie zakryje etykiet dróg. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. W tym przypadku dodano wyrażenia w celu pobrania informacji o obrazie ikony i etykiecie tekstowej z właściwości każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w lokalizacji fikcyjnej firmy Fabrikam (w Seattle), a punkt docelowy w lokalizacji biura firmy Microsoft. W obsłudze zdarzeń ładowania mapy dodaj następujący kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-blue'
    });
    ```
    
    Ten kod tworzy dwa [obiekty GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) reprezentujące punkt początkowy i punkt końcowy trasy. Do każdego punktu są dodawane właściwości `title` i `icon`.

3. Następnie dodaj poniższy kod JavaScript, aby dodać pinezki punktu początkowego i punktu końcowego do mapy:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```
    
    Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To pole ograniczenia jest używane do ustawiania widoku kamer mapy nad punktem początkowym i końcowym za pomocą funkcji `map.setCamera`. W celu skompensowania wymiarów pikseli ikon symboli dodawane jest wypełnienie.

4. Zapisz plik i odśwież przeglądarkę, aby wyświetlić znaczniki na mapie. Mapa jest teraz wyśrodkowana na Seattle. Punkt początkowy jest oznaczony niebieskim kółkiem, a punkt końcowy — niebieskim znacznikiem.

   ![Wyświetlanie mapy z punktem początkowym i punktem końcowym](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderowanie tras z uwzględnieniem priorytetów na podstawie sposobu podróży

W tej sekcji pokazano, jak używać interfejsu API usługi Route Service w usłudze Maps do znajdowania wielu tras z danego punktu początkowego do punktu docelowego na podstawie sposobu podróży. Usługa Route Service udostępnia interfejsy API do planowania *najszybszej*, *najkrótszej*, *najciekawszej* lub *najbardziej ekologicznej* trasy między dwiema lokalizacjami z uwzględnieniem bieżących warunków drogowych. Umożliwia ona też użytkownikom planowanie tras w przyszłości, korzystając z obszernej historycznej bazy danych ruchu drogowego na platformie Azure i przewidując długość podróży trasami w dowolnym dniu i czasie. Aby uzyskać więcej informacji, zobacz [GetRoute Directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Wszystkie poniższe bloki kodu powinny zostać dodane **w elemencie eventListener ładowania mapy** w celu zagwarantowania ich załadowania po pełnym załadowaniu mapy.

1. Utwórz wystąpienie usługi klienta, dodając następujący kod JavaScript do obsługi zdarzeń ładowania mapy.

    ```JavaScript
    //If the service client hasn't been created, create an instance of it.
    if (!client) {
        client = new atlas.service.Client(atlas.getSubscriptionKey());
    }
    ```
    
2. Dodaj następujący blok kodu, aby skonstruować ciąg zapytania trasy.

    ```JavaScript
    //Create the route request with the query being the start and end point in the format 'startLongitude,startLatitude:endLongitude,endLatitude'.
    var routeQuery = startPoint.geometry.coordinates[1] +
        ',' +
        startPoint.geometry.coordinates[0] +
        ':' +
        endPoint.geometry.coordinates[1] +
        ',' +
        endPoint.geometry.coordinates[0];
    ```

3. Dodaj następujący kod JavaScript, który umożliwia wysłanie żądania dotyczącego trasy dla ciężarówki wiozącej ładunek klasy USHazmatClass2 i wyświetlenie wyników:

    ```JavaScript
    //Execute the truck route query then add the route to the map.
    client.route.getRouteDirections(routeQuery, {
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```
    Powyższy fragment kodu wysyła zapytanie do usługi trasowania Azure Maps za pośrednictwem metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) i analizuje odpowiedź w formacie GeoJSON przy użyciu metody [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Następnie tworzona jest tablica współrzędnych dla zwróconej trasy, która jest dodawana do źródła danych. Dodawany jest również do niej indeks o wartości 0, aby się upewnić, że będzie ona renderowana przed wszystkimi innymi liniami w źródle danych. Ta czynność jest wymagana, ponieważ obliczanie trasy dla samochodów ciężarowych zachodzi często wolniej niż dla samochodów osobowych. Jeśli linia trasy dla samochodów ciężarowych zostanie dodana do źródła danych po trasie dla samochodów osobowych, zostanie wyrenderowana powyżej niej. Do linii trasy dla samochodów ciężarowych są dodawane dwie właściwości — kolor pociągnięcia w odcieniach niebieskiego i szerokość pociągnięcia o wartości 9 pikseli. 

4. Dodaj następujący kod JavaScript, który umożliwia wysłanie żądania dotyczącego trasy dla samochodu osobowego i wyświetlenie wyników:

    ```JavaScript
    //Execute the car route query then add the route to the map.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Get the route line and add some style properties to it.
        var routeLine = geoJsonResponse.getGeoJsonRoutes().features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source.
        datasource.add(routeLine);
    });
    ```
    Ten fragment kodu korzysta z tego samego zapytania dotyczącego trasy dla samochodu ciężarowego na potrzeby samochodu osobowego. Następnie wysyła zapytanie do usługi trasowania Azure Maps za pośrednictwem metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) i analizuje odpowiedź w formacie GeoJSON przy użyciu metody [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). W dalszej kolejności tworzy tablicę współrzędnych dla zwróconej trasy i dodaje ją do źródła danych. Do linii trasy dla samochodów osobowych są dodawane dwie właściwości — kolor pociągnięcia w odcieniach purpurowego i szerokość pociągnięcia o wartości 5 pikseli. 

5. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce, aby wyświetlić wynik. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Określanie priorytetów tras za pomocą usługi Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa ciężarówki jest oznaczona grubą, niebieską linią, a trasa samochodu osobowego — cienką, purpurową linią. Trasa samochodu osobowego biegnie drogą I-90, przez Jezioro Waszyngtona, tunelami znajdującymi się pod dzielnicami mieszkaniowymi. Na tej trasie obowiązuje zakaz przewozu niebezpiecznych odpadów. Trasa ciężarówki uwzględnia ładunek należący do klasy 2 materiałów niebezpiecznych i została poprawnie poprowadzona inną autostradą.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

Przykładowy kod z tego samouczka jest dostępny tutaj:

> [Wiele tras w usłudze Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

[Zobacz tutaj działający przykład](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Aby dowiedzieć się więcej o zasięgu i możliwościach usługi Azure Maps, zobacz:

> [!div class="nextstepaction"]
> [Poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md)

Aby uzyskać dodatkowe przykłady kodu i zapoznać się z interaktywnym środowiskiem kodowania:

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy](how-to-use-map-control.md)
