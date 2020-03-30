---
title: Pokazy informacji o współrzędnej na mapie | Mapy platformy Microsoft Azure
description: Dowiedz się, jak wyświetlać informacje o adresie na mapie, gdy użytkownik wybierze współrzędną.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 174bdc496e52a6ac8f2a2d631db92e0f21a819be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371434"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule pokazano, jak wykonać odwrotne wyszukiwanie adresów, które pokazuje adres klikniętyej lokalizacji wyskakujących.

Istnieją dwa sposoby wyszukiwania adresu wstecznego. Jednym ze sposobów jest zapytanie [interfejsu API wyszukiwania odwrotnego adresu usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) za pośrednictwem modułu usługi. Innym sposobem jest użycie [interfejsu API pobierania,](https://fetch.spec.whatwg.org/) aby złożyć żądanie do [interfejsu API wyszukiwania odwrotnego adresu usługi Azure Maps,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) aby znaleźć adres. Poniżej ankietowane są oba sposoby.

## <a name="make-a-reverse-search-request-via-service-module"></a>Złożyć żądanie wyszukiwania wstecznego za pośrednictwem modułu usługi

<iframe height='500' scrolling='no' title='Uzyskaj informacje ze współrzędnych (moduł serwisowy)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Pióro <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Pobierz informacje ze współrzędnych (modułu usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu `TokenCredential` tworzy do uwierzytelniania żądań HTTP do usługi Azure Maps za pomocą tokenu dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) wystąpienie. Reprezentuje `searchURL` adres URL do operacji usługi Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

Trzeci blok kodu aktualizuje styl kursora myszy do wskaźnika i tworzy obiekt [wyskakujący.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Możesz zobaczyć [dodaj wyskakujące okienko na mapie,](./map-add-popup.md) aby uzyskać instrukcje.

Czwarty blok kodu dodaje [detektor zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)kliknięcia myszą . Po wyzwoleniu tworzy kwerendę wyszukiwania ze współrzędnymi klikniętym punktem. Następnie używa [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)metody kwerendy [Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) dla adresu współrzędnych. Kolekcja funkcji GeoJSON jest następnie `geojson.getFeatures()` wyodrębniana przy użyciu metody z odpowiedzi.

Piąty blok kodu konfiguruje zawartość wyskakujących wiadomości HTML, aby wyświetlić adres odpowiedzi dla klikniętym położeniu współrzędnych.

Zmiana kursora, obiektu podręcznego i zdarzenia click są tworzone w [detektorze zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)mapy . Ta struktura kodu zapewnia pełne obciążenie mapy przed pobraniem informacji o współrzędnych.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Składanie żądania wyszukiwania wstecznego za pośrednictwem interfejsu API pobierania

Kliknij na mapę, aby wysłać żądanie odwrócenia geokodu dla tej lokalizacji za pomocą pobierania.

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Pióro Pobierz informacje ze<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>współrzędnych</a> przez usługi Azure Maps ( ) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania, aby użyć tokenu dostępu. Możesz zobaczyć [tworzenie mapy](./map-create.md) dla instrukcji.

Drugi blok kodu aktualizuje styl kursora myszy do wskaźnika. Wystąpienia obiektu [popup.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) Możesz zobaczyć [dodaj wyskakujące okienko na mapie,](./map-add-popup.md) aby uzyskać instrukcje.

Trzeci blok kodu dodaje detektor zdarzeń dla kliknięć myszą. Po kliknięciu myszą używa [interfejsu API pobierania](https://fetch.spec.whatwg.org/) do wykonywania zapytań interfejsu API wyszukiwania [odwrotnego adresu usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) dla klikniętego adresu współrzędnych. Aby uzyskać pomyślną odpowiedź, zbiera adres klikniętych lokalizacji. Definiuje zawartość wyskakujących i położenie przy użyciu funkcji [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) klasy popup.

Zmiana kursora, obiektu podręcznego i zdarzenia click są tworzone w [detektorze zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)mapy . Ta struktura kodu zapewnia, że mapa w pełni ładuje się przed pobraniem informacji o współrzędnych.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać pełne przykłady kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](./map-route.md)

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu](./map-show-traffic.md)
