---
title: Renderowanie danych niestandardowych na mapie rastrowej | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować dane niestandardowe na mapie rastrowej przy użyciu usługi obrazu statycznego Usługi map microsoft azure.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b8d47b69b4aba14c86fb09176b662aee7d5482d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335516"
---
# <a name="render-custom-data-on-a-raster-map"></a>Renderowanie danych niestandardowych na mapie rastrowej

W tym artykule wyjaśniono, jak korzystać z [usługi obrazów statycznych](https://docs.microsoft.com/rest/api/maps/render/getmapimage), z funkcją kompozycji obrazu, aby zezwolić na nakładki na mapie rastrowej. Kompozycja obrazu zawiera możliwość odzyskania kafelka rastrowego z dodatkowymi danymi, takimi jak niestandardowe pineki, etykiety i nakładki geometrii.

Aby renderować niestandardowe pineki, etykiety i nakładki geometrii, można użyć aplikacji Postman. Za pomocą [interfejsów API usługi danych](https://docs.microsoft.com/rest/api/maps/data) usługi Azure Maps można przechowywać i renderować nakładki.

> [!Tip]
> Często jest znacznie bardziej opłacalne użycie narzędzia Azure Maps Web SDK do pokazynia prostej mapy na stronie sieci web niż do korzystania z usługi obrazu statycznego. Zestaw SDK sieci web używa kafelków map i jeśli użytkownik nie przesuwa i nie powiększa mapy, często generuje tylko ułamek transakcji na obciążenie mapy. Należy zauważyć, że zestaw SDK sieci Web usługi Azure Maps ma opcje wyłączania przesuwania i powiększania. Ponadto zestaw SDK sieci Web usługi Azure Maps zapewnia bogatszy zestaw opcji wizualizacji danych niż statyczna usługa sieci web mapy.  

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-an-azure-maps-account"></a>Tworzenie konta usługi Azure Maps

Aby wykonać procedury w tym artykule, należy najpierw utworzyć konto usługi Azure Maps i uzyskać klucz konta mapy. Postępuj zgodnie z instrukcjami w [tworzenie konta,](quick-demo-map-app.md#create-an-account-with-azure-maps) aby utworzyć subskrypcję konta usługi Azure Maps i wykonaj kroki w [celu uzyskania klucza podstawowego,](quick-demo-map-app.md#get-the-primary-key-for-your-account) aby uzyskać klucz podstawowy dla swojego konta. Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Renderowanie pinezków z etykietami i obrazem niestandardowym

> [!Note]
> Procedura opisana w tej sekcji wymaga konta usługi Azure Maps w warstwie cenowej S0 lub S1.

Warstwa konta Usługi Azure Maps S0 `pins` obsługuje tylko jedno wystąpienie parametru. Umożliwia renderowanie maksymalnie pięciu pinez, określonych w żądaniu adresu URL, z obrazem niestandardowym.

Aby renderować pineki z etykietami i obrazem niestandardowym, wykonaj następujące kroki:

1. Utwórz kolekcję, w której mają być przechowywane żądania. W aplikacji Listonosz wybierz pozycję **Nowy**. W oknie **Utwórz nowy** wybierz pozycję **Kolekcja**. Nazwij kolekcję i wybierz przycisk **Utwórz.** 

2. Aby utworzyć żądanie, wybierz ponownie pozycję **Nowy.** W oknie **Utwórz nowy** wybierz pozycję **Żądaj**. Wprowadź **nazwę żądania** pineki. Wybierz kolekcję utworzoną w poprzednim kroku jako lokalizację, aby zapisać żądanie. Następnie wybierz pozycję **Zapisz**.
    
    ![Tworzenie żądania w listonoszu](./media/how-to-render-custom-data/postman-new.png)

3. Wybierz metodę GET HTTP na karcie konstruktora i wprowadź następujący adres URL, aby utworzyć żądanie GET.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Oto wynikowy obraz:

    ![Niestandardowy pinez z etykietą](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Pobierz dane z magazynu danych usługi Azure Maps

> [!Note]
> Procedura opisana w tej sekcji wymaga konta usługi Azure Maps w warstwie cenowej S1.

Informacje o lokalizacji ścieżki i przypięcia można również uzyskać za pomocą [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). Wykonaj poniższe czynności, aby przesłać dane ścieżki i pinów.

1. W aplikacji Postman otwórz nową kartę w kolekcji utworzonej w poprzedniej sekcji. Wybierz metodę POST HTTP na karcie konstruktora i wprowadź następujący adres URL, aby wysłać żądanie POST:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Na karcie **Params** wprowadź następujące pary klucz/wartość, które są używane dla adresu URL żądania POST. Zastąp `subscription-key` wartość kluczem subskrypcji usługi Azure Maps.
    
    ![Params klucz/wartość w listonosz](./media/how-to-render-custom-data/postman-key-vals.png)

3. Na karcie **Treść** wybierz format pliku wejściowego nieprzetworzonego i wybierz JSON jako format wejściowy z listy rozwijanej. Podaj ten JSON jako dane do przekazania:
    
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

4. Wybierz **pozycję Wyślij** i przejrzyj nagłówek odpowiedzi. Po pomyślnym żądaniu nagłówek Lokalizacja będzie zawierał identyfikator URI stanu, aby sprawdzić bieżący stan żądania przekazania. Identyfikator URI stanu będzie w następującym formacie.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Skopiuj identyfikator URI stanu i dołącz do niego parametr klucza subskrypcji o wartości klucza subskrypcji konta usługi Azure Maps. Użyj tego samego klucza subskrypcji konta, który został użyty do przekazania danych. Format identyfikatora URI stanu powinien wyglądać następująco poniżej:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. Aby uzyskać identyfikator udId, otwórz nową kartę w aplikacji Postman. Wybierz pozycję POBIERZ METODĘ HTTP na karcie konstruktora. Jeśli przekazywanie danych zakończyło się pomyślnie, otrzymasz identyfikator udId w treści odpowiedzi. Skopiuj identyfikator udId.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Użyj `udId` wartości otrzymanej z interfejsu API przekazywania danych, aby renderować obiekty na mapie. Aby to zrobić, otwórz nową kartę w kolekcji utworzonej w poprzedniej sekcji. Wybierz metodę GET HTTP na karcie konstruktora, zastąp {subscription-key} i {udId} wartościami i wprowadź ten adres URL, aby złożyć żądanie GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    Oto obraz odpowiedzi:

    ![Pobierz dane z magazynu danych usługi Azure Maps](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renderowanie wielokąta z kolorem i kryciem

> [!Note]
> Procedura opisana w tej sekcji wymaga konta usługi Azure Maps w warstwie cenowej S1.


Wygląd wielokąta można zmodyfikować za pomocą modyfikatorów stylu z [parametrem path](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).

1. W aplikacji Postman otwórz nową kartę w kolekcji utworzonej wcześniej. Wybierz metodę GET HTTP na karcie konstruktora i wprowadź następujący adres URL, aby skonfigurować żądanie GET do renderowania wielokąta z kolorem i kryciem:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    Oto obraz odpowiedzi:

    ![Renderowanie nieprzezroczystego wielokąta](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Renderowanie okręgu i pinez z etykietami niestandardowymi

> [!Note]
> Procedura opisana w tej sekcji wymaga konta usługi Azure Maps w warstwie cenowej S1.


Wygląd pinezki można zmodyfikować, dodając modyfikatory stylu. Na przykład, aby zwiększyć lub zmniejszyć pineki i `sc` ich etykiety, użyj modyfikatora "styl skali". Ten modyfikator przyjmuje wartość, która jest większa niż zero. Wartość 1 jest standardową skalą. Wartości większe niż 1 sprawią, że kołki będą większe, a wartości mniejsze niż 1 zmniejszą je. Aby uzyskać więcej informacji na temat modyfikatorów stylu, zobacz [parametry ścieżki usługi obrazu statycznego](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Wykonaj następujące kroki, aby renderować okrąg i pineki z etykietami niestandardowymi:

1. W aplikacji Postman otwórz nową kartę w kolekcji utworzonej wcześniej. Wybierz metodę GET HTTP na karcie konstruktora i wprowadź ten adres URL, aby złożyć żądanie GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Oto obraz odpowiedzi:

    ![Renderowanie okręgu za pomocą niestandardowych pinezek](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Aby zmienić kolor pinezek z ostatniego kroku, zmień modyfikator stylu "co". Spójrz `pins=default|la15+50|al0.66|lc003C62|co002D62|`na , bieżący kolor zostanie określony jako #002D62 w CSS. Załóżmy, że chcesz go zmienić na #41d42a. Napisz nową wartość koloru po specyfikatorze "co", w ten sposób: `pins=default|la15+50|al0.66|lc003C62|co41D42A|`. Złożyć nowe żądanie GET:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Oto obraz odpowiedzi po zmianie kolorów pinów:

    ![Renderowanie okręgu ze zaktualizowanymi pinezkami](./media/how-to-render-custom-data/circle-updated-pins.png)

Podobnie można zmieniać, dodawać i usuwać inne modyfikatory stylu.

## <a name="next-steps"></a>Następne kroki


* Zapoznaj się z dokumentacją [interfejsu API obrazów obrazów map usługi Azure Maps.](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* Aby dowiedzieć się więcej o usłudze Azure Maps Data Service, zapoznaj się z [dokumentacją usługi](https://docs.microsoft.com/rest/api/maps/data).

