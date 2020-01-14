---
title: Narzędzia do rysowania — typy interakcji i skróty klawiaturowe na mapie | Mapy Microsoft Azure
description: Jak rysować i edytować kształty przy użyciu myszy, ekranu dotykowego lub klawiatury w zestawie Microsoft Azure Web SDK mapy
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 267579f7b4bbfe026f3aa01b00f01f3b872cf4a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911652"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcji i skróty klawiaturowe w module narzędzi do rysowania

W tym artykule opisano różne sposoby rysowania i edytowania kształtów na mapie przy użyciu myszy, ekranu dotykowego lub skrótów klawiaturowych.

Menedżer rysowania obsługuje trzy różne sposoby współpracy z mapą do rysowania kształtów.

* Współrzędne `click` są dodawane po kliknięciu przycisku myszy lub dotknięciem.
* Współrzędne `freehand ` są dodawane, gdy mysz lub dotknięcie zostanie przeciągnięte na mapie. 
* Współrzędne `hybrid` są dodawane, gdy mysz lub dotknięcie zostanie kliknięte lub przeciągnięte.

## <a name="how-to-draw-shapes"></a>Jak rysować kształty

Poniżej przedstawiono wszystkie różne sposoby rysowania kształtów na mapie. Przed narysowaniem dowolnego kształtu opcja `drawingMode` Menedżera rysowania musi być ustawiona na obsługiwane ustawienie rysowania. Można to zrobić programowo lub naciskając jeden z przycisków rysowania na pasku narzędzi. Tryb rysowania pozostaje włączony, nawet po narysowaniu kształtu, ułatwiając rysowanie dodatkowych kształtów tego samego typu. Tryb rysowania może być przełączany do stanu bezczynności programowo lub przez kliknięcie przycisku Tryby rysowania bieżące na pasku narzędzi. 

### <a name="how-to-draw-a-point"></a>Jak narysować punkt

Gdy Menedżer rysowania działa w trybie rysowania `draw-point`, można wykonać następujące akcje, aby narysować punkty na mapie. Metody te działają ze wszystkimi trybami interakcji.

**Rozpocznij rysowanie**
 - Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać punkt do mapy. 
 - Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany przy użyciu współrzędnych położenia wskaźnika myszy. Zapewnia to większą dokładność metody dodawania punktu do mapy, ponieważ będzie mniej ruchu nad myszą ze względu na wciśnięcie lewego przycisku myszy.
 - Kontynuuj klikanie, dotknięcie lub naciskając `F`, aby dodać więcej punktów do mapy.
 
**Zakończ rysowanie**
 - Kliknij dowolny przycisk na pasku narzędzi Rysowanie. 
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-line"></a>Jak narysować linię

Gdy Menedżer rysowania działa w trybie `draw-line`, można wykonać następujące akcje, aby narysować punkty na mapie, w zależności od tego, jaki tryb interakcji jest ustawiony na.

**Rozpocznij rysowanie**
 - Tryb kliknięcia
   * Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać każdy punkt linii na mapie. Współrzędna jest dodawana do wiersza dla każdego kliknięcia/dotknięcia. 
   * Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany przy użyciu współrzędnych położenia wskaźnika myszy. Zapewnia to większą dokładność metody dodawania punktu do mapy, ponieważ będzie mniej ruchu nad myszą ze względu na wciśnięcie lewego przycisku myszy.
   * Kontynuuj klikaj do momentu dodania do wiersza odpowiednich punktów.
 - Tryb FreeHand
   * Naciśnij lewy przycisk myszy lub dotknij na mapie, a następnie przeciągnij myszą lub punkt dotykowy. Współrzędne są dodawane do wiersza, ponieważ mysz lub punkt dotykowy przesuwa się wokół mapy. Gdy tylko zostanie wyzwolone zdarzenie myszy lub dotknięcia, rysunek zostanie ukończony. Częstotliwość dodawania współrzędnych jest definiowana przez menedżerów rysowania `freehandInterval` opcji.
 - Tryb hybrydowy
   * Alternatywa między metodami klikania i FreeHand w trakcie rysowania pojedynczej linii. Na przykład kliknij kilka punktów, następnie przytrzymaj i przeciągnij myszą, aby dodać wiele punktów, a następnie kliknij kilka więcej. 

**Zakończ rysowanie**
 - Tryb hybrydowy/kliknięcia
   * Kliknij dwukrotnie mapę w ostatnim punkcie. 
   * Kliknij dowolny przycisk na pasku narzędzi Rysowanie. 
   * Programowo Ustaw tryb rysowania. 
 - Tryb FreeHand
   * Zwolnij przycisk myszy lub punkt dotykowy.
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-polygon"></a>Jak narysować Wielokąt

Gdy Menedżer rysowania działa w trybie `draw-polygon`, można wykonać następujące akcje, aby narysować punkty na mapie, w zależności od tego, jaki tryb interakcji jest ustawiony na.

**Rozpocznij rysowanie**
 - Tryb kliknięcia
   * Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać każdy punkt wielokąta na mapie. Współrzędna jest dodawana do wielokąta dla każdego kliknięcia/dotknięcia. 
   * Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany przy użyciu współrzędnych położenia wskaźnika myszy. Zapewnia to większą dokładność metody dodawania punktu do mapy, ponieważ będzie mniej ruchu nad myszą ze względu na wciśnięcie lewego przycisku myszy.
   * Kontynuuj klikaj do momentu, aż wszystkie żądane punkty zostaną dodane do wielokąta.
 - Tryb FreeHand
   * Naciśnij lewy przycisk myszy lub dotknij na mapie, a następnie przeciągnij myszą lub punkt dotykowy. Współrzędne są dodawane do wielokąta, ponieważ mysz lub punkt dotykowy porusza się wokół mapy. Gdy tylko zostanie wyzwolone zdarzenie myszy lub dotknięcia, rysunek zostanie ukończony. Należy zauważyć, że częstotliwość dodawania współrzędnych jest definiowana przez menedżerów rysowania `freehandInterval` opcji.
 - Tryb hybrydowy
   * Alternatywa między metodami klikania i FreeHand w trakcie rysowania pojedynczego wielokąta. Na przykład kliknij kilka punktów, następnie przytrzymaj i przeciągnij myszą, aby dodać wiele punktów, a następnie kliknij kilka więcej. 

**Zakończ rysowanie**
 - Tryb hybrydowy/kliknięcia
   * Kliknij dwukrotnie mapę w ostatnim punkcie. 
   * Kliknij pierwszy punkt w wielokąta.
   * Kliknij dowolny przycisk na pasku narzędzi Rysowanie. 
   * Programowo Ustaw tryb rysowania. 
 - Tryb FreeHand
   * Zwolnij przycisk myszy lub punkt dotykowy.
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-rectangle"></a>Jak narysować prostokąt

Gdy Menedżer rysowania działa w trybie `draw-rectangle`, można wykonać następujące akcje, aby narysować punkty na mapie, w zależności od tego, jaki tryb interakcji jest ustawiony na. Wygenerowany kształt będzie podążał [za rozszerzoną specyfikacją GEOJSON dla prostokątów](extend-geojson.md#rectangle).

**Rozpocznij rysowanie**
 - Naciśnij przycisk myszy w dół lub dotknij na mapie, aby dodać pierwszy narożnik prostokąta, a następnie przeciągnij, aby utworzyć prostokąt. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-circle"></a>Jak narysować okrąg

Gdy Menedżer rysowania działa w trybie `draw-circle`, można wykonać następujące akcje, aby narysować punkty na mapie, w zależności od tego, jaki tryb interakcji jest ustawiony na. Wygenerowany kształt będzie podążać [za rozszerzoną specyfikacją GEOJSON dla okręgów](extend-geojson.md#circle).

**Rozpocznij rysowanie**
 - Naciśnij przycisk myszy w dół lub dotknij na mapie, aby dodać środek okręgu, a następnie przeciągnij go do pozycji promień. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Narzędzia do rysowania obsługują skróty klawiaturowe ułatwiające rysowanie i edytowanie kształtów na mapie. Te skróty klawiaturowe są funkcjonalne, gdy mapa ma fokus.

| Klucz      | Działanie                            |
|----------|-----------------------------------|
| `C` | Kończy wszystkie rysunki, które są w toku i ustawia tryb rysowania jako bezczynny. Fokus zostanie przeniesiony do elementu mapy najwyższego poziomu.  |
| `Escape` | Anuluje wszystkie rysunki, które są w toku i ustawia tryb rysowania na bezczynny. Fokus zostanie przeniesiony do elementu mapy najwyższego poziomu.  |
| `F` | Dodaje współrzędną do punktu, linii lub wielokąta, gdy wskaźnik myszy znajduje się na mapie. Równoważna Akcja po kliknięciu mapy w trybie kliknij lub hybrydowym. Ten skrót umożliwia dokładniejsze i szybsze rysowanie, ponieważ można użyć jednej z nich do położenia myszy i innych, aby nacisnąć przycisk bez przesuwania wskaźnika myszy od gestu naciśnięcia przycisku. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas w module narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)