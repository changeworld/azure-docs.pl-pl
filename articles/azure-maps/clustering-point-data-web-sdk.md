---
title: Dane punktu klastrowania na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wskazać dane punktu klastra i renderować je na mapie za pomocą zestawu Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 846abb61511ae1d5aedf77059ed2f1e9f4e5dbfb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911736"
---
# <a name="clustering-point-data"></a>Dane punktu klastrowania

Podczas wizualizacji wielu punktów danych na mapie, punkty nakładają się na siebie, mapa jest nieczytelna i trudno jest zobaczyć i korzystać z niej. Do poprawy tego środowiska użytkownika można użyć klastrowania danych punktów. Dane punktu klastrowania to proces łączenia danych punktów, które są blisko siebie i reprezentowania ich na mapie jako pojedynczy klastrowany punkt danych. Gdy użytkownik powiększa mapę, klastry rozdzielą się na poszczególne punkty danych.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Włączanie klastrowania w źródle danych

Klastrowanie można łatwo włączyć w klasie `DataSource` przez ustawienie opcji `cluster` na wartość true. Ponadto promień pikseli służący do wybierania punktów w pobliżu do łączenia się z klastrem można ustawić przy użyciu `clusterRadius` i można określić poziom powiększenia, w którym ma zostać wyłączona logika klastrowania przy użyciu opcji `clusterMaxZoom`. Oto przykład sposobu włączania klastrowania w źródle danych.

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
> Jeśli dwa punkty danych są blisko siebie, możliwe, że klaster nigdy nie ulegnie przerwie, niezależnie od tego, jak zamknięcie użytkownika zostanie powiększone. Aby rozwiązać ten potrzeba, można ustawić opcję `clusterMaxZoom` źródła danych, która określa na poziomie powiększenia, aby wyłączyć logikę klastrowania i po prostu wyświetlić wszystko.

Klasa `DataSource` ma również następujące metody związane z klastrowaniem:

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera elementy podrzędne danego klastra na następnym poziomie powiększenia. Te elementy podrzędne mogą być kombinacją kształtów i podklastrów. Podklastry będą funkcjami o właściwościach pasujących do ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Numer&lt;Promise&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub przerywanie. |
| getClusterLeaves (clusterId: Number, limit: Number, offset: Number) | Funkcja Promise&lt;Array&lt;funkcji&lt;geometria&gt; \|&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw `limit`, aby zwracał podzbiór punktów, i użyj `offset` do strony za pomocą punktów. |

## <a name="display-clusters-using-a-bubble-layer"></a>Wyświetlanie klastrów przy użyciu warstwy bąbelkowej

Warstwa bąbelków to świetny sposób renderowania punktów w klastrze, ponieważ można łatwo skalować promień i zmieniać kolor na podstawie liczby punktów w klastrze przy użyciu wyrażenia. Podczas wyświetlania klastrów przy użyciu warstwy bąbelków należy również użyć osobnej warstwy do renderowania nieklastrowanych punktów danych. Często warto również mieć możliwość wyświetlenia rozmiaru klastra w górnej części bąbelków. Do osiągnięcia tego zachowania można użyć warstwy symbol z tekstem i nie ma ikony. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe klastrowanie warstwy bąbelków" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z tematem zaAzure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>warstwie bąbelkowej</a> dla języka Basic na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Wyświetlanie klastrów przy użyciu warstwy symboli

Podczas wizualizacji danych punktu przy użyciu warstwy symbol domyślnie automatycznie ukrywa symbole, które nakładają się na siebie w celu utworzenia środowiska czyszczącego, ale może to nie być wymagane środowisko, jeśli chcesz zobaczyć gęstość punktów danych na mapie. Ustawienie opcji `allowOverlap` właściwości `iconOptions` warstwy symboli na `true` wyłącza to środowisko, ale spowoduje to wyświetlenie wszystkich wyświetlanych symboli. Użycie klastrowania pozwala zobaczyć gęstość wszystkich danych podczas tworzenia całkiem czystego środowiska użytkownika. W tym przykładzie niestandardowe symbole będą używane do reprezentowania klastrów i poszczególnych punktów danych.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klastrowana warstwa symboli" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>warstwą symboli klastrowanych</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klastrowanie i warstwa mapy cieplnej

Mapy cieplne to doskonały sposób wyświetlania gęstości danych na mapie. Wizualizacja może obsługiwać dużą liczbę punktów danych, ale może obsługiwać jeszcze więcej danych, jeśli punkty danych są klastrowane i rozmiar klastra jest używany jako waga mapy cieplnej. Ustaw opcję `weight` warstwy mapy cieplnej, aby `['get', 'point_count']` to osiągnąć. Gdy promień klastra jest mały, Mapa cieplna będzie wyglądać niemal identycznie jak mapa cieplna korzystająca z nieklastrowanych punktów danych, ale będzie działać znacznie lepiej. Jednak im mniejsza jest wartość promienia klastra, tym dokładniejsza jest mapa cieplna, ale z mniejszą korzyścią dla wydajności.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa cieplna ważona klastra" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>z Azure Maps</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Zdarzenia myszy w klastrowanych punktach danych

Gdy zdarzenia myszy występują na warstwie zawierającej klastrowane punkty danych, klastrowany punkt danych zostanie zwrócony do zdarzenia jako obiekt funkcji punktu GEOJSON. Ta funkcja wskazuje następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | wartość logiczna | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | string  | Unikatowy identyfikator klastra, który może być używany z metodami DataSource `getClusterExpansionZoom`, `getClusterChildren`i `getClusterLeaves`. |
| `point_count`             | numer  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | string  | Ciąg, który skróci wartość `point_count`, jeśli jest długi. (na przykład 4 000 to 4K)  |

Ten przykład pobiera warstwę bąbelkową, która renderuje punkty klastra i dodaje zdarzenie kliknięcia, które po wyzwoleniu, obliczeniu i powiększeniu mapy do następnego poziomu powiększenia, przy którym klaster zostanie rozbicie przy użyciu metody `getClusterExpansionZoom` klasy `DataSource` i właściwości `cluster_id` klikniętego klastrowanego punktu danych. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="GetClusterExpansionZoom klastra" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/moZWeV/'>GetClusterExpansionZoom klastra</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Wyświetlanie obszaru klastra 

Dane punktów reprezentowane przez klaster są rozłożone na obszar. W tym przykładzie, gdy wskaźnik myszy znajduje się nad klastrem, poszczególne punkty danych, które zawiera, zostaną użyte do obliczenia wypukłego kadłuba i wyświetlenia na mapie w celu wyświetlenia obszaru. Wszystkie punkty zawarte w klastrze mogą być pobierane ze źródła danych przy użyciu metody `getClusterLeaves`. Kadłub wypukły jest wielokątem, który otacza zestaw punktów, takich jak elastyczny zakres i może być obliczany przy użyciu metody `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kadłub wypukłych obszarów klastra" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kadłubem obszar klastra piórem wypukły</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregowanie danych w klastrach

Często klastry są reprezentowane przy użyciu symbolu z liczbą punktów znajdujących się w klastrze, jednak czasami pożądane jest dalsze dostosowanie stylu klastrów na podstawie pewnej metryki, na przykład łącznego przychodu wszystkich punktów w klastrze. Właściwości niestandardowe z agregacją klastra mogą być tworzone i wypełniane przy użyciu obliczeń [wyrażeń agregujących](data-driven-style-expressions-web-sdk.md#aggregate-expression) .  Agregacje klastrów można definiować w `clusterProperties` opcji `DataSource`.

Poniższy przykład używa wyrażenia agregującego, aby obliczyć liczbę na podstawie właściwości typu jednostki każdego punktu danych w klastrze.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregacje klastra" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregaty klastra</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource — Klasa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Obiekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math — przestrzeń nazw](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy cieplnej](map-add-heat-map-layer.md)
