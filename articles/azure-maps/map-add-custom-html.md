---
title: Dodawanie znacznika HTML do Azure Maps | Microsoft Docs
description: Jak dodać znacznik HTML do mapy JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 65777a87cd042a4d8b3b14255dbf99241cd42e18
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638755"
---
# <a name="add-html-markers-to-the-map"></a>Dodawanie znaczników HTML do mapy

W tym artykule opisano sposób dodawania niestandardowego kodu HTML, takiego jak plik obrazu do mapy, jako znacznika HTML.

> [!NOTE]
> Znaczniki HTML nie łączą się ze źródłami danych. Zamiast tego informacje o pozycji są dodawane bezpośrednio do znacznika i znacznik jest dodawany do właściwości mapss `markers` , która jest [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> W przeciwieństwie do większości warstw w Azure Maps formancie sieci Web, który używa WebGL do renderowania, znaczniki HTML używają tradycyjnych elementów DOM do renderowania. W związku z tym więcej znaczników HTML dodano do strony, tym więcej elementów DOM. Wydajność może być gorsza po dodaniu kilku tysięcy znaczników HTML. W przypadku większych zestawów danych należy wziąć pod uwagę klastrowanie danych lub użycie symbolu lub warstwy bąbelkowej.

## <a name="add-an-html-marker"></a>Dodawanie znacznika HTML

Klasa HtmlMarker ma styl domyślny. Znacznik można dostosować, ustawiając opcje koloru i tekstu znacznika. Domyślny styl klasy HtmlMarker to szablon SVG, który ma kolor i symbol zastępczy tekstu. Ustaw właściwości koloru i tekstu w opcjach HtmlMarker na potrzeby szybkiego dostosowywania. 

<br/>

<iframe height='500' scrolling='no' title='Dodawanie znacznika HTML do mapy' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Dodaj znacznik HTML do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu dodaje [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) do mapy przy użyciu właściwości [znaczniki](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) klasy [map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . HtmlMarker jest dodawany do mapy w funkcji detektora [zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że jest ona wyświetlana po całkowitym załadowaniu mapy.

## <a name="create-svg-templated-html-marker"></a>Tworzenie znacznika HTML z szablonem SVG

Domyślnym `htmlContent` znacznikiem HTML jest szablon SVG z folderami `{color}` umieszczania i `{text}` w nim. Można utworzyć niestandardowe ciągi SVG i dodać te same symbole zastępcze do SVG, takie jak Ustawianie `color` opcji i `text` znacznika aktualizowanie tych symboli zastępczych w formacie SVG.

<br/>

<iframe height='500' scrolling='no' title='Znacznik HTML z niestandardowym szablonem SVG' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>znacznik HTML</a> pióra z niestandardowym szablonem SVG<a href='https://codepen.io/azuremaps'>@azuremaps</a>, Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Dodawanie znacznika HTML z stylem CSS

Jedną z zalet znaczników HTML jest to, że istnieje wiele doskonałych dostosowań, które można osiągnąć przy użyciu CSS. W tym przykładzie zawartość HtmlMarker składa się z kodu HTML i CSS, który tworzy animowany numer PIN, który spadnie w miejscu i pulsie.

<br/>

<iframe height='500' scrolling='no' title='Źródło danych HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>Źródło danych HTML</a> pióra według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Przeciągnięte znaczniki HTML

Ten przykład pokazuje, jak sprawić, aby można było przeciągnąć znacznik HTML. Obsługa `drag` znacznikówHTML`dragend`izdarzeń. `dragstart`

<br/>

<iframe height='500' scrolling='no' title='Przeciągany znacznik HTML' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>znacznik HTML</a> z przeciągnięciem piórem przez<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Dodawanie zdarzeń myszy do znaczników HTML

Te przykłady pokazują, jak dodać mysz i przeciągnąć zdarzenia do znacznika HTML.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie zdarzeń myszy do znaczników HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Dodawanie zdarzeń myszy do znaczników HTML</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](./map-add-bubble-layer.md)