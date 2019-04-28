---
title: Dodawanie kontrolki mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać powiększenia, kontrola pomysłu, element sterujący i selektor stylu do mapy w usługi Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108723"
---
# <a name="add-map-controls-to-azure-maps"></a>Dodawanie kontrolki mapy do usługi Azure Maps

W tym artykule przedstawiono sposób dodawania kontrolki mapy do mapy. Zostanie również dowiesz się, jak utworzyć mapę przy użyciu wszystkich formantów i [selektor stylu](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

<iframe height='500' scrolling='no' title='Dodawanie kontrolki powiększania' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Dodawanie kontrolki powiększenia</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Kontrolka powiększenia dodaje możliwość powiększanie i pomniejszanie mapy. Drugi blok kodu tworzy obiekt Powiększenie, przy użyciu atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody. Kontrolka powiększenia mieści się w planie **odbiornik zdarzeń** zapewnienie ładuje po mapy ładuje pełni.

## <a name="add-pitch-control"></a>Dodaj kontrolkę pomysłu

<iframe height='500' scrolling='no' title='Dodawanie kontrolki pomysłu' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Dodawanie kontrolki pomysłu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Wysokość formantu dodaje możliwości, aby zmienić wysokość mapy. Drugi blok kodu tworzy obiekt formantu pomysłu, używając atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody. Formant skoku znajduje się w mapie **odbiornik zdarzeń** zapewnienie ładuje po mapy ładuje pełni.

## <a name="add-compass-control"></a>Dodaj kontrolkę kompasu

<iframe height='500' scrolling='no' title='Dodawanie kontrolki Obróć' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>dodając element sterujący</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi blok kodu tworzy obiekt Compass kontrolki, używając atlas [kontroli Compass](/javascript/api/azure-maps-control/atlas.control.compasscontrol). Dodaje także kompasu formant do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody. Kontrolka Compass mieści się w planie **odbiornik zdarzeń** zapewnienie ładuje po mapy ładuje pełni.

## <a name="a-map-with-all-controls"></a>Mapa przy użyciu wszystkich formantów

<iframe height='500' scrolling='no' title='Mapy z wszystkimi kontrolkami' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>mapy z wszystkimi kontrolkami</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi blok kodu tworzy obiekt Compass kontrolki, używając atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody.

Trzeci bloku kodu tworzy obiekt Powiększenie, przy użyciu atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody.

Czwarty blok kodu tworzy obiekt formantu pomysłu, używając atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody.

Ostatni blok kodu tworzy obiekt selektor stylu przy użyciu atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) i dodaje je do mapy za pomocą mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody. Wszystkie obiekty kontrolki są dodawane w mapie **odbiornik zdarzeń** aby upewnić się, są one ładowane po mapy ładuje pełni.

Kolejność obiektów kontroli w skrypcie decyduje o kolejności, w jakiej są wyświetlane na mapie. Aby zmienić kolejność formantów na mapie, możesz zmienić ich kolejność w skrypcie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać pełny kod:

> [!div class="nextstepaction"]
> [Dodawanie numeru pin](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](./map-add-popup.md)
