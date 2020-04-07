---
title: Zastępowanie stanu hierarchicznego
description: W tym artykule wyjaśniono pojęcie hierarchicznego zastępowania składników stanu.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680793"
---
# <a name="hierarchical-state-override"></a>Zastępowanie stanu hierarchicznego

W wielu przypadkach konieczna jest dynamiczna zmiana wyglądu części [modelu](../../concepts/models.md), na przykład ukrywanie subgrafów lub przełączanie części do przezroczystego renderowania. Zmiana materiałów każdej z zaangażowanych części nie jest praktyczna, ponieważ wymaga iteracji całego wykresu sceny i zarządzania klonowaniem materiału i przypisywaniem w każdym węźle.

Aby osiągnąć ten przypadek użycia przy jak `HierarchicalStateOverrideComponent`najmniejszym możliwym narzucie, użyj pliku . Ten składnik implementuje hierarchiczne aktualizacje stanu na dowolnych gałęziach wykresu sceny. Oznacza to, że stan może być zdefiniowany na dowolnym poziomie na wykresie sceny i spływa w dół hierarchii, dopóki nie zostanie zastąpiony przez nowy stan lub zastosowany do obiektu liścia.

Na przykład należy wziąć pod uwagę model samochodu i chcesz przełączyć cały samochód na przezroczysty, z wyjątkiem wewnętrznej części silnika. Ten przypadek użycia obejmuje tylko dwa wystąpienia składnika:

* Pierwszy komponent jest przypisany do węzła głównego modelu i włącza przezroczyste renderowanie dla całego samochodu.
* Drugi składnik jest przypisany do węzła głównego aparatu i zastępuje stan ponownie przez jawne wyłączenie trybu przezroczystego.

## <a name="features"></a>Funkcje

Stały zestaw stanów, które mogą być zastąpione są:

* **Ukryte**: Odpowiednie siatki na wykresie sceny są ukryte lub pokazane.
* **Kolor tinty:** Renderowany obiekt może być barwiony kolorem o indywidualnym kolorze odcienia i wadze odcienia. Poniższy obraz eksploruje kolor barwienia obręczy koła.
  
  ![Odcień koloru](./media/color-tint.png)

* **Przezroczyste**: Geometria jest renderowana półprzezroczysto, na przykład w celu ujawnienia wewnętrznych części obiektu. Na poniższej ilustracji przedstawiono cały samochód renderowany w trybie przezroczystym, z wyjątkiem czerwonego zacisku hamulca:

  ![Przezroczyste](./media/see-through.png)

  > [!IMPORTANT]
  > Efekt przezroczystości działa tylko wtedy, gdy używany jest [tryb renderowania](../../concepts/rendering-modes.md) *TileBasedComposition.*

* **Zaznaczone**: Geometria jest renderowana z [konspektem zaznaczenia](outlines.md).

  ![Konspekt zaznaczenia](./media/selection-outline.png)

* **DisableCollision**: Geometria jest zwolniona z [zapytań przestrzennych](spatial-queries.md). **Ukryta** flaga nie wyłącza kolizji, więc te dwie flagi są często ustawione razem.

## <a name="hierarchical-overrides"></a>Zastąpienia hierarchiczne

Można `HierarchicalStateOverrideComponent` dołączyć na wielu poziomach hierarchii obiektów. Ponieważ w jednostce może istnieć tylko jeden `HierarchicalStateOverrideComponent` składnik każdego typu, każdy zarządza stanami dla ukrytych, przezroczystych, wybranych, odcienia kolorów i kolizji.

W związku z tym każdy stan można ustawić na jeden z:

* `ForceOn`- stan jest włączony dla całej siatki na i poniżej tego węzła
* `ForceOff`- stan jest wyłączony dla wszystkich oczek na i poniżej tego węzła
* `InheritFromParent`- ten składnik zastępowania nie ma wpływu na stan

Stany można zmieniać bezpośrednio lub `SetState` za pomocą funkcji:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Kolor tinty

Zastąpienie koloru odcienia jest nieco specjalne, ponieważ istnieje zarówno stan włączania/ wyłączania/dziedziczenia, jak i właściwości koloru tinty. Część alfa koloru odcienia definiuje masę efektu barwienia: Jeśli ustawiono na 0,0, nie jest widoczny kolor odcienia, a jeśli ustawiono na 1,0, obiekt będzie renderowany czystym kolorem odcienia. W przypadku wartości in-between kolor końcowy zostanie zmieszany z kolorem tinty. Kolor odcienia można zmieniać na podstawie klatki, aby uzyskać animację kolorów.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Samo wystąpienie `HierarchicalStateOverrideComponent` nie dodaje wiele nakładów pracy. Jednak zawsze dobrą praktyką jest utrzymanie niskiej liczby aktywnych składników. Na przykład podczas implementowania systemu zaznaczania, który wyróżnia pobrany obiekt, zaleca się usunięcie składnika po usunięciu podświetlenia. Utrzymywanie komponentów wokół z neutralnymi funkcjami może szybko się sumować.

Przezroczyste renderowanie powoduje większe obciążenie procesorów GPU serwera niż standardowe renderowanie. Jeśli duże części wykresu sceny są przełączane na *przezroczyste,* a wiele warstw geometrii jest widocznych, może stać się wąskim gardłem wydajności. To samo dotyczy obiektów z [konturami zaznaczenia](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Następne kroki

* [Kontury](../../overview/features/outlines.md)
* [Tryby renderowania](../../concepts/rendering-modes.md)
* [Zapytania przestrzenne](../../overview/features/spatial-queries.md)
