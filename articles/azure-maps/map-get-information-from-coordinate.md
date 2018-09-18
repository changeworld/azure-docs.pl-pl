---
title: Wyświetlanie informacji na temat współrzędnych przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Sposób wyświetlania informacji na temat adresu na mapie, gdy użytkownik wybierze Współrzędna
author: jingjing-z
ms.author: jinzh
ms.date: 09/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: bf44437f4d0b60a5d56c2be29418b7132346da2e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45732298"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wprowadzania wyszukiwania adres zwrotny, który zawiera adres lokalizacji kliknięto okna podręcznego.

Istnieją dwa sposoby utworzenia wyszukiwanie wsteczne adresu. Jednym ze sposobów jest zapytania [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) przez moduł usługi. Druga metoda jest zapewnienie [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API można znaleźć adresu. Obie metody są badane poniżej.

## <a name="make-a-reverse-search-request-via-service-module"></a>Żądania wyszukiwania wstecznego, za pomocą modułu usług

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci bloku kodu aktualizuje styl kursor myszy na wskaźnik.

Czwarty blok kodu tworzy za pomocą okna podręcznego [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu [dodanie detektora](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) dla kliknięcia myszą. Po kliknięciu myszą za pomocą współrzędne punktu klikniętego tworzy zapytania wyszukiwania. Następnie wykorzystuje mapy [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) zapytania adres współrzędne punktu końcowego.

Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego.

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>Wyślij żądanie wyszukiwania wstecznego za pośrednictwem XMLHttpRequest

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>uzyskać informacje na podstawie współrzędnych</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu aktualizuje styl kursor myszy na wskaźnik.

Trzeci bloku kodu tworzy za pomocą okna podręcznego [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open). Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą, wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego

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
