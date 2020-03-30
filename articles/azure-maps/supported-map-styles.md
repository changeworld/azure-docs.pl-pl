---
title: Obsługiwane style map | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o różnych stylach renderowania map obsługiwanych przez usługi Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334035"
---
# <a name="azure-maps-supported-map-styles"></a>Obsługiwane style map usługi Azure Maps
Usługa Azure Maps obsługuje kilka różnych wbudowanych stylów map, jak opisano poniżej.

## <a name="road"></a>Road
Mapa **drogowa** to standardowa mapa, która wyświetla drogi, naturalne i sztuczne obiekty wraz z etykietami dla tych obiektów.

![styl mapy drogowej](./media/supported-map-styles/road.png)

**Odpowiednie interfejsy API:**
* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek Mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida

## <a name="blank-and-blank_accessible"></a>puste i blank_accessible

**Style mapy puste** i **blank_accessible** zapewniają pustą kanwę, na której można wizualizować dane. Styl **blank_accessible** będzie nadal dostarczał aktualizacji czytnika ekranu ze szczegółami lokalizacji mapy, nawet jeśli mapa bazowa nie jest wyświetlana.

> [!Note]
> W web SDK można zmienić kolor tła mapy, `background-color` ustawiając styl CSS elementu DIV mapy.

**Odpowiednie interfejsy API:**
* Sterowanie mapą zestawu SDK w sieci Web

## <a name="satellite"></a>satellite 
Styl **satelity** to połączenie zdjęć satelitarnych i lotniczych.

![styl mapy kafelków satelitarnych](./media/supported-map-styles/satellite.png)

**Odpowiednie interfejsy API:**
* [Płytka satelitarna](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida

## <a name="satellite_road_labels"></a>satellite_road_labels
Ten styl mapy jest hybrydą dróg i etykiet nałożonych na zdjęcia satelitarne i lotnicze.

![satellite_road_labels styl mapy](./media/supported-map-styles/satellite-road-labels.png)

**Odpowiednie interfejsy API:**
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida

## <a name="grayscale_dark"></a>grayscale_dark
**szarość ciemny** jest ciemną wersją stylu mapy drogowej.

![gray_scale styl mapy](./media/supported-map-styles/grayscale-dark.png)

**Odpowiednie interfejsy API:**
* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek Mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Sterowanie mapą zestawu SDK w sieci Web 
* Kontrola mapy Androida


## <a name="grayscale_light"></a>grayscale_light
**światło w skali szarości** jest lekką wersją stylu mapy drogowej.

![styl mapy światła w skali szarości](./media/supported-map-styles/grayscale-light.png)

**Odpowiednie interfejsy API:**
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida


## <a name="night"></a>noc
**noc** to ciemna wersja stylu mapy drogowej z kolorowymi drogami i symbolami.

![styl mapy nocnej](./media/supported-map-styles/night.png)

**Odpowiednie interfejsy API:**
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida

## <a name="road_shaded_relief"></a>road_shaded_relief
**ulga w zacieniona droga** to główny styl usługi Azure Maps uzupełniony o kontury Ziemi.

![styl mapy cieniowane relief](./media/supported-map-styles/shaded-relief.png)

**Odpowiednie interfejsy API:**
* [Kafelek Mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Sterowanie mapą zestawu SDK w sieci Web
* Kontrola mapy Androida

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** jest ciemnym stylem mapy o wyższym kontraście niż inne style.

![styl ciemnej mapy o wysokim kontraście](./media/supported-map-styles/high-contrast-dark.png)

**Odpowiednie interfejsy API:**
* Sterowanie mapą zestawu SDK w sieci Web

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak ustawić styl mapy w usłudze Azure Maps:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
