---
title: Dane punktów klastrowania na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak klastrować dane punktów i renderować go na mapie przy użyciu zestawu Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: ce2891201331ee1efd861d2f13cec78c0551b6ba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804575"
---
# <a name="clustering-point-data"></a>Dane punktu klastrowania

Podczas wizualizacji wielu punktów danych na mapie punkty danych mogą nakładać się na siebie. Nakładanie się może spowodować, że mapa może stać się nieczytelna i trudna w użyciu. Dane punktów klastrowania to proces łączenia danych punktowych, które znajdują się blisko siebie i reprezentowania ich na mapie jako pojedynczego klastrowanego punktu danych. Gdy użytkownik powiększa mapę, klastry rozpadają się na poszczególne punkty danych. Podczas pracy z dużą liczbą punktów danych należy użyć procesów klastrowania, aby poprawić środowisko użytkownika.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Włączanie klastrowania w źródle danych

Włącz klastrowanie `DataSource` w klasie, `cluster` ustawiając opcję true. Ustaw, `ClusterRadius` aby wybierać pobliskie punkty i łączyć je w klastrze. Wartość `ClusterRadius` jest w pikselach. Służy `clusterMaxZoom` do określania poziomu powiększenia, przy którym można wyłączyć logikę klastrowania. Oto przykład włączania klastrowania w źródle danych.

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
> Jeśli dwa punkty danych znajdują się blisko siebie na ziemi, jest możliwe, że klaster nigdy się nie rozpadnie, bez względu na to, jak blisko powiększy użytkownika. Aby rozwiązać ten problem, `clusterMaxZoom` można ustawić opcję, aby wyłączyć logikę klastrowania i po prostu wyświetlić wszystko.

Oto dodatkowe metody, `DataSource` które klasa zapewnia klastrowania:

| Metoda | Zwracany typ | Opis |
|--------|-------------|-------------|
| getClusterChildren(clusterId: liczba) | Geometria&lt;&lt;operacji tablicy&gt; \| Promise,&lt;dowolny kształt&gt;&gt; | Pobiera korle danego klastra na następnym poziomie powiększenia. Te dzieci mogą być kombinacją kształtów i podklastrów. Podklastery będą funkcje z właściwości pasujących ClusteredProperties. |
| getClusterExpansionZoom(clusterId: liczba) | Numer&lt;obietnicy&gt; | Oblicza poziom powiększenia, przy którym klaster rozpocznie rozszerzanie lub rozpadanie. |
| getClusterLeaves(clusterId: liczba, limit: liczba, przesunięcie: liczba) | Geometria&lt;&lt;operacji tablicy&gt; \| Promise,&lt;dowolny kształt&gt;&gt; | Pobiera wszystkie punkty w klastrze. Ustaw, `limit` aby zwrócić podzbiór punktów `offset` i użyj strony do punktu. |

## <a name="display-clusters-using-a-bubble-layer"></a>Wyświetlanie klastrów przy użyciu warstwy bąbelkowej

Warstwa bąbelkowa to świetny sposób renderowania punktów grupowanych. Użyj wyrażeń, aby skalować promień i zmieniać kolor na podstawie liczby punktów w klastrze. W przypadku wyświetlania klastrów przy użyciu warstwy bąbelkowej należy użyć oddzielnej warstwy do renderowania niesklastrowanych punktów danych.

Aby wyświetlić rozmiar klastra na wierzchu polana, użyj warstwy symboli z tekstem i nie używaj ikony.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klastrowanie warstw bąbelkowych podstawowych" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>klastrowanie warstw bąbelkowych Pióra podstawowego</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Wyświetlanie klastrów przy użyciu warstwy symboli

Podczas wizualizacji punktów danych warstwa symboli automatycznie ukrywa symbole, które nakładają się na siebie, aby zapewnić czystszy interfejs użytkownika. To domyślne zachowanie może być niepożądane, jeśli chcesz pokazać gęstość punktów danych na mapie. Można jednak zmienić te ustawienia. Aby wyświetlić wszystkie symbole, należy ustawić `allowOverlap` opcję właściwości Symbol layers `iconOptions` na `true`. 

Klastrowanie służy do pokazywania gęstości punktów danych przy zachowaniu czystego interfejsu użytkownika. Poniższy przykład pokazuje, jak dodawać symbole niestandardowe i reprezentować klastry i poszczególne punkty danych przy użyciu warstwy symboli.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Warstwa symboli klastrowanych" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>warstwę symboli klastrowanych</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Klastrowanie i warstwa map cieplnych

Mapy skupień to świetny sposób na wyświetlanie gęstości danych na mapie. Ta metoda wizualizacji może obsługiwać dużą liczbę punktów danych samodzielnie. Jeśli punkty danych są klastrowane, a rozmiar klastra jest używany jako waga mapy skupień, mapa skupień może obsłużyć jeszcze więcej danych. Aby osiągnąć tę opcję, należy ustawić `weight` opcję `['get', 'point_count']`warstwy mapy cieplnej na . Gdy promień klastra jest mały, mapa skupień będzie wyglądać prawie identycznie jak mapa skupień przy użyciu niesklastrowanych punktów danych, ale będzie działać znacznie lepiej. Jednak im mniejszy promień klastra, tym dokładniejsza będzie mapa skupień, ale z mniejszą wydajnością.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ważony mapę cieplną klastra" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz mapę <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>cieplną ważoną klastra</a> piór przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Zdarzenia myszy w klastrowanych punktach danych

Gdy zdarzenia myszy występują na warstwie zawierającej klastrowane punkty danych, klastrowany punkt danych powraca do zdarzenia jako obiekt obiektowy punktu GeoJSON. Ta funkcja punktu będzie miała następujące właściwości:

| Nazwa właściwości             | Typ    | Opis   |
|---------------------------|---------|---------------|
| `cluster`                 | wartość logiczna | Wskazuje, czy funkcja reprezentuje klaster. |
| `cluster_id`              | ciąg  | Unikatowy identyfikator klastra, który może być `getClusterExpansionZoom`używany `getClusterChildren`z `getClusterLeaves` datasource , i metody. |
| `point_count`             | numer  | Liczba punktów, które zawiera klaster.  |
| `point_count_abbreviated` | ciąg  | Ciąg, który skraca `point_count` wartość, jeśli jest długa. (na przykład 4000 staje się 4K)  |

W tym przykładzie przyjmuje warstwę bąbelkową, która renderuje punkty klastra i dodaje zdarzenie kliknięcia. Gdy zdarzenie kliknięcia wyzwala, kod oblicza i powiększa mapę do następnego poziomu powiększenia, na którym klaster rozpada się. Ta funkcja jest implementowana `getClusterExpansionZoom` przy `DataSource` użyciu metody `cluster_id` klasy i właściwości kliknięty punkt danych klastrowanych.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klaster getClusterRozwińZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterRozwiń zzoom klastra</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>piórowego przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Wyświetlanie obszaru klastra 

Dane punktowe, które reprezentuje klaster, są rozłożone na obszar. W tym przykładzie, gdy mysz jest najechany na klastrze, występują dwa główne zachowania. Po pierwsze, poszczególne punkty danych zawarte w klastrze będą używane do obliczania wypukłego kadłuba. Następnie wypukły kadłub zostanie wyświetlony na mapie, aby pokazać obszar.  Wypukły kadłub to wielokąt, który owija zestaw punktów jak gumka i może być obliczany `atlas.math.getConvexHull` metodą. Wszystkie punkty zawarte w klastrze można pobrać ze `getClusterLeaves` źródła danych przy użyciu metody.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Powierzchnia gromady wypukła kadłub" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>obszar klastra pióra wypukły kadłub</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Agregowanie danych w klastrach

Często klastry są reprezentowane przy użyciu symbolu z liczbą punktów, które znajdują się w klastrze. Ale czasami jest pożądane, aby dostosować styl klastrów z dodatkowych metryk. Za pomocą agregatów klastra właściwości niestandardowe można tworzyć i wypełniać za pomocą obliczeń [wyrażenia agregacji.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Agregaty klastra `clusterProperties` można zdefiniować w opcji `DataSource`.

Poniższy przykład używa wyrażenia agregacji. Kod oblicza liczbę na podstawie właściwości typu jednostki każdego punktu danych w klastrze. Gdy użytkownik kliknie klaster, pojawi się okno podręczne z dodatkowymi informacjami o klastrze.

<iframe height="500" style="width: 100%;" scrolling="no" title="Agregaty klastra" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>agregacje klastra</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>piór przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Klasa DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Obiekt DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math obszar nazw](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)
