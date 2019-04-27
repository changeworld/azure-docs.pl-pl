---
title: Wiele tras w usłudze Azure Maps | Microsoft Docs
description: Znajdowanie tras dla różnych sposobów podróży za pomocą usługi Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3e33eef430db627a82bb82e8c22ddc861d08982b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692110"
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
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>

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

3. Dodaj następujący kod JavaScript do funkcji `GetMap`. Zastąp ciąg `<Your Azure Maps Key>` za pomocą klucza podstawowego, który został skopiowany z Twojego konta usługi Maps.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    `atlas.Map` Klasa udostępnia kontrolkę wizualnej i interaktywnej sieci web mapy i jest składnikiem interfejsu API kontrolki mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Wizualizowanie przepływu ruchu

1. Dodaj widok przepływu ruchu do mapy. Mapy `ready` zdarzeń oczekuje na zasoby mapy są ładowane i zechcesz bezpiecznie korzystać z niego.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Na mapie `ready` procedura obsługi zdarzeń, ustawienie przepływu ruchu na mapie ma wartość `relative`, czyli szybkość ruchu na drodze względem swobodnego przepływu. Tę wartość możesz również ustawić na `absolute` (względna szybkość ruchu na drodze) lub `relative-delay` (umożliwia wyświetlanie względnej szybkości, gdy różni się ona od swobodnego przepływu ruchu).

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. Jeśli wchodzić w interakcje z mapą i powiększenie w Los Angeles, powinien zostać wyświetlony streets z bieżącymi danymi ruchu.

   ![Wyświetlanie mapy ruchu ulicznego](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku na mapie zostaną obliczone i wyrenderowane dwie trasy. Jedna trasa będzie prowadziła po drogach dostępnych dla samochodów osobowych, a druga dla samochodów ciężarowych. Po wyrenderowaniu zostanie wyświetlona ikona symbolu początku i końca trasy. Każda trasa zostanie oznaczona liniami w innych kolorach.

1. Po inicjowanie na mapie, Dodaj następujący kod JavaScript w mapach `ready` programu obsługi zdarzeń.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
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
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    W mapach `ready` procedura obsługi zdarzeń, tworzone jest źródło danych do przechowywania wierszy trasy, a także punkty początkowy i końcowy. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Za pomocą wyrażeń są pobierane szerokość i kolor linii z właściwości funkcji linii trasy. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Dzięki temu linia trasy nie zakryje etykiet dróg. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. W tym przypadku dodano wyrażenia w celu pobrania informacji o obrazie ikony i etykiecie tekstowej z właściwości każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w lokalizacji fikcyjnej firmy Fabrikam (w Seattle), a punkt docelowy w lokalizacji biura firmy Microsoft. W mapach `ready` procedura obsługi zdarzeń, Dodaj następujący kod.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
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

    Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. Ta obwiedni jest używana do ustawiania aparatów widoku mapy za pośrednictwem za pomocą marszruty całej `map.setCamera` funkcji. W celu skompensowania wymiarów pikseli ikon symboli dodawane jest wypełnienie.

4. Zapisz plik i odśwież przeglądarkę, aby wyświetlić znaczniki na mapie. Mapa jest teraz wyśrodkowana na Seattle. Punkt początkowy jest oznaczony niebieskim kółkiem, a punkt końcowy — niebieskim znacznikiem.

   ![Wyświetlanie mapy z punktem początkowym i punktem końcowym](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderowanie tras z uwzględnieniem priorytetów na podstawie sposobu podróży

W tej sekcji pokazano, jak używać interfejsu API usługi service map trasy do znajdowania wielu tras z punktu rozpoczęcia danego do punktu końcowego, w oparciu o podróży. Usługa Route Service udostępnia interfejsy API do planowania *najszybszej*, *najkrótszej*, *najciekawszej* lub *najbardziej ekologicznej* trasy między dwiema lokalizacjami z uwzględnieniem bieżących warunków drogowych. Umożliwia ona też użytkownikom planowanie tras w przyszłości, korzystając z obszernej historycznej bazy danych ruchu drogowego na platformie Azure i przewidując długość podróży trasami w dowolnym dniu i czasie. Aby uzyskać więcej informacji, zobacz [GetRoute Directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Wszystkie poniższe bloki kodu powinny zostać dodane **w elemencie eventListener ładowania mapy** w celu zagwarantowania ich załadowania po pełnym załadowaniu mapy.

1. W funkcji GetMap Dodaj następujący element do kodu w języku Javascript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` Tworzy `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądań HTTP do usługi Azure Maps za pomocą klucza subskrypcji. `atlas.service.MapsURL.newPipeline()` Przyjmuje `SubscriptionKeyCredential` zasad i tworzy [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) wystąpienia. `routeURL` Reprezentuje adres URL do usługi Azure Maps [trasy](https://docs.microsoft.com/rest/api/maps/route) operacji.

2. Po skonfigurowaniu poświadczeń i adres URL, Dodaj następujący kod JavaScript kod, aby utworzyć trasę od początku do punktu końcowego dla ciężarówki wykonywania USHazmatClass2 gniazdami ładunku i wyświetlić wyniki.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Ten fragment kodu powyżej zapytania usługi routingu usługi Azure Maps za pośrednictwem [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metody. Wiersz trasy następnie są wyodrębniane z kolekcji funkcji GeoJSON z odpowiedzi, które zostały wyodrębnione przy użyciu `geojson.getFeatures()` metody. Wiersz trasy jest dodawane do źródła danych. Dodaje także indeks 0, aby upewnić się, że jest on renderowany przed innymi wierszami w źródle danych. Ta czynność jest wymagana, ponieważ obliczanie trasy dla samochodów ciężarowych zachodzi często wolniej niż dla samochodów osobowych. Jeśli linia trasy dla samochodów ciężarowych zostanie dodana do źródła danych po trasie dla samochodów osobowych, zostanie wyrenderowana powyżej niej. Dwie właściwości są dodawane do wiersza trasa ciężarówki, kolor pociągnięcia, ładny odcień niebieski i grubość dziewięć pikseli.

3. Dodaj następujący kod JavaScript, aby utworzyć trasę dla samochodu i wyświetlić wyniki.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Ten fragment kodu powyżej zapytania usługi routingu usługi Azure Maps za pośrednictwem [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-iot-typescript-latest) metody. Wiersz trasy następnie są wyodrębniane z kolekcji funkcji GeoJSON z odpowiedzi, które zostały wyodrębnione przy użyciu `geojson.getFeatures()` metody. Wiersz trasy jest dodawane do źródła danych. Dwie właściwości są dodawane do wiersza trasa samochodu, kolor pociągnięcia, który jest odcień purpurowy i grubość 5 pikseli.  

4. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce, aby wyświetlić wynik. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Określanie priorytetów tras za pomocą usługi Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa ciężarówki jest oznaczona grubą, niebieską linią, a trasa samochodu osobowego — cienką, purpurową linią. Trasa samochodu osobowego biegnie drogą I-90, przez Jezioro Waszyngtona, tunelami znajdującymi się pod dzielnicami mieszkaniowymi. Na tej trasie obowiązuje zakaz przewozu niebezpiecznych odpadów. Trasa ciężarówki uwzględnia ładunek należący do klasy 2 materiałów niebezpiecznych i została poprawnie poprowadzona inną autostradą.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

> [!div class="nextstepaction"]
> [Wyświetl pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Wyświetl przykład na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

W następnym samouczku przedstawiony jest proces tworzenia prostego lokalizatora sklepów przy użyciu usługi Azure Maps.

> [!div class="nextstepaction"]
> [Create a store locator using Azure Maps (Tworzenie lokalizatora sklepów przy użyciu usługi Azure Maps)](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Za pomocą wyrażeń opartych na danych stylu](data-driven-style-expressions-web-sdk.md)