---
title: Dodawanie paska narzędzi Rysowanie do Azure Maps | Microsoft Docs
description: Jak dodać pasek narzędzi rysowania do mapy za pomocą Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 63848ecf92daecf28f6e0046e87da15355aa35c1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561834"
---
# <a name="drawing-tool-events"></a>Zdarzenia narzędzia do rysowania

W przypadku korzystania z narzędzi do rysowania na mapie często warto reagować na niektóre zdarzenia, gdy użytkownik rysuje na mapie. W poniższej tabeli wymieniono wszystkie zdarzenia obsługiwane przez klasę `DrawingManager`.

| Wydarzenie | Opis |
|-------|-------------|
| `drawingchanged` | Uruchamiany, gdy dodaliśmy lub zmieniono dowolna Współrzędna kształtu. | 
| `drawingchanging` | Uruchamiany, gdy zostanie wyświetlona Współrzędna podglądu dla kształtu. Na przykład program uruchamia wiele razy, gdy Współrzędna zostanie przeciągnięta. | 
| `drawingcomplete` | Uruchamiany po zakończeniu rysowania lub wyjęciu kształtu w trybie edycji. |
| `drawingmodechanged` | Uruchamiany po zmianie trybu rysowania. Nowy tryb rysowania jest przenoszona do procedury obsługi zdarzeń. |
| `drawingstarted` | Uruchamiany, gdy użytkownik rozpocznie Rysowanie kształtu lub umieszcza kształt w trybie edycji.  |

Poniższy kod przedstawia sposób działania zdarzeń w module narzędzi do rysowania. Rysuj kształty na mapie i obserwuj je jako pożarowe zdarzenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia narzędzi do rysowania" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>zdarzenia narzędzi do rysowania</a> piórem według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Przykłady

Poniżej przedstawiono przykłady niektórych typowych scenariuszy korzystających z zdarzeń narzędzi do rysowania.

### <a name="select-points-in-polygon-area"></a>Wybierz punkty w obszarze wielokąta

Poniższy kod pokazuje, jak monitorować rysowanie kształtów reprezentujących obszary wielokąta (wielokąty, prostokąty i okręgi), a następnie określić, które punkty danych na mapie znajdują się w obszarze rysunku. Zdarzenie `drawingcomplete` służy do wyzwalania logiki SELECT. W ramach logiki SELECT wszystkie punkty danych na mapie są załączone i przetestowane pod kątem przechodzenia do sekcji z wieloczęściowym obszarem rysowania kształtu. W tym przykładzie użyto biblioteki typu open source [Turf. js](https://turfjs.org/) do wykonania obliczeń między częściami przestrzennymi.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wybierz dane w wyrysowanym obszarze wielokątów" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Wybierz dane w wyrysowanym obszarze wielokątów</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rysowanie i wyszukiwanie w obszarze wielokąta

Poniższy kod pokazuje, jak przeprowadzić wyszukiwanie punktów zainteresowania w obszarze kształtu po zakończeniu rysowania kształtu przez użytkownika. Zdarzenie `drawingcomplete` służy do wyzwalania logiki wyszukiwania. Jeśli użytkownik rysuje prostokąt lub Wielokąt, wykonywane jest wyszukiwanie wewnątrz geometrii. Jeśli okrąg jest rysowany, pozycja promień i środek zostanie użyta do przeprowadzenia wyszukiwania w punkcie zainteresowania. Zdarzenie `drawingmodechanged` służy do ustalania, kiedy użytkownik przechodzi do trybu rysowania, i czyści kanwę rysunku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie i wyszukiwanie w obszarze wielokąta" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>rysowanie i wyszukiwanie piórem w obszarze wielokąta</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Tworzenie narzędzia pomiarowego

Poniższy kod pokazuje, jak zdarzenia rysowania mogą być używane do tworzenia narzędzia pomiarowego. `drawingchanging` służy do monitorowania kształtu podczas jego rysowania. Gdy użytkownik przesuwa mysz, są obliczane wymiary kształtu. Zdarzenie `drawingcomplete` służy do wykonywania ostatecznego obliczenia na kształcie po jego narysowaniu. Zdarzenie `drawingmodechanged` służy do ustalania, kiedy użytkownik przechodzi do trybu rysowania, i czyści kanwę rysunku i stare informacje o pomiarach.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Narzędzie pomiarowe" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Narzędzie pomiar</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Pobierz dane kształtu](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o module usług:

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)

Zapoznaj się z dodatkowymi przykładami kodu:

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)
