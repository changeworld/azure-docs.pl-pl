---
title: Obsługa zdarzeń przy użyciu Azure Maps | Microsoft Docs
description: Jak utworzyć interaktywną mapę sieci Web SDK przy użyciu zdarzeń mapy
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899426"
---
# <a name="interact-with-the-map"></a>Korzystanie z mapy

W tym artykule pokazano, jak używać właściwości [zdarzenia klasy mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) do wyróżniania zdarzeń na mapie i na różnych warstwach mapy. Pokazano w nim także, jak używać właściwości zdarzenia klasy mapy do wyróżniania zdarzeń podczas korzystania z znacznika HTML.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

Odtwórz przy użyciu mapy poniżej i zobacz odpowiednie zdarzenia myszy wyróżnione po prawej stronie. Możesz kliknąć **kartę js** , aby wyświetlić i edytować kod JavaScript. Możesz również kliknąć przycisk **Edytuj na CodePen** i edytować kod w CodePen.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z mapy — zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>z zdarzeń dotyczących mapy — wskaźnik myszy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Korzystanie z warstw mapy

Poniższy kod wyróżnia nazwę zdarzeń, które są wyzwalane podczas korzystania z warstwy symboli. Warstwa symbol, bąbelki, linia i Wielokąt obsługują ten sam zestaw zdarzeń. Warstwy mapy cieplnej i kafelki nie obsługują żadnego z tych zdarzeń.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z zdarzeń mapy — zdarzenia warstwy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>z zdarzeń mapy — zdarzenia warstwy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Korzystanie z znacznika HTML

Poniższy kod dodaje zdarzenia mapy JavaScript do znacznika HTML. Wyróżnia także nazwę zdarzeń, które są wyzwalane podczas korzystania z znacznika HTML.

<br/>

<iframe height='500' scrolling='no' title='Korzystanie z zdarzeń mapy — znaczniki HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz, jak <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>korzystać z pióra z zdarzeniami znacznika mapy kodu HTML</a> według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () w <a href='https://codepen.io'>CodePen</a>.
</iframe>

W poniższej tabeli wymieniono wszystkie obsługiwane zdarzenia klas mapy.

| Wydarzenie             | Opis |
|-------------------|-------------|
| boxzoomend        | Uruchamiany po zakończeniu interakcji "pole Powiększ".|
| boxzoomstart      | Uruchamiany po rozpoczęciu interakcji "Box zoom".|
| polecenie             | Uruchamiany, gdy urządzenie wskazujące zostanie naciśnięte i wydane w tym samym punkcie na mapie.|
| zamknij             | Uruchamiany, gdy menu podręczne jest zamknięte ręcznie lub programowo.|
| zmieniona       | Uruchamiany po kliknięciu prawego przycisku myszy.|
| dodano dane         | Uruchamiany, gdy kształty są dodawane do źródła danych.|
| Usunięto       | Uruchamiany, gdy kształty są usuwane ze źródła danych.|
| datasourceupdated | Uruchamiany, gdy obiekt DataSource zostanie zaktualizowany.|
| DblClick          | Uruchamiany, gdy urządzenie wskazujące jest kliknięte dwukrotnie w tym samym punkcie na mapie.|
| Przeciągnąć              | Wywoływane wielokrotnie podczas interakcji "przeciągnij do kadrowania" w znaczniku mapy, popup lub HTML.|
| dragend           | Uruchamiany po zakończeniu interakcji "przeciągnij do kadrowania" w znaczniku mapy, popup lub HTML.|
| dragstart         | Uruchamiany, gdy interakcja "przeciągnij do panoram" zaczyna się w znaczniku mapy, popup lub HTML.|
| błąd             | Uruchamiany, gdy wystąpi błąd.|
| zdarzenia           | Uruchamiany po naciśnięciu klawisza.|
| KeyPress          | Uruchamiany, gdy zostanie naciśnięty klucz generujący znak typable (klucz ANSI).|
| KeyUp             | Uruchamiany po wydaniu klucza.|
| layeradded        | Uruchamiany, gdy warstwa zostanie dodana do mapy.|
| load              | Uruchamiany natychmiast po pobraniu wszystkich niezbędnych zasobów i wykonaniu pierwszego wizualnego renderowania mapy.|
| MouseDown         | Uruchamiany, gdy urządzenie wskazujące zostanie naciśnięte na mapie.|
| MouseMove         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone na mapie.|
| mouseout          | Uruchamiany, gdy urządzenie punktowe opuści kanwę mapy.|
| MouseOver         | Uruchamiany, gdy urządzenie wskazujące jest przenoszone na mapie.|
| MouseUp           | Uruchamiany, gdy urządzenie wskazujące jest wydzierżawione na mapie.|
| Przenieś              | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| moveend           | Uruchamiany tuż po zakończeniu przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| movestart         | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego widoku do drugiego w wyniku interakcji z użytkownikiem lub metod.|
| open              | Uruchamiany po ręcznym otwarciu okna podręcznego lub programowo.|
| tonu             | Uruchamiany zawsze, gdy nastąpi zmiana skoku mapy (pochylenie) w wyniku interakcji z użytkownikiem lub metod.|
| pitchend          | Uruchamiany natychmiast po pochylenie mapy (nachylenie) zakończy się zmianami w wyniku interakcji z użytkownikiem lub metodami.|
| pitchstart        | Uruchamiany zawsze, gdy gęstość mapy (nachylenie) rozpoczyna zmianę w wyniku interakcji z użytkownikiem lub metod.|
| gotowy             | Uruchamiany, gdy minimalne wymagane zasoby mapy są ładowane, zanim mapa będzie gotowa do programistycznego manipulowania.|
| renderowania            | <p> Uruchamiany za każdym razem, gdy mapa zostanie narysowana na ekranie, w wyniku:<ul><li>Zmiana pozycji mapy, powiększenia, skoku lub nanoszenia.</li><li>Zmiana stylu mapy.</li><li>Zmiana źródła źródła danych.</li><li>Ładowanie kafelka wektorowego, pliku GEOJSON, symbolu lub Sprite.</li></ul></p>|
| Zmień rozmiar            | Uruchamiany natychmiast po zmianie rozmiaru mapy.|
| obróceni            | Wywoływane wielokrotnie podczas interakcji "przeciągnij, aby obrócić".|
| rotateend         | Uruchamiany po zakończeniu interakcji "przeciągnij do obrotu".|
| rotatestart       | Uruchamiany po rozpoczęciu interakcji "przeciągnij do obrotu".|
| shapechanged      | Uruchamiany, gdy zostanie zmieniona właściwość obiektu kształtu.|
| sourceadded       | Uruchamiany, gdy do mapy zostanie dodane źródło danych lub VectorTileSource.|
| sourceremoved     | Uruchamiany po usunięciu źródła danych lub VectorTileSource z mapy.|
| styledata         | Uruchamiany, gdy styl mapy zostanie załadowany lub zmieniony.|
| tokenacquired     | Uruchamiany, gdy zostanie uzyskany token dostępu usługi AAD.|
| touchcancel       | Uruchamiany, gdy w mapie wystąpi zdarzenie touchcancel.|
| touchend          | Uruchamiany, gdy w mapie wystąpi zdarzenie touchend.|
| touchmove         | Uruchamiany, gdy w mapie wystąpi zdarzenie TouchMove.|
| touchstart        | Uruchamiany, gdy w mapie wystąpi zdarzenie touchstart.|
| trzykołowych             | Uruchamiany, gdy na mapie wystąpi zdarzenie kółka myszy.|
| Zmieniać              | Uruchamiany wielokrotnie podczas animowanego przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|
| zoomend           | Uruchamiany tuż po zakończeniu przez mapę przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|
| zoomstart         | Uruchamiany tuż przed rozpoczęciem przez mapę przejścia z jednego poziomu powiększenia na inny w wyniku interakcji z użytkownikiem lub metod.|


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Korzystanie z modułu Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
