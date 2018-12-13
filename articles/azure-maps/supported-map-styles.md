---
title: Obsługiwane style mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Obsługiwane przez usługi Azure Maps style mapy
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2f426ab85d9ab15dd080a487337b8d48358ed040
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888464"
---
# <a name="azure-maps-supported-map-styles"></a>Usługi Azure Maps obsługiwane style mapy
Usługi Azure maps obsługuje kilka różnych mapy wbudowane style, zgodnie z poniższym opisem.

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

![nocne](./media/supported-map-styles/night.PNG)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript 