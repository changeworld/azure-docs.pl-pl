---
title: Dodawanie warstwy mapy cieplnej do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać warstwę mapy skupień do mapy przy użyciu sdk Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208543"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy skupień, znane również jako mapy gęstości punktów, są rodzajem wizualizacji danych. Są one używane do reprezentowania gęstości danych przy użyciu zakresu kolorów i pokazują dane "hot spoty" na mapie. Mapy skupień to świetny sposób renderowania zestawów danych z dużą liczbą punktów. 

Renderowanie dziesiątek tysięcy punktów jako symboli może obejmować większość obszaru mapy. Ten przypadek prawdopodobnie powoduje, że wiele symboli nakłada się na siebie. Utrudnianie lepszego zrozumienia danych. Jednak wizualizacja tego samego zestawu danych co mapa cieplna ułatwia zobaczenie gęstości i gęstości względnej każdego punktu danych.

Mapy skupień można używać w wielu różnych scenariuszach, w tym:

- **Dane temperatury:** Zawiera przybliżenia temperatury między dwoma punktami danych.
- **Dane dotyczące czujników hałasu:** Pokazuje nie tylko intensywność hałasu, w którym znajduje się czujnik, ale może również zapewnić wgląd w rozpraszanie na odległość. Poziom hałasu w jednym miejscu może nie być wysoki. Jeśli obszar pokrycia hałasu z wielu czujników nakłada się na siebie, możliwe, że ten nakładający się obszar może wystąpić wyższy poziom hałasu. W związku z tym nakładający się obszar będzie widoczny na mapie cieplnej.
- **Śledzenie GPS:** Zawiera prędkość jako ważony mapę wysokości, gdzie intensywność każdego punktu danych jest oparta na prędkości. Na przykład ta funkcja umożliwia sprawdzenie, gdzie pojazd pędził z nadmierną prędkością.

> [!TIP]
> Warstwy mapy natchnienia domyślnie renderują współrzędne wszystkich geometrii w źródle danych. Aby ograniczyć warstwę tak, aby renderować tylko `filter` obiekty geometrii `['==', ['geometry-type'], 'Point']`punktowej, ustaw właściwość warstwy na . Jeśli chcesz również uwzględnić funkcje MultiPoint, ustaw `filter` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`właściwość warstwy na .

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródło danych punktów jako mapę cieplną, przekaż `HeatMapLayer` źródło danych do wystąpienia klasy i dodaj je do mapy.

W poniższym kodzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomach powiększenia. Aby zapewnić lepsze wrażenia użytkownika, mapa ciepła znajduje się poniżej warstwy etykiet. Etykiety pozostają wyraźnie widoczne. Dane w tym przykładzie pochodzą z [programu USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). To jest dla znaczących trzęsień ziemi, które miały miejsce w ciągu ostatnich 30 dni.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Oto kompletny przykładowy kod uruchomiony poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa mapy cieplnej' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz warstwę <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>prostej mapy cieplnej</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

W poprzednim przykładzie dostosowano mapę cieplną, ustawiając opcje promienia i krycia. Warstwa mapy cieplnej zawiera kilka opcji dostosowywania, w tym:

* `radius`: Definiuje promień piksela, w którym ma być renderowany każdy punkt danych. Promień można ustawić jako liczbę stałą lub jako wyrażenie. Za pomocą wyrażenia można skalować promień na podstawie poziomu powiększenia i reprezentować spójny obszar przestrzenny na mapie (na przykład promień 5 mil).
* `color`: Określa sposób pokolorowania mapy cieplnej. Gradient kolorów jest częstą cechą map cieplnych. Efekt można osiągnąć za `interpolate` pomocą wyrażenia. Można również użyć `step` wyrażenia do kolorowania mapy cieplnej, rozbijając gęstość wizualnie na zakresy przypominające mapę stylu konturu lub radaru. Te palety kolorów definiują kolory od wartości minimalnej do maksymalnej gęstości. 

  Wartości kolorów dla map skupień można `heatmap-density` określić jako wyrażenie wartości. Kolor obszaru, w którym nie ma danych, jest zdefiniowany w indeksie 0 wyrażenia "Interpolacja" lub domyślnym kolorze wyrażenia "Krokowe". Za pomocą tej wartości można zdefiniować kolor tła. Często ta wartość jest ustawiona na przezroczystą lub półprzezroczystą czerń. 
   
  Oto przykłady wyrażeń kolorów:

  | Wyrażenie koloru interpolacji | Stopniowane wyrażenie koloru | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolacja",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowy",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["gęstość cieplna",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczysty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, "fioletowy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["gęstość cieplna",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;"przejrzysty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, "marynarka wojenna",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, "zielony",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "czerwony"<br/>\] |   

- `opacity`: Określa, jak nieprzezroczysta lub przezroczysta jest warstwa mapy cieplnej.
- `intensity`: Stosuje mnożnik do wagi każdego punktu danych, aby zwiększyć ogólną intensywność mapy cieplnej. Powoduje to różnicę w masie punktów danych, ułatwiając wizualizację.
- `weight`: Domyślnie wszystkie punkty danych mają wagę 1 i są ważone jednakowo. Opcja wagi działa jako mnożnik i można ustawić ją jako liczbę lub wyrażenie. Jeśli liczba jest ustawiona jako waga, jest to równoważność umieszczania każdego punktu danych na mapie dwukrotnie. Na przykład, jeśli waga wynosi 2, gęstość podwaja się. Ustawienie opcji wagi na liczbę powoduje, że mapa ciepła jest podobna do opcji intensywności. 

  Jednak jeśli używasz wyrażenia, waga każdego punktu danych może być oparta na właściwościach każdego punktu danych. Załóżmy na przykład, że każdy punkt danych reprezentuje trzęsienie ziemi. Wartość wielkości była ważną metryką dla każdego punktu danych trzęsienia ziemi. Trzęsienia ziemi zdarzają się cały czas, ale większość z nich ma niską wielkość i nie są zauważone. Użyj wartości wielkości w wyrażeniu, aby przypisać wagę do każdego punktu danych. Za pomocą wartości wielkości przypisać wagę, masz lepszą reprezentację znaczenia trzęsień ziemi w mapie ciepła.
- `source`oraz `source-layer`: Umożliwia aktualizację źródła danych.

Oto narzędzie do testowania różnych opcji warstwy mapy cieplnej.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy na ciepło' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>opcje warstwy mapy cieplnej</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Spójna mapa cieplna z zoomem

Domyślnie promienie punktów danych renderowanych w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. Podczas powiększania mapy dane są agregowane, a warstwa mapy skupień wygląda inaczej. 

Użyj `zoom` wyrażenia, aby skalować promień dla każdego poziomu powiększenia, tak aby każdy punkt danych obejmował ten sam obszar fizyczny mapy. To wyrażenie sprawia, że warstwa mapy ciepła wygląda bardziej statycznie i spójnie. Każdy poziom powiększenia mapy ma dwa razy więcej pikseli w pionie i poziomie niż poprzedni poziom powiększenia. 

Skalowanie promienia tak, aby podwajał się przy każdym poziomie powiększenia, tworzy mapę cieplną, która wygląda spójnie na wszystkich poziomach powiększenia. Aby zastosować to skalowanie, należy `zoom` `exponential interpolation` użyć wyrażenia podstawowego 2, jak pokazano w poniższym przykładzie. Powiększ mapę, aby zobaczyć, jak mapa ciepła jest skalowana z poziomem powiększenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spójna mapa cieplna z zoomem" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapę cieplną</a> z funkcją<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Consistent według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych punkty, które znajdują się blisko siebie, są zgrupowane jako punkt klastrowany. Liczbę punktów każdego klastra można użyć jako wyrażenia wagi dla mapy skupień. Może to znacznie zmniejszyć liczbę punktów do renderowania. Liczba punktów klastra jest przechowywana we `point_count` właściwości funkcji punktu: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Jeśli promień klastrowania wynosi tylko kilka pikseli, będzie istnieć niewielka różnica wizualna w renderowaniu. Większy promień grupuje więcej punktów do każdego klastra i poprawia wydajność mapy cieplnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions (Opucjaje heatmaplayeroptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)
