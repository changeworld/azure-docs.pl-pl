---
title: Tworzenie mapy za pomocą Azure Maps | Microsoft Docs
description: Jak utworzyć mapę JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8a4f67290e93d8b296added9023fe9b6947ba02c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638683"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule pokazano, jak utworzyć mapę i animować mapę.  

## <a name="understand-the-code"></a>Zrozumienie kodu

Istnieją dwa sposoby konstruowania mapy. Można ustawić kamerę mapy, określając punkt środkowy i poziom powiększenia lub można ustawić granice aparatu mapy, określając punkty środkowe i środkowe.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustawianie aparatu

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Utwórz mapę `CameraOptions` </a>przez Azure Location based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) jest tworzony przez `new atlas.Map()` , a ustawienia Wyśrodkuj i powiększania są ustawione. Właściwości mapy, takie jak centrum i poziom powiększenia, są częścią [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustawianie granic kamery

<iframe height='500' scrolling='no' title='Tworzenie mapy za pomocą CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Utwórz mapę `CameraBoundsOptions` </a>przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) jest konstruowany przez `new atlas.Map()`. Właściwości mapy, takie `CameraBoundsOptions` jak można zdefiniować za pomocą funkcji setcamera klasy map. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) Właściwości Bound i uzupełnienie są ustawiane przy `setCamera`użyciu.

### <a name="animate-map-view"></a>Animuj widok mapy

<iframe height='500' scrolling='no' title='Animuj widok mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Obejrzyj <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Widok mapy animacji</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie, pierwszy blok kodu tworzy [obiekt mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Map()`. Właściwości mapy, takie jak centrum i poziom powiększenia, są częścią [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions`można zdefiniować w konstruktorze mapy lub za pomocą [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) funkcji setcamera klasy map. [Styl mapy](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) jest ustawiony na `road`.

Drugi blok kodu tworzy funkcję mapy animacji, która Animuj zmiany w widoku mapy przez definiowanie [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) przez funkcję setcamera [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Funkcja jest wyzwalana przez przycisk "Animuj map", aby wygenerować losowy poziom powiększenia przy każdym kliknięciu.

## <a name="try-out-the-code"></a>Wypróbuj kod

Zapoznaj się z przykładowym kodem powyżej. Kod JavaScript można edytować na **karcie js** po lewej stronie i zobaczyć zmiany w widoku mapy na **karcie wynik** po prawej stronie. Możesz również kliknąć przycisk **Edytuj na CodePen** i edytować kod w CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Dodaj kontrolki mapy](map-add-controls.md)
