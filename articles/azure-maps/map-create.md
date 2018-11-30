---
title: Tworzenie mapy za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Tworzenie Javascript map
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97b94cf54454a83510c5be2cf0d71281dbf5b004
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424243"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule przedstawiono sposoby tworzenia mapy i animować mapy.  

## <a name="understand-the-code"></a>Zrozumienie kodu

Istnieją dwa sposoby, możesz utworzyć mapę. Aparat fotograficzny, mapy można ustawić przez określenie punktu centralnego i poziom powiększenia lub można ustawić granice aparatu mapy, określając zachodzie i północno-wschodnie blokujących punktów.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustaw aparat fotograficzny

<iframe height='500' scrolling='no' title='Utwórz mapę za pośrednictwem CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Tworzenie mapy za pomocą `CameraOptions` </a>przez usług Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie [obiektu mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) zostały utworzone za pomocą `new atlas.Map()` i wyśrodkowanie i powiększenie są ustawione. Właściwości mapy, takie jak poziom Wyśrodkowanie i powiększenie są częścią [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustaw granice kamery

<iframe height='500' scrolling='no' title='Utwórz mapę za pośrednictwem CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Tworzenie mapy za pomocą `CameraBoundsOptions` </a>przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie [obiektu mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) jest tworzony za pomocą `new atlas.Map()`. Właściwości mapy, takich jak `CameraBoundsOptions` mogą być definiowane za pomocą [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkcji klasy mapy. Granice i wypełnienie właściwości są ustawiane przy użyciu `setCamera`.

### <a name="animate-map-view"></a>Animowanie widoku mapy

<iframe height='500' scrolling='no' title='Animowanie widoku mapy' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animować widoku mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy [obiektu mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Map()`. Właściwości mapy, takie jak poziom Wyśrodkowanie i powiększenie są częścią [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` można zdefiniować w Konstruktorze mapy lub za pośrednictwem [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkcji klasy mapy. [Mapy styl](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) ustawiono `road`.

Drugi blok kodu tworzy funkcji mapy animowanie animuje zmiany w widoku mapy, definiując [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) za pośrednictwem [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkcji. Funkcja jest wyzwalana po kliknięciu przycisku "Animować mapy" do generowania losowego powiększenia na każde kliknięcie.

## <a name="try-out-the-code"></a>Wypróbuj kod

Spójrz na powyższym przykładowym kodzie. Można edytować kod JavaScript na **kartę JS** po lewej i zobacz, w widoku mapy zmieni się na **karty wyników** po prawej stronie. Możesz również kliknąć **edytować na funkcji codepen można** znajdujący się i edytowanie kodu w funkcji codepen można.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz przykłady kodu, aby dodać funkcje do aplikacji:

> [!div class="nextstepaction"]
> [Wybierz style mapy](choose-map-style.md)

> [!div class="nextstepaction"]
> [Dodawanie kontrolki mapy](map-add-controls.md)
