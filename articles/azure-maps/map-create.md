---
title: Tworzenie mapy za pomocą Azure Maps | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować mapę na stronie sieci Web za pomocą Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911104"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule pokazano, jak utworzyć mapę i animować mapę.  

## <a name="loading-a-map"></a>Ładowanie mapy

Aby załadować mapę, Utwórz nowe wystąpienie [klasy map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Podczas inicjowania mapy, identyfikator elementu DIV służący do renderowania mapy i zestaw opcji do użycia podczas ładowania mapy są przesyłane. Jeśli w przestrzeni nazw `atlas` nie określono domyślnych informacji o uwierzytelnianiu, te informacje będą musiały zostać określone w opcjach mapy podczas ładowania mapy. Mapa ładuje wiele zasobów asynchronicznie dla wydajności. W związku z tym po utworzeniu wystąpienia mapy należy dołączyć zdarzenie `ready` lub `load` do mapy, a następnie dodać dowolny dodatkowy kod, który współdziała z mapą w tym obsłudze zdarzeń. Zdarzenie `ready` wyzwalane, gdy tylko mapa ma wystarczającą liczbę zasobów, z których załadowano program programowo. Zdarzenie `load` wyzwalane po całkowitym załadowaniu widoku mapy początkowej. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Podstawowe obciążenie mapy" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>podstawowe mapowanie</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Na tej samej stronie można ładować wiele map, a każda z nich może korzystać z tych samych ustawień uwierzytelniania i języka.

## <a name="show-a-single-copy-of-the-world"></a>Pokaż pojedynczą kopię świata

Gdy mapa zostanie powiększona na szerokim ekranie, wiele kopii świata będzie wyświetlanych w poziomie. Jest to doskonałe rozwiązanie w przypadku większości scenariuszy, ale niektóre dla niektórych aplikacji mogą być pożądane, aby widoczna była tylko jedna kopia świata. Można to zrobić, ustawiając opcję Mapuj `renderWorldCopies`, aby `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = FAŁSZ" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Sterowanie kamerą mapy

Istnieją dwa sposoby ustawiania obszaru wyświetlania mapy przy użyciu aparatu. Można ustawić opcje kamery, takie jak Center i zoom, podczas ładowania mapy lub wywoływać opcję `setCamera` w dowolnym momencie po załadowaniu mapy w celu programowego zaktualizowania widoku mapy.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustawianie aparatu

W poniższym kodzie tworzony jest [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) i opcje wyśrodkowania i powiększania są ustawione. Właściwości mapy, takie jak centrum i poziom powiększenia, są częścią [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Utwórz mapę za pośrednictwem `CameraOptions` </a>przez Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustawianie granic kamery

W poniższym kodzie [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) jest konstruowany za pośrednictwem `new atlas.Map()`. Właściwości mapy, takie jak `CameraBoundsOptions`, można zdefiniować za pomocą funkcji [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) klasy map. Właściwości Bound i uzupełnienie są ustawiane przy użyciu `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Utwórz mapę za pośrednictwem `CameraBoundsOptions` </a>przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animuj widok mapy

W poniższym kodzie pierwszy blok kodu tworzy mapę i ustawia styl mapy, wartości środkowe i powiększenia. W drugim bloku kodu dla przycisku Animuj jest tworzony program obsługi zdarzeń kliknięcia. Po kliknięciu tego przycisku Funkcja setcamera jest wywoływana z niektórymi wartościami losowymi dla [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animuj widok mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Obejrzyj <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Widok mapy animacji</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Wypróbuj kod

Zapoznaj się z przykładowym kodem powyżej. Kod JavaScript można edytować na **karcie js** po lewej stronie i zobaczyć zmiany w widoku mapy na **karcie wynik** po prawej stronie. Możesz również kliknąć przycisk **Edytuj na CodePen** i edytować kod w CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Zmień styl mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Dodawanie formantów do mapy](map-add-controls.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)