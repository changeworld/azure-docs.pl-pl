---
title: Typy interakcji narzędzi do rysowania i skróty klawiaturowe na mapie | Mapy platformy Microsoft Azure
description: Jak rysować i edytować kształty za pomocą myszy, ekranu dotykowego lub klawiatury w pakiecie Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198296"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Typy interakcji i skróty klawiaturowe w module narzędzi do rysowania

W tym artykule opisano wszystkie sposoby rysowania i edytowania kształtów za pomocą myszy, ekranu dotykowego lub skrótów klawiaturowych.

Menedżer rysunków obsługuje trzy różne sposoby interakcji z mapą, aby rysować kształty.

* `click`- Współrzędne są dodawane po kliknięciu myszy lub dotyku.
* `freehand `- Współrzędne są dodawane, gdy mysz lub dotyk jest przeciągany na mapie.
* `hybrid`- Współrzędne są dodawane po kliknięciu lub przecięciu myszy lub dotyku.

## <a name="how-to-draw-shapes"></a>Jak rysować kształty

 Przed narysowaniem dowolnego kształtu `drawingMode` należy ustawić opcję menedżera rysunku na obsługiwane ustawienie rysunku. To ustawienie można zaprogramować lub wywołać, naciskając jeden z przycisków rysowania na pasku narzędzi. Tryb rysowania pozostaje włączony, nawet po narysowaniu kształtu, co ułatwia rysowanie dodatkowych kształtów tego samego typu. Programowo ustaw tryb rysowania na stan bezczynności. Możesz też przełączyć się na stan bezczynności, klikając przycisk bieżące tryby rysowania na pasku narzędzi.

W następnych sekcjach przedstawiono wszystkie różne sposoby rysowania kształtów na mapie.

### <a name="how-to-draw-a-point"></a>Jak narysować punkt

Gdy menedżer rysunku `draw-point` jest w trybie rysowania, można wykonać następujące czynności, aby narysować punkty na mapie. Metody te działają ze wszystkimi trybami interakcji.

**Rozpocznij rysowanie**
 - Kliknij lewym przyciskiem myszy lub dotknij mapy, aby dodać punkt do mapy. 
 - Jeśli mysz znajduje się nad `F` mapą, naciśnij klawisz, a punkt zostanie dodany na współrzędnej wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Będzie mniej ruchu na myszy ze względu na ruch naciskając lewy przycisk myszy.
 - Klikaj, dotykaj `F` lub naciskaj, aby dodać więcej punktów do mapy.
 
**Zakończ rysowanie**
 - Kliknij dowolny przycisk na pasku narzędzi rysowania. 
 - Programowo ustaw tryb rysowania. 
 - Naciśnij `C` klawisz.

**Anuluj rysunek**
 - Naciśnij `Escape` klawisz.

### <a name="how-to-draw-a-line"></a>Jak narysować linię

Gdy menedżer rysunku `draw-line` jest w trybie, można wykonać następujące czynności, aby narysować punkty na mapie, w zależności od trybu interakcji.

**Rozpocznij rysowanie**
 - Tryb klikania
   * Kliknij lewym przyciskiem myszy lub dotknij mapy, aby dodać każdy punkt linii na mapie. Współrzędna jest dodawana do linii dla każdego kliknięcia lub dotyku. 
   * Jeśli mysz znajduje się nad `F` mapą, naciśnij klawisz, a punkt zostanie dodany na współrzędnej wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Będzie mniej ruchu na myszy ze względu na ruch naciskając lewy przycisk myszy.
   * Klikaj, aż do linii nie dodano wszystkich żądanych punktów.
 - Tryb odręczny
   * Naciśnij lewy przycisk myszy lub dotknij mapy i przeciągnij mysz lub punkt dotykowy. Współrzędne są dodawane do linii, gdy mysz lub punkt dotykowy porusza się po mapie. Po wyzwoleniu myszy lub zdarzenia retuszu rysunek zostanie zakończony. Częstotliwość dodawania współrzędnych jest definiowana przez opcję menedżerów rysunku. `freehandInterval`
 - Tryb hybrydowy
   * Alternatywnie między metody klikania i odręczne, zgodnie z potrzebami, podczas rysowania jednej linii. Na przykład kliknij kilka punktów, a następnie przytrzymaj i przeciągnij mysz, aby dodać kilka punktów, a następnie kliknij kilka innych. 

**Zakończ rysowanie**
 - Tryb hybrydowy/click
   * Kliknij dwukrotnie mapę w ostatnim punkcie. 
   * Kliknij dowolny przycisk na pasku narzędzi rysowania. 
   * Programowo ustaw tryb rysowania. 
 - Tryb odręczny
   * Zwolnij przycisk myszy lub punkt dotykowy.
 - Naciśnij `C` klawisz.

**Anuluj rysunek**
 - Naciśnij `Escape` klawisz.

### <a name="how-to-draw-a-polygon"></a>Jak narysować wielokąt

Gdy menedżer rysunku `draw-polygon` jest w trybie, można wykonać następujące czynności, aby narysować punkty na mapie, w zależności od trybu interakcji.

**Rozpocznij rysowanie**
 - Tryb klikania
   * Kliknij lewym przyciskiem myszy lub dotknij mapy, aby dodać każdy punkt wielokąta na mapie. Współrzędna jest dodawana do wielokąta dla każdego kliknięcia lub dotyku. 
   * Jeśli mysz znajduje się nad `F` mapą, naciśnij klawisz, a punkt zostanie dodany na współrzędnej wskaźnika myszy. Ta metoda zapewnia większą dokładność dodawania punktu do mapy. Będzie mniej ruchu na myszy ze względu na ruch naciskając lewy przycisk myszy.
   * Klikaj, aż do wielokąta dołognie doliczono wszystkie żądane punkty.
 - Tryb odręczny
   * Naciśnij lewy przycisk myszy lub dotknij mapy i przeciągnij mysz lub punkt dotykowy. Współrzędne są dodawane do wielokąta, gdy mysz lub punkt dotykowy porusza się po mapie. Po wyzwoleniu myszy lub zdarzenia retuszu rysunek zostanie zakończony. Częstotliwość dodawania współrzędnych jest definiowana przez opcję menedżerów rysunku. `freehandInterval`
 - Tryb hybrydowy
   * Alternatywnie między metody klikania i odręczne, zgodnie z potrzebami, podczas rysowania jednego wielokąta. Na przykład kliknij kilka punktów, a następnie przytrzymaj i przeciągnij mysz, aby dodać kilka punktów, a następnie kliknij kilka innych. 

**Zakończ rysowanie**
 - Tryb hybrydowy/click
   * Kliknij dwukrotnie mapę w ostatnim punkcie. 
   * Kliknij pierwszy punkt wielokąta.
   * Kliknij dowolny przycisk na pasku narzędzi rysowania. 
   * Programowo ustaw tryb rysowania. 
 - Tryb odręczny
   * Zwolnij przycisk myszy lub punkt dotykowy.
 - Naciśnij `C` klawisz.

**Anuluj rysunek**
 - Naciśnij `Escape` klawisz.

### <a name="how-to-draw-a-rectangle"></a>Jak narysować prostokąt

Gdy menedżer rysunku `draw-rectangle` jest w trybie, można wykonać następujące czynności, aby narysować punkty na mapie, w zależności od trybu interakcji. Wygenerowany kształt będzie zgodny z [rozszerzoną specyfikacją GeoJSON dla prostokątów](extend-geojson.md#rectangle).

**Rozpocznij rysowanie**
 - Naciśnij lewy przycisk myszy lub dotknij mapy, aby dodać pierwszy narożnik prostokąta i przeciągnąć, aby utworzyć prostokąt. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo ustaw tryb rysowania. 
 - Naciśnij `C` klawisz.

**Anuluj rysunek**
 - Naciśnij `Escape` klawisz.

### <a name="how-to-draw-a-circle"></a>Jak narysować okrąg

Gdy menedżer rysunku `draw-circle` jest w trybie, można wykonać następujące czynności, aby narysować punkty na mapie, w zależności od trybu interakcji. Wygenerowany kształt będzie zgodny z [rozszerzoną specyfikacją GeoJSON dla okręgów](extend-geojson.md#circle).

**Rozpocznij rysowanie**
 - Naciśnij lewy przycisk myszy lub dotknij mapy, aby dodać środek okręgu i przeciągnąć, nadać okręgom promień. 

**Zakończ rysowanie**
 - Zwolnij przycisk myszy lub punkt dotykowy.
 - Programowo ustaw tryb rysowania. 
 - Naciśnij `C` klawisz.

**Anuluj rysunek**
 - Naciśnij `Escape` klawisz.

## <a name="keyboard-shortcuts"></a>Skróty klawiaturowe

Narzędzia do rysowania obsługują skróty klawiaturowe. Te skróty klawiaturowe działają, gdy mapa jest w centrum uwagi.

| Klucz      | Akcja                            |
|----------|-----------------------------------|
| `C` | Kończy każdy rysunek, który jest w toku i ustawia tryb rysowania na bezczynny. Fokus zostanie przesuń do elementu mapy najwyższego poziomu.  |
| `Escape` | Anuluje każdy rysunek, który jest w toku i ustawia tryb rysowania na bezczynny. Fokus zostanie przesuń do elementu mapy najwyższego poziomu.  |
| `F` | Dodaje współrzędną do punktu, linii lub wielokąta, jeśli mysz znajduje się nad mapą. Równoważne działanie kliknięcia mapy w trybie kliknięcia lub hybrydowego. Ten skrót pozwala na bardziej precyzyjne i szybsze rysunki. Możesz użyć jednej ręki, aby ustawić mysz, a drugą, aby nacisnąć przycisk bez przesuwania myszy z gestu naciśnięcia. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach w module narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Kierownik rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Pasek narzędzi rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
