---
title: Pokazyj wskazówki dojazdu na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać wskazówki dotyczące wskazówek dojazdu między dwiema lokalizacjami na mapie przy użyciu zestawu SDK w sieci Web map platformy Microsoft Azure.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371406"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B

W tym artykule pokazano, jak złożyć żądanie trasy i wyświetlić trasę na mapie.

Istnieją dwa sposoby, aby to zrobić. Pierwszym sposobem jest zapytanie [interfejsu API marszruty usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) za pośrednictwem modułu usługi. Drugim sposobem jest użycie [interfejsu API pobierania](https://fetch.spec.whatwg.org/) do żądania wyszukiwania w [interfejsie API marszruty usługi Azure Maps.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Poniżej omówiono oba sposoby.

## <a name="query-the-route-via-service-module"></a>Zapytanie o trasę za pomocą modułu serwisowego

<iframe height='500' scrolling='no' title='Pokaż wskazówki dojazdu od A do B na mapie (moduł serwisowy)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz sposób <a href='https://codepen.io/azuremaps/pen/RBZbep/'>pokazywanych piórem wskazówek dojazdu od A do B na mapie (moduł usługi)</a> przez usługę Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do usługi Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) wystąpienie. Reprezentuje `routeURL` adres URL do operacji [marszruty](https://docs.microsoft.com/rest/api/maps/route) usługi Azure Maps.

Trzeci blok kodu tworzy i dodaje [Obiekt DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Czwarty blok kodu tworzy obiekty [punktów](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) początkowych i końcowych i dodaje je do obiektu dataSource.

Linia jest [funkcją](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) dla linestringu. [Warstwa linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje obiekty liniowe zawinięte w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako linie na mapie. Czwarty blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii w [linestringlayeroptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstów lub ikon do renderowania danych opartych na punktach zawiniętych w [źródło danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Teksty lub ikony są renderowane jako symbole na mapie. Piąty blok kodu tworzy i dodaje warstwę symboli do mapy.

Szósty blok kodu wysyła zapytania do usługi routingu usługi Azure Maps, która jest częścią [modułu usługi](how-to-use-services-module.md). Metoda [calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) routeurl jest używana do uzyskania trasy między punktami początkowymi i końcowymi. Kolekcja funkcji GeoJSON z odpowiedzi jest następnie `geojson.getFeatures()` wyodrębniana przy użyciu metody i jest dodawana do źródła danych. Następnie renderuje odpowiedź jako trasę na mapie. Aby uzyskać więcej informacji na temat dodawania wiersza do mapy, zobacz [dodawanie linii na mapie](map-add-line-layer.md).

Ostatni blok kodu ustawia granice mapy przy użyciu map [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Kwerenda trasy, źródło danych, symbol, warstwy linii i granice kamery są tworzone wewnątrz [detektora zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Ta struktura kodu zapewnia, że wyniki są wyświetlane tylko po w pełni załadowana mapa.

## <a name="query-the-route-via-fetch-api"></a>Zapytanie o trasę za pośrednictwem interfejsu API pobierania

<iframe height='500' scrolling='no' title='Pokazywki z A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz sposób <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>pokazywalkowego pióra ze strony Od A do B na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu tworzy i dodaje [Obiekt DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) do mapy.

Trzeci blok kodu tworzy punkty początkowe i docelowe dla trasy. Następnie dodaje je do źródła danych. Możesz zobaczyć [dodać pinezkę na mapie,](map-add-pin.md) aby uzyskać instrukcje dotyczące korzystania z [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest).

[Warstwa linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje obiekty liniowe zawinięte w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako linie na mapie. Czwarty blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii w [temacie LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest).

[Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punktach zawiniętych w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboli na mapie. Piąty blok kodu tworzy i dodaje warstwę symboli do mapy. Zobacz właściwości warstwy symbolu w [polu SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest).

Następny blok kodu `SouthWest` `NorthEast` tworzy i punkty od punktu początkowego i docelowego i ustawia granice mapy przy użyciu map [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) właściwości.

Ostatni blok kodu używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/) do żądania wyszukiwania w [interfejsie API marszruty usługi Azure Maps.](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) Odpowiedź jest następnie analizowana. Jeśli odpowiedź powiodła się, informacje o szerokości i długości geograficznej są używane do tworzenia tablicy linii przez połączenie tych punktów. Dane linii są następnie dodawane do źródła danych w celu renderowania trasy na mapie. Możesz zobaczyć [dodaj linię na mapie, aby](map-add-line-layer.md) uzyskać instrukcje.

Kwerenda trasy, źródło danych, symbol, warstwy linii i granice kamery są tworzone wewnątrz [detektora zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Ponownie chcemy upewnić się, że wyniki są wyświetlane po załadowaniu mapy w pełni.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące korzystania z usługi routingu](how-to-use-best-practices-for-search.md)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać pełne przykłady kodu:

> [!div class="nextstepaction"]
> [Pokazywal ruchu na mapie](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Interakcja z mapą - zdarzenia myszy](./map-events.md)
