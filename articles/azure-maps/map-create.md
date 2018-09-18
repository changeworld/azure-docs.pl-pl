---
title: Tworzenie mapy za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Tworzenie Javascript map
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730013"
---
# <a name="create-a-map"></a>Tworzenie mapy

W tym artykule pokazano, jak utworzyć mapę.  

## <a name="understand-the-code"></a>Zrozumienie kodu

Istnieją dwa sposoby, możesz utworzyć mapę. Możesz ustawić aparatu mapy, określając punkt środkowy i poziom powiększenia. Ustaw granice aparatu mapy przez określenie regionów południowo-zachodnich, które są blokujących punktu, północno-wschodnie blokujących punktu.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Ustaw aparat fotograficzny

<iframe height='310' scrolling='no' title='Utwórz mapę za pośrednictwem CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Tworzenie mapy za pomocą `CameraOptions` </a>przez usług Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie [obiektu mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) zostały utworzone za pomocą `new atlas.Map()`. Właściwości mapy, takie jak poziom Wyśrodkowanie i powiększenie są częścią [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest). `CameraOptions` można zdefiniować w Konstruktorze mapy lub za pośrednictwem [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) funkcji klasy mapy.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Ustaw granice kamery

<iframe height='310' scrolling='no' title='Utwórz mapę za pośrednictwem CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Tworzenie mapy za pomocą `CameraBoundsOptions` </a>przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie [obiektu mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) jest tworzony za pomocą `new atlas.Map()`. Właściwości mapy, takie jak pole ograniczenia są częścią [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest). `CameraBoundsOptions` można zdefiniować za pośrednictwem [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy.

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
