---
title: Dodaj numeru pin przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak dodać numeru pin do mapy kodu Javascript
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: dc9649e94f5d1757e5ec65098ccc7e4ffa927bf0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599861"
---
# <a name="add-pins-to-the-map"></a>Dodaj numery PIN do mapy

W tym artykule przedstawiono sposób dodawania do mapy numer pin.  

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Dodawanie do mapy numer pin' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Dodawanie numeru pin do mapy</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu pin jest tworzony i dodawany do mapy. Numer pin jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) z [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) jako jego właściwości funkcji. Użyj `new atlas.data.Feature(new atlas.data.Point())` utworzyć numeru pin i określeniu jego właściwości. Warstwa numer pin jest tablicą numerów PIN. Użyj [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) funkcja klasy mapy, aby dodać warstwę numeru pin do mapy oraz zdefiniować właściwości warstwy numeru pin. Zobacz właściwości warstwy numeru pin w [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
