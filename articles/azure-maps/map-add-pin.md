---
title: Dodawanie numeru pin przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać numeru pin do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 35b22e28a6a339af6f6f6e8db0655f2ae6de0118
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122178"
---
# <a name="add-pins-to-the-map"></a>Dodawanie numerów PIN do mapy

W tym artykule przedstawiono sposób dodawania numeru pin do mapy.  

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Dodawanie numeru pin do mapy' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Dodawanie numeru pin do mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu pin zostanie utworzony i dodany do mapy. Numer pin jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) z [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest) jako właściwość jego funkcji. Użyj `new atlas.data.Feature(new atlas.data.Point())` Utwórz numer pin i określeniu jego właściwości. Warstwa numer pin jest tablicą numerów PIN. Użyj [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) funkcji klasy mapy, aby dodać warstwę numeru pin do mapy oraz zdefiniować właściwości warstwy numeru pin. Zobacz właściwości warstwy numeru pin w [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu dodać do map:

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtu](./map-add-shape.md)