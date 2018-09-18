---
title: Wyświetlanie instrukcji dotyczących trasy z usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlić kierunkach między dwiema lokalizacjami na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: ed522779f5a86e38ee12a246cea9ac85d0379f9e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729064"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B

W tym artykule pokazano, jak utworzyć żądanie trasy i wyświetlania trasy na mapie.

Istnieją dwa sposoby, aby to zrobić. Pierwszy sposób to zapytanie [interfejsu API usługi Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) przez moduł usługi. Druga metoda jest zapewnienie [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API. Obie metody zostały podane poniżej.

## <a name="query-the-route-via-service-module"></a>Zapytanie tras za pomocą modułu usług

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/RBZbep/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci blok kodu, inicjuje [linii warstwy ciągu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) na mapie.

Czwarty bloku kodu tworzy i dodaje pinezki na mapie, aby reprezentować początkowy i punkt końcowy trasy. Możesz zobaczyć [Dodawanie numeru pin na mapie](map-add-pin.md) instrukcje dotyczące obsługi [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Kolejny blok kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić pole mapy oparte na początkowy i punkt końcowy trasy.

Szósty blok kodu tworzy zapytanie trasy.

Ostatni blok kodu zapytania usługi routingu usługi Azure Maps za pośrednictwem [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) metodę, aby uzyskać trasy między punkt początkowy, jak i docelowym. Odpowiedź następnie jest analizowany w formacie GeoJSON, używając [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) metody. Dodaje te wiersze na mapę do renderowania trasy. Zobacz artykuł poświęcony [dodawaniu wiersza na mapie](./map-add-shape.md#addALine), aby uzyskać więcej informacji.

## <a name="query-the-route-via-xmlhttprequest"></a>Zapytanie tras za pomocą XMLHttpRequest

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu tworzy i dodaje pinezki na mapie, aby reprezentować początkowy i punkt końcowy trasy. Możesz zobaczyć [Dodawanie numeru pin na mapie](map-add-pin.md) instrukcje dotyczące obsługi [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Trzeci blok kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić pole mapy oparte na początkowy i punkt końcowy trasy.

Czwarty bloku kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API usługi Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Ostatni blok kodu analizuje przychodzącą odpowiedź. Pomyślnej odpowiedzi zbiera szerokości i długości geograficznej informacje dotyczące każdego punkt nawigacyjny. Tworzy tablicę wierszy, łącząc każdy punkt nawigacyjny do jego kolejnych punkt nawigacyjny. Dodaje te wiersze na mapę do renderowania trasy. Możesz zobaczyć [Dodaj wiersz na mapie](./map-add-shape.md#addALine) instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu na mapie](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakcja z mapą — zdarzenia myszy](./map-events.md)
