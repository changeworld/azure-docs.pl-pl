---
title: Dodawanie okna podręcznego do punktu na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać okno podręczne do punktu przy użyciu zestawu Microsoft Azure Web SDK mapy.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 79bafb331cb7ad38ea7cad9e510b22886b647764
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911144"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie okna podręcznego do mapy

W tym artykule opisano sposób dodawania okna podręcznego do punktu na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

Poniższy kod dodaje funkcję punktu, która ma `name` i `description` właściwości, do mapy za pomocą warstwy symboli. Wystąpienie [klasy popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) jest tworzone, ale nie jest wyświetlane. Zdarzenia myszy są dodawane do warstwy symboli, aby wyzwolić otwieranie i zamykanie okna podręcznego, gdy wskaźnik myszy zostanie zatrzymany i wyłączony na znaczniku symbolu. Gdy symbol znacznika jest aktywowany, właściwość `position` menu podręcznego jest aktualizowana przy użyciu pozycji znacznika, a opcja `content` jest aktualizowana przy użyciu pewnego kodu HTML, który otacza `name` i `description` właściwości punktu, w którym znajduje się kursor. Następnie zostanie wyświetlone okno podręczne na mapie przy użyciu funkcji `open`.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wyskakujących okienek przy użyciu Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Dodaj wyskakujące okienko przy użyciu Azure Maps</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Używanie okna podręcznego z wieloma punktami

Jeśli masz dużą liczbę punktów i chcesz wyświetlić tylko jedno podręczne, najlepszym rozwiązaniem jest utworzenie jednego podręcznego i ponowne użycie go zamiast tworzenia okna podręcznego dla każdej funkcji punktu. Przy użyciu okna podręcznego, liczba elementów DOM utworzonych przez aplikację jest znacznie zmniejszona, co może zapewnić lepszą wydajność. Poniższy przykład tworzy funkcje 3-punktowe. Po kliknięciu dowolnego z nich zostanie wyświetlone okno podręczne z zawartością dla tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='Używanie podręcznego z wieloma numerami PIN' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>menu podręcznego z wieloma</a> numerami pin przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Dostosowywanie okna podręcznego

Domyślnie okno podręczne ma białe tło, strzałka wskaźnik u dołu i przycisk Zamknij w prawym górnym rogu. Poniższy przykład zmienia kolor tła na czarny przy użyciu opcji `fillColor` okienka podręcznego. Przycisk Zamknij jest usuwany przez ustawienie opcji `shoCloseButton` na false. Zawartość HTML okna podręcznego używa dopełnianego 10 pikseli od krawędzi okna podręcznego, a tekst jest zmieniany, aby pokazywał dobrze na czarnym tle.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Niestandardowe okno podręczne" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>niestandardowe podręczne</a> pióro, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Zdarzenia wyskakujące

Okienka wyskakujące mogą być otwierane, zamykane i przeciągane. Klasa popup zawiera zdarzenia dla deweloperów pomocy, którzy reagują na te akcje. W poniższym przykładzie przedstawiono zdarzenia, które są wyzwalane po otwarciu, zamknięciu lub przeciągnięciu menu podręcznego. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia wyskakujące" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>zdarzenia wyskakujące</a> pióro według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Zobacz następujące wspaniałe artykuły dotyczące pełnych przykładów kodu:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie znacznika HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)