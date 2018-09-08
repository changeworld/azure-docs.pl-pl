---
title: Wyświetlanie informacji na temat współrzędnych przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Sposób wyświetlania informacji na temat adresu na mapie, gdy użytkownik wybierze Współrzędna
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d1baa4adc555e65c4a25928d19f201dba6109142
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44157688"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wyszukiwania wstecznego adresu, a po kliknięciu myszą Pokaż adres kliknięto lokalizacji, w okienku wyskakującym.

Istnieją dwa sposoby utworzenia wyszukiwanie wsteczne adresu, on, badając [Azure zwrotny adres wyszukiwania interfejsu API usługi mapy](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) za pośrednictwem usługi modułu, a drugi jest udostępniając [XMLHttpRequest](https://xhr.spec.whatwg.org/) do interfejsu API, aby wykonać zapytanie adres. Omówimy zarówno poniżej.

## <a name="use-the-service-module-to-make-a-reverse-address-search"></a>Używaj modułu service umożliwia wyszukiwanie wsteczne adresu

### <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci bloku kodu aktualizuje styl kursor myszy na wskaźnik.

Czwarty blok kodu tworzy okno podręczne. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą za pomocą współrzędne punktu klikniętego tworzy zapytania wyszukiwania. Następnie wykorzystuje mapy [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) zapytania adres współrzędne punktu końcowego.

Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego.

## <a name="use-xmlhttprequest-to-make-a-reverse-address-search"></a>Używaj XMLHTTPRequest umożliwia wyszukiwanie wsteczne adresu

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
