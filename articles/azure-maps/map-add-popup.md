---
title: Dodawanie menu podręcznego za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać okno podręczne do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568118"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie menu podręcznego do mapy

W tym artykule pokazano, jak dodać okno podręczne z punktem na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Dodawanie konta pop przy użyciu usługi Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>jest dodanie punktu pop przy użyciu usługi Azure Maps</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje. Tworzy również HTML zawartości mają być wyświetlane w menu podręcznego.

Tworzy drugi blok kodu, źródła danych obiektu przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Punkt znajduje się [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klasy. Obiekt point z właściwościami nazwę i opis jest następnie tworzony i dodawany do źródła danych.

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie.  Warstwa symboli jest tworzony w trzecim bloku kodu. Źródło danych jest dodawane do warstwy "symbol", która następnie zostanie dodany do mapy.

Czwarty bloku kodu tworzy [obiekt menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Popup()`. Menu podręczne właściwości, takie jak pozycja i pixelOffset są częścią [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions można zdefiniować w Konstruktorze podręczny lub za pośrednictwem [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkcji klasy okna podręcznego. A `mouseover` odbiornik zdarzeń dla warstwy symbol zostanie utworzony.

Ostatni blok kodu tworzy funkcję, która jest wyzwalana przez `mouseover` odbiornik zdarzeń. Ona ustawia zawartość i właściwości wyskakujące okienko i dodaje obiekt menu podręczne do mapy.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły doskonałe przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Dodawanie kształtu](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie niestandardowego kodu HTML](./map-add-custom-html.md)
