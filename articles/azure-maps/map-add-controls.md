---
title: Dodawanie formantów mapy w Azure Maps | Microsoft Docs
description: Jak dodać kontrolkę powiększenia, kontrolkę gęstość, obrócić formant i selektor stylu do mapy w Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638770"
---
# <a name="add-map-controls-to-azure-maps"></a>Dodaj kontrolki mapy do Azure Maps

W tym artykule pokazano, jak dodać kontrolki mapy do mapy. Dowiesz się również, jak utworzyć mapę ze wszystkimi kontrolkami i [selektorem stylów](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

<iframe height='500' scrolling='no' title='Dodawanie kontrolki powiększenia' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>dodając kontrolkę powiększenia</a> przez<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy przy użyciu mechanizmu uwierzytelniania anonimowego. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Kontrolka powiększenia dodaje możliwość powiększania i pomniejszania mapy. Drugi blok kodu tworzy obiekt kontrolki powiększenia przy użyciu [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) Atlas i dodaje go do mapy przy użyciu kontrolek mapy [. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę. Kontrolka powiększenia znajduje się w obrębie **odbiornika zdarzeń** mapy, aby upewnić się, że ładuje się po całkowitym załadowaniu mapy.

## <a name="add-pitch-control"></a>Dodaj kontrolkę gęstość

<iframe height='500' scrolling='no' title='Dodawanie kontrolki skoku' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>dodając kontrolkę gęstość</a> do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy przy użyciu mechanizmu uwierzytelniania anonimowego. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Kontrolka gęstość dodaje możliwość zmiany wysokości mapy. Drugi blok kodu tworzy obiekt sterujący skoku przy użyciu [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Atlas i dodaje go do mapy przy użyciu kontrolek mapy [. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę. Kontrolka gęstość znajduje się w **odbiorniku zdarzenia** mapy, aby upewnić się, że ładuje się po całkowitym załadowaniu mapy.

## <a name="add-compass-control"></a>Dodaj kontrolkę kompas

<iframe height='500' scrolling='no' title='Dodawanie kontrolki Obróć' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>dodając kontrolkę Obróć</a> do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy przy użyciu mechanizmu uwierzytelniania anonimowego. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy obiekt formantu kompasu przy użyciu [kontrolki kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol)w Atlasie. Dodaje również formant kompas do mapy przy użyciu [formantów mapy. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę. Kontrolka kompas znajduje się w obrębie **odbiornika zdarzeń** mapy, aby upewnić się, że ładuje się po całkowitym załadowaniu mapy.

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi kontrolkami

<iframe height='500' scrolling='no' title='Mapa ze wszystkimi kontrolkami' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Mapa ze wszystkimi kontrolkami</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy przy użyciu mechanizmu uwierzytelniania anonimowego. Aby uzyskać instrukcje dotyczące sposobu tworzenia mapy, zobacz temat [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy obiekt formantu kompasu przy użyciu [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) Atlas i dodaje go do mapy za pomocą [formantów mapy. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę.

Trzeci blok kodu tworzy obiekt kontrolki powiększenia przy użyciu [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) Atlas i dodaje go do mapy przy użyciu kontrolek mapy [. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę.

Czwarty blok kodu tworzy obiekt sterujący skoku przy użyciu [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) Atlas i dodaje go do mapy przy użyciu kontrolek mapy [. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę.

Ostatni blok kodu tworzy obiekt selektora stylu przy użyciu [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Atlas i dodaje go do mapy za pomocą [formantów mapy. Dodaj](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę. Wszystkie obiekty sterujące są dodawane w **detektorze zdarzeń** mapy, aby upewnić się, że są ładowane po całkowitym załadowaniu mapy.

Kolejność obiektów formantów w skrypcie określa kolejność, w jakiej są wyświetlane na mapie. Aby zmienić kolejność formantów na mapie, można zmienić ich kolejność w skrypcie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Pełny kod można znaleźć w następujących artykułach:

> [!div class="nextstepaction"]
> [Dodaj numer PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](./map-add-popup.md)
