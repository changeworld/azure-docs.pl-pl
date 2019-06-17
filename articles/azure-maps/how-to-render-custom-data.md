---
title: Sposób renderowania niestandardowych danych na mapie rastrowe w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Renderowanie danych niestandardowych na mapie rastrowe w usługi Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/03/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a9fed8464bd19c4b8a32e37c8c97698f0a2d9503
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734299"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderowanie danych niestandardowych na mapy rastrowej

W tym artykule wyjaśniono, jak używać [usługi obraz statyczny](https://docs.microsoft.com/rest/api/maps/render/getmapimage) przy użyciu obrazu kompozycji funkcje umożliwiające nakładki na podstawie mapy rastrowej. Kompozycja obraz obejmuje możliwość skorzystania z kafelka rastrowych, wraz z dodatkowymi danymi, takich jak pinezki niestandardowe, etykiety i nakładki geometrii.

Aby renderować pinezki niestandardowe, etykiety i nakładki geometry, można użyć aplikacji Postman. Możesz użyć usługi Azure Maps [interfejsów API usługi danych](https://docs.microsoft.com/rest/api/maps/data) do przechowywania i renderowania nakładki.


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury opisane w tym artykule, należy najpierw [utworzyć konto usługi Azure Maps](how-to-manage-account-keys.md) w warstwę cenową S1.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderowanie pinezki z etykietami i obraz niestandardowy

> [!Note]
> Procedura w tej sekcji wymaga konta usługi Azure Maps w warstwie cenowej S0 lub S1.

Usługi Azure Maps konta S0 warstwy obsługuje tylko jedno wystąpienie `pins` parametru. Umożliwia renderowanie pinezki do pięciu, określony w żądaniu adresu URL, za pomocą obrazu niestandardowego.

Aby renderować pinezki z etykietami i niestandardowego obrazu, wykonaj następujące kroki:

1. Utwórz kolekcję, w którym będzie przechowywany żądania. W aplikacji Postman wybierz **New**. W **Utwórz nowy** wybierz **kolekcji**. Nazwij kolekcję, a następnie wybierz pozycję **Utwórz** przycisku. 

2. Aby utworzyć żądanie, wybierz **New** ponownie. W **Utwórz nowy** wybierz **żądania**. Wprowadź **nazwy żądania** pinezki, wybierz kolekcję utworzoną w poprzednim kroku jako lokalizacja, w której chcesz zapisać żądanie, a następnie wybierz **Zapisz**.
    
    ![Utwórz żądanie w narzędziu Postman](./media/how-to-render-custom-data/postman-new.png)

3. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Oto obraz wynikowy:

    ![Niestandardowe pinezki z etykietą](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Pobieranie danych z magazynu danych usługi Azure Maps

> [!Note]
> Procedura w tej sekcji wymaga konta usługi Azure Maps w warstwę cenową S1.

Możesz również uzyskać informacje o lokalizacji ścieżki i numer pin przy użyciu [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Wykonaj poniższe kroki, aby przekazać dane ścieżki i numerów PIN.

1. W aplikacji Postman Otwórz na nowej karcie w kolekcji, który został utworzony w poprzedniej sekcji. Wybierz metodę POST protokołu HTTP, na karcie konstruktora i wprowadź następujący adres URL żądania POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na **Params** wprowadź następujące pary klucz/wartość, które są używane do adresu URL żądania POST. Zastąp `subscription-key` wartość z kluczem subskrypcji usługi Azure Maps.
    
    ![Parametry klucz wartość w narzędziu Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na **treści** kartę, zaznacz pierwotne format wejściowy i wybierz JSON jako format wejściowy z listy rozwijanej. Podaj dane JSON jako dane do przekazania:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. Wybierz **wysyłania** i przejrzyj nagłówka odpowiedzi. Nagłówek location zawiera identyfikator URI używany do uzyskanie dostępu i pobranie danych do użycia w przyszłości. Zawiera także unikatowy identyfikator `udId` przekazanych danych.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. Użyj `udId` wartość odebrana od API przekazywanie danych do renderowania funkcje na mapie. Aby to zrobić, otwórz nową kartę w kolekcji, który został utworzony w poprzedniej sekcji. Wybierz metodę GET HTTP, na karcie konstruktora, a następnie wprowadź ten adres URL żądania GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Poniżej przedstawiono na ilustracji odpowiedzi:

    ![Pobieranie danych z magazynu danych usługi Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderowanie Wielokąt przy użyciu koloru i krycia

> [!Note]
> Procedura w tej sekcji wymaga konta usługi Azure Maps w warstwę cenową S1.


Można zmodyfikować wygląd wielokąta przy użyciu stylu Modyfikatory z [parametr ścieżki](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. W aplikacji Postman Otwórz na nowej karcie w kolekcji, która została utworzona wcześniej. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL, aby skonfigurować żądanie GET w celu renderowania Wielokąt przy użyciu koloru i krycia:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

    Poniżej przedstawiono na ilustracji odpowiedzi:

    ![Renderowanie nieprzezroczyste wielokąta](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderowanie okrąg i pinezki z etykietami niestandardowymi

> [!Note]
> Procedura w tej sekcji wymaga konta usługi Azure Maps w warstwę cenową S1.


Aby włączyć pinezki i ich etykiet większy lub mniejszy przy użyciu `sc` modyfikator styl skali. Ten modyfikator przyjmuje wartość, która jest większa niż zero. Wartość 1 jest standardowa skali. Wartości większej niż 1 spowoduje, że numery PIN będzie większa, a wartości mniejszej niż 1 spowoduje, że ich mniejsze. Aby uzyskać więcej informacji na temat Modyfikatory styl zobacz [parametry ścieżki usługi obraz statyczny](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Wykonaj następujące kroki do renderowania okrąg i pinezki z etykiety niestandardowe:

1. W aplikacji Postman Otwórz na nowej karcie w kolekcji, która została utworzona wcześniej. Wybierz metodę GET HTTP, na karcie konstruktora, a następnie wprowadź ten adres URL żądania GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Poniżej przedstawiono na ilustracji odpowiedzi:

    ![Renderowanie koło z niestandardowych pinezki](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Kolejne kroki


* Zapoznaj się z [Azure Pobierz mapy obrazu interfejsu API usługi mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dokumentacji.
* Aby dowiedzieć się więcej na temat usługi Azure Maps Data Service, zobacz [dokumentacja usługi](https://docs.microsoft.com/rest/api/maps/data).

