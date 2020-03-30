---
title: Obsługa zdarzeń mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak tworzyć interaktywną mapę SDK sieci Web ze zdarzeniami map przy użyciu sdk microsoft azure maps web.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534902"
---
# <a name="interact-with-the-map"></a>Interakcja z mapą

W tym artykule pokazano, jak używać [klasy zdarzeń mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). Obiekt wyróżnia zdarzenia na mapie i na różnych warstwach mapy. Zdarzenia można również wyróżnić podczas interakcji ze znacznikiem HTML.

## <a name="interact-with-the-map"></a>Interakcja z mapą

Zagraj z poniższą mapą i zobacz odpowiednie zdarzenia myszy podświetlone po prawej stronie. Możesz kliknąć **kartę JS,** aby wyświetlić i edytować kod JavaScript. Można również kliknąć na **Edytuj na CodePen,** aby zmodyfikować kod na CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interakcja z mapą – zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interakcja z mapą — zdarzenia myszy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interakcja z warstwami mapy

Poniższy kod podświetla zdarzenie podpalone podczas interakcji z warstwą symboli. Warstwa symbolu, bąbelka, linii i wielokąta obsługuje ten sam zestaw zdarzeń. Warstwy mapy cieplnej i kafelków nie obsługują żadnego z tych zdarzeń.

<br/>

<iframe height='600' scrolling='no' title='Interakcja z mapą – zdarzenia warstwy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakcji z mapą — zdarzenia warstw</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>codepen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interakcja ze znacznikiem HTML

Poniższy kod dodaje zdarzenia mapy Javascript do znacznika HTML. Wskazuje również nazwę zdarzeń, które są uruchamiane podczas interakcji ze znacznikiem HTML.

<br/>

<iframe height='500' scrolling='no' title='Interakcja z mapą — zdarzenia znaczników HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interakcji z mapą - zdarzenia znacznika HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W poniższej tabeli wymieniono wszystkie obsługiwane zdarzenia klasy mapy.

| Wydarzenie               | Opis |
|---------------------|-------------|
| `boxzoomend`        | Uruchamiany po zakończeniu interakcji "box zoom".|
| `boxzoomstart`      | Uruchamiany po rozpoczęciu interakcji "box zoom".|
| `click`             | Uruchamiany po naciśnięciu i zwolnieniu urządzenia wskazującego w tym samym punkcie na mapie.|
| `close`             | Uruchamiany, gdy okno podręczne jest zamknięte ręcznie lub programowo.|
| `contextmenu`       | Odpalany po kliknięciu prawego przycisku myszy.|
| `data`              | Uruchamiany, gdy dane mapy są wczytywane lub zmieniane. |
| `dataadded`         | Uruchamiane, gdy kształty `DataSource`są dodawane do .|
| `dataremoved`       | Uruchamiane, gdy kształty `DataSource`są usuwane z pliku .|
| `datasourceupdated` | Uruchamiany, `DataSource` gdy obiekt jest aktualizowany.|
| `dblclick`          | Uruchamiany, gdy urządzenie wskazujące zostanie dwukrotnie kliknięty w tym samym punkcie na mapie.|
| `drag`              | Wielokrotnie uruchamiany podczas interakcji "przeciągnij do przesuwania" na mapie, wyskakującym okienku lub znaczniku HTML.|
| `dragend`           | Uruchamiane po zakończeniu interakcji "przeciągnij do przesuwania" na mapie, wyskakującym okienku lub znaczniku HTML.|
| `dragstart`         | Uruchamiane, gdy na mapie, wyskakującym okienku lub znaczniku HTML rozpoczyna się interakcja "przeciągnij do przeciągnięcia".|
| `error`             | Uruchamiany, gdy wystąpi błąd.|
| `idle`              | <p>Odpalone po ostatniej klatce renderowane przed mapą wejdzie w stan "bezczynny":<ul><li>Nie są w toku żadne przejścia kamery.</li><li>Załadowano wszystkie aktualnie żądane kafelki.</li><li>Wszystkie animacje zanikania/przejścia zostały ukończone.</li></ul></p>|
| `keydown`           | Wystrzelony po naciśnięciu klawisza.|
| `keypress`          | Uruchamiany po naciśnięciu klawisza, który wytwarza typowalny znak (klucz ANSI).|
| `keyup`             | Uruchamiany po zwolnieniu klucza.|
| `layeradded`        | Uruchamiany po dodaniu warstwy do mapy.|
| `layerremoved`      | Uruchamiany po usunięciu warstwy z mapy.|
| `load`              | Odpalone natychmiast po pobraniu wszystkich niezbędnych zasobów i pierwszym wizualnie kompletnym renderowaniu mapy.|
| `mousedown`         | Uruchamiany po naciśnięciu urządzenia wskazującego na mapie lub gdy znajduje się na elemencie.|
| `mouseenter`        | Uruchamiane, gdy urządzenie wskazujące jest początkowo przenoszone na mapę lub element. |
| `mouseleave`        | Uruchamiany, gdy urządzenie wskazujące jest przenoszone z mapy lub elementu. |
| `mousemove`         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone w obrębie mapy lub elementu.|
| `mouseout`          | Wystrzeliwany, gdy urządzenie punktowe opuszcza płótno mapy, pozostawia element.|
| `mouseover`         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone na mapę lub element.|
| `mouseup`           | Uruchamiany, gdy urządzenie wskazujące jest zwalniane na mapie lub gdy znajduje się na szczycie elementu.|
| `move`              | Wielokrotnie uruchamiane podczas animowanego przejścia z jednego widoku do drugiego, w wyniku interakcji użytkownika lub metod.|
| `moveend`           | Uruchamiany tuż po zakończeniu mapy przejścia z jednego widoku do drugiego, w wyniku interakcji użytkownika lub metod.|
| `movestart`         | Został wystrzelony tuż przed rozpoczęciem przejścia mapy z jednego widoku do drugiego, w wyniku interakcji użytkownika lub metod.|
| `open`              | Uruchamiany, gdy okno podręczne jest otwierane ręcznie lub programowo.|
| `pitch`             | Uruchamiany za każdym razem, gdy wysokość (nachylenie) mapy zmienia się w wyniku interakcji z użytkownikiem lub metod.|
| `pitchend`          | Wystrzelony natychmiast po zakończeniu nachylenia mapy (nachylenie) w wyniku interakcji użytkownika lub metod.|
| `pitchstart`        | Uruchamiany za każdym razem, gdy wysokość mapy (nachylenie) rozpoczyna się w wyniku interakcji z użytkownikiem lub metod.|
| `ready`             | Uruchamiane po załadowaniu minimalnych wymaganych zasobów mapy, zanim mapa będzie gotowa do programowej interakcji.|
| `render`            | <p>Odpalane za każdym razem, gdy mapa jest rysowana na ekranie, w wyniku:<ul><li>Zmiana położenia, powiększenia, wysokości lub łożyska mapy.</li><li>Zmiana stylu mapy.</li><li>Zmiana `DataSource` źródła.</li><li>Ładowanie kafelka wektorowego, pliku GeoJSON, glifów lub sprite'a.</li></ul></p>|
| `resize`            | Wystrzelony natychmiast po przesiąknieniu mapy.|
| `rotate`            | Wystrzeliwany wielokrotnie podczas interakcji "przeciągnij, aby obrócić".|
| `rotateend`         | Uruchamiany po zakończeniu interakcji "przeciągnij, aby obrócić".|
| `rotatestart`       | Uruchamiany, gdy rozpocznie się interakcja "przeciągnij, aby obrócić".|
| `shapechanged`      | Uruchamiany po zmianie właściwości obiektu kształtu.|
| `sourcedata`        | Uruchamiany, gdy jedno ze źródeł mapy ładuje się lub zmienia, w tym jeśli kafelek należący do źródła ładuje się lub zmienia. |
| `sourceadded`       | Wystrzeliwany `DataSource` po `VectorTileSource` dodaniu lub dodaniu do mapy.|
| `sourceremoved`     | Wystrzeliwany `DataSource` po `VectorTileSource` usunięciu lub usunięciu z mapy.|
| `styledata`         | Uruchamiany, gdy styl mapy ładuje się lub zmienia.|
| `styleimagemissing` | Uruchamiany, gdy warstwa próbuje załadować obraz ze sprite'a obrazu, który nie istnieje |
| `tokenacquired`     | Uruchamiany po uzyskaniu tokenu dostępu AAD.|
| `touchcancel`       | Uruchamiany, gdy na mapie wystąpi zdarzenie touchcancel.|
| `touchend`          | Uruchamiany, gdy na mapie wystąpi zdarzenie touchend.|
| `touchmove`         | Uruchamiany, gdy na mapie wystąpi zdarzenie touchmove.|
| `touchstart`        | Uruchamiany, gdy na mapie wystąpi zdarzenie touchstart.|
| `wheel`             | Uruchamiany, gdy na mapie wystąpi zdarzenie kółka myszy.|
| `zoom`              | Wielokrotnie uruchamiane podczas animowanego przejścia z jednego poziomu powiększenia do drugiego, w wyniku interakcji użytkownika lub metod.|
| `zoomend`           | Uruchamiany tuż po zakończeniu mapy przejścia z jednego poziomu powiększenia do drugiego, w wyniku interakcji użytkownika lub metod.|
| `zoomstart`         | Uruchamiany tuż przed rozpoczęciem mapy przejścia z jednego poziomu powiększenia do drugiego, w wyniku interakcji użytkownika lub metod.|


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby uzyskać pełne przykłady kodu:

> [!div class="nextstepaction"]
> [Korzystanie z modułu Usługi Usługi Azure Maps](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
