---
title: Jak korzystać z modułu we/wy usługi Azure Maps | Mapy platformy Microsoft Azure
description: Dowiedz się, jak korzystać z modułu we/wy przestrzennej udostępnianej przez zestaw SDK usługi Azure Maps Web. Ten moduł zapewnia niezawodne funkcje ułatwiające deweloperom integrowanie danych przestrzennych z sdk sieci Web usługi Azure Maps.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1de9dd9721700418b1aa9ba661fc070db1dbedcc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804643"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Jak korzystać z modułu Usługi we/wy usługi Azure Maps

Zestaw Azure Maps Web SDK udostępnia **moduł IO przestrzennego,** który integruje dane przestrzenne z zestawem SDK sieci Web usługi Azure Maps przy użyciu języka JavaScript lub TypeScript. Niezawodne funkcje tego modułu umożliwiają deweloperom:

- [Odczytywanie i zapisywanie typowych plików danych przestrzennych](spatial-io-read-write-spatial-data.md). Obsługiwane formaty plików obejmują: KML, KMZ, GPX, GeoRSS, GML, GeoJSON i CSV pliki zawierające kolumny z informacjami przestrzennymi. Obsługuje również dobrze znany tekst (WKT).
- [Połącz się z usługami Open Geospatial Consortium (OGC) i zintegruj się z zestawem SDK sieci Web usługi Azure Maps. Nakładki usług map internetowych (WMS) i usługi kafelek map internetowych (WMTS) jako warstwy na mapie](spatial-io-add-ogc-map-layer.md).
- [Zapytanie o dane w usłudze elementów charakterystycznych sieci Web (WFS)](spatial-io-connect-wfs-service.md).
- [Nakładaj złożone zestawy danych, które zawierają informacje o stylu i powodują ich automatyczne renderowanie przy użyciu minimalnego kodu](spatial-io-add-simple-data-layer.md).
- [Korzystaj z szybkiego kodu XML i rozdzielanych klas czytnika plików i modułu zapisującego.](spatial-io-core-operations.md)

W tym przewodniku dowiemy się, jak zintegrować i używać modułu Przestrzenne we/wy w aplikacji sieci web.

Ten klip wideo zawiera omówienie modułu we/wy przestrzennej w zestawie Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0" title="Łatwa integracja danych przestrzennych z usługą Azure Maps — Microsoft Channel 9 Video"></iframe>


> [!WARNING]
> Używaj tylko danych i usług pochodzących z zaufanego źródła, zwłaszcza jeśli odwołujesz się do niego z innej domeny. Moduł we/wy przestrzennej wykonuje kroki w celu zminimalizowania ryzyka, jednak najbezpieczniejsze podejście jest zbyt nie zezwala na żadnych danagerous danych do aplikacji, aby rozpocząć. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było korzystać z modułu Interwłasze [przestrzenne,](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) musisz stworzyć konto usługi Azure Maps i [uzyskać podstawowy klucz subskrypcji dla swojego konta.](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account)

## <a name="installing-the-spatial-io-module"></a>Instalowanie modułu We/Wy przestrzennej

Moduł we/wy usługi Azure Maps można załadować przy użyciu jednej z dwóch opcji:

* Globalnie hostowana usługa Azure CDN dla modułu usługi Azure Maps do y io. W przypadku tej opcji należy dodać odwołanie `<head>` do języka JavaScript w elemencie pliku HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* Kod źródłowy dla [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) można załadować lokalnie, a następnie hostować za pomocą aplikacji. Ten pakiet zawiera również definicje TypeScript. W przypadku tej opcji użyj następującego polecenia, aby zainstalować pakiet:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Następnie dodaj odwołanie do języka JavaScript w elemencie `<head>` dokumentu HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Korzystanie z modułu We/Wy przestrzennej

1. Utwórz nowy plik HTML.

2. Załaduj zestaw SDK sieci Web usługi Azure Maps i zaiówtuje formant mapy. Szczegółowe informacje można znaleźć w przewodniku [sterowania mapami usługi Azure Maps.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) Po wykonaniu tego kroku plik HTML powinien wyglądać następująco:

    ```html
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
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Załaduj moduł we/wy usługi Azure Maps. W tym ćwiczeniu użyj sieci CDN dla modułu usługi Azure Maps io. Dodaj poniższe odwołanie `<head>` do elementu pliku HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Zainicjować `datasource`program i dodać źródło danych do mapy. Zainicjować `layer`program i dodać źródło danych do warstwy mapy. Następnie renderuj zarówno źródło danych, jak i warstwę. Przed przewinięciem w dół, aby wyświetlić pełny kod w następnym kroku, zastanów się nad najlepszymi miejscami, w których można umieścić fragmenty kodu źródła danych i warstwy. Przypomnijmy, że zanim programowo manipulujemy mapą, powinniśmy poczekać, aż zasób mapy będzie gotowy.

    ```javascript
    var datasource, layer;
    ```

    i

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Łącząc to wszystko razem, kod HTML powinien wyglądać jak następujący kod. W tym przykładzie pokazano, jak odczytać plik XML z adresu URL. Następnie załaduj i wyświetl dane obiektu pliku na mapie. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Pamiętaj, `<Your Azure Maps Key>` aby zastąpić kluczem podstawowym. Otwórz plik HTML, a zobaczysz wyniki podobne do następujących obrazów:

    <center>

    ![Przykład danych przestrzennych](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Następne kroki

Funkcja, którą tutaj zademonstrowaliśmy, jest tylko jedną z wielu funkcji dostępnych w module Spatial IO. Zapoznaj się z poniższymi przewodnikami, aby dowiedzieć się, jak korzystać z innych funkcji w module Spatial IO:

> [!div class="nextstepaction"]
> [Dodawanie prostej warstwy danych](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych przestrzennych](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Łączenie się z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Wykorzystanie podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)

Zapoznaj się z dokumentacją usługi We/Wy usługi Azure Maps:

> [!div class="nextstepaction"]
> [Pakiet io usługi Azure Maps Spatial We/Wy](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
