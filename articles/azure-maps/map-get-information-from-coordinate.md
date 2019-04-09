---
title: Wyświetlanie informacji na temat współrzędnych przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Sposób wyświetlania informacji na temat adresu na mapie, gdy użytkownik wybierze Współrzędna
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256060"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wprowadzania wyszukiwania adres zwrotny, który zawiera adres lokalizacji kliknięto okna podręcznego.

Istnieją dwa sposoby utworzenia wyszukiwanie wsteczne adresu. Jednym ze sposobów jest zapytania [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) przez moduł usługi. Druga metoda obejmuje korzystanie z [Fetch API](https://fetch.spec.whatwg.org/) Aby zgłosić wniosek o [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) można znaleźć adresu. Obie metody są badane poniżej.

## <a name="make-a-reverse-search-request-via-service-module"></a>Żądania wyszukiwania wstecznego, za pomocą modułu usług

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i ustawia mechanizm uwierzytelniania, korzystanie z klucza subskrypcji. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Tworzy drugi blok kodu `SubscriptionKeyCredentialPolicy` do uwierzytelniania żądań HTTP do usługi Azure Maps za pomocą klucza subskrypcji. A następnie `atlas.service.MapsURL.newPipeline()` przyjmuje `SubscriptionKeyCredential` zasad i tworzy [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) wystąpienia. `searchURL` Reprezentuje adres URL do usługi Azure Maps [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) operacji.

Aktualizuje styl kursor myszy na wskaźnik trzeci bloku kodu i tworzy [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) obiektu. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Czwarty bloku kodu dodaje kliknięcie myszą [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events). Po wyzwoleniu tworzy zapytanie wyszukiwania za pomocą współrzędnych punktu klikniętego. Następnie używa moduł usługi [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-) metody zapytania [Uzyskaj adres odwrotnego interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) adresu współrzędne. Kolekcja funkcji GeoJSON z odpowiedzi jest wyodrębniany przy użyciu `geojson.getFeatures()` metody.

Piąty bloku kodu konfiguruje zawartość okna podręcznego HTML, aby wyświetlić adres odpowiedzi kliknięto pozycję współrzędnych.

Zmiana kursora, obiekt menu podręczne i zdarzenie click są tworzone na mapie [odbiornik zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zapewnienia pełnego ładowania mapy, aby można było pobrać informacji o współrzędnych.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Wyślij żądanie wyszukiwania wstecznego za pośrednictwem pobrania interfejsu API

Kliknij na mapie, aby zgłosić wniosek geocode odwrotnego dla tej lokalizacji przy użyciu pobierania.

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>uzyskać informacje na podstawie współrzędnych</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy i ustawia mechanizm uwierzytelniania, korzystanie z klucza subskrypcji. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu aktualizuje styl kursor myszy na wskaźnik i [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) obiektu. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Trzeci bloku kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą, wykorzystuje [pobrania interfejsu API](https://fetch.spec.whatwg.org/) do wykonywania zapytań [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) adresu kliknięto współrzędnych. Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkcji klasy okna podręcznego.

Zmiana kursora, obiekt menu podręczne i zdarzenie click są tworzone na mapie [odbiornik zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zapewnienia pełnego ładowania mapy, aby można było pobrać informacji o współrzędnych.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B](./map-route.md)

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu](./map-show-traffic.md)
