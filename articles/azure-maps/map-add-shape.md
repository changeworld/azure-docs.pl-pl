---
title: Dodawanie kształtu przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać kształt do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547989"
---
# <a name="add-a-shape-to-a-map"></a>Dodawanie kształtu do mapy

W tym artykule przedstawiono sposób renderowania geometrii na mapie za pomocą wiersza i wielokąta warstwy. Zestaw SDK usługi Azure Maps w sieci Web umożliwia także tworzenie geometrii okrąg zgodnie z definicją w [rozszerzony schemat GeoJSON](extend-geojson.md#circle). Wszystkie geometrii funkcji można łatwo aktualizować, jeśli jest ujęte w nawiasy [kształt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) klasy.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Dodaj wiersze do mapy

`LineString` i `MultiLineString` funkcje są używane do reprezentowania opisanych na mapie i ścieżki.

### <a name="add-a-line"></a>Dodaj wiersz

<iframe height='500' scrolling='no' title='Dodaj wiersz do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qomaKv/'>dodać wiersz do mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie konstrukcji obiektu mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) obiekt zostanie utworzony i dodany do źródła danych.

A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje wiersz obiektów w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Ostatni blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii w [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwie wiersza są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, czy wiersz jest wyświetlany po mapy ładuje pełni.

### <a name="add-symbols-along-a-line"></a>Dodawanie symboli wzdłuż linii

W tym przykładzie przedstawiono sposób dodawania ikony strzałek wzdłuż linii na mapie. Gdy za pomocą warstwy symboli, ustaw opcję "umieszczania" "wiersz", spowoduje to renderowania symbole wzdłuż linii i Obróć ikony (0 stopni = po prawej stronie).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pokaż strzałkę wzdłuż linii" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Pokaż strzałkę wzdłuż linii</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Dodawanie gradientu obrysu do linii

Oprócz możliwości dotyczą kolorem pociągnięcia pojedynczy wiersz możesz również wypełnić linię gradient kolorów, aby pokazać przejście od jednego segmentu wiersz do następnego. Na przykład pochylenia linii może służyć do reprezentowania zmiany w czasie i w odległości lub w różnych temperatur w linii połączonej obiektów. Aby można było zastosować tę funkcję do wiersza, źródło danych musi mieć `lineMetrics` opcji ma wartość true, a następnie wyrażenie gradientów kolorów może być przekazywany do `strokeColor` opcji wiersza. Wyrażenie gradientu obrysu ma odwołanie `['line-progress']` wyrażenie danych, który udostępnia metryki linii obliczeniowe do wyrażenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wiersz gradientem pociągnięcia" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>wiersza gradientem obrysu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Dostosowywanie warstwę linii

Wiersz w warstwie kilka opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>wiersz opcje warstwy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Dodaj wielokąta do mapy

`Polygon` i `MultiPolygon` funkcje są często używane do reprezentowania obszar na mapie. 

### <a name="use-a-polygon-layer"></a>Użyj warstwę wielokątów 

Warstwa wielokątów renderuje obszaru wielokąta. 

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodawanie wielokąta do mapy </a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) zostanie utworzony przy użyciu tablicy współrzędnych i dodany do źródła danych. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwę wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wielokąta jest wyświetlany po mapy ładuje pełni.

### <a name="use-a-polygon-and-line-layer-together"></a>Jednocześnie używać warstwy wielokątów i linii

Warstwa linii może zostać użyty do renderowania zarys wielokąta. 

<iframe height='500' scrolling='no' title='Warstwa wielokątów i linii do dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>warstwy wielokątów i linii, aby dodać wielokąta</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. A [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) zostanie utworzony przy użyciu tablicy współrzędnych i dodany do źródła danych. 

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). A [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) jest tablicą wierszy. Zobacz właściwości warstwy linii w [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Trzeci bloku kodu tworzy warstw wielokątów i linii.

Ostatni blok kodu dodaje warstw wielokątów i linii do mapy. Źródło danych i warstwy są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że wielokąta jest wyświetlany po mapy ładuje pełni.

> [!TIP]
> Warstwy linii domyślnie będą renderowane współrzędne wielokąty, a także wierszy w źródle danych. Aby ograniczyć warstwy w taki sposób, że tylko renderuje LineString funkcje zestawu `filter` właściwości warstwy, aby `['==', ['geometry-type'], 'LineString']` lub `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` aby obejmują również funkcje MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Wypełnij wielokąta z wzorcem

Oprócz wypełnianie wielokąta kolorem wzorzec obrazu można również. Ładowanie wzorzec obrazu do zasobów sprite obrazu mapy, a następnie Odwołaj ten obraz z `fillPattern` właściwości warstwy wielokątów.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Deseń wypełnienia wielokąta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>deseń wypełnienia wielokąta</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwę wielokątów

Warstwa wielokątów ma tylko kilka opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Dodaj koło do mapy

Rozszerzona wersja schematu GeoJSON, który zawiera definicję dla okręgów, jak wspomniano korzysta z usługi Azure Maps [tutaj](extend-geojson.md#circle). Okrąg mogą być renderowane na mapie, tworząc `Point` funkcja, która ma `subType` właściwość z wartością `"Circle"` i `radius` właściwość, która ma numer reprezentuje promień w metrach. Na przykład:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Zestaw SDK usługi sieci Web Azure map konwertuje te `Pooint` funkcji do `Polygon` funkcje w sposób niewidoczny i mogą być renderowane na mapie za pomocą warstw wielokątów i linii, jak pokazano poniżej.

<iframe height='500' scrolling='no' title='Dodaj koło z mapą' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj koło z mapą</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie konstrukcji obiektu mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Okrąg jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) i ma `subType` właściwością `"Circle"` i `radius` wartości właściwości w metrach. Gdy funkcja punktu z `subType` z `"Circle"` zostanie dodany do źródła danych, jej konwertowane na okręgu wielokąta w mapie.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwę wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, czy koła jest wyświetlany po mapy ładuje pełni.

## <a name="make-a-geometry-easy-to-update"></a>Ułatw zaktualizować geometrii

A `Shape` klasy zawija [geometrii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) lub [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i można łatwo aktualizować i obsługiwać je.
`new Shape(data: Feature<data.Geometry, any>)` Tworzy obiekt kształt i inicjuje ją przy użyciu określonej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>aktualizacji właściwości kształtu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Punkt znajduje się [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klasy. Drugi blok kodu inicjuje promienia dla elementu HTML suwaka i następnie tworzy i otacza obiekt point w [kształt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) obiektu klasy.

Trzeci blok kodu tworzy funkcję, która przyjmuje wartość z elementu HTML suwaka zakresu i zmienia wartość usługi radius przy użyciu klasy kształt [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metody.

W czwartym bloku kodu, obiekt źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Punkt jest dodawane do źródła danych.

A [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) powoduje wyświetlenie danych w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Trzeci bloku kodu tworzy warstwę wielokątów. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych, program obsługi zdarzeń kliknięcie i warstwę wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) aby upewnić się, że punkt jest wyświetlany po mapy ładuje pełni.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Za pomocą wyrażeń opartych na danych stylu](data-driven-style-expressions-web-sdk.md)
