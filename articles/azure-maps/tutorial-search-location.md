---
title: 'Poradnik: Wyszukiwanie pobliskich lokalizacji na mapie | Mapy platformy Microsoft Azure'
description: W tym samouczku dowiesz się, jak wyszukiwać punkty szczególne na mapie przy użyciu usług Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 0b0cb92cd6b4918e28e143178a5cdbbbb19ac9af
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333632"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Samouczek: Wyszukiwanie pobliskich punktów szczególnych za pomocą usługi Azure Maps

W tym samouczku przedstawiono konfigurowanie konta przy użyciu usługi Azure Maps, a następnie wyszukiwanie punktu orientacyjnego za pomocą interfejsów API usługi Maps. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Maps
> * Pobieranie klucza podstawowego dla konta usługi Maps
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wyszukiwanie pobliskiego punktu orientacyjnego przy użyciu usługi wyszukiwania Maps

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Tworzenie konta przy użyciu usługi Azure Maps

Utwórz nowe konto usługi Maps, wykonując następujące czynności:

1. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.
2. W polu *Wyszukaj w portalu Marketplace* wpisz **Maps**.
3. Z listy *Wyniki* wybierz pozycję **Maps**. Kliknij przycisk **Utwórz** znajdujący się poniżej mapy.
4. Na stronie **Tworzenie konta usługi Maps** wprowadź następujące wartości:
    * *Subskrypcja*, która ma być używana dla tego konta.
    * Nazwa *grupy zasobów* dla tego konta. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    * *Nazwa* nowego konta.
    * Warstwa *Cen dla* tego konta.
    * Zapoznaj się z *Licencją* oraz *Zasadami zachowania poufności informacji* i zaznacz pole wyboru, aby zaakceptować warunki.
    * Kliknij przycisk **Utwórz**.

![Tworzenie konta usługi Azure Maps w witrynie Azure portal](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Pobieranie klucza podstawowego konta

Po pomyślnym utworzeniu konta usługi Maps pobierz klucz, który umożliwia wysyłanie zapytań do interfejsów API usługi Maps. Zalecamy używanie klucza podstawowego konta jako klucza subskrypcji podczas wywoływania usług Azure Maps.

1. Otwórz konto usługi Maps w portalu.
2. W sekcji ustawienia wybierz pozycję **Uwierzytelnianie**.
3. Skopiuj **klucz podstawowy** do schowka. Zapisz go lokalnie — będzie używany w dalszej części tego samouczka.

![Pobierz klucz podstawowy w witrynie Azure portal](./media/tutorial-search-location/get-key.png)

Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Tworzenie nowej mapy

Interfejs API kontroli mapy jest wygodną biblioteką klienta. Ten interfejs API umożliwia łatwą integrację aplikacji Map do aplikacji sieci web. Ukrywa złożoność wywołań usługi REST i zwiększa produktywność dzięki konfigurowalnym komponentom. Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy.

1. Na maszynie lokalnej utwórz nowy plik i nadaj mu nazwę **MapSearch.html**.
2. Dodaj następujące składniki HTML do pliku:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zwróć uwagę na zdarzenie `onload` w treści strony, które spowoduje wywołanie funkcji `GetMap` po załadowaniu treści strony. Funkcja `GetMap` będzie zawierać wbudowany kod JavaScript, aby uzyskać dostęp do interfejsów API usługi Azure Maps.

3. Dodaj następujący kod JavaScript do funkcji `GetMap` w pliku HTML. Zastąp ciąg `<Your Azure Maps Key>` kluczem podstawowym skopiowanym z konta Mapy.

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

   Ten segment inicjuje interfejs API kontrolki mapy dla klucza konta usługi Azure Maps. `atlas`jest obszarem nazw zawierającym interfejs API i powiązane składniki wizualne. `atlas.Map`zapewnia kontrolę wizualnej i interaktywnej mapy internetowej.

4. Zapisz zmiany w pliku i otwórz stronę HTML w przeglądarce. Pokazana mapa jest najbardziej podstawową mapą, którą możesz zrobić, dzwoniąc `atlas.Map` za pomocą klucza konta.

   ![Wyświetlanie mapy](./media/tutorial-search-location/basic-map.png)

5. W funkcji `GetMap` po zainicjowaniu mapy dodaj następujący kod JavaScript.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   W tym segmencie `ready` kodu do mapy zostanie dodane zdarzenie, które zostanie podpalone po załadowaniu zasobów mapy i przygotowaniu mapy do uzyskania dostępu. W programie `ready` obsługi zdarzeń mapy jest tworzony źródło danych do przechowywania danych wynikowych. Tworzona jest warstwa symboli, która jest następnie dołączana do źródła danych. Ta warstwa określa sposób renderowania danych wynikowych w źródle danych. W takim przypadku wynik jest renderowany za pomocą ciemnoniebieskiej okrągłej ikony pinezki, wyśrodkowany nad współrzędną wyników i umożliwia nakładanie się innych ikon. Warstwa wynikowa zostanie dodana do warstw mapy.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Dodawanie funkcji wyszukiwania

W tej sekcji pokazano, jak znaleźć interesujący obszar mapy za pomocą [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) w mapach. Jest to interfejs API RESTful umożliwiający deweloperom wyszukiwanie adresów, punktów orientacyjnych i innych informacji geograficznych. Usługa Search Service przypisuje informacje o długości i szerokości geograficznej do określonego adresu. Opisany poniżej **moduł usługi** może być używany do wyszukiwania lokalizacji za pomocą interfejsu API usługi Search w usłudze Maps.

### <a name="service-module"></a>Moduł usługi

1. W programie `ready` obsługi zdarzeń mapy skonstruuj adres URL usługi wyszukiwania, dodając następujący kod Javascript.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   Tworzy `SubscriptionKeyCredential` do `SubscriptionKeyCredentialPolicy` uwierzytelniania żądań HTTP do usługi Azure Maps z kluczem subskrypcji. Przyjmuje `atlas.service.MapsURL.newPipeline()` w `SubscriptionKeyCredential` zasadach i tworzy [Pipeline wystąpienie.](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) Reprezentuje `searchURL` adres URL do operacji usługi Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

2. Następnie dodaj poniższy blok skryptu w celu utworzenia zapytania wyszukiwania. Ten fragment kodu używa usługi Fuzzy Search Service — podstawowego interfejsu API wyszukiwania usługi Search Service. Usługa Fuzzy Search Service obsługuje większość rozmytych danych wejściowych, takich jak adresy, miejsca i punkty orientacyjne (POI). Ten kod wyszukuje pobliskie stacje benzynowe w określonym promieniu podanej szerokości i długości geograficznej. Kolekcja funkcji GeoJSON z odpowiedzi jest następnie `geojson.getFeatures()` wyodrębniana przy użyciu metody i dodawana do źródła danych, co automatycznie powoduje renderowanie danych na mapie za pośrednictwem warstwy symboli. Ostatnia część skrypt ustawia widok kamery mapy za pomocą pola ograniczenia wyników z wykorzystaniem właściwości mapy [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Zapisz plik **MapSearch.html** i odśwież przeglądarkę. Powinieneś zobaczyć mapę wyśrodkowany na Seattle z okrągłoniebieskimi szpilkami dla lokalizacji stacji benzynowych w okolicy.

   ![Wyświetlanie mapy z wynikami wyszukiwania](./media/tutorial-search-location/pins-map.png)

4. Wprowadzenie następującego żądania HTTP w przeglądarce spowoduje wyświetlenie danych pierwotnych renderowanych przez mapę. Ciąg \<Your Azure Maps Key\> zastąp kluczem podstawowym.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Na stronie MapSearch można teraz wyświetlić lokalizacje punktów orientacyjnych, zwracanych przez zapytanie wyszukiwania rozmytego. Do lokalizacji dodamy informacje oraz interaktywne funkcje.

## <a name="add-interactive-data"></a>Dodawanie interaktywnych danych

W wynikach wyszukiwania na utworzonej mapie są używane tylko dane o długości i szerokości geograficznej. Jednak nieprzetworzone JSON, który zwraca usługa wyszukiwania map zawiera dodatkowe informacje o każdej stacji benzynowej. Z nazwą i adresem ulicy. Dane te można zintegrować z mapą za pomocą interaktywnych wyskakujących okienek.

1. Dodaj następujące wiersze kodu `ready` w programie obsługi zdarzeń mapy po kodzie, aby zbadać usługę wyszukiwania rozmytego. Ten kod utworzy wystąpienie popup i dodać mouseover zdarzenia do warstwy symbolu.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    Interfejs `*atlas.Popup` API udostępnia okno informacyjne zakotwiczone w wymaganej pozycji na mapie. 

2. Dodaj następujący kod `GetMap` w ramach funkcji, aby wyświetlić informacje o wyniku najechaszką na wskaźnik myszy w wyskakującym okienku.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Zapisz plik i odśwież przeglądarkę. Teraz mapa w przeglądarce wyświetla wyskakujące okienka informacyjne po najechaniu kursorem na dowolny znacznik wyszukiwania.

    ![Kontrolka mapy platformy Azure i usługa Search Service](./media/tutorial-search-location/popup-map.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta przy użyciu usługi Azure Maps
> * Pobieranie klucza podstawowego konta
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wyszukiwanie punktu orientacyjnego w pobliżu przy użyciu usługi Search Service

> [!div class="nextstepaction"]
> [Wyświetl pełny kod źródłowy](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Zobacz próbkę na żywo](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Następny samouczek przedstawia sposób wyświetlenia trasy między dwiema lokalizacjami.

> [!div class="nextstepaction"]
> [Trasa do miejsca docelowego](./tutorial-route-location.md)
