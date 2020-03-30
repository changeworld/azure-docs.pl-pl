---
title: Zmienianie stylu mapy w usłudze Azure Maps | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o funkcjach związanych ze stylem dostępnych w sdk sieci Web Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335686"
---
# <a name="change-the-style-of-the-map"></a>Zmienianie stylu mapy

Mapa obsługuje kilka różnych [opcji stylu,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) które można ustawić podczas inicjowania mapy lub nowszego przy użyciu funkcji map. `setStyle` W tym artykule pokazano, jak używać tych opcji stylu, aby dostosować wygląd mapy. Dowiedz się, jak ustawić styl podczas ładowania mapy i nauczyć się ustawiać nowy styl mapy za pomocą kontrolki selektora stylów.

## <a name="set-the-style-options"></a>Ustawianie opcji stylu 

Opcje stylu mogą być przekazywane do mapy, gdy jest `setStyle` inicjowana lub aktualizowana później za pomocą funkcji map.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

W poniższym narzędziu pokazano, jak różne opcje stylu zmieniają sposób renderowania mapy. Aby zobaczyć budynki 3D, przybliż się do dużego miasta. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje stylu mapy" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>opcje stylu mapy</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Wybieranie stylu mapy bazowej

Jedna z najpopularniejszych opcji stylu mapy służy do zmiany stylu mapy bazowej, która jest stylizowana. Wiele [obsługiwanych stylów map w usłudze Azure Maps](supported-map-styles.md) jest dostępnych w sdk sieci Web. 

### <a name="set-base-map-style-on-map-load"></a>Ustawianie stylu mapy bazowej przy obciążeniu mapy


Styl mapy można określić podczas inicjowania mapy, ustawiając `style` tę opcję. W poniższym kodzie `style` opcja mapy jest `grayscale_dark` ustawiona na inicjowanie.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Ustawianie stylu na obciążeniu mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Pióro Ustawianie stylu ładowania</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>mapy przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Aktualizowanie stylu mapy bazowej

 Styl mapy można aktualizować `setStyle` za pomocą `style` funkcji i ustawiając opcję na żądany styl mapy.

```javascript
map.setStyle({ style: 'satellite' });
```

W poniższym kodzie po załadowaniu wystąpienia mapy styl `road` `satellite` mapy jest aktualizowany z funkcji [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Pióro <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Aktualizowanie stylu</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Dodawanie selektora stylów

Kontrolka selektora stylu zapewnia łatwy w użyciu przycisk z panelem wysuwu, który może być używany przez użytkownika końcowego do zmiany stylu mapy. Selektor stylu ma dwie różne opcje układu. Domyślnie selektor stylów używa `icons` układu i wyświetla cały styl mapy jako poziomy wiersz ikon. 

<center>

![Układ ikony selektora stylów](media/choose-map-style/style-picker-icon-layout.png)</center>

Nazywa się `list` druga opcja układu i wyświetla przewijaną listę stylów mapy.  

<center>

![Układ listy selektorów stylów](media/choose-map-style/style-picker-list-layout.png)</center>


Poniższy kod pokazuje, jak utworzyć wystąpienie formantu selektora stylu i dodać go do prawego górnego rogu mapy. Selektor stylów ma ciemny styl i wyświetla kilka wybranych stylów mapy za pomocą warstwy listy.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

Poniższy kod dodaje kontrolkę selektora stylów z ustawieniami domyślnymi do mapy, dzięki czemu użytkownik może łatwo przełączać się między różnymi stylami mapy. Przełącz styl mapy za pomocą kontrolki stylu mapy w prawym górnym rogu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie selektora stylów' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Dodawanie selektora</a> stylu<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie podczas korzystania z warstwy cenowej S0 usługi Azure Maps, formant selektora stylów zawiera listę wszystkich dostępnych stylów. Jeśli chcesz zmniejszyć liczbę stylów na tej liście, przekaż tablicę stylów, które `mapStyle` mają być wyświetlane na liście, do opcji selektora stylów. Jeśli używasz S1 i chcesz pokazać wszystkie dostępne `mapStyles` style, ustaw opcję `"all"`selektora stylu na .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opcje stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [Kontrola stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [Opcje kontroli stylu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Dodaj kontrolki do map:

> [!div class="nextstepaction"]
> [Dodawanie kontrolek mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Dodawanie numeru PIN](map-add-pin.md)
