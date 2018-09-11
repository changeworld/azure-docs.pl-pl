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
ms.openlocfilehash: 993d1da4b2a99ec0f30a5a685835d9f6b6d35a9e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302898"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wyszukiwania wstecznego adresu, a po kliknięciu myszą Pokaż adres kliknięto lokalizacji, w okienku wyskakującym.

Istnieją dwa sposoby utworzenia wyszukiwanie wsteczne adresu, on, badając [Azure zwrotny adres wyszukiwania interfejsu API usługi mapy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) za pośrednictwem usługi modułu, a drugi jest udostępniając [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API, aby wykonać zapytanie adres. Omówimy zarówno poniżej.

## <a name="making-a-reverse-search-request-via-service-module"></a>Żądania wyszukiwania wstecznego za pomocą modułu usług

### <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci bloku kodu aktualizuje styl kursor myszy na wskaźnik.

Czwarty blok kodu tworzy okno podręczne. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą za pomocą współrzędne punktu klikniętego tworzy zapytania wyszukiwania. Następnie wykorzystuje mapy [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) zapytania adres współrzędne punktu końcowego.

Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego.

## <a name="making-a-reverse-search-request-via-xmlhttprequest"></a>Żądania wyszukiwania wstecznego za pośrednictwem XMLHttpRequest

### <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji na podstawie współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>uzyskać informacje na podstawie współrzędnych</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu aktualizuje styl kursor myszy na wskaźnik.

Trzeci bloku kodu tworzy okno podręczne. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą, wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [Azure Maps odwrotnego adres interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [Odwrócone wyszukiwanie adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Zamknij](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:
* [Wyświetlanie instrukcji dotyczących trasy od A do B](./map-route.md)
* [Wyświetlanie ruchu](./map-show-traffic.md)
