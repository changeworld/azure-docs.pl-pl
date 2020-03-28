---
title: 'Samouczek: Kieruj pojazdy elektryczne za pomocą notesów platformy Azure (Python) | Mapy platformy Microsoft Azure'
description: Rozsyłaj pojazdy elektryczne przy użyciu interfejsów API routingu usługi Microsoft Azure Maps i notesów platformy Azure.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3118ca39ec0efd42c9f7b622c91f857034ef4b03
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333831"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Samouczek: Kieruj pojazdy elektryczne za pomocą notesów platformy Azure (Python)

Usługa Azure Maps to portfolio interfejsów API usług geoprzestrzennych, które są natywnie zintegrowane z platformą Azure. Te interfejsy API umożliwiają deweloperom, przedsiębiorstwom i dostawcom oprogramowania tworzenie aplikacji obsługujących lokalizację, IoT, mobilność, logistykę i rozwiązania do śledzenia zasobów. 

Interfejsy API REST usługi Azure Maps można wywołać z języków takich jak Python i R, aby włączyć analizę danych geoprzestrzennych i scenariusze uczenia maszynowego. Usługa Azure Maps oferuje niezawodny zestaw [interfejsów API routingu,](https://docs.microsoft.com/rest/api/maps/route) które umożliwiają użytkownikom obliczanie tras między kilkoma punktami danych. Obliczenia są oparte na różnych warunkach, takich jak typ pojazdu lub obszar osiągalny. 

W tym samouczku, idziesz pomóc kierowcy, którego bateria pojazdu elektrycznego jest niska. Kierowca musi znaleźć najbliższą możliwą stację ładowania z lokalizacji pojazdu.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie i uruchamianie notesu Jupyter w [notesach platformy Azure](https://docs.microsoft.com/azure/notebooks) w chmurze.
> * Wywołanie interfejsów API REST usługi Azure Maps w języku Python.
> * Wyszukaj dostępny zasięg w oparciu o model zużycia pojazdu elektrycznego.
> * Wyszukaj stacje ładowania pojazdów elektrycznych w zasięgu osiągalnym lub izochrone.
> * Renderuj na mapie osiągalną granicę zasięgu i stacje ładowania.
> * Znajdź i wizualizuj trasę do najbliższej stacji ładowania pojazdów elektrycznych na podstawie czasu jazdy.


## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten samouczek, musisz najpierw utworzyć konto usługi Azure Maps i uzyskać klucz podstawowy (klucz subskrypcji). 

Aby utworzyć subskrypcję konta usługi Azure Maps, postępuj zgodnie z instrukcjami w [sekcji Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps). Potrzebujesz subskrypcji konta usługi Azure Maps z warstwą cenową S1. 

Aby uzyskać podstawowy klucz subskrypcji dla swojego konta, postępuj zgodnie z instrukcjami dotyczącymi [uzyskania klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Aby uzyskać więcej informacji na temat uwierzytelniania w usłudze Azure Maps, zobacz [zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebook"></a>Tworzenie notesu platformy Azure

Aby wykonać ten samouczek, należy utworzyć projekt notesu platformy Azure i pobrać i uruchomić plik notesu Jupyter. Plik notesu zawiera kod języka Python, który implementuje scenariusz w tym samouczku. Aby utworzyć projekt notesu platformy Azure i przekazać do niego dokument notesu Jupyter, wykonaj następujące czynności:

1. Przejdź do [usługi Azure Notess](https://notebooks.azure.com) i zaloguj się. Aby uzyskać więcej informacji, zobacz [Szybki start: Zaloguj się i ustaw identyfikator użytkownika](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. U góry strony profilu publicznego wybierz pozycję **Moje projekty**.

    ![Przycisk Moje projekty](./media/tutorial-ev-routing/myproject.png)

1. Na stronie **Moje projekty** wybierz pozycję **Nowy projekt**.
 
   ![Przycisk Nowy projekt](./media/tutorial-ev-routing/create-project.png)

1. W okienku **Utwórz nowy projekt** wprowadź nazwę projektu i identyfikator projektu.
 
    ![Okienko Utwórz nowy projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Wybierz **pozycję Utwórz**.

1. Po utworzeniu projektu pobierz ten [plik dokumentu notesu Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [repozytorium notesu usługi Azure Maps Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Na liście projekty na stronie **Moje projekty** wybierz projekt, a następnie wybierz pozycję **Przekaż,** aby przekazać plik dokumentu notesu Jupyter. 

    ![przesyłanie notesu](./media/tutorial-ev-routing/upload-notebook.png)

1. Przekaż plik z komputera, a następnie wybierz pozycję **Gotowe**.

1. Po pomyślnym zakończeniu przekazywania plik jest wyświetlany na stronie projektu. Kliknij dwukrotnie plik, aby otworzyć go jako notes Jupyter.

Spróbuj zrozumieć funkcje, które są implementowane w pliku notesu. Uruchom kod, w pliku notesu, po jednej komórce naraz. Kod można uruchomić w każdej komórce, wybierając przycisk **Uruchom** u góry aplikacji notesu.

  ![Przycisk Uruchom](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalowanie pakietów na poziomie projektu

Aby uruchomić kod w notesie, zainstaluj pakiety na poziomie projektu, wykonując następujące kroki:

1. Pobierz plik [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [repozytorium notesu usługi Azure Maps Jupyter,](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)a następnie przekaż go do projektu.
1. Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**. 
1. W **okienku Ustawienia projektu** wybierz kartę **Środowisko,** a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Kroki konfiguracji środowiska**wykonaj następujące czynności:   
    a. Z pierwszej listy rozwijanej wybierz pozycję **Requirements.txt**.  
    b. Na drugiej liście rozwijanej wybierz plik *requirements.txt.*  
    d. W trzeciej liście rozwijanej wybierz **pythonową wersję 3.6** jako wersję.
1. Wybierz **pozycję Zapisz**.

    ![Instalowanie pakietów](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Załaduj wymagane moduły i struktury

Aby załadować wszystkie wymagane moduły i struktury, uruchom następujący skrypt.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Poproś o granicę zakresu osiąganego

Firma dostarczająca paczki ma w swojej flocie kilka pojazdów elektrycznych. W ciągu dnia pojazdy elektryczne muszą być ładowane bez konieczności powrotu do magazynu. Za każdym razem, gdy pozostałe naładowanie spadnie do mniej niż godziny, wyszukujesz zestaw stacji ładowania, które znajdują się w osiągalnym zasięgu. Zasadniczo wyszukujesz stację ładującą, gdy bateria jest naładowana. I otrzymujesz informacje o granicach dla tego zakresu stacji ładowania. 

Ponieważ firma woli korzystać z tras, które wymagają równowagi ekonomicznej i prędkości, żądana routeType jest *eko*. Poniższy skrypt wywołuje [interfejs API get range marszruty](https://docs.microsoft.com/rest/api/maps/route/getrouterange) usługi routingu usługi Azure Maps. Wykorzystuje parametry dla modelu zużycia pojazdu. Następnie skrypt analizuje odpowiedź, aby utworzyć obiekt wielokąta formatu geojson, który reprezentuje maksymalny zasięg samochodu.

Aby określić granice osiągalnej odległości pojazdu elektrycznego, uruchom skrypt w następującej komórce:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Wyszukiwanie stacji ładowania pojazdów elektrycznych w zasięgu

Po określiniu osiągalnej odległości (izochrone) dla pojazdu elektrycznego możesz wyszukać stacje ładowania w tym zakresie. 

Poniższy skrypt wywołuje [interfejs API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)wewnątrz geometrii usługi Azure Maps Post . Wyszukuje stacje ładowania pojazdów elektrycznych, w granicach maksymalnego zasięgu samochodu. Następnie skrypt analizuje odpowiedź do tablicy dostępnych lokalizacji.

Aby wyszukać stacje ładowania pojazdów elektrycznych w zasięgu osiągalnym, uruchom następujący skrypt:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Przekazywanie osiągalnej oferty i punktów ładowania do usługi Azure Maps Data Service

Na mapie warto wizualizować stacje ładowania i granicę dla maksymalnego zasięgu pojazdu elektrycznego. W tym celu przekaż dane dotyczące granic i stacje ładowania jako obiekty geojson do usługi Azure Maps Data Service. Użyj [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Aby przekazać dane dotyczące granicy i punktu ładowania do usługi Azure Maps Data Service, uruchom następujące dwie komórki:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Renderowanie stacji ładowania i osiągalnej odległości na mapie

Po przekazaniu danych do usługi danych należy wywołać usługę Azure Maps [Pobierz obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage). Ta usługa służy do renderowania punktów ładowania i maksymalnej osiągalnej granicy na statycznym obrazie mapy, uruchamiając następujący skrypt:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Mapa pokazująca zakres lokalizacji](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Znajdź optymalną stację ładującą

Po pierwsze, chcesz określić wszystkie potencjalne stacje ładowania w zasięgu osiągalnym. Następnie chcesz wiedzieć, który z nich można osiągnąć w minimalnym czasie. 

Poniższy skrypt wywołuje [interfejs API routingu macierzowej](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)usługi Azure Maps . Zwraca określoną lokalizację pojazdu, czas podróży i odległość do każdej stacji ładującej. Skrypt w następnej komórce analizuje odpowiedź, aby zlokalizować najbliższą dostępną stację ładującą w odniesieniu do czasu.

Aby znaleźć najbliższą dostępną stację ładującą, do której można dotrzeć w jak najmniejszym czasie, uruchom skrypt w następującej komórce:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Oblicz trasę do najbliższej stacji ładowania

Po znalezieniu najbliższej stacji ładowania możesz zadzwonić do [interfejsu API Pobierz wskazówki trasy,](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) aby poprosić o szczegółową trasę z bieżącej lokalizacji pojazdu elektrycznego do stacji ładowania.

Aby uzyskać trasę do stacji ładującej i przeanalizować odpowiedź w celu utworzenia obiektu geojson reprezentującego trasę, uruchom skrypt w następującej komórce:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Wizualizuj trasę

Aby ułatwić wizualizację trasy, należy najpierw przekazać dane trasy jako obiekt geojson do usługi Azure Maps Data Service. Aby to zrobić, użyj [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)usługi Azure Maps . Następnie zadzwoń do usługi renderowania, [Pobierz interfejs API obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage), aby renderować trasę na mapie i wizualizować ją.

Aby uzyskać obraz renderowanego szlaku na mapie, uruchom następujący skrypt:

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Mapa przedstawiająca trasę](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak bezpośrednio wywoływać interfejsy API REST usługi Azure Maps i wizualizować dane usługi Azure Maps przy użyciu języka Python.

Aby zapoznać się z interfejsami API usługi Azure Maps, które są używane w tym samouczku, zobacz:

* [Uzyskaj zakres trasy](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Opublikuj wyszukiwanie wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Przekazywanie danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Render - Pobierz obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Macierz postu](https://docs.microsoft.com/rest/api/maps/route/postroutematrix)
* [Uzyskaj wskazówki dojazdu](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Aby uzyskać pełną listę interfejsów API REST usługi Azure Maps, zobacz [Interfejsy API REST usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Aby dowiedzieć się więcej o notesach platformy Azure, zobacz [Notesy platformy Azure](https://docs.microsoft.com/azure/notebooks).
