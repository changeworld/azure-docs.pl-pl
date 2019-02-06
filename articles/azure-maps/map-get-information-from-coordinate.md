---
title: Wyświetlanie informacji na temat współrzędnych przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Sposób wyświetlania informacji na temat adresu na mapie, gdy użytkownik wybierze Współrzędna
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 20616adf649924a13e80411aa5135889a175f442
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750192"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wprowadzania wyszukiwania adres zwrotny, który zawiera adres lokalizacji kliknięto okna podręcznego.

Istnieją dwa sposoby utworzenia wyszukiwanie wsteczne adresu. Jednym ze sposobów jest zapytania [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) przez moduł usługi. Druga metoda jest zapewnienie [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API można znaleźć adresu. Obie metody są badane poniżej.

## <a name="make-a-reverse-search-request-via-service-module"></a>Żądania wyszukiwania wstecznego, za pomocą modułu usług

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy wystąpienie usługi klienta.

Trzeci bloku kodu aktualizuje styl kursor myszy na wskaźnik i [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) obiektu. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Dodaje czwarty bloku kodu [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) dla kliknięcia myszą. Po kliknięciu myszą za pomocą współrzędnych punktu klikniętego tworzy zapytania wyszukiwania. Następnie używa mapy [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest) zapytania adres współrzędnych punktu końcowego.

Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkcji klasy okna podręcznego.

Zmiana kursora, obiekt menu podręczne i zdarzenie click są tworzone na mapie [odbiornik zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zapewnienia pełnego ładowania mapy, aby można było pobrać informacji o współrzędnych.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Wyślij żądanie wyszukiwania wstecznego za pośrednictwem XMLHttpRequest

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>uzyskać informacje na podstawie współrzędnych</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu aktualizuje styl kursor myszy na wskaźnik i [okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) obiektu. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Trzeci bloku kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą, wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) zapytania dla adresu kliknięto współrzędnych. Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) funkcji klasy okna podręcznego.

Zmiana kursora, obiekt menu podręczne i zdarzenie click są tworzone na mapie [odbiornik zdarzeń ładowania](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zapewnienia pełnego ładowania mapy, aby można było pobrać informacji o współrzędnych.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)

> [!div class="nextstepaction"]
> [Wyświetlanie ruchu](./map-show-traffic.md)
