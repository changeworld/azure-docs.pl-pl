---
title: Dodaj warstwę mapy cieplnej do usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać warstwę mapy cieplnej do mapy Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59258865"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy cieplnej, znany także jako punktu mapy gęstości, rodzaj wizualizacji danych używany do reprezentowania gęstość danych przy użyciu zakresu kolorów. One często są używane do wyświetlania danych "aktywne" na mapie i to świetny sposób renderowania dużych punktu zestawów danych.  Na przykład renderowania dziesiątki tysięcy punktów w widoku mapy jako symbole, zajmuje większość obszaru mapy i mogłoby spowodować wiele symboli nakładających się siebie, co utrudnia dużo wgląd w dane. Jednak ten sam zestaw danych jako mapa cieplna wizualizacji ułatwia Zobacz, gdzie dane punktu rozmiarów i gęstości względnej do innych obszarów. Istnieje wiele scenariuszy, w których ciepła mapy, są używane. Poniżej przedstawiono kilka przykładów;

* Dane dotyczące temperatury jest często renderowane jako mapa cieplna, ponieważ oferuje przybliżenia dla jakich temperatury między punktami danych dwa.
* Renderowanie danych czujników szumu jako mapa cieplna nie tylko pokazuje intensywność hałas, gdzie jest czujnik, ale zapewnia także szczegółowe informacje dotyczące rozproszenia na odległości. Poziomu hałasu w jednej lokacji może nie być wysoka, jednak w przypadku nakłada szumu pokrycia obszaru, od kilku czujników, mogą wystąpić wyższe poziomy szumu ten obszar nakładające się, a zatem będą widoczne na mapie cieplnej.
* Wizualizacja GPS śledzenia, obejmującą szybkości jako mapę ważona wysokość, gdzie intensywność każdego punktu danych jest na podstawie szybkości jest doskonały sposób, aby zobaczyć, gdzie został skracając pojazdu.

> [!TIP]
> Warstwy mapy cieplnej domyślnie będą renderowane współrzędne wszystkich geometrii w źródle danych. Ograniczenie warstwy tak, aby go tylko renderuje punktu funkcji geometrii `filter` właściwości warstwy, aby `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` aby obejmują również funkcje MultiPoint.

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Aby renderować źródła danych z punktów jako mapa cieplna, należy przekazać źródła danych do wystąpienia `HeatMapLayer` klasy i dodać do mapy, jak pokazano poniżej.

<br/>

<iframe height='500' scrolling='no' title='Warstwy mapy cieplnej prosty' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>warstwy mapy cieplnej proste</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W tym przykładzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomy powiększenia. Podczas dodawania warstwy mapy cieplnej do mapy, w tym przykładzie wstawia poniżej warstwy etykiety, aby tworzyć lepsze efekty etykiety są wyraźnie widoczne powyżej mapy cieplnej. Dane w tym przykładzie pochodzi z [agencji usgs jest Pobierany trzęsienie ziemi zagrożenia Program](https://earthquake.usgs.gov/) i stanowi znaczące trzęsienia ziemi, które miały miejsce w ciągu ostatnich 30 dni.

## <a name="customizing-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowane Mapa cieplna przez ustawienie opcji usługi radius i przezroczystość. Warstwy mapy cieplnej oferuje kilka opcji dostosowywania;

* `radius`: Określa promień piksela, w której ma zostać renderowania każdego punktu danych. Promień można ustawić jako stałą liczbą lub wyrażenie. Za pomocą wyrażenia, istnieje możliwość skalowania usługi radius na podstawie poziomu powiększenia, w wyświetlonym do reprezentowania spójny obszar przestrzennych, na mapie (na przykład radius ogniw w łańcuchu 5).
* `color`: Określa, jak jest w trybie kolorowym mapy cieplnej. Gradient kolorów jest często używana do mapy cieplnej i można osiągnąć za pomocą `interpolate` wyrażenia. Za pomocą `step` wyrażenie kolorowanie Mapa cieplna dzielony gęstość wizualnie do zakresów, bardziej podobny mapę Styl konturu lub radarowego. Te palety kolorów definiowanie kolorów od wartości minimalnej, wartość maksymalna gęstość. Wartości kolorów dla mapy cieplnej są określone jako wyrażenie na `heatmap-density` wartość. Kolor pod indeksem 0 w wyrażeniu interpolacji lub domyślny kolor wyrażenia krokowego definiuje kolor obszaru w przypadku, gdy nie ma żadnych danych i może służyć do definiowania kolor tła. Wiele chcesz ustawić tę wartość na przezroczyste lub częściowo przezroczysty czarny. Poniżej przedstawiono przykłady wyrażeń kolor;

| Interpolation Color Expression | Stepped Color Expression | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;interpolacji,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowej"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapy cieplnej gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "purpurowy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapy cieplnej gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"przezroczyste"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "granatowym",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "zieloną",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość 0,75 "red"<br/>\] | 

* `opacity`: Określa, jak nieprzezroczyste lub przezroczyste ciepło jest warstwy mapy.
* `intensity`: Dotyczy mnożnik wagę każdego punktu danych w celu zwiększenia ogólnej intensywności cieplnej i pomaga zapewnić niewielkie różnice w wagi punktów danych, stają się łatwiejsze do wizualizacji.
* `weight`: Domyślnie wszystkie punkty danych ma wagę 1, w związku z tym wszystkie punkty danych są liczone tak samo. Opcja wagi działa jako mnożnik i może być ustawiona jako liczbą ani wyrażeniem. Jeśli numer jest ustawiony jako wagi, załóżmy, że 2 byłoby wielokrotność wprowadzania do każdego punktu danych na mapie, dwa razy, dlatego podwojenie gęstość. Ustawienie dla opcji wagi liczbą powoduje wyświetlenie na mapie cieplnej w podobny sposób można za pomocą opcji Intensywność. Jednak jeśli wyrażenie jest używany, waga każdego punktu danych może opierać się na właściwości każdego punktu danych. Wypełnij trzęsienie ziemi dane, na przykład każdy punkt danych reprezentują trzęsienie ziemi. Ważną metryką każdego punktu danych trzęsienie ziemi, to wartość wielkości. Przez cały czas się zdarzyć, trzęsienia ziemi, ale większość ma niski wielkości i nie są jeszcze uznało. Przy użyciu wartości wielkości w wyrażeniu, aby przypisać wagę do poszczególnych danych będą punktu umożliwia bardziej znaczące trzęsienia ziemi lepiej jest reprezentowana w ciągu na mapie cieplnej.
* Oprócz korzystania z opcji warstwy podstawowej; min/max powiększyć, widoczne i filtrowanie, dostępna jest również `source` opcję, jeśli chcesz zaktualizować źródło danych i `source-layer` opcję, jeśli źródło danych znajduje się źródło kafelka wektora.

W tym miejscu jest narzędziem do przetestowania opcje warstwy mapy cieplnej różne.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy cieplnej' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>opcje warstwy mapy cieplnej</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Mapa cieplna którą można powiększać spójne

Domyślnie promienia punktów danych, renderowane przy użyciu warstwy mapy cieplnej mieć promień stały pikseli na wszystkich poziomach powiększenia. Jak mapy jest powiększony razem agregacji danych i warstwy mapy cieplnej wygląda inaczej. A `zoom` wyrażenia można skalować usługi radius dla każdego poziomu powiększenia w taki sposób, że każdy punkt danych obejmuje ten sam fizyczny obszar mapy. Dzięki temu warstwy mapy cieplnej Szukaj bardziej statycznych i spójne. Każdy poziom powiększenia mapy ma dwa razy wiele pikseli w pionie i w poziomie jako poprzedniego poziomu powiększenia. Skalowanie promień w taki sposób, że podwaja się przy użyciu każdego poziomu powiększenia utworzy mapę cieplną, która będzie spójny na wszystkich poziomach powiększenia. Można to zrobić za pomocą `zoom` przy podstawie 2 `exponential interpolation` wyrażenia, jak pokazano w poniższym przykładzie. Powiększenie mapy, aby zobaczyć, jak mapa cieplna jest skalowana w miarę poziom powiększenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa cieplna którą można powiększać spójne" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Mapa cieplna którą można powiększać spójne</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych punkty, które znajdują się blisko siebie nawzajem są grupowane jako punkt klastra. Liczba punktów każdego klastra może służyć jako wyrażenie wagi Mapa cieplna i znacznie zmniejszyć liczbę punktów, które mają być renderowane. Liczba punktów w klastrze są przechowywane w `point_count` właściwości funkcji punktów, jak pokazano poniżej. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Jeśli klastrowania radius jest tylko kilka z powyższych pikseli będzie można nieco visual różnica renderowania. Większe radius będzie grupować większej liczby punktów w każdym klastrze i poprawić wydajność cieplnej, ale mają lepiej widoczne będą różnice.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Za pomocą wyrażeń opartych na danych stylu](data-driven-style-expressions-web-sdk.md)