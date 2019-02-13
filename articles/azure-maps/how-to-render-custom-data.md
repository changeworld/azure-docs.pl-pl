---
title: Sposób renderowania niestandardowych danych na mapie rastrowe w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Renderowanie danych niestandardowych na mapie rastrowe w usługi Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9b3c0f7b1ff56cb269f6852be8fd2affeca8b8f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56143801"
---
# <a name="render-custom-data-on-raster-map"></a>Renderowanie danych niestandardowych na mapy rastrowej

W tym artykule dowiesz się, jak używać [usługi obraz statyczny](https://docs.microsoft.com/rest/api/maps/render/getmapimage) przy użyciu obrazu kompozycji funkcje umożliwiające nakładki na podstawie mapy rastrowej. Kompozycja obraz obejmuje możliwość uzyskania kafelka rastrowych z powrotem dodatkowe dane, takie jak pinezki niestandardowe, etykiety i nakładki geometrii. Aby renderować pinezki niestandardowe, etykiety i geometry, będziemy używać aplikacji postman. Otwórz aplikację Postman, kliknij przycisk Nowa | Utwórz nowy, wybierz nazwę kolekcji lub folder w celu zapisania go i kliknij przycisk Zapisz.

Będziemy używać usługi Azure Maps [interfejsów API usługi danych](https://docs.microsoft.com/rest/api/maps/data) do przechowywania i renderowania nakładki. 


## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps 

Aby wykonać kroki opisane w tym przewodniku, należy najpierw zobacz [Zarządzanie kontem i kluczami](how-to-manage-account-keys.md) do tworzenia i zarządzania subskrypcją konta S1 warstwy cenowej.

## <a name="render-pushpins-with-labels-and-custom-image"></a>Renderowanie pinezki z etykietami i obrazu niestandardowego

> [!Note]
> W tym przykładzie konieczne jest posiadanie konta usługi Azure Maps w modelu cen warstwy S0 lub S1. 

Konto usługi Azure Maps, jednostki SKU S0 obsługuje tylko jedno wystąpienie `pins` parametr, pozwalając użytkownikom na maksymalnie 5 pinezki określony w żądaniu adresu url przy użyciu niestandardowego obrazu renderowania.

Do renderowania wypychania numerów PIN z etykietami i niestandardowego obrazu, wykonaj następujące czynności:

1. Otwórz aplikację Postman i kliknij nowy | Utwórz nową, a następnie wybierz żądanie. Wprowadź nazwę żądania pinezki renderowania, wybierz kolekcję lub folder w celu zapisania go i kliknij przycisk Zapisz.
    
    ![Przekaż wirtualne ogrodzenia przy użyciu narzędzia Postman](./media/tutorial-geofence/postman-new.png)

2. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL żądania GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Poniżej znajduje się obraz odpowiedzi, który jest pobierany.

    ![Renderowanie niestandardowe pinezki z etykietami](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Pobieranie danych z magazynu danych usługi Azure Maps

> [!Note]
> W tym przykładzie wymaga konta usługi Azure Maps w modelu cen warstwy S1.

Informacje o lokalizacji ścieżki i numerów PIN można także uzyskać za pośrednictwem [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/mapdata/upload). Wykonaj poniższe kroki, aby przekazać dane ścieżki i numerów PIN.

1. W aplikacji Postman Otwórz na nowej karcie w tej samej kolekcji, które zostały utworzone powyżej. Wybierz metodę POST protokołu HTTP, na karcie konstruktora i wprowadź następujący adres URL żądania POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Kliknij parametry, a następnie wprowadź następujące pary klucz-wartość służący do adresu URL żądania POST. Zastąp wartość klucz subskrypcji z kluczem subskrypcji usługi Azure Maps.
    
    ![Parametry klucz-wartość narzędzia Postman](./media/how-to-render-custom-data/postman-key-vals.png)

3. Kliknij przycisk **treści** a następnie wybierz format raw danych wejściowych i wybrać JSON jako format wejściowy z listy rozwijanej. Podaj następujące dane JSON jako dane do przekazania:
    
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

4. Kliknij przycisk Wyślij i przejrzyj nagłówka odpowiedzi. Nagłówek location zawiera identyfikator URI do uzyskanie dostępu i pobranie danych do użycia w przyszłości. Zawiera ona także unikatowy `udId` przekazanych danych.   

  ```HTTP
  https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
  ```



5. Użyj wartości `udid` otrzymane od API przekazywanie danych do renderowania funkcje na mapie. Aby to zrobić, otwórz nową kartę w tej samej kolekcji, które zostały utworzone powyżej. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL żądania GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. Obraz odpowiedź powinna wyglądać następująco:

    ![Renderowanie danych](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-polygon-with-color-and-opacity"></a>Renderowanie Wielokąt przy użyciu koloru i krycia

> [!Note]
> W tym przykładzie wymaga konta usługi Azure Maps w modelu cen warstwy S1.

Można zmodyfikować wygląd wielokąta przy użyciu stylu Modyfikatory z [parametr ścieżki](https://docs.microsoft.com/rest-staging/api/maps/render/getmapimage#uri-parameters).

1. W aplikacji Postman Otwórz na nowej karcie w tej samej kolekcji, które zostały utworzone powyżej. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET do renderowania Wielokąt przy użyciu koloru i krycia:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

Obraz odpowiedź powinna wyglądać następująco:

![Renderowanie nieprzezroczyste wielokąta](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-polygon-with-circle-and-push-pins-with-custom-labels"></a>Renderowanie wielokąta z okrąg i wypychania numerów PIN z etykietami niestandardowymi

> [!Note]
> W tym przykładzie wymaga konta usługi Azure Maps w modelu cen warstwy S1.

Umożliwia pinezki i ich etykiet większy lub mniejszy przy użyciu modyfikatora styl skalowania "sc". Jest to wartość większą niż zero. Wartość 1 jest standardowa skali. Wartości większej niż 1 spowoduje, że numery PIN będzie większa, a wartości mniejszej niż 1 spowoduje, że ich mniejsze. Aby uzyskać więcej informacji na temat styl modyfikatorów, zobacz [parametry ścieżki usługi obraz statyczny](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

Wykonaj poniższe kroki, aby renderować wielokąta z okrąg i wypychania numerów PIN przy użyciu etykiet niestandardowych:

1. W aplikacji Postman Otwórz na nowej karcie w tej samej kolekcji, które zostały utworzone powyżej. Wybierz metodę GET HTTP, na karcie konstruktora i wprowadź następujący adres URL żądania GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

Obraz odpowiedź powinna wyglądać następująco:

![Renderowanie koło z niestandardowych numerów PIN](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z [Azure Pobierz mapy obrazu interfejsu API usługi mapy](https://docs.microsoft.com/rest/api/maps/search) dokumentacji.
* Aby dowiedzieć się więcej na temat funkcji usługi Azure Maps Data Service, zobacz [dokumentacja usługi](https://docs.microsoft.com/rest/api/maps/data).