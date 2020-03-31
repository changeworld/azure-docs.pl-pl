---
title: Łączenie się z usługą WFS (Web Feature Service) | Mapy platformy Microsoft Azure
description: Dowiedz się, jak połączyć się z usługą WFS, a następnie zbadać usługę WFS przy użyciu sDK sieci Web usługi Azure Maps i modułu Spatial WeO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334200"
---
# <a name="connect-to-a-wfs-service"></a>Łączenie się z usługą WFS

Web Feature Service (WFS) to usługa sieci web do wykonywania zapytań o dane przestrzenne, która ma znormalizowany interfejs API, który jest zdefiniowany przez Open Geospatial Consortium (OGC). Klasa `WfsClient` w modułze we/wy przestrzennej umożliwia deweloperom łączenie się z usługą WFS i wykonywanie zapytań o dane z usługi.

Następujące funkcje są obsługiwane `WfsClient` przez klasę:

- Obsługiwane wersje: `1.0.0` `1.1.0`, , i`2.0.0`
- Obsługiwane operatory filtrów: porównania binarne, `bbox`logika, matematyka, wartość i .
- Wnioski są `HTTP GET` składane tylko przy użyciu.
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities (Zdolności do uzyskania) | Generuje dokument metadanych z prawidłowymi operacjami i parametrami WFS |
    | GetFeature (Niefościernianie) | Zwraca wybór funkcji ze źródła danych |
    | Opis Typ podłości | Zwraca obsługiwane typy operacji |

## <a name="using-the-wfs-client"></a>Korzystanie z klienta WFS

Klasa `atlas.io.ogc.WfsClient` w modułze we/wy przestrzennej ułatwia wykonywanie zapytań w usłudze WFS i konwertowanie odpowiedzi na obiekty GeoJSON. Ten obiekt GeoJSON może być następnie używany do innych celów mapowania.

Poniższy kod wysyła zapytanie do usługi WFS i renderuje zwrócone obiekty na mapie.

<br/>

<iframe height='700' scrolling='no' title='Prosty przykład WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz przykład Pióra <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Prosty WFS</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Obsługiwane filtry

Specyfikacja standardu WFS wykorzystuje filtry OGC. Poniższe filtry są obsługiwane przez klienta WFS, przy założeniu, że usługa wywoływana obsługuje również te filtry. Niestandardowe ciągi filtrów `CustomFilter` mogą być przekazywane do klasy.

**Operatory logiczne**

- `And`
- `Or`
- `Not`

**Operatory wartości**

- `GmlObjectId`
- `ResourceId`

**Operatorzy matematyczni**

- `Add`
- `Sub`
- `Mul`
- `Div`

**Operatory porównania**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

Poniższy kod pokazuje użycie różnych filtrów z klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Przykłady filtrów WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>przykłady filtrów Pióra WFS</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Eksplorator usług WFS

Poniższy kod używa klienta WFS do eksplorowania usług WFS. Wybierz warstwę typu właściwości w usłudze i zobacz skojarzoną legendę.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Eksplorator usług WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>Eksploratora usług WFS</a> pióra przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Aby uzyskać dostęp do usług WFS hostowanych w punktach końcowych z włączoną usługą CORS, usługa serwera proxy z włączoną usługą CORS może zostać przekazana do `proxyService` opcji klienta WFS, jak pokazano poniżej. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [WfsClient (właśc.](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Wykorzystanie podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
