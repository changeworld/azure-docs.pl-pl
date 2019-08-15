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
ms.openlocfilehash: e83b3c5f7f7cb6fa729a628f01f4103d44c19df8
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976182"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy cieplne, znane także jako mapy gęstości punktów, są typem wizualizacji danych używanym do reprezentowania gęstości danych przy użyciu różnych kolorów. Są one często używane do wyświetlania danych "gorąca" na mapie i są doskonałym sposobem renderowania zestawów danych z dużymi punktami.  Na przykład, renderowanie dziesiątek tysięcy punktów w widoku mapy jako symboli, obejmuje większość obszaru mapy i spowoduje, że wiele symboli nakłada się na siebie, utrudniając uzyskanie wielu szczegółowych informacji. Wizualizacja tego samego zestawu danych jako mapy cieplnej ułatwia jednak sprawdzenie, gdzie dane punktu są najgęste i względną gęstość do innych obszarów. Istnieje wiele scenariuszy, w których są używane mapy cieplne. Oto kilka przykładów:

- Dane temperatury są zwykle renderowane jako mapa cieplna, ponieważ zapewnia przybliżenie temperatury między dwoma punktami danych.
- Renderowanie danych dla czujników szumów jako mapy cieplnej nie tylko pokazuje intensywność szumu, w którym czujnik jest, ale również może zapewnić wgląd w rozproszenie na odległość. Poziom szumu w jednej lokacji może być niewysoki, jednak jeśli obszar pokrycia szumów z wielu czujników nakłada się na siebie, istnieje możliwość, że ten nakładający się obszar może mieć wyższy poziom szumu i dlatego będzie widoczny na mapie cieplnej.
- Wizualizacja śledzenia GPS, która obejmuje prędkość jako mapę ważonej wysokości, w której intensywność każdego punktu danych zależy od szybkości, jest doskonałym sposobem, aby zobaczyć, gdzie nastąpiło przyspieszenie pojazdu.

> [!TIP]
> Warstwy mapy cieplnej domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę tak, aby była renderowana tylko funkcja geometrii punktu, należy `filter` ustawić właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` , jeśli chcesz również uwzględnić funkcje MultiPoint.

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródło danych punktów jako mapę cieplną, Przekaż źródło danych do wystąpienia `HeatMapLayer` klasy i Dodaj je do mapy, jak pokazano poniżej.

W poniższym kodzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomach powiększenia. Podczas dodawania warstwy mapy cieplnej do mapy, ten przykład wstawia ją poniżej warstwy etykiet, aby utworzyć lepszy interfejs użytkownika, ponieważ etykiety są wyraźnie widoczne powyżej mapy cieplnej. Dane w tym przykładzie pochodzą z [programu agencji usgse zagrożenia ziemią](https://earthquake.usgs.gov/) i przedstawiają znaczne trzęsienia, które wystąpiły w ciągu ostatnich 30 dni.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa mapy cieplnej' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>warstwą prostej mapy ciepła</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowany do mapy cieplnej przez ustawienie opcji promień i nieprzezroczystość. Warstwa mapy cieplnej zawiera kilka opcji dostosowywania;

* `radius`: Definiuje promień w pikselach, w którym ma być renderowany każdy punkt danych. Promień można ustawić jako stałą liczbę lub jako wyrażenie. Korzystając z wyrażenia, można skalować promień na podstawie poziomu powiększenia, który wydaje się przedstawiać spójny obszar przestrzenny mapy (na przykład promień 5-milowej).
* `color`: Określa sposób kolorowania mapy cieplnej. Gradient koloru jest często używany w przypadku map cieplnych i można go osiągnąć przy `interpolate` użyciu wyrażenia. `step` Użycie wyrażenia do kolorowania mapy cieplnej dzieli się na wizualizację w coraz większym zakresie, podobnie jak mapa stylu konturu lub wykresu radarowego. Te palety kolorów definiują kolory z minimum do wartości maksymalnej gęstości. Wartości koloru dla map cieplnych są określone jako wyrażenie w `heatmap-density` wartości. Kolor pod indeksem 0 w wyrażeniu interpolacji lub domyślnym kolorem wyrażenia kroku definiuje kolor obszaru, w którym nie ma danych i może służyć do definiowania koloru tła. Wiele woli ustawić tę wartość na przezroczystą lub częściowo przezroczystą czerń. Oto przykłady wyrażeń koloru;

| Wyrażenie koloru interpolacji | Wyrażenie koloru z możliwością | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"Interpolacja",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowe\]",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "purpurowe",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapę cieplną-gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "Granat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zielony",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, czerwony<br/>\] | 

- `opacity`: Określa sposób nieprzezroczystości lub przezroczystości warstwy mapy cieplnej.
- `intensity`: Stosuje mnożnik do wagi każdego punktu danych, aby zwiększyć całkowite intensywność mapę cieplną i pomaga uprościć nieznaczne różnice w wadze punktów danych.
- `weight`: Domyślnie wszystkie punkty danych mają wagę 1, więc wszystkie punkty danych są ważone równomiernie. Opcja wagi pełni rolę mnożnika i można ją ustawić jako liczbę lub wyrażenie. Jeśli liczba jest ustawiona jako waga, powiedzmy 2, będzie odpowiednikiem umieszczania każdego punktu danych na mapie dwa razy, a tym samym podwaja gęstość. Ustawienie opcji wagi na liczbę renderuje mapę cieplną w podobny sposób, aby użyć opcji intensywność. Jeśli jednak wyrażenie jest używane, waga każdego punktu danych może opierać się na właściwościach poszczególnych punktów danych. Na przykład należy wziąć pod sobą dane z ziemi, każdy punkt danych reprezentuje trzęsienie ziemi. Ważną metryką każdego punktu danych trzęsienia ziemi jest wartość wielkości. Zdarza się cały czas, ale większość z nich ma niską wartość i jeszcze nie odczuwa. Użycie wartości wielkości w wyrażeniu w celu przypisania wagi do każdego punktu danych umożliwi lepsze przedstawienie bardziej znaczących ziemi w ramach mapy cieplnej.
- Oprócz opcji warstwy podstawowej; minimalne/maksymalne powiększenie, widoczne i filtru, istnieje również `source` opcja, jeśli chcesz zaktualizować źródło danych i `source-layer` opcję, jeśli źródło danych jest źródłem kafelka wektorowego.

Oto narzędzie do testowania różnych opcji warstwy mapy cieplnej.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy cieplnej' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>Opcje warstwy mapy ciepła</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Spójna Mapa cieplna z powiększaniem

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. `zoom` Wyrażenie może służyć do skalowania promienia dla każdego poziomu powiększenia, tak że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. Spowoduje to, że warstwa mapy cieplnej będzie wyglądać bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. Skalowanie promienia w taki sposób, że podwaja z każdym poziomem powiększenia, spowoduje utworzenie mapy cieplnej, która będzie wyglądać spójnie na wszystkich poziomach powiększenia. Można to osiągnąć przy użyciu `zoom` wyrażenia z podstawą 2 `exponential interpolation` , jak pokazano w poniższym przykładzie. Powiększ mapę, aby zobaczyć, jak mapa cieplna jest skalowana z poziomem powiększenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Spójna Mapa cieplna z powiększaniem" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz spójną <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>mapę termiczną</a> o powiększeniu powiększenia<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych, punkty, które są blisko siebie, są pogrupowane jako punkt klastrowany. Liczba punktów każdego klastra może służyć jako wyrażenie wagi dla mapy cieplnej i znacząco zmniejszyć liczbę punktów, które muszą być renderowane. Liczba punktów klastra jest przechowywana we `point_count` właściwości funkcji Point, jak pokazano poniżej. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Jeśli promień klastrowania to tylko kilka pikseli, renderowanie będzie miało niewielkie różnice wizualne. Większy promień będzie grupować więcej punktów w każdym klastrze i zwiększyć wydajność mapę cieplną, ale ma bardziej zauważalne różnice.

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