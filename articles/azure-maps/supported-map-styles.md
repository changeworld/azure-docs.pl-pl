---
title: Obsługiwane style mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Obsługiwane przez usługi Azure Maps style mapy
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 923b9fef75fa610ee817a86ce0b4d6b322153f01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113435"
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

## <a name="satellite"></a>Satelity 
**Satelitarnej** stylu jest kombinacją satelitarne i zdjęcia lotnicze.

![Satelity](./media/supported-map-styles/satellite.png)

**Odpowiednie interfejsy API:**
* [Kafelek satelity](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Kontrolki mapy Javascript

## <a name="satelliteroadlabels"></a>satellite_road_labels
Ten styl mapy jest hybrydą, dróg i nałożony na podstawie satelitarne i zdjęcia lotnicze etykiety.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript

## <a name="grayscaledark"></a>grayscale_dark
**Ciemny skali szarości** ciemny wersja style mapy drogowej.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript 

## <a name="night"></a>nocne
**nocne** ciemny wersja style mapy drogowej z kolorowym drogach i symbole.

![nocne](./media/supported-map-styles/night.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript

## <a name="roadshadedrelief"></a>road_shaded_relief
**drogowa cieniowanie zwolnienia** jest styl głównego usługi Azure Maps zostało ukończone z konturów ziemi.

![zacieniony zwolnienia](./media/supported-map-styles/shaded-relief.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript
* [Kafelek mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
