---
title: Klastrowanie punktu danych w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak klastra punktu danych w zestawie SDK sieci Web
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 6dbd4461e7b8382ec3c4075b9688de59678f98f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957320"
---
# <a name="clustering-point-data"></a>Klastrowanie punktu danych

Gdy wizualizacja wiele punktów danych na mapie, punkty nakładają się na siebie, nieczytelny mapy i staje się trudne do przeglądania i używania. Klastrowanie punktu danych można ulepszyć to środowisko użytkownika. Klastrowanie punktu danych jest proces łączenia punktu danych, które są obok siebie i przedstawiania ich na mapie jako pojedynczego klastra punktu danych. Jako użytkownik powiększa do mapy, klastry przerwać odległość między ich poszczególne punkty danych.

## <a name="enabling-clustering-on-a-data-source"></a>Włączenie usługi klastrowania na źródło danych

Klastrowanie, łatwo można włączyć dla `DataSource` klasy, ustawiając `cluster` opcji na wartość true. Ponadto radius pikseli, aby wybrać punktów orientacyjnych w pobliżu połączyć w klaster można ustawić za pomocą `clusterRadius` i poziom powiększenia można określić, jaką można wyłączyć, klastrowania przy użyciu logiki `clusterMaxZoom` opcji. Poniżej przedstawiono przykładowy sposób włączenia klastrowania w źródle danych.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Jeśli na podstawie dwóch punktów znajdują się blisko siebie, istnieje możliwość, klaster nigdy nie spowoduje przerwanie od siebie, niezależnie od tego, jak blisko powiększa się użytkownika. Aby rozwiązać ten problem, możesz ustawić `clusterMaxZoom` opcję źródła danych, który określa na poziom powiększenia, aby wyłączyć logiki klastrowania i po prostu wyświetlanie wszystkich elementów.

`DataSource` Klasa ma również następujące metody, które są związane z klastrowania:

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;tablicy&lt;funkcji&lt;Geometry, wszelkie&gt; \| kształtu&gt;&gt; | Pobiera elementy podrzędne danego klastra na następny poziom powiększenia. Tych elementów podrzędnych może być kombinacją kształty i subclusters. Subclusters będzie funkcji dopasowywania ClusteredProperties właściwości. |
| getClusterExpansionZoom(clusterId: number) | Promise&lt;numer&gt; | Oblicza poziom powiększenia, jaką rozpoczęcia, rozszerzając lub Rozdziel klastra. |
| getClusterLeaves (clusterId: liczba limit: numer, offset: numer) | Promise&lt;tablicy&lt;funkcji&lt;Geometry, wszelkie&gt; \| kształtu&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw `limit` zwracanie podzbioru punktów i używać `offset` do strony za pośrednictwem punktów. |

## <a name="display-clusters-using-a-bubble-layer"></a>Wyświetlanie klastrów za pomocą warstwy bąbelkowych

Warstwa bąbelków to świetny sposób renderowania punktów klastrowane można łatwo skalować promień i zmień kolor, który je na podstawie liczby punktów w klastrze za pomocą wyrażenia. Przy wyświetlaniu klastrów za pomocą warstwy bąbelków, możesz także użyć osobnej warstwie renderowania punktów danych rozłączony. Często jest to dobre rozwiązanie również mieć możliwość wyświetlania rozmiar klastra na szczycie rozbieżnościach. Warstwa symbol tekst i ikona nie może służyć do osiągnięcia tego zachowania. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa podstawowa bąbelków klastrowania" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>klastrowania warstwy podstawowa bąbelków</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Wyświetlanie klastrów za pomocą warstwy symboli

W przypadku wizualizowania danych punktu, przy użyciu warstwy symboli, domyślnie była automatycznie ukrywana symboli tego nakładania się siebie, aby utworzyć bardziej przejrzyste środowisko, jednak może to nie być założenie, jeśli chcesz zobaczyć gęstość danych punktów na mapie. Ustawienie `allowOverlap` opcji warstwy Symbol `iconOptions` właściwość `true` powoduje wyłączenie tego środowiska, ale spowoduje wszystkie symbole są wyświetlane. Przy użyciu klastra umożliwia zobaczenie gęstość wszystkich danych podczas tworzenia dobre rozwiązanie, wyczyść komfortu. W tym przykładzie niestandardowe symbole będzie służyć do reprezentowania klastrów i poszczególnych punktów danych.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klastrowane warstwy symboli" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>warstwy klastrowane Symbol</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klastrowanie i ciepło mapuje warstwy

Mapy cieplnej są doskonałym sposobem na wyświetlanie gęstość danych na mapie. Tę wizualizację może obsługiwać dużą liczbę punktów danych na swój własny, ale może obsługiwać nawet większej ilości danych, jeśli punkty danych są klastrowane, a rozmiar klastra jest używany jako wagę Mapa cieplna. Ustaw `weight` opcji warstwy mapy cieplnej `['get', 'point_count']` można to osiągnąć. Gdy radius klastra jest mała, mapa cieplna wygląd niemal identyczne do mapy cieplnej przy użyciu punktów danych rozłączony, ale będzie wykonywać znacznie lepiej. Jednak mniejszy promień, bardziej precyzyjne Mapa cieplna zostaną, ale przy mniejszym wydajności korzyści.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klaster ważona Mapa cieplna" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>klastra ważona Mapa cieplna</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Zdarzenia myszy na punktach danych klastra

Po wystąpieniu zdarzenia myszy na warstwy, która zawiera punkty danych klastrowanych, punktu danych klastra zostanie przywrócony do zdarzenia jako obiekt funkcji punktu GeoJSON. Ta funkcja punktu mają następujące właściwości:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Klastra | wartość logiczna | Wskazuje, czy funkcja reprezentuje klastra. |
| cluster_id | string | Unikatowy identyfikator dla klastra, który może być używany z źródła danych `getClusterExpansionZoom`, `getClusterChildren`, i `getClusterLeaves` metody. |
| point_count | numer | Liczba punktów, znajdujących się w klastrze. |
| point_count_abbreviated | string | Ciąg, który wyświetla trzyliterowy skrót `point_count` wartość, jeśli jest za długa. (na przykład 4000 staje się 4K) |

W tym przykładzie pobiera warstwy bąbelków, która renderuje punktów klastra i dodaje zdarzenie click, po wyzwoleniu obliczania i powiększenia mapy na wyższy poziom powiększenia, w którym klastra spowoduje przerwanie od siebie przy użyciu `getClusterExpansionZoom` metody `DataSource` klasy i `cluster_id` właściwość kliknięto klastrowane punktu danych. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom klastra" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom klastra</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="display-cluster-area"></a>Wyświetl obszar klastra 

Dane punktu, która reprezentuje klaster jest rozłożona na obszar. W tym przykładzie, gdy wskaźnik myszy jest aktywowany przez klaster dane poszczególnych punktów, że zawiera on (liści) będą używane do obliczania wypukłe kadłuba i wyświetlane na mapie, aby wyświetlić obszar. Wszystkie punkty znajdujących się w klastrze mogą być pobierane ze źródła danych przy użyciu `getClusterLeaves` metody. Wypukłe kadłuba jest wielokąta, który otacza zestaw punktów, takie jak elastycznej poza pasmem i mogą być obliczane przy użyciu `atlas.math.getConvexHull` metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kadłuba wypukłe obszaru klastra" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióra <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>klastra obszaru wypukłe kadłuba</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Klasa źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Obiekt dataSourceOptions musi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [przestrzeń nazw Atlas.Math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkowych](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md)
