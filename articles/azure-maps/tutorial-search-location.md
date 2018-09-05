---
title: Wyszukiwanie przy użyciu usługi Azure Maps | Microsoft Docs
description: Wyszukiwanie pobliskiego punktu orientacyjnego za pomocą usługi Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 08/23/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e30d84c70f786a5bea25073c70a29b63c9a00ae9
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917666"
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Wyszukiwanie pobliskich punktów orientacyjnych za pomocą usługi Azure Maps

W tym samouczku przedstawiono konfigurowanie konta przy użyciu usługi Azure Maps, a następnie wyszukiwanie punktu orientacyjnego za pomocą interfejsów API usługi Maps. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta usługi Azure Maps
> * Pobieranie klucza podstawowego dla konta usługi Maps
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wyszukiwanie pobliskiego punktu orientacyjnego przy użyciu usługi wyszukiwania Maps

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Tworzenie konta przy użyciu usługi Azure Maps

Utwórz nowe konto usługi Maps, wykonując następujące czynności:

1. W lewym górnym rogu witryny [Azure Portal](https://portal.azure.com) kliknij przycisk **Utwórz zasób**.
2. W polu *Wyszukaj w portalu Marketplace* wpisz **Maps**.
3. Z listy *Wyniki* wybierz pozycję **Maps**. Kliknij przycisk **Utwórz** znajdujący się poniżej mapy. 
4. Na stronie **Tworzenie konta usługi Maps** wprowadź następujące wartości:
    - *Nazwa* nowego konta. 
    - *Subskrypcja*, która ma być używana dla tego konta.
    - Nazwa *grupy zasobów* dla tego konta. Można wybrać pozycję *Utwórz nowe* lub *Użyj istniejącego* dla grupy zasobów.
    - Wybierz *lokalizację grupy zasobów*.
    - Zapoznaj się z *Licencją* oraz *Zasadami zachowania poufności informacji* i zaznacz pole wyboru, aby zaakceptować warunki. 
    - Kliknij przycisk **Utwórz**.
   
    ![Tworzenie konta usługi Maps w portalu](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Pobieranie klucza podstawowego konta

Po pomyślnym utworzeniu konta usługi Maps pobierz klucz, który umożliwia wysyłanie zapytań do interfejsów API usługi Maps.

1. Otwórz konto usługi Maps w portalu.
2. W sekcji ustawień wybierz pozycję **Klucze**.
3. Skopiuj **klucz podstawowy** do schowka. Zapisz go lokalnie — będzie używany w dalszej części tego samouczka. 

    ![Pobieranie klucza podstawowego w portalu](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Tworzenie nowej mapy 
Interfejs API kontrolki mapy to wygodna biblioteka klienta, która ułatwia integrowanie usługi Maps z aplikacją internetową. Zmniejsza ona złożoność podstawowych wywołań usługi REST i zwiększa produktywność dzięki składnikom z możliwością definiowania stylu i dostosowywania. Poniższe kroki pokazują, jak utworzyć statyczną stronę HTML osadzoną przy użyciu interfejsu API kontrolki mapy. 

1. Na maszynie lokalnej utwórz nowy plik i nadaj mu nazwę **MapSearch.html**. 
2. Dodaj następujące składniki HTML do pliku:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" /> 
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
    Zwróć uwagę, że nagłówek HTML zawiera pliki zasobów CSS i JavaScript obsługiwane przez bibliotekę kontrolek mapy platformy Azure. Zanotuj segment *script* dodany do *treści* pliku HTML. Ten segment będzie zawierać śródwierszowy kod JavaScript umożliwiający dostęp do interfejsów API usługi Azure Maps.
 
3. Dodaj następujący kod JavaScript do bloku *script* w pliku HTML. Zastąp ciąg **\<your account key\>** kluczem podstawowym, skopiowanym z konta usługi Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Ten segment inicjuje interfejs API kontrolki mapy dla klucza konta usługi Azure Maps. **Atlas** to przestrzeń nazw, która zawiera interfejs API i powiązane składniki wizualne. **Atlas.Map** udostępnia kontrolkę wizualnej i interaktywnej mapy internetowej. 
    
4. Zapisz zmiany w pliku i otwórz stronę HTML w przeglądarce. Jest to najbardziej podstawowa mapa utworzona przez wywołanie **atlas.map** i podanie klucza konta. 

   ![Wyświetlanie mapy](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Dodawanie funkcji wyszukiwania

W tej sekcji przedstawiono sposób wyszukiwania punktu orientacyjnego na mapie przy użyciu interfejsu API usługi Search w usłudze Maps. Jest to interfejs API RESTful umożliwiający deweloperom wyszukiwanie adresów, punktów orientacyjnych i innych informacji geograficznych. Usługa Search Service przypisuje informacje o długości i szerokości geograficznej do określonego adresu. Opisany poniżej **moduł usługi** może być używany do wyszukiwania lokalizacji za pomocą interfejsu API usługi Search w usłudze Maps.

### <a name="service-module"></a>Moduł usługi

1. Dodaj nową warstwę do mapy, aby wyświetlić wyniki wyszukiwania. Za kodem inicjującym mapę w bloku skryptu dodaj następujący kod Javascript. 
    
    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Aby utworzyć wystąpienie usługi klienta, za kodem inicjującym mapę w bloku skryptu dodaj następujący kod Javascript.

    ```JavaScript
    var client = new atlas.service.Client(subscriptionKey);
    ```

3. Dodaj następujący blok skryptu w celu utworzenia zapytania. Ten fragment kodu używa usługi Fuzzy Search Service — podstawowego interfejsu API wyszukiwania usługi Search Service. Usługa Fuzzy Search Service obsługuje większość rozmytych danych wejściowych, takich jak dowolna kombinacja adresu i tokenów punktu orientacyjnego (POI). Wyszukuje najbliższe stacje benzynowe w obrębie wybranego promienia wyszukiwania. Odpowiedź jest następnie przekształcana do formatu GeoJSON i konwertowana na cechy punktu, które są dodawane do mapy w formie pinezek. Ostatnia część skryptu dodaje ograniczenia zasięgu wyświetlania aparatu do mapy za pomocą właściwości [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) w usłudze Maps.

    ```JavaScript
    client.search.getSearchFuzzy("gasoline station", {
     lat: 47.6292,
     lon: -122.2337,
     radius: 100000
    }).then(response => {
       // Parse the response into GeoJSON 
       var geojsonResponse = new atlas.service.geojson.GeoJsonSearchResponse(response); 
 
       // Create the point features that will be added to the map as pins 
       var searchPins = geojsonResponse.getGeoJsonResults().features.map(poiResult => { 
           var poiPosition = [poiResult.properties.position.lon, poiResult.properties.position.lat]; 
           return new atlas.data.Feature(new atlas.data.Point(poiPosition), { 
                name: poiResult.properties.poi.name, 
                address: poiResult.properties.address.freeformAddress, 
                position: poiPosition[1] + ", " + poiPosition[0] 
           }); 
       }); 
 
       // Add pins to the map for each POI 
       map.addPins(searchPins, { 
           name: searchLayerName 
       }); 
 
       // Set the camera bounds 
       map.setCameraBounds({ 
           bounds: geojsonResponse.getGeoJsonResults().bbox, 
           padding: 50 
       ); 
    }); 
    ```
4. Zapisz plik **MapSearch.html** i odśwież przeglądarkę. Mapa powinna przedstawiać Seattle ze stacjami paliw oznaczonymi za pomocą niebieskich znaczników. 

   ![Wyświetlanie mapy z wynikami wyszukiwania](./media/tutorial-search-location/pins-map.png)

5. Wprowadzenie następującego żądania HTTP w przeglądarce spowoduje wyświetlenie danych pierwotnych renderowanych przez mapę. Ciąg \<your account key\> zastąp kluczem podstawowym. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Na stronie MapSearch można teraz wyświetlić lokalizacje punktów orientacyjnych, zwracanych przez zapytanie wyszukiwania rozmytego. Do lokalizacji dodamy informacje oraz interaktywne funkcje. 

## <a name="add-interactive-data"></a>Dodawanie interaktywnych danych

W wynikach wyszukiwania na utworzonej mapie są używane tylko dane o szerokości i długości geograficznej. Widać jednak, że nieprzetworzone dane JSON zwracane przez usługę Search Service w usłudze Maps zawierają dodatkowe informacje o każdej stacji paliw, takie jak nazwa i adres. Dane te można zintegrować z mapą za pomocą interaktywnych wyskakujących okienek. 

1. Dodaj następujące wiersze do bloku *script*, aby utworzyć wyskakujące okienka dla punktów orientacyjnych zwróconych przez usługę Search Service:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    Element **atlas.Popup** interfejsu API udostępnia okno informacyjne zakotwiczone w żądanym położeniu na mapie. Ten fragment kodu ustawia zawartość i położenie wyskakującego okienka, a także dodaje do kontrolki `map` odbiornik zdarzeń oczekujący na przesunięcie _myszy_ przez wyskakujące okienko. 

4. Zapisz plik i odśwież przeglądarkę. Teraz mapa w przeglądarce wyświetla wyskakujące okienka informacyjne po najechaniu kursorem na dowolny znacznik wyszukiwania. 

    ![Kontrolka mapy platformy Azure i usługa Search Service](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie konta przy użyciu usługi Azure Maps
> * Pobieranie klucza podstawowego konta
> * Tworzenie nowej strony internetowej przy użyciu interfejsu API kontrolki mapy
> * Wyszukiwanie punktu orientacyjnego w pobliżu przy użyciu usługi Search Service

Następny samouczek przedstawia sposób wyświetlenia trasy między dwiema lokalizacjami. 

> [!div class="nextstepaction"]
> [Trasa do miejsca docelowego](./tutorial-route-location.md)
