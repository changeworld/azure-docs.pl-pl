---
title: 'Samouczek: Znajdź wiele tras według sposobu podróży | Mapy platformy Microsoft Azure'
description: W tym samouczku dowiesz się, jak znaleźć trasy dla różnych trybów podróży za pomocą usług Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: c7ed2421f468dfbb64c635683a7909b517105bc7
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333804"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Samouczek: Znajdowanie tras dla różnych trybów podróży za pomocą usługi Azure Maps

W tym samouczku pokazano, jak korzystać z konta usługi Azure Maps i usługi trasy. Usługa trasy może znaleźć trasę do punktu zainteresowania, priorytetowego dla sposobu podróży. Na mapie można wyświetlić dwie różne trasy: jedną dla samochodów osobowych i jedną dla samochodów ciężarowych. Usługa wyznaczania tras bierze pod uwagę ograniczenia ze względu na wzrost i masę pojazdu lub jeżeli pojazd przewozi ładunek niebezpieczny. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem postępuj zgodnie z instrukcjami w [obszarze Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i wybierz warstwę cenową S1. Wykonaj kroki opisane w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](how-to-manage-authentication.md).

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
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

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

3. Dodaj następujący kod JavaScript do funkcji `GetMap`. Zastąp ciąg `<Your Azure Maps Key>` kluczem podstawowym skopiowanym z konta Mapy.

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

    Klasa `atlas.Map` zapewnia formant dla wizualnej i interaktywnej mapy sieci web i jest składnikiem interfejsu API kontroli mapy platformy Azure.

4. Zapisz plik i otwórz go w przeglądarce. Masz teraz podstawową mapę, którą możesz rozbudowywać.

   ![Wyświetlanie podstawowej mapy](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Wizualizowanie przepływu ruchu

1. Dodaj widok przepływu ruchu do mapy. Zdarzenie `ready` mapy czeka, aż zasoby map zostaną załadowane i będą gotowe do bezpiecznej interakcji z nim.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    W programie `ready` obsługi zdarzeń mapy ustawienie przepływu ruchu `relative`na mapie jest ustawione na , czyli prędkość drogi w stosunku do swobodnego przepływu. Tę wartość możesz również ustawić na `absolute` (względna szybkość ruchu na drodze) lub `relative-delay` (umożliwia wyświetlanie względnej szybkości, gdy różni się ona od swobodnego przepływu ruchu).

2. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce. Jeśli wchodzisz w interakcję z mapą i powiększasz los Angeles, powinieneś zobaczyć ulice z bieżącymi danymi o ruchu drogowym.

   ![Wyświetlanie ruchu na mapie](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definiowanie sposobu renderowania trasy

W tym samouczku na mapie zostaną obliczone i wyrenderowane dwie trasy. Jedna trasa będzie prowadziła po drogach dostępnych dla samochodów osobowych, a druga dla samochodów ciężarowych. Po wyrenderowanie wyświetlimy ikonę symbolu dla początku i końca trasy oraz różne kolorowe linie dla każdej ścieżki trasy.

1. Po zainicjowaniu mapy dodaj następujący kod JavaScript `ready` w programie obsługi zdarzeń map.

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
    
    W programie `ready` obsługi zdarzeń map tworzone jest źródło danych do przechowywania linii trasy oraz punktów początkowych i końcowych. Tworzona jest warstwa linii, która jest następnie dołączana do źródła danych w celu zdefiniowania sposobu renderowana linii trasy. Za pomocą wyrażeń są pobierane szerokość i kolor linii z właściwości funkcji linii trasy. Podczas dodawania warstwy do mapy przekazywany jest drugi parametr o wartości `'labels'`. Określa on, że ta warstwa ma być renderowana poniżej etykiet mapy. Gwarantuje to, że linia trasy nie obejmuje etykiet drogowych. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania punktów początkowych i końcowych. W takim przypadku dodano wyrażenia w celu pobrania informacji o obrazie ikony i etykiecie tekstowej z właściwości każdego obiektu punktu. 
    
2. W tym samouczku ustawimy punkt początkowy w lokalizacji fikcyjnej firmy Fabrikam (w Seattle), a punkt docelowy w lokalizacji biura firmy Microsoft. W programie `ready` obsługi zdarzeń map dodaj następujący kod.

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

    Punkt początkowy i końcowy zostały dodane do źródła danych. Pole ograniczenia dla punktu początkowego i końcowego jest obliczane przy użyciu funkcji `atlas.data.BoundingBox.fromData`. To obwiednia służy do ustawiania widoku kamer `map.setCamera` mapy na całej trasie za pomocą funkcji. W celu skompensowania wymiarów pikseli ikon symboli dodawane jest wypełnienie.

4. Zapisz plik i odśwież przeglądarkę, aby wyświetlić znaczniki na mapie. Teraz mapa jest wyśrodkowany nad Seattle. Możesz zobaczyć okrągły niebieski pin oznaczający punkt początkowy i niebieski pin oznaczający punkt mety.

   ![Wyświetlanie mapy z punktem początkowym i punktem końcowym](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderowanie tras z uwzględnieniem priorytetów na podstawie sposobu podróży

W tej sekcji pokazano, jak korzystać z interfejsu API usługi trasy mapy. Interfejs API trasy służy do znajdowania wielu tras z danego punktu początkowego do punktu końcowego, na podstawie środka transportu. Usługa trasy zapewnia interfejsy API do planowania *najszybszych,* *najkrótszych,* *eko*lub *ekscytujących* tras. Interfejsy API nie tylko planują trasy między dwiema lokalizacjami, ale także uwzględniają bieżące warunki drogowe. 

Interfejs API trasy umożliwia użytkownikom planowanie tras w przyszłości przy użyciu rozległej historycznej bazy danych ruchu platformy Azure. Interfejs API można przewidzieć czas trwania trasy dla danego dnia i czasu. Aby uzyskać więcej informacji, zobacz [GetRoute Directions (Uzyskiwanie wskazówek dojazdu)](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Wszystkie następujące bloki kodu powinny zostać dodane **w ramach zdarzenia ładowania mapyListener,** aby upewnić się, że ładują się po całkowicie załadowaniu mapy.

1. W funkcji GetMap dodaj następujące elementy do kodu JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   Tworzy `SubscriptionKeyCredential` do `SubscriptionKeyCredentialPolicy` uwierzytelniania żądań HTTP do usługi Azure Maps z kluczem subskrypcji. Przyjmuje `atlas.service.MapsURL.newPipeline()` w `SubscriptionKeyCredential` zasadach i tworzy [Pipeline wystąpienie.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Reprezentuje `routeURL` adres URL do operacji [marszruty](https://docs.microsoft.com/rest/api/maps/route) usługi Azure Maps.

2. Po skonfigurowaniu poświadczeń i adresu URL dodaj następujący kod JavaScript, aby utworzyć trasę od punktu początkowego do końcowego dla ciężarówki przewożącej ładunek klasy USHazmatClass2 i wyświetlić wyniki.

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

    Ten fragment kodu powyżej zapytania usługi routingu usługi Azure Maps za pośrednictwem [metody getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Linia trasy jest następnie wyodrębniana z kolekcji funkcji GeoJSON z `geojson.getFeatures()` odpowiedzi, która jest wyodrębniana przy użyciu metody. Linia trasy jest następnie dodawana do źródła danych. Indeks 0 zapewnia, że jest renderowany przed innymi wierszami w źródle danych. Odbywa się to, ponieważ obliczanie trasy samochodu ciężarowego będzie często wolniejsze niż obliczanie trasy samochodu. Jeśli linia trasy ciężarówki zostanie dodana do źródła danych po trasie samochodu, zostanie ono renderowane nad nią. Dwie właściwości są dodawane do linii trasy ciężarówki, kolor obrysu, który jest ładny odcień niebieskiego i szerokość obrysu dziewięciu pikseli.

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

    Ten fragment kodu powyżej zapytania usługi routingu usługi Azure Maps za pośrednictwem [metody getRouteDirections.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) Linia trasy jest następnie wyodrębniana z kolekcji funkcji GeoJSON z `geojson.getFeatures()` odpowiedzi, która jest wyodrębniana przy użyciu metody. Linia trasy jest następnie dodawana do źródła danych. Dwie właściwości są dodawane do linii trasy samochodu, kolor obrysu, który jest odcieniem fioletu, i szerokość obrysu pięciu pikseli.  

4. Zapisz plik **MapTruckRoute.html** i odśwież stronę w przeglądarce, aby wyświetlić wynik. W przypadku pomyślnego połączenia z interfejsami API usługi Maps powinna pojawić się mapa podobna do poniższej.

    ![Określanie priorytetów tras za pomocą usługi Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Trasa ciężarówki jest gruba niebieska, a trasa samochodu jest cienka fioletowa. Trasa samochodem prowadzi przez Jezioro Waszyngton przez I-90, który przechodzi przez tunele pod obszarami mieszkalnymi. Ponieważ tunele znajdują się w pobliżu obszarów mieszkalnych, ładunek odpadów niebezpiecznych jest ograniczony. Trasa ciężarówki, która określa typ ładunku USHazmatClass2, jest skierowana do korzystania z innej autostrady.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wizualizowanie przepływu ruchu na mapie
> * Tworzenie zapytań o trasę zawierających deklarację sposobu podróży
> * Wyświetlanie wielu tras na mapie

> [!div class="nextstepaction"]
> [Wyświetl pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Zobacz próbkę na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

W następnym samouczku przedstawiony jest proces tworzenia prostego lokalizatora sklepów przy użyciu usługi Azure Maps.

> [!div class="nextstepaction"]
> [Create a store locator using Azure Maps (Tworzenie lokalizatora sklepów przy użyciu usługi Azure Maps)](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)