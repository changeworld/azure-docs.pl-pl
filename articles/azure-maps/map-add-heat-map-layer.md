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
ms.openlocfilehash: cbf134640f981056c0996ffc6768ebc1381ce2ac
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995154"
---
# <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Mapy cieplnej, znany także jako punktu mapy gęstości, rodzaj wizualizacji danych używany do reprezentowania gęstość danych przy użyciu zakresu kolorów. One są często używane, aby wyświetlić dane "aktywne" na mapie i to świetny sposób renderowania dużych punktu zestawów danych.  Na przykład, renderowanie dziesiątki tysięcy punktów w widoku mapy jako symbole zakrywał większość obszaru mapy i spowodują wiele symboli, które są objęte innych osób, co utrudnia dużo wgląd w dane. Jednak ten sam zestaw danych jako mapa cieplna wizualizacji ułatwia Zobacz, gdzie dane punktu rozmiarów i gęstości względnej do innych obszarów. Istnieje wiele scenariuszy, w których ciepła mapy, są używane. Poniżej przedstawiono kilka przykładów;

* Dane dotyczące temperatury jest często renderowane jako mapa cieplna, ponieważ oferuje przybliżenia dla jakich temperatury między punktami danych dwa.
* Renderowanie danych czujników szumu jako mapa cieplna nie tylko pokazuje intensywność hałas, gdzie jest czujnik, ale zapewnia także szczegółowe informacje dotyczące rozproszenia na odległości. Poziomu hałasu w jednej lokacji może nie być wysoka, jednak w przypadku nakłada szumu pokrycia obszaru, od kilku czujników, mogą wystąpić wyższe poziomy szumu ten obszar nakładające się, a zatem będą widoczne na mapie cieplnej.
* Wizualizacja GPS śledzenia, obejmującą szybkości jako mapę wysokość wagi, gdzie intensywność każdego punktu danych jest na podstawie szybkości jest doskonałym sposobem na szybko sprawdzić, gdzie został skracając pojazdu.

> [!TIP]
> Warstwy bąbelków domyślnie będą renderowane współrzędne wszystkich geometrii w źródle danych. Ograniczenie warstwy tak, aby go tylko renderuje punktu funkcji geometrii `filter` właściwości warstwy `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Dodawanie warstwy mapy cieplnej

Renderowanie danych źródłowych punktów jako mapę cieplną proste przekazać źródła danych do wystąpienia klasy HeatMapLayer i dodać do mapy, jak pokazano poniżej.

<br/>

<iframe height='500' scrolling='no' title='Warstwy mapy cieplnej prosty' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>warstwy mapy cieplnej proste</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W tym przykładzie każdy punkt ciepła ma promień 10 pikseli na wszystkich poziomy powiększenia. Podczas dodawania warstwy mapy cieplnej do mapy, w tym przykładzie wstawia pod warstwą etykiety. Spowoduje to utworzenie lepsze środowisko użytkownika etykiety są wyraźnie widoczne powyżej mapy cieplnej. To źródło danych, w tym przykładzie [agencji usgs jest Pobierany trzęsienie ziemi zagrożenia Program](https://earthquake.usgs.gov/) i składa się z punktów, które reprezentuje znaczące trzęsienia ziemi, które miały miejsce w ciągu ostatnich 30 dni.

## <a name="customizing-the-heat-map-layer"></a>Dostosowywanie warstwy mapy cieplnej

Poprzedni przykład dostosowane Mapa cieplna przez ustawienie opcji usługi radius i przezroczystość. Warstwy mapy cieplnej oferuje kilka opcji dostosowywania;

* `radius`: Określa promień piksela, w której ma zostać renderowania każdego punktu danych. Promień można ustawić jako stałą liczbą lub wyrażenie. Ustawienia usługi radius, ponieważ wyrażenie, które ustawia promień w oparciu o poziom powiększenia może spowodować mapy cieplnej, wyświetlanymi promień reprezentujący spójne przestrzenne obszar na mapie.
* `color`: Określa, jak jest w trybie kolorowym mapy cieplnej. Palety kolorów gradientu jest często używana do mapy cieplnej, ale schodkowego kolorów palety również są przydatne, jeśli chcesz Twoja Mapa cieplna bardziej wyglądać rozkład danych. Te palety kolorów definiowanie kolorów od wartości minimalnej, wartość maksymalna gęstość. Wartości kolorów dla mapy cieplnej są określone jako wyrażenie na `heatmap-density` wartość. Kolor pod indeksem 0 w wyrażeniu gradientu lub kolor domyślny kolor krok określa kolor obszaru w przypadku, gdy nie ma danych, lub kolor tła. Wiele chcesz ustawić tę wartość na przezroczyste lub częściowo przezroczysty czarny. Poniżej przedstawiono przykłady wyrażeń kolor;

| Wyrażenie koloru gradientu | Wyrażenie koloru schodkowym | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;interpolacji,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["liniowej"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapy cieplnej gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "przezroczyste",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "purpurowy",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"krok"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["mapy cieplnej gęstość"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"przezroczyste"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "granatowym",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "granatowym",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "zieloną",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "żółty",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1,00 "red"<br/>\] |   

* `opacity`: Określa, jak nieprzezroczyste lub przezroczyste ciepło jest warstwy mapy.
* `intensity`: Dotyczy mnożnik wagę każdego punktu danych w celu zwiększenia ogólnej intensywności cieplnej. To ułatwia niewielkie różnice w wagi punktów danych stają się łatwiejsze do wizualizacji.
* `weight`: Domyślnie wszystkie punkty danych ma wagę 1, w związku z tym wszystkie punkty danych są liczone tak samo. Opcja wagi działa jako mnożnik i może być ustawiona jako liczbą ani wyrażeniem. Jeśli liczba jest ustawiona jako numer, załóżmy, że 2 byłoby wielokrotność wprowadzania do każdego punktu danych na mapie, dwa razy, dlatego podwojenie gęstość. Ustawienie dla opcji wagi liczbą powoduje wyświetlenie na mapie cieplnej w podobny sposób można za pomocą opcji Intensywność. Jednak jeśli wyrażenie jest używany, waga każdego punktu danych może opierać się na inny i jest oparta na niektóre metryki we właściwościach punktu. Wypełnij trzęsienie ziemi dane, na przykład każdy punkt danych reprezentują trzęsienie ziemi i ważną metryką, którą ma każdy trzęsienie ziemi jest wielkości. Przez cały czas się zdarzyć, trzęsienia ziemi, ale większość ma niski wielkości i nie są jeszcze uznało. Przy użyciu wartości wielkości w wyrażeniu, aby przypisać wagę opcji umożliwi bardziej znaczące trzęsienia ziemi, należy zwiększyć lepiej jest reprezentowana w ramach Mapa cieplna.
* Oprócz opcji do warstwy podstawowej; min/max powiększyć, widoczne i filtrowanie, dostępna jest również `source` opcję, jeśli chcesz zaktualizować źródło danych i `source-layer` opcję, jeśli źródło danych znajduje się źródło kafelka wektora.

W tym miejscu jest narzędziem do przetestowania opcje warstwy mapy cieplnej różne.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy cieplnej' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>opcje warstwy mapy cieplnej</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

> [!TIP]
> Po włączeniu klastrowania w źródle danych punkty, które znajdują się blisko siebie nawzajem są grupowane jako punkt klastra. Liczba punktów każdego klastra może służyć jako wyrażenie wagi Mapa cieplna i znacznie zmniejszyć liczbę punktów, które mają być renderowane. Liczba punktów w klastrze są przechowywane we właściwości point_count funkcji punktu, jak pokazano poniżej. 
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

