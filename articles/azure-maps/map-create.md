---
title: Tworzenie mapy za pomocą usługi Azure Maps | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować mapę na stronie sieci Web przy użyciu sdk Microsoft Azure Maps Web.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cfeff430e5313c8728582c4790c9aca9482d63aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534921"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule przedstawiono sposoby tworzenia mapy i animowania jej.  

## <a name="loading-a-map"></a>Ładowanie mapy

Aby załadować mapę, utwórz nowe wystąpienie [klasy Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Podczas inicjowania mapy należy przekazać identyfikator elementu DIV, aby renderować mapę i przekazać zestaw opcji do użycia podczas ładowania mapy. Jeśli domyślne informacje o uwierzytelnianiu nie są określone w obszarze `atlas` nazw, informacje te będą musiały być określone w opcjach mapy podczas ładowania mapy. Mapa ładuje kilka zasobów asynchronicznie dla wydajności. W związku z tym po utworzeniu `ready` `load` wystąpienia mapy dołącz lub zdarzenia do mapy, a następnie dodać dowolny dodatkowy kod, który współdziała z mapą do programu obsługi zdarzeń. Zdarzenie `ready` jest uruchamiane, gdy tylko mapa ma wystarczającą ilość zasobów załadowanych do interakcji z programowo. Zdarzenie `load` zostanie podpalone po całkowitym załadowaniu początkowego widoku mapy. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe obciążenie mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>ładowanie mapy</a> Pen Basic<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

> [!TIP]
> Na tej samej stronie można załadować wiele map. Wiele map na tej samej stronie może używać tego samego lub innego uwierzytelniania i ustawień języka.

## <a name="show-a-single-copy-of-the-world"></a>Pokaż jedną kopię świata

Gdy mapa zostanie powiększona na szerokim ekranie, wiele kopii świata pojawi się poziomo. Ta opcja jest świetna w niektórych scenariuszach, ale w przypadku innych aplikacji pożądane jest wyświetlenie jednej kopii świata. To zachowanie jest implementowane `renderWorldCopies` przez `false`ustawienie opcji mapy na .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz renderowanie pióraWorldCopies =<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>false</a> przez usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opcje mapy

Podczas tworzenia mapy istnieje kilka różnych typów opcji, które można przekazać, aby dostosować sposób działania mapy, jak wymieniono poniżej.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) i [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) są używane do określenia obszaru mapy powinny być wyświetlane.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) są używane do określenia, jak mapa powinna wchodzić w interakcje z usługami, które zasilają mapę.
- [Opcje style](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) są używane do określenia mapy powinny być stylizowane i renderowane.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) są używane do określenia, jak mapa powinna dotrzeć, gdy użytkownik wchodzi w interakcję z mapą. 

Opcje te można również aktualizować po załadowaniu `setStyle`mapy `setUserInteraction` za pomocą programu `setCamera`, `setServiceOptions`i funkcji. 

## <a name="controlling-the-map-camera"></a>Sterowanie kamerą mapy

Za pomocą kamery mapy można ustawić dwa sposoby ustawienia wyświetlanego obszaru mapy. Opcje kamery można ustawić podczas ładowania mapy. Można też wywołać `setCamera` tę opcję w dowolnym momencie po załadowaniu mapy, aby programowo zaktualizować widok mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustawianie kamery

Kamera mapy kontroluje to, co jest wyświetlane w rzutni obszaru roboczego mapy. Opcje kamery mogą być przekazywane do opcji mapy podczas `setCamera` inicjowania lub przekazywania do funkcji map.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

W poniższym kodzie tworzony jest [obiekt Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) i ustawiane są opcje środka i powiększenia. Właściwości mapy, takie jak poziom środka i powiększenia, są częścią [cameraoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą cameraoptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>pióro Tworzenie `CameraOptions` mapy za pośrednictwem </a>usług opartych na lokalizacji platformy Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustawianie granic kamery

Do aktualizacji kamery mapy można użyć obwiedni. Jeśli obwiednia została obliczona na podstawie danych punktowych, często warto również określić wartość dopełnienia piksela w opcjach aparatu, aby uwzględnić rozmiar ikony. Pomoże to zapewnić, że punkty nie spadną z krawędzi rzutni mapy.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

W poniższym kodzie [obiekt Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) `new atlas.Map()`jest konstruowany za pomocą pliku . Właściwości mapy, `CameraBoundsOptions` takie jak mogą być zdefiniowane za pomocą funkcji [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) klasy Map. Granice i właściwości dopełnienie `setCamera`są ustawiane przy użyciu programu .

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą cameraboundsoptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>pióro Tworzenie `CameraBoundsOptions` mapy przez </a>usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animowanie widoku mapy

Podczas ustawiania opcji kamery mapy można również ustawić [opcje animacji.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) Opcje te określają typ animacji i czas trwania, jaki powinien zająć przeniesienie kamery.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

W poniższym kodzie pierwszy blok kodu tworzy mapę i ustawia style mapy enter i zoom. W drugim bloku kodu jest tworzony program obsługi zdarzeń kliknięcia dla przycisku animate. Po kliknięciu tego przycisku `setCamera` funkcja jest wywoływana z pewnymi losowymi [wartościami dla cameraoptions](/javascript/api/azure-maps-control/atlas.cameraoptions) i [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animowany widok mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz widok <a href='https://codepen.io/azuremaps/pen/WayvbO/'>map animowanych</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Wypróbuj kod

Spójrz na przykłady kodu. Możesz edytować kod JavaScript wewnątrz **karty JS** i zobaczyć zmiany widoku mapy na **karcie Wynik**. Można również kliknąć **edytuj na CodePen**, w prawym górnym rogu i zmodyfikować kod w CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions (Ochemaczenie kamery)](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Propozycje animacji](/javascript/api/azure-maps-control/atlas.animationoptions)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Zmienianie stylu mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Dodawanie kontrolek do mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
