---
title: Znajdowanie trasy przy użyciu usługi Azure Maps | Microsoft Docs
description: Znajdowanie trasy do punktu orientacyjnego przy użyciu usługi Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/14/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 7fb382c6c57972484eff80c0d7355319a1f9178a
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893555"
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
    <html>
    <head>
        <title>Map Route</title>
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

            #map {
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

    Element `atlas.Map` zapewnia kontrolkę dla wizualnej interakcyjnej mapy internetowej i jest składnikiem interfejsu API kontrolki mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](./media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku zostanie wyrenderowana prosta trasa przy użyciu ikon symboli przedstawiających początek i koniec trasy oraz linii przedstawiającej przebieg trasy.

1. W funkcji GetMap po zainicjowaniu mapy dodaj następujący kod JavaScript.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('load', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
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
    
    Zdarzenie ładowania jest dodawane do mapy. Zostanie ono wyzwolone po całkowitym załadowaniu zasobów mapy. W obsłudze zdarzeń ładowania mapy tworzone jest źródło danych do przechowywania linii trasy oraz punktów początkowych i końcowych. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Linia trasy zostanie wyrenderowana w odcieniu koloru niebieskiego o szerokości 5 pikseli z zaokrąglonymi połączeniami oraz zakończeniami. Dodawany jest filtr w celu zapewnienia, że ta warstwa będzie renderowała tylko dane LineString w formacie GeoJSON. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Dzięki temu linia trasy nie zakryje etykiet dróg. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. W tym przypadku dodano wyrażenia w celu pobrania informacji o obrazie ikony i etykiecie tekstowej z właściwości każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w siedzibie firmy Microsoft, a punkt końcowy na stacji paliw w Seattle. W obsłudze zdarzeń ładowania mapy dodaj następujący kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: 'Microsoft',
        icon: 'pin-round-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: 'Contoso Oil & Gas',
        icon: 'pin-blue'
    });    
    ```

    Ten kod tworzy dwa [obiekty punktów GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) reprezentujące punkt początkowy i punkt końcowy trasy. Do każdego punktu są dodawane właściwości `title` i `icon`.
    
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
    
    Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To pole ograniczenia jest używane do ustawiania widoku kamer mapy nad punktem początkowym i końcowym za pomocą funkcji **map.setCamera**. W celu skompensowania wymiarów pikseli ikon symboli dodawane jest wypełnienie.

3. Zapisz plik **MapRoute.html** i odśwież przeglądarkę. Mapa jest teraz wyśrodkowana na Seattle. Punkt początkowy jest oznaczony niebieskim kółkiem, a punkt końcowy — niebieskim znacznikiem.

   ![Wyświetlanie mapy z zaznaczonym punktem początkowym i punktem końcowym](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Uzyskiwanie wskazówek dojazdu

W tej sekcji pokazano, jak używać interfejsu API usługi Route Service w usłudze Maps do znajdowania trasy z danego punktu początkowego do punktu docelowego. Usługa Route Service udostępnia interfejsy API do planowania *najszybszej*, *najkrótszej*, *najciekawszej* lub *najbardziej ekologicznej* trasy między dwiema lokalizacjami. Umożliwia ona też użytkownikom planowanie tras w przyszłości, korzystając z obszernej historycznej bazy danych ruchu drogowego na platformie Azure i przewidując długość podróży trasami w dowolnym dniu i czasie. Aby uzyskać więcej informacji, zobacz [Get route directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Wszystkie poniższe funkcje powinny zostać dodane **w elemencie eventListener ładowania mapy** w celu zagwarantowania ich załadowania po pełnym załadowaniu mapy.

1. Utwórz wystąpienie usługi klienta, dodając następujący kod JavaScript do obsługi zdarzeń ładowania mapy.

    ```JavaScript
    //If the service client hasn't already been created, create an instance.
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

3. Aby uzyskać trasę, dodaj następujący blok kodu do skryptu. Wysyła on zapytania do usługi trasowania Azure Maps za pośrednictwem metody [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest), a następnie analizuje odpowiedź w formacie GeoJSON przy użyciu metody [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest). Następnie dodaje linię trasy w odpowiedzi na źródło danych, co powoduje automatyczne wyświetlenie jej na mapie.

    ```JavaScript
    //Execute the car route query then add the route to the map once a response is received.
    client.route.getRouteDirections(routeQuery).then(function (response) {
        // Parse the response into GeoJSON
        var geoJsonResponse = new atlas.service.geojson.GeoJsonRouteDirectionsResponse(response);

        //Add the route line to the data source.
        datasource.add(geoJsonResponse.getGeoJsonRoutes().features[0]);
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

Przykładowy kod z tego samouczka jest dostępny tutaj:

> [Znajdowanie trasy przy użyciu usługi Azure Maps](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

[Zobacz tutaj ten działający przykład](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

Następny samouczek przedstawia tworzenie zapytania o trasę z ograniczeniami dotyczącymi np. sposobu podróży lub rodzaju ładunku, a następnie wyświetlanie wielu tras na tej samej mapie.

> [!div class="nextstepaction"]
> [Znajdowanie tras dla różnych sposobów podróży](./tutorial-prioritized-routes.md)
