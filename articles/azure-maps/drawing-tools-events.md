---
title: Dodawanie paska narzędzi do rysowania do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać pasek narzędzi do rysowania do mapy przy użyciu zestawu Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804677"
---
# <a name="drawing-tool-events"></a>Zdarzenia narzędzia Rysowanie

Podczas korzystania z narzędzi do rysowania na mapie warto reagować na określone zdarzenia, gdy użytkownik rysuje na mapie. W tej tabeli wymieniono wszystkie `DrawingManager` zdarzenia obsługiwane przez klasę.

| Wydarzenie | Opis |
|-------|-------------|
| `drawingchanged` | Uruchamiany, gdy dobudowa lub zmieniana została jakakolwiek współrzędna w kształcie. | 
| `drawingchanging` | Uruchamiany, gdy wyświetlana jest dowolna współrzędna podglądu kształtu. Na przykład to zdarzenie zostanie uruchamiane wiele razy, gdy współrzędna jest przeciągana. | 
| `drawingcomplete` | Uruchamiany po zakończeniu rysowania lub wyjęciu kształtu z trybu edycji. |
| `drawingmodechanged` | Uruchamiany po zmianie trybu rysowania. Nowy tryb rysowania jest przekazywany do programu obsługi zdarzeń. |
| `drawingstarted` | Uruchamiany, gdy użytkownik rozpoczyna rysowanie kształtu lub przełącza kształt w tryb edycji.  |

Poniższy kod pokazuje, jak działają zdarzenia w module Narzędzia do rysowania. Rysuj kształty na mapie i obserwuj, jak wydarzenia płonie.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zdarzenia narzędzi do rysowania" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>zdarzenia narzędzi do rysowania</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Przykłady

Zobaczmy kilka typowych scenariuszy, które używają zdarzeń narzędzi do rysowania.

### <a name="select-points-in-polygon-area"></a>Zaznaczanie punktów w obszarze wielokąta

Ten kod pokazuje, jak monitorować zdarzenie kształtów rysunku użytkownika. W tym przykładzie kod monitoruje kształty wielokątów, prostokątów i okręgów. Następnie określa, które punkty danych na mapie znajdują się w obszarze rysowanym. Zdarzenie `drawingcomplete` jest używane do wyzwalania logiki wyboru. W logice wyboru kod pętli przez wszystkie punkty danych na mapie. Sprawdza, czy istnieje przecięcie punktu i obszaru narysowanego kształtu. W tym przykładzie używa biblioteki [Turf.js](https://turfjs.org/) open source do wykonywania obliczeń przecięcia przestrzennego.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wybieranie danych w obszarze ciągnienia wielokątów" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/XWJdeja'>dane pióra Wybierz w obszarze rysowane wielokąt</a> przez Usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rysowanie i wyszukiwanie w obszarze wielokąta

Ten kod wyszukuje punkty zainteresowania wewnątrz obszaru kształtu po zakończeniu rysowania kształtu przez użytkownika. Możesz zmodyfikować i wykonać kod, klikając "Edytuj na piórze kodu" w prawym górnym rogu ramki. Zdarzenie `drawingcomplete` jest używane do wyzwalania logiki wyszukiwania. Jeśli użytkownik rysuje prostokąt lub wielokąt, wykonywane jest wyszukiwanie wewnątrz geometrii. Jeśli okrąg jest rysowany, promień i pozycja środkowa są używane do wyszukiwania punktów szczególnych. Zdarzenie `drawingmodechanged` służy do określenia, kiedy użytkownik przełączy się do trybu rysowania, a to zdarzenie czyści kanwę rysunku.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie i wyszukiwanie w obszarze wielokąta" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>rysowanie pióra i wyszukiwanie w obszarze wielokąta</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Tworzenie narzędzia pomiarowego

Poniższy kod pokazuje, jak zdarzenia rysunku mogą być używane do tworzenia narzędzia pomiarowego. Jest `drawingchanging` używany do monitorowania kształtu, jak to jest rysowane. Gdy użytkownik przesuwa mysz, wymiary kształtu są obliczane. Zdarzenie `drawingcomplete` służy do wykonywania ostatecznych obliczeń kształtu po jego narysowaniu. Zdarzenie `drawingmodechanged` służy do określenia, kiedy użytkownik przełącza się w tryb rysowania. Ponadto `drawingmodechanged` zdarzenie czyści kanwę rysunku i czyści stare informacje pomiarowe.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Narzędzie pomiarowe" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>narzędzie do pomiaru</a> piór przez usługę Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Pobieranie danych kształtów](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o module Usługi:

> [!div class="nextstepaction"]
> [Moduł usług](how-to-use-services-module.md)

Sprawdź więcej przykładów kodu:

> [!div class="nextstepaction"]
> [Przykładowa strona kodu](https://aka.ms/AzureMapsSamples)
