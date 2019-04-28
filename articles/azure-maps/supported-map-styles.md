---
title: Obsługiwane style mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Obsługiwane przez usługi Azure Maps style mapy
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 76ab49c7f28260249483bf3bc4387e8cbaca13b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767216"
---
# <a name="azure-maps-supported-map-styles"></a>Usługi Azure Maps obsługiwane style mapy
Usługi Azure Maps obsługuje kilka różnych mapy wbudowane style, zgodnie z poniższym opisem.

## <a name="road"></a>Drogowa
A **drogowej** mapa jest standardowa mapę, która wyświetla drogach naturalnych i sztuczne funkcji wraz z etykiety dla tych funkcji.

![Drogowa](./media/supported-map-styles/road.png)

**Odpowiednie interfejsy API:**
* [Obraz mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolki mapy Javascript
* Kontrolki mapy dla systemu android

## <a name="satellite"></a>Satelity 
**Satelitarnej** stylu jest kombinacją satelitarne i zdjęcia lotnicze.

![Satelity](./media/supported-map-styles/satellite.png)

**Odpowiednie interfejsy API:**
* [Kafelek satelity](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kontrolki mapy Javascript
* Kontrolki mapy dla systemu android

## <a name="satelliteroadlabels"></a>satellite_road_labels
Ten styl mapy jest hybrydą, dróg i nałożony na podstawie satelitarne i zdjęcia lotnicze etykiety.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript
* Kontrolki mapy dla systemu android

## <a name="grayscaledark"></a>grayscale_dark
**Ciemny skali szarości** ciemny wersja style mapy drogowej.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript 
* Kontrolki mapy dla systemu android

## <a name="night"></a>nocne
**nocne** ciemny wersja style mapy drogowej z kolorowym drogach i symbole.

![nocne](./media/supported-map-styles/night.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript
* Kontrolki mapy dla systemu android

## <a name="roadshadedrelief"></a>road_shaded_relief
**drogowa cieniowanie zwolnienia** jest styl głównego usługi Azure Maps zostało ukończone z konturów ziemi.

![zacieniony zwolnienia](./media/supported-map-styles/shaded-relief.png)

**Odpowiednie interfejsy API:**
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Kontrolki mapy Javascript
* Kontrolki mapy dla systemu android
