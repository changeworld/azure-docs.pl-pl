---
title: Wyświetlanie informacji o współrzędnych mapy | Mapy Microsoft Azure
description: Dowiedz się, jak wyświetlać informacje o adresie na mapie, gdy użytkownik wybierze współrzędną.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0b1ab7b2c233eb1e6e231b0ae7935b6c24363948
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988536"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule pokazano, jak utworzyć wyszukiwanie odwrotnego adresu, które pokazuje adres klikniętej lokalizacji podręcznej.

Istnieją dwa sposoby przeszukiwania adresów odwrotnych. Jednym ze sposobów jest zbadanie [interfejsu API wyszukiwania odwrotnego adresu Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) za pomocą modułu usługi. Innym sposobem jest użycie [interfejsu API pobierania](https://fetch.spec.whatwg.org/) w celu żądania do [Azure Maps interfejsu API wyszukiwania wstecznego adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) w celu znalezienia adresu. Oba sposoby zostały przedstawione poniżej.

## <a name="make-a-reverse-search-request-via-service-module"></a>Utwórz żądanie wyszukiwania wstecznego za pośrednictwem modułu usługi

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji z współrzędnych (modułu usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Pobierz informacje z współrzędnych (modułu usługi)</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie, pierwszy blok konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy `TokenCredential` do uwierzytelniania żądań HTTP do Azure Maps z tokenem dostępu. Następnie przekazuje `TokenCredential` do `atlas.service.MapsURL.newPipeline()` i tworzy wystąpienie [potoku](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) . `searchURL` reprezentuje adres URL Azure Maps operacji [wyszukiwania](https://docs.microsoft.com/rest/api/maps/search) .

Trzeci blok kodu aktualizuje styl kursora myszy do wskaźnika i tworzy obiekt [podręczny](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . Aby uzyskać instrukcje, zobacz sekcję [Dodawanie okna podręcznego na mapie](./map-add-popup.md) .

Czwarty blok kodu dodaje [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)kliknięcia myszą. Gdy jest wyzwalane, tworzy zapytanie wyszukiwania ze współrzędnymi klikniętego punktu. Następnie używa metody [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-), aby wykonać zapytanie dotyczące [zwrotnego interfejsu API uzyskiwania adresu wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) dla adresu współrzędnych. Kolekcja funkcji GEOJSON jest następnie wyodrębniana przy użyciu metody `geojson.getFeatures()` z odpowiedzi.

Piąty blok kodu konfiguruje zawartość HTML podręczną, aby wyświetlić adres odpowiedzi dla klikniętej pozycji współrzędnej.

W [detektorze zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy wszystkie elementy kursora, obiektu podręcznego i zdarzenia kliknięcia są tworzone w celu zapewnienia całkowitego ładowania mapy przed pobraniem informacji o współrzędnych.

## <a name="make-a-reverse-search-request-via-fetch-api"></a>Utwórz żądanie wyszukiwania wstecznego za pośrednictwem interfejsu API pobierania

Kliknij mapę, aby przetworzyć żądanie odwrotnego kodu dla tej lokalizacji przy użyciu funkcji pobierania.

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, aby <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>uzyskać informacje ze współrzędnych</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy i ustawia mechanizm uwierzytelniania do korzystania z tokenu dostępu. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu aktualizuje styl kursora myszy do wskaźnika. Tworzy wystąpienie obiektu [podręcznego](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) . Aby uzyskać instrukcje, zobacz sekcję [Dodawanie okna podręcznego na mapie](./map-add-popup.md) .

Trzeci blok kodu dodaje odbiornik zdarzeń do kliknięć myszą. Gdy klikniesz myszą, użyje [interfejsu API pobierania](https://fetch.spec.whatwg.org/) , aby wysłać zapytanie do [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) dla klikniętego adresu Azure Maps. W przypadku pomyślnej odpowiedzi zbierany jest adres dla klikniętej lokalizacji. Definiuje zawartość podręczną i położenie przy użyciu funkcji [SetOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) klasy popup.

W [detektorze zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy wszystkie elementy kursora, obiekt podręczny i zdarzenie kliknięcia są tworzone w celu zapewnienia całkowitego ładowania mapy przed pobraniem informacji o współrzędnych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Elementy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Pokaż kierunki od A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Pokaż ruch](./map-show-traffic.md)
