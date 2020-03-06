---
title: Nawiązywanie połączenia z usługą funkcji sieci Web (WFS) | Mapy Microsoft Azure
description: Dowiedz się, jak nawiązać połączenie z usługą WFS, a następnie wykonać zapytanie dotyczące usługi WFS przy użyciu zestawu SDK sieci Web Azure Maps i modułu operacji we/wy.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402758"
---
# <a name="connect-to-a-wfs-service"></a>Nawiązywanie połączenia z usługą WFS

Usługa funkcji sieci Web (WFS) to usługa sieci Web służąca do wykonywania zapytań dotyczących danych przestrzennych, które mają ustandaryzowany interfejs API, który został zdefiniowany przez Open Geospatial Consortium (OGC). Klasa `WfsClient` w module we/wy przestrzennym umożliwia deweloperom łączenie się z usługą WFS i wykonywanie zapytań dotyczących danych z usługi.

Następujące funkcje są obsługiwane przez klasę `WfsClient`:

- Obsługiwane wersje: `1.0.0`, `1.1.0`i `2.0.0`
- Obsługiwane operatory filtrów: porównania binarne, logiki, matematyczne, wartości i `bbox`.
- Żądania są nawiązywane tylko przy użyciu `HTTP GET`.
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities | Generuje dokument metadanych z prawidłowymi operacjami WFS i parametrami |
    | Getfeature | Zwraca wybór funkcji ze źródła danych |
    | DescribeFeatureType | Zwraca obsługiwane typy funkcji |

## <a name="using-the-wfs-client"></a>Korzystanie z klienta WFS

Klasa `atlas.io.ogc.WfsClient` w module we/wy przestrzennym ułatwia wykonywanie zapytań do usługi WFS i konwertowanie odpowiedzi na obiekty GEOJSON. Tego obiektu GEOJSON można następnie użyć do innych celów mapowania.

Poniższy kod wysyła zapytanie do usługi WFS i renderuje zwrócone funkcje na mapie.

<br/>

<iframe height='700' scrolling='no' title='Prosty przykład WFS' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>prosty przykład WFS</a> piórem, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="supported-filters"></a>Obsługiwane filtry

Specyfikacja standardu WFS wykorzystuje filtry OGC. Poniższe filtry są obsługiwane przez klienta WFS, przy założeniu, że wywoływana usługa również obsługuje te filtry. Ciągi filtru niestandardowego mogą być przesyłane do klasy `CustomFilter`.

**Operatory logiczne**

- `And`
- `Or`
- `Not`

**Operatory wartości**

- `GmlObjectId`
- `ResourceId`

**Operatory matematyczne**

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

Poniższy kod ilustruje użycie różnych filtrów z klientem WFS.

<br/>

<iframe height='500' scrolling='no' title= 'Przykłady filtrów WFS' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>przykłady filtru WFS</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="wfs-service-explorer"></a>Eksplorator usługi WFS

Poniższy kod używa klienta WFS do eksplorowania usług WFS Services. Wybierz warstwę typów właściwości w ramach usługi i zobacz skojarzoną legendę.

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'Eksplorator usługi WFS' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS usługi</a> pióra, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Możesz również użyć usługi proxy do załadowania zasobów, które są hostowane w domenach, które nie są włączone przy użyciu mechanizmu CORs. Najpierw należy zdefiniować zmienną do przechowywania adresu URL usługi serwera proxy i ustawić opcję `proxyService` dla klienta WFS. Aby renderować opcję usługi proxy dla użytkownika, Dodaj dane wejściowe użytkownika do interfejsu użytkownika. Załaduj adres URL usługi po kliknięciu danych wejściowych. Poniższe fragmenty kodu pokazują, jak korzystać z usługi proxy.

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

Poniższy fragment kodu HTML odnosi się do powyższego kodu JavaScript:

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
