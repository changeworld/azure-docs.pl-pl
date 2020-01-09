---
title: Dodaj warstwę mapy cieplnej do Azure Maps | Microsoft Docs
description: Jak dodać warstwę mapy cieplnej do Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0d07f18682bf39558180753de38a9c5ff106ee3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408527"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych używanym do reprezentowania gęstości danych przy użyciu różnych kolorów. Są one często używane do wyświetlania danych "gorąca" na mapie i są doskonałymi sposobami renderowania zestawów DataSet z dużymi punktami. 

Na przykład renderowanie dziesiątek tysięcy punktów w widoku mapy jako symboli obejmuje większość obszaru mapy. Może to spowodować, że wiele symboli nakłada się na siebie, co utrudnia uzyskanie szczegółowego wglądu w dane. Wizualizacja tego samego zestawu danych jako mapy cieplnej ułatwia jednak sprawdzenie, gdzie dane punktu są najgęste i względną gęstość do innych obszarów.

W wielu różnych scenariuszach można używać map cieplnych, takich jak:

- **Dane temperatury**: zawiera przybliżenia między dwoma punktami danych.
- **Dane dla czujników szumu**: pokazuje nie tylko intensywność szumu, w którym czujnik jest, ale również może zapewnić wgląd w rozproszenie na odległość. Poziom szumu w jednej lokacji może być niewysoki. Ale jeśli obszar pokrycia szumów z wielu czujników nakłada się na siebie, istnieje możliwość, że ten obszar nakładający się może mieć wyższy poziom szumu i dlatego będzie widoczny na mapie cieplnej.
- **Śledzenie GPS**: zawiera szybkość jako mapę ważonej wysokości, w której intensywność każdego punktu danych jest oparta na szybkości. Na przykład umożliwia to sprawdzenie, gdzie nastąpiło przyspieszenie pojazdu.

> [!TIP]
> Warstwy mapy cieplnej domyślnie renderują współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, ustaw właściwość `filter` warstwy na `['==', ['geometry-type'], 'Point']`. Jeśli chcesz również uwzględnić funkcje systemu MultiPoint, ustaw właściwość `filter` warstwy na `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródło danych punktów jako mapę cieplną, Przekaż źródło danych do wystąpienia klasy `HeatMapLayer` i Dodaj je do mapy.

W poniższym kodzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomach powiększenia. Po dodaniu warstwy mapy cieplnej do mapy, ten przykład wstawia ją poniżej warstwy etykieta, aby utworzyć lepszy interfejs użytkownika. Etykiety są wyraźnie widoczne powyżej mapy cieplnej. Dane w tym przykładzie pochodzą z [programu agencji usgse zagrożenia ziemią](https://earthquake.usgs.gov/)i przedstawiają znaczne trzęsienia, które wystąpiły w ciągu ostatnich 30 dni.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Oto kompletny przykładowy kod dla poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa mapy cieplnej' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>warstwą prostej mapy ciepła</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowany do mapy cieplnej przez ustawienie opcji promień i nieprzezroczystość. Warstwa mapy cieplnej oferuje kilka opcji dostosowywania, takich jak:

* `radius`: Określa promień w pikselach, w którym ma być renderowany każdy punkt danych. Promień można ustawić jako liczbę stałą lub jako wyrażenie. Za pomocą wyrażenia można skalować promień na podstawie poziomu powiększenia i reprezentować spójny obszar przestrzenny mapy (na przykład promień 5-milowej).
* `color`: określa sposób kolorowania mapy cieplnej. Gradient koloru jest wspólną funkcją map ciepła i można osiągnąć efekt za pomocą wyrażenia `interpolate`. Możesz również użyć wyrażenia `step`, aby kolorować mapę cieplną, dzieląc gęstość na wizualizację na zakresy podobne do mapy w stylu konturu lub wykresu radarowego. Te palety kolorów definiują kolory z minimum do wartości maksymalnej gęstości. 

  Należy określić wartości koloru dla map cieplnych jako wyrażenie dla wartości `heatmap-density`. Kolor pod indeksem 0 w wyrażeniu interpolacji lub domyślny kolor wyrażenia kroku definiuje kolor obszaru, w którym nie ma żadnych danych. Można jej użyć do zdefiniowania koloru tła. Często ta wartość jest ustawiona na przezroczysty lub półprzezroczysty kolor przezroczysty. 
   
  Oto przykłady wyrażeń koloru:

  | Wyrażenie koloru interpolacji | Wyrażenie koloru z możliwością | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Interpolacja",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowy"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "purpurowy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Step",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Granat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zielony",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "Red"<br/>\] |   

- `opacity`: określa sposób nieprzezroczystości lub przezroczystości warstwy mapy cieplnej.
- `intensity`: stosuje mnożnik do wagi każdego punktu danych w celu zwiększenia całkowitego intensywności mapę cieplną. Dzięki temu małe różnice w wadze punktów danych są łatwiejsze do wizualizacji.
- `weight`: Domyślnie wszystkie punkty danych mają wagę 1 i są ważone w równym stopniu. Opcja wagi pełni rolę mnożnika i można ją ustawić jako liczbę lub wyrażenie. Jeśli liczba (na przykład 2) jest ustawiona jako waga, jest to odpowiednik umieszczania każdego punktu danych na mapie dwa razy, co Podwajanie gęstość. Ustawienie opcji wagi na liczbę renderuje mapę cieplną w podobny sposób, aby użyć opcji intensywność. 

  Jeśli jednak używasz wyrażenia, waga każdego punktu danych może opierać się na właściwościach poszczególnych punktów danych. Załóżmy na przykład, że każdy punkt danych reprezentuje trzęsienie ziemi. Ważną metryką każdego punktu danych trzęsienia ziemi jest wartość wielkości. Zdarza się cały czas, ale większość z nich ma niską wartość i jeszcze nie odczuwa. Za pomocą wartości wielkości w wyrażeniu do przypisywania wagi do każdego punktu danych, można lepiej reprezentować znaczenie ziemi na mapie cieplnej.
- `source` i `source-layer`: umożliwia zaktualizowanie źródła danych.

Oto narzędzie do testowania różnych opcji warstwy mapy cieplnej.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy cieplnej' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opcje warstwy mapy ciepła</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Spójna Mapa cieplna z powiększaniem

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. 

Użyj wyrażenia `zoom`, aby skalować promień dla każdego poziomu powiększenia, w taki sposób, że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. Dzięki temu warstwa mapy cieplnej jest bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. 

Skalowanie promienia tak, aby podwajał się przy każdym poziomie powiększenia tworzy mapę cieplną, która wygląda spójnie na wszystkich poziomach powiększenia. W tym celu należy użyć `zoom` z wyrażeniem podstawowego 2 `exponential interpolation`, jak pokazano w poniższym przykładzie. Powiększ mapę, aby zobaczyć, jak mapa cieplna jest skalowana z poziomem powiększenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spójna Mapa cieplna z powiększaniem" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>spójną mapę termiczną o powiększeniu powiększenia</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych punkty, które są blisko siebie, są pogrupowane jako punkt klastrowany. Można użyć liczby punktów każdego klastra jako wyrażenia wagi dla mapy cieplnej i znacząco zmniejszyć liczbę punktów, które muszą być renderowane. Liczba punktów klastra jest przechowywana we właściwości `point_count` funkcji Point: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Jeśli promień klastrowania ma tylko kilka pikseli, podczas renderowania nie ma żadnej różnicy wizualnej. Większa liczba grup usługi RADIUS zwiększa liczbę punktów w każdym klastrze i poprawia wydajność mapę cieplną.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)
