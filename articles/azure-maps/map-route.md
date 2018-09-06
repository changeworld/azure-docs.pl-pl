---
title: Wyświetlanie instrukcji dotyczących trasy z usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlić kierunkach między dwiema lokalizacjami na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781500"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B 

W tym artykule pokazano, jak utworzyć żądanie trasy i wyświetlania trasy na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci blok kodu, inicjuje [linii warstwy ciągu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) na mapie.

Czwarty bloku kodu tworzy i dodaje pinezki na mapie, aby reprezentować początkowy i punkt końcowy trasy. Możesz zobaczyć [Dodawanie numeru pin na mapie](map-add-pin.md) instrukcje.

Kolejny blok kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić pole mapy oparte na początkowy i punkt końcowy trasy.

Szósty blok kodu tworzy zapytanie trasy.

Ostatni blok kodu zapytania usługi routingu usługi Azure Maps za pośrednictwem [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metodę, aby uzyskać trasy między punkt początkowy, jak i docelowym. Odpowiedź następnie jest analizowany w formacie GeoJSON, używając [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metody. Dodaje te wiersze na mapę do renderowania trasy. Możesz zobaczyć [Dodaj wiersz na mapie](./map-add-shape.md#addALine) Aby uzyskać więcej informacji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Warstwa ciągu linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Wyświetlanie ruchu na mapie](./map-show-traffic.md)
* [Interakcja z mapą — zdarzenia myszy](./map-events.md)
