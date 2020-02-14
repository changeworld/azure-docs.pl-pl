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
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198296"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcji i skróty klawiaturowe w module narzędzi do rysowania

W tym artykule opisano różne sposoby rysowania i edytowania kształtów przy użyciu myszy, ekranu dotykowego lub skrótów klawiaturowych.

Menedżer rysowania obsługuje trzy różne sposoby współpracy z mapą, aby rysować kształty.

* Współrzędne `click` są dodawane po kliknięciu przycisku myszy lub dotknięciem.
* Współrzędne `freehand ` są dodawane, gdy mysz lub dotknięcie zostanie przeciągnięte na mapie.
* Współrzędne `hybrid` są dodawane, gdy mysz lub dotknięcie zostanie kliknięte lub przeciągnięte.

## <a name="how-to-draw-shapes"></a>Jak rysować kształty

 Przed narysowaniem dowolnego kształtu ustaw opcję `drawingMode` w Menedżerze rysowania na obsługiwane ustawienie rysowania. To ustawienie może być zaprogramowane lub wywoływane przez naciśnięcie jednego z przycisków rysowania na pasku narzędzi. Tryb rysowania pozostaje włączony, nawet po narysowaniu kształtu, ułatwiając rysowanie dodatkowych kształtów tego samego typu. Programowo Ustaw tryb rysowania na stan bezczynności. Lub przejdź do stanu bezczynności, klikając przycisk bieżące tryby rysowania na pasku narzędzi.

Kolejne sekcje przedstawiają różne sposoby rysowania kształtów na mapie.

### <a name="how-to-draw-a-point"></a>Jak narysować punkt

Gdy Menedżer rysowania działa w trybie rysowania `draw-point`, można wykonać następujące akcje, aby narysować punkty na mapie. Metody te działają ze wszystkimi trybami interakcji.

**Rozpocznij rysowanie**
 - Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać punkt do mapy. 
 - Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany na współrzędnych wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Na myszy będzie mniej ruchu z powodu naciskania lewego przycisku myszy.
 - Kontynuuj klikanie, dotknięcie lub naciskając `F`, aby dodać więcej punktów do mapy.
 
**Zakończ rysowanie**
 - Kliknij dowolny przycisk na pasku narzędzi Rysowanie. 
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-line"></a>Jak narysować linię

Gdy Menedżer rysowania działa w trybie `draw-line`, można wykonać następujące akcje, aby rysować punkty na mapie, w zależności od trybu interakcji.

**Rozpocznij rysowanie**
 - Tryb kliknięcia
   * Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać każdy punkt linii na mapie. Współrzędna jest dodawana do wiersza dla każdego kliknięcia lub dotknięcia. 
   * Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany na współrzędnych wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Na myszy będzie mniej ruchu z powodu naciskania lewego przycisku myszy.
   * Kontynuuj klikaj do momentu dodania do wiersza odpowiednich punktów.
 - Tryb FreeHand
   * Naciśnij przycisk myszy w dół, a następnie przeciągnięcie na mapę i przeciągnij myszą lub wskaż punkt dotykowy. Współrzędne są dodawane do wiersza, ponieważ mysz lub punkt dotykowy przesuwa się wokół mapy. Gdy tylko zostanie wyzwolone zdarzenie myszy lub dotknięcia, rysunek zostanie ukończony. Częstotliwość dodawania współrzędnych jest definiowana przez menedżerów rysowania `freehandInterval` opcji.
 - Tryb hybrydowy
   * Alternatywa między metodami klikania i odfreehand podczas rysowania pojedynczego wiersza. Na przykład kliknij kilka punktów, następnie przytrzymaj i przeciągnij myszą, aby dodać wiele punktów, a następnie kliknij kilka więcej. 

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

Gdy Menedżer rysowania działa w trybie `draw-polygon`, można wykonać następujące akcje, aby rysować punkty na mapie, w zależności od trybu interakcji.

**Rozpocznij rysowanie**
 - Tryb kliknięcia
   * Kliknij lewy przycisk myszy lub dotknij mapy, aby dodać każdy punkt wielokąta na mapie. Współrzędna jest dodawana do wielokąta dla każdego kliknięcia lub dotknięcia. 
   * Jeśli wskaźnik myszy znajduje się na mapie, naciśnij klawisz `F`, a punkt zostanie dodany na współrzędnych wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Na myszy będzie mniej ruchu z powodu naciskania lewego przycisku myszy.
   * Kontynuuj klikaj do momentu, aż wszystkie żądane punkty zostaną dodane do wielokąta.
 - Tryb FreeHand
   * Naciśnij przycisk myszy w dół, a następnie przeciągnięcie na mapę i przeciągnij myszą lub wskaż punkt dotykowy. Współrzędne są dodawane do wielokąta, ponieważ mysz lub punkt dotykowy porusza się wokół mapy. Gdy tylko zostanie wyzwolone zdarzenie myszy lub dotknięcia, rysunek zostanie ukończony. Częstotliwość dodawania współrzędnych jest definiowana przez menedżerów rysowania `freehandInterval` opcji.
 - Tryb hybrydowy
   * Alternatywa między metodami klikania i odfreehand, jak to konieczne, podczas rysowania pojedynczego wielokąta. Na przykład kliknij kilka punktów, następnie przytrzymaj i przeciągnij myszą, aby dodać wiele punktów, a następnie kliknij kilka więcej. 

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

Gdy Menedżer rysowania działa w trybie `draw-rectangle`, można wykonać następujące akcje, aby rysować punkty na mapie, w zależności od trybu interakcji. Wygenerowany kształt będzie podążał [za rozszerzoną specyfikacją GEOJSON dla prostokątów](extend-geojson.md#rectangle).

**Rozpocznij rysowanie**
 - Naciśnij przycisk myszy w dół, aby dodać do mapy pierwszy narożnik prostokąta, a następnie przeciągnij, aby utworzyć prostokąt. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

### <a name="how-to-draw-a-circle"></a>Jak narysować okrąg

Gdy Menedżer rysowania działa w trybie `draw-circle`, można wykonać następujące akcje, aby rysować punkty na mapie, w zależności od trybu interakcji. Wygenerowany kształt będzie podążać [za rozszerzoną specyfikacją GEOJSON dla okręgów](extend-geojson.md#circle).

**Rozpocznij rysowanie**
 - Naciśnij przycisk myszy w dół lub kliknij pozycję styk na mapie, aby dodać środek okręgu, a następnie przeciągnij kółkiem do promienia. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo Ustaw tryb rysowania. 
 - Naciśnij klawisz `C`.

**Anuluj rysowanie**
 - Naciśnij klawisz `Escape`.

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Narzędzia do rysowania obsługują skróty klawiaturowe. Te skróty klawiaturowe są funkcjonalne, gdy mapa jest fokus.

| Klucz      | Akcja                            |
|----------|-----------------------------------|
| `C` | Kończy wszystkie rysunki, które są w toku i ustawia tryb rysowania jako bezczynny. Fokus zostanie przeniesiony do elementu mapy najwyższego poziomu.  |
| `Escape` | Anuluje wszystkie rysunki, które są w toku i ustawia tryb rysowania na bezczynny. Fokus zostanie przeniesiony do elementu mapy najwyższego poziomu.  |
| `F` | Dodaje współrzędną do punktu, linii lub wielokąta, gdy wskaźnik myszy znajduje się na mapie. Równoważna Akcja po kliknięciu mapy w trybie kliknij lub hybrydowym. Ten skrót pozwala na dokładniejsze i szybsze rysunki. Możesz użyć jednej z nich, aby ustawić mysz i inne, aby nacisnąć przycisk bez przesuwania myszy od gestu naciśnięcia przycisku. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas w module narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
