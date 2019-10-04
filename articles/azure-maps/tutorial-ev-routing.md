---
title: Routing pojazdu elektrycznego przy użyciu Azure Notebooks (Python) | Microsoft Docs
description: Routing EV przy użyciu Azure Maps interfejsów API routingu i Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: c4b46bc952782fc7c9b56d6f0c049fe17b63d0f2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836337"
---
# <a name="electric-vehicle-routing-using-azure-notebooks-python"></a>Elektryczne Routing pojazdu przy użyciu Azure Notebooks (Python)

Azure Maps to portfolio interfejsów API usług geoprzestrzennych natywnie zintegrowanych z platformą Azure, które umożliwiają deweloperom, firmom i dostawcom oprogramowania Tworzenie aplikacji obsługujących lokalizację oraz rozwiązań IoT, mobilności, logistycznych i śledzenia zasobów. Interfejsy API REST Azure Maps mogą być wywoływane z języków, takich jak Python i R, aby umożliwić analizę danych geoprzestrzennych i scenariusze uczenia maszynowego. Azure Maps oferuje niezawodny zestaw [interfejsów API routingu](https://docs.microsoft.com/rest/api/maps/route) , który umożliwia użytkownikom obliczanie tras między kilkoma punktami danych w oparciu o różne warunki, takie jak typ pojazdu lub dostępny obszar. W tym samouczku przedstawiono scenariusz, w którym można uzyskać pomoc dotyczącą sterownika pojazdu elektrycznego, którego pojazd jest niski, aby znaleźć najbliższą możliwą stację ładowania w odniesieniu do czasu dysku.

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Tworzenie i uruchamianie Jupyter Notebook na [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) w chmurze
> * Wywoływanie interfejsów API REST Azure Maps w języku Python
> * Wyszukaj osiągalny zakres oparty na modelu zużycia pojazdu elektrycznego.
> * Wyszukaj stacje opłat za pojazdy elektryczne w dostępnym zakresie (lub isochrone).
> * Renderuj dostępną granicę zakresu i stacje ładowania na mapie.
> * Znajdź i Wizualizuj trasę do najbliższej stacji naładowania pojazdu elektrycznego na podstawie czasu.


## <a name="prerequisites"></a>Wymagania wstępne 

Aby wykonać kroki opisane w tym samouczku, musisz najpierw utworzyć konto Azure Maps i uzyskać klucz podstawowy (klucz subskrypcji). Postępuj zgodnie z instrukcjami w sekcji [Zarządzanie kontem](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) , aby utworzyć subskrypcję konta usługi Azure Maps z warstwą cenową S1, a następnie wykonaj kroki opisane w sekcji [Uzyskiwanie klucza podstawowego](./tutorial-search-location.md#getkey) , aby uzyskać podstawowy klucz subskrypcji dla konta.

## <a name="create-an-azure-notebook"></a>Tworzenie notesu platformy Azure

Aby wykonać czynności opisane w tym samouczku, należy utworzyć projekt notesu platformy Azure i pobrać i uruchomić plik notesu Jupyter. Plik notesu zawiera kod języka Python, który implementuje scenariusz w tym samouczku. Wykonaj poniższe kroki, aby utworzyć projekt notesu platformy Azure i przekazać do niego dokument notesu Jupyter.

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. Aby uzyskać więcej informacji, zobacz [Szybki Start](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
2. Na stronie Twój profil publiczny wybierz pozycję **Moje projekty** w górnej części strony.

    ![mój projekt](./media/tutorial-ev-routing/myproject.png)

3. Na stronie **Moje projekty** wybierz pozycję **Nowy projekt**.
 
   ![Nowy projekt](./media/tutorial-ev-routing/create-project.png)

4. W wyświetlonym oknie podręcznym **Utwórz nowy projekt** Wprowadź poniższe informacje, a następnie kliknij pozycję **Utwórz**:
    * Nazwa projektu
    * Identyfikator projektu
 
    ![Utwórz projekt](./media/tutorial-ev-routing/create-project-window.png)

5. Po utworzeniu projektu Pobierz [plik dokumentu notesu Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [repozytorium Jupyter Notebook Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

6. Wybierz projekt z listy projekty na stronie **Moje projekty** i kliknij przycisk **Przekaż** , aby przekazać plik dokumentu notesu Jupyter. Przekaż plik z komputera, a następnie kliknij przycisk **gotowe**.

    ![Przekaż Notes](./media/tutorial-ev-routing/upload-notebook.png)

7. Po pomyślnym przekazaniu plik zostanie wyświetlony na stronie Twojego projektu. Kliknij plik notesu, aby otworzyć go jako Jupyter Notebook.

Aby lepiej zrozumieć funkcje zaimplementowane w pliku notesu, zalecamy uruchomienie kodu w notesie po jednej komórce w danym momencie. Możesz uruchomić kod w każdej komórce, klikając przycisk **Run (Uruchom** ) w górnej części aplikacji Notes.

  ![Wykonane](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Zainstaluj pakiety na poziomie projektu

Aby można było uruchomić kod w notesie, konieczne będzie zainstalowanie pakietów na poziomie projektu. Wykonaj poniższe kroki, aby zainstalować wymagane pakiety:

1. Pobierz plik ["Requirements. txt"](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [repozytorium Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) i przekaż go do projektu.
2. Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**. 
3. W wyświetlonym oknie podręcznym wybierz **kartę środowisko**, a następnie wybierz pozycję **Dodaj**.
4. W obszarze **czynności konfiguracyjne środowiska** 
    * W pierwszej kontrolce listy rozwijanej wybierz pozycję **Requirements. txt**.
    * W drugiej kontrolce listy rozwijanej wybierz plik "Requirements. txt".
    * W trzeciej kontrolce listy rozwijanej wybierz wersję 3,6 języka Python jako wersję języka Python.
7. Wybierz pozycję **Zapisz**.

    ![Zainstaluj pakiety](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-required-modules-and-frameworks"></a>Załaduj wymagane moduły i platformy

Uruchom Poniższy skrypt, aby załadować wszystkie wymagane moduły i struktury.

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-for-reachable-range-boundary"></a>Wniosek o dostępną granicę zakresu

W naszym scenariuszu firma dostarczająca pakiet ma pewne pojazdy elektryczne w ich flotie. W ciągu dnia należy ponownie naliczać pojazdy elektryczne bez konieczności powrotu do hurtowni danych. Za każdym razem, gdy bieżąca pozostała opłata za pojazd elektryczny jest mniejsza niż godzina (pojazd elektryczny jest niski), musimy wyszukać zestaw stacji ładowania, które znajdują się w dostępnym zakresie i uzyskać informacje o granicach dla tego zakresu. Ze względu na to, że firma preferuje używanie tras zrównoważonych przez ekonomię i szybkość, żądany element routetype ma wartość "ekonomiczna". Poniższy skrypt wywołuje [interfejs API pobierania zakresu tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange) usługi routingu Azure Maps z parametrami modelu zużycia pojazdu i analizuje odpowiedź w celu utworzenia obiektu wielokąta w formacie GEOJSON reprezentującego maksymalny osiągalny zakres samochodu .

Uruchom skrypt w komórce poniżej, aby uzyskać granice dla dostępnego zakresu pojazdu elektrycznego.

```python
subscriptionKey = "Your Azure Maps primary subscription key"
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


# Get bounds for the electric vehicle's reachable range.
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

## <a name="search-electric-vehicle-charging-stations-within-reachable-range"></a>Wyszukaj stacje naładowania pojazdu elektrycznego w dostępnym zakresie

Gdy mamy dostęp do dostępnego zakresu (isochrone) dla pojazdu elektrycznego, możemy wyszukać w tym zakresie stacje opłat. Poniższy skrypt wywołuje funkcję Azure Maps [post Search w interfejsie API geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) , aby wyszukać stacje połączeń pojazdów elektrycznych w granicach maksymalnego osiągalnego zakresu samochodu, a następnie przeanalizować odpowiedź na tablicę osiągalnych lokalizacji.

Uruchom następujący skrypt, aby wyszukać stacje opłat za pojazdy elektryczne w ramach dostępnego zakresu.

```python
# Search for EV stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Przekaż osiągalny zakres i punkty ładowania do usługi danych Azure Maps

Aby wizualizować stacje ładowania i granice maksymalnego osiągalnego zakresu pojazdu elektrycznego na mapie, musimy przekazać dane graniczne i pobrać dane stacji jako obiekty GEOJSON do usługi danych Azure Maps przy użyciu [interfejsu API przekazywania danych ](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Uruchom poniższe dwie komórki, aby przekazać dane dotyczące granicy i ładowania punktów do usługi danych Azure Maps.

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

# Upload range data to Azure Maps data service.
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

# Upload EV charging stations data to Azure Maps data service.
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

## <a name="render-charging-stations-and-reachable-range-on-map"></a>Renderuj stacje ładowania i osiągalny zakres na mapie

Po przekazaniu danych do usługi danych zostanie uruchomiony następujący skrypt służący do wywołania Azure Maps [Get Image Service](https://docs.microsoft.com/rest/api/maps/render/getmapimage) w celu renderowania punktów ładowania i maksymalnej dostępnej granicy na obrazie mapy statycznej.

```python
# Get bounds for bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
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

# Render range and EV charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![zakres lokalizacji](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station-to-stop"></a>Znajdź optymalną stację ładowania do zatrzymania

Po wybraniu wszystkich potencjalnych stacji ładowania w osiągalnym zakresie chcemy wiedzieć, które z tych stacji można osiągnąć w minimalnym czasie. Poniższy skrypt wywołuje Azure Maps [interfejsu API routingu macierzy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) , zwracając dla danej lokalizacji pojazdu czas podróży i odległość do każdej podanej lokalizacji stacji naliczania opłat. Skrypt w następnej komórce analizuje odpowiedź w celu uzyskania lokalizacji dla najbliższej dostępnej stacji ładowania w odniesieniu do czasu.

Uruchom następującą komórkę, aby znaleźć najbliższą osiągalną stację ładowania, którą można osiągnąć w minimalnym czasie.

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

# Get the travel time and distance to every given charging station location.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-route-to-the-closest-charging-station"></a>Oblicz trasę do najbliższej stacji ładowania

Teraz, gdy znaleźliśmy najbliższą stację ładowania, następnie wywołamy [interfejs API wskazówek dotyczących trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) , aby zażądać szczegółowej trasy z bieżącej lokalizacji pojazdu elektrycznego do stacji ładowania.

Uruchom skrypt w poniższej komórce, aby uzyskać trasę, i Przeanalizuj odpowiedź, aby utworzyć obiekt GEOJSON reprezentujący trasę.

```python
# Get route from current location to the closest charging station. 
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

## <a name="visualize-the-route"></a>Wizualizacja trasy

W celu wizualizacji trasy najpierw przekażemy dane trasy jako obiekt GEOJSON do usługi danych Azure Maps przy użyciu [interfejsu API przekazywania danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)Azure Maps. Następnie Wywołaj usługę renderowania, [Pobierz interfejs API obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage) w celu renderowania trasy na mapie i wizualizacji.

Uruchom następujący skrypt, aby uzyskać obraz dla renderowanej trasy na mapie.

```python
# Upload route data to Azure data service.
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


# Get bounds for bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

#Buffer the bounding box by 10% to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Szlak](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób bezpośredniego Azure Maps wywoływania interfejsów API REST i wizualizacji danych Azure Maps przy użyciu języka Python.

Aby poznać Azure Maps interfejsów API używanych w tym samouczku, zobacz:

* [Pobierz zakres tras](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Opublikuj wyszukiwanie w geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Przekazywanie danych](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Renderowanie — Pobieranie obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Macierz po trasie](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Pobierz wskazówki dotyczące trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz:

* [Interfejsy API REST Azure Maps](https://docs.microsoft.com/azure/azure-maps/#reference)

Aby dowiedzieć się więcej na temat Azure Notebooks, zobacz:

* [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)