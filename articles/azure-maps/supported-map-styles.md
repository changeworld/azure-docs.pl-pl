---
title: Obsługiwane style mapy w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Obsługiwane przez usługi Azure Maps style mapy
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8f0910e9040c962bae30a33b91a93e71e692dfdb
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713446"
---
# <a name="azure-maps-supported-map-styles"></a>Usługi Azure Maps obsługiwane style mapy
Usługi Azure maps obsługuje cztery style różnych wbudowanych mapy. Poniżej przedstawiono style wraz z opisami.

## <a name="road"></a>Drogowa
A **drogowej** mapa jest standardowa mapę, która wyświetla drogach naturalnych i sztucznych funkcji wraz z etykiety dla tych funkcji.

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

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
Ten styl mapy jest hybrydą, dróg i nałożony na podstawie satelitarne i zdjęcia lotnicze etykiety.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript

## <a name="grayscaledark"></a>Grayscale_Dark
**Ciemny skali szarości** ciemny wersja style mapy drogowej.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Odpowiednie interfejsy API:**
* Kontrolki mapy Javascript 