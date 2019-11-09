---
title: Obsługiwane style mapy w Azure Maps | Microsoft Docs
description: Style mapy obsługiwane przez Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cd16ee87b213c2bfa4c541c4d3c453a4499855d0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887732"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps obsługiwane style mapy
Azure Maps obsługuje kilka różnych wbudowanych stylów mapy, zgodnie z poniższym opisem.

## <a name="road"></a>zwiększa
Mapa **drogowa** to standardowa mapa, która wyświetla drogi, naturalne i sztuczne funkcje wraz z etykietami dla tych funkcji.

![zwiększa](./media/supported-map-styles/road.png)

**Odpowiednie interfejsy API:**
* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android

## <a name="blank-and-blank_accessible"></a>puste i blank_accessible

Style " **puste** " i " **blank_accessible** map" zawierają pustą kanwę, na której można wizualizować dane. Styl **blank_accessible** będzie w dalszym ciągu udostępniać aktualizacje czytnika ekranu informacjami o lokalizacji, w której znajduje się mapa, nawet jeśli mapa podstawowa nie jest wyświetlana.

> [!Note]
> W zestawie SDK sieci Web można zmienić kolor tła mapy, ustawiając styl CSS `background-color` stylu elementu DIV mapy.

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web

## <a name="satellite"></a>urządzenia 
Styl **satelity** to kombinacja zdjęć satelitarnych i antenowych.

![urządzenia](./media/supported-map-styles/satellite.png)

**Odpowiednie interfejsy API:**
* [Kafelek satelitarny](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Ten styl mapy jest hybrydą dróg i etykiet, które są nakładane na Zdjęcia satelitarne i anteny.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android

## <a name="grayscale_dark"></a>grayscale_dark
**ciemna Skala szarości** jest ciemną wersją stylu mapy drogowej.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web 
* Kontrolka mapy systemu Android


## <a name="grayscale_light"></a>grayscale_light
**sygnalizator skali szarości** to jasna wersja stylu mapy drogowej.

![oświetlenie w skali szarości](./media/supported-map-styles/grayscale_light.png)

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android


## <a name="night"></a>godzinach
**nocna** to ciemna wersja stylu mapy drogowej z kolorami dróg i symboli.

![godzinach](./media/supported-map-styles/night.png)

**Odpowiednie interfejsy API:**
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android

## <a name="road_shaded_relief"></a>road_shaded_relief
nastąpi **odcień drogi** jest Azure Maps głównym stylem, który został ukończony z obprezentacją ziemi.

![zacieniowane zwolnienie](./media/supported-map-styles/shaded-relief.png)

**Odpowiednie interfejsy API:**
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolka mapy zestawu SDK sieci Web
* Kontrolka mapy systemu Android


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat sposobu ustawiania stylu mapy w Azure Maps:

> [!div class="nextstepaction"]
> [Wybieranie stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)