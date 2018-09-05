---
title: Wyświetlanie informacji na temat współrzędnych przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Sposób wyświetlania informacji na temat adresu na mapie, gdy użytkownik wybierze Współrzędna
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667485"
---
# <a name="get-information-from-a-coordinate"></a>Uzyskiwanie informacji na podstawie współrzędnych

W tym artykule przedstawiono sposób wyszukiwania wstecznego adresu, a po kliknięciu myszą Pokaż adres kliknięto lokalizacji, w okienku wyskakującym.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Uzyskiwanie informacji o współrzędnych (moduł usługi)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>uzyskać informacje na podstawie współrzędnych (moduł usługi)</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Wiersz w drugim bloku kodu tworzy klienta usługi.

Trzeci bloku kodu aktualizuje styl kursor myszy na wskaźnik.

Czwarty blok kodu tworzy okno podręczne. Możesz zobaczyć [dodać okno podręczne na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornika zdarzeń dla kliknięcia myszą. Po kliknięciu myszą za pomocą współrzędne punktu klikniętego tworzy zapytania wyszukiwania. Następnie wykorzystuje mapy [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) zapytania adres współrzędne punktu końcowego.

Pomyślnej odpowiedzi go zbiera adresów kliknięto lokalizacji, a następnie definiuje zawartość okna podręcznego i położenie za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego.

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
