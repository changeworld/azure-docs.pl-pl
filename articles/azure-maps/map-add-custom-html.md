---
title: Dodawanie znacznika HTML do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać znacznik HTML do mapy przy użyciu zestawu SDK w sieci Web map platformy Microsoft Azure.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2700d42c25d58911fb275ad9ce6c5610cd22624d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536772"
---
# <a name="add-html-markers-to-the-map"></a>Dodawanie znaczników HTML do mapy

W tym artykule pokazano, jak dodać niestandardowy kod HTML, taki jak plik obrazu do mapy jako znacznik HTML.

> [!NOTE]
> Znaczniki HTML nie łączą się ze źródłami danych. Zamiast tego informacje o pozycji są dodawane bezpośrednio do `markers` znacznika, a znacznik jest dodawany do właściwości maps, która jest [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> W przeciwieństwie do większości warstw w formancie azure maps web, które używają WebGL do renderowania, znaczniki HTML używać tradycyjnych elementów DOM do renderowania. W związku z tym im więcej znaczników HTML dodanych do strony, tym więcej jest elementów DOM. Wydajność może ulec pogorszeniu po dodaniu kilkuset znaczników HTML. W przypadku większych zestawów danych należy wziąć pod uwagę klastrowanie danych lub przy użyciu warstwy Symbol lub Bąbelki.

## <a name="add-an-html-marker"></a>Dodawanie znacznika HTML

[Klasa HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) ma styl domyślny. Znacznik można dostosować, ustawiając opcje koloru i tekstu znacznika. Domyślnym stylem klasy znacznika HTML jest szablon `{color}` SVG, który ma symbol zastępczy i `{text}` zastępczy. Ustaw właściwości koloru i tekstu w opcjach znacznika HTML, aby uzyskać szybkie dostosowanie. 

Poniższy kod tworzy znacznik HTML i ustawia właściwość color na "DodgerBlue" i właściwość text na "10". Wyskakujące okienko jest `click` dołączone do znacznika i zdarzenie służy do przełączania widoczności okna podręcznego.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie znacznika HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>pióro Dodaj znacznik HTML do</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>mapy przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Tworzenie znacznika HTML z szablonem SVG

Domyślnym `htmlContent` znacznikiem Html jest szablon SVG `{color}` z `{text}` folderami miejsc i w nim. Można utworzyć niestandardowe ciągi SVG i dodać te same symbole `color` zastępcze do SVG w taki sposób, aby ustawienie i `text` opcje znacznika aktualizować te symbole zastępcze w SVG.

<br/>

<iframe height='500' scrolling='no' title='Znacznik HTML z niestandardowym szablonem SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz znacznik HTML pióra <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>z niestandardowym szablonem SVG</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web usługi Azure Maps udostępnia kilka szablonów obrazów SVG, które mogą być używane ze znacznikami HTML. Aby uzyskać więcej informacji, zobacz [dokument Jak używać szablonów obrazów.](how-to-use-image-templates-web-sdk.md)

## <a name="add-a-css-styled-html-marker"></a>Dodawanie znacznika HTML w stylu CSS

Jedną z zalet znaczników HTML jest to, że istnieje wiele wspaniałych dostosowań, które można osiągnąć za pomocą CSS. W tym przykładzie zawartość HtmlMarker składa się z HTML i CSS, które tworzą animowany pin, który spada na miejsce i impulsy.

<br/>

<iframe height='500' scrolling='no' title='Źródło danych HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz źródło <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>danych HTML</a> pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Znaczniki HTML z przeciąganymi

W tym przykładzie pokazano, jak można przeciągać znacznik HTML. Obsługa `drag`znaczników `dragstart`HTML `dragend` i zdarzeń.

<br/>

<iframe height='500' scrolling='no' title='Znacznik HTML z przeciąganie' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz znacznik <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>HTML z przeciąganiem pióra</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>kodzie CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Dodawanie zdarzeń myszy do znaczników HTML

Te przykłady pokazują, jak dodać myszy i przeciągnąć zdarzenia do znacznika HTML.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie zdarzeń myszy do znaczników HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Dodawanie zdarzeń myszy do znaczników HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Znacznik html](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions (Niem. html)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Menedżer HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](./map-add-bubble-layer.md)
