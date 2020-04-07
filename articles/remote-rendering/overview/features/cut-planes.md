---
title: Płaszczyzny cięte
description: Wyjaśnia, czym są cięte płaszczyzny i jak z nich korzystać
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681027"
---
# <a name="cut-planes"></a>Płaszczyzny cięte

*Płaszczyzna cięcia* to funkcja wizualna, która przycina piksele po jednej stronie wirtualnej płaszczyzny, odsłaniając [wnętrze siatek.](../../concepts/meshes.md)
Poniższy obraz ekspozycyjnie pokazuje efekt. Po lewej stronie znajduje się oryginalna siatka, po prawej można zajrzeć do siatki:

![Płaszczyzna cięcia](./media/cutplane-1.png)

## <a name="limitations"></a>Ograniczenia

* Na razie zdalne renderowanie platformy Azure obsługuje **maksymalnie osiem aktywnych płaszczyzn cięcia.** Można utworzyć więcej komponentów płaszczyzny cięcia, ale jeśli spróbujesz włączyć więcej jednocześnie, zignoruje aktywację. Najpierw wyłącz inne płaszczyzny, jeśli chcesz przełączyć komponent, który powinien mieć wpływ na scenę.
* Każda płaszczyzna cięcia wpływa na wszystkie zdalnie renderowane obiekty. Obecnie nie można wykluczyć określonych obiektów lub części siatki.
* Wycięte płaszczyzny są wyłącznie funkcją wizualną, nie wpływają na wynik [zapytań przestrzennych.](spatial-queries.md) Jeśli chcesz promieniować do siatki z otwartym cięciem, możesz dostosować punkt początkowy promienia, aby znajdował się na płaszczyźnie cięcia. W ten sposób promień może trafić tylko widoczne części.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Każda aktywna płaszczyzna cięcia wiąże się z niewielkimi kosztami podczas renderowania. Wyłącz lub usuń wycięte płaszczyzny, gdy nie są potrzebne.

## <a name="cutplanecomponent"></a>CutPlaneComponent (Skomponent)

Płaszczyznę cięcia można dodać do sceny, tworząc *przycisk CutPlaneComponent*. Położenie i orientacja płaszczyzny jest określana przez [jednostkę](../../concepts/entities.md)właściciela komponentu .

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Właściwości CutPlaneComponent

Następujące właściwości są widoczne na składniku płaszczyzny cięcia:

* **Włączono:** Można tymczasowo wyłączyć płaszczyzny odcięcia, wyłączając komponent. Wyłączone płaszczyzny cięcia nie ponoszą narzutów renderowania, a także nie są wliczane do globalnego limitu płaszczyzny cięcia.

* **Normalny:** Określa, który kierunek (+X,-X,+Y,-Y,+Z,-Z) jest używany jako normalna płaszczyzna. Ten kierunek jest względem orientacji jednostki właściciela. Przenoszenie i obracanie jednostki właściciela w celu dokładnego umieszczenia.

* **FadeColor** i **FadeLength:**

  Jeśli wartość alfa *FadeColor* jest niezerowa, piksele w pobliżu płaszczyzny cięcia znikną w kierunku części RGB FadeColor. Siła kanału alfa określa, czy będzie całkowicie zanikać w kierunku koloru zanikania, czy tylko częściowo. *FadeLength* określa, w jakiej odległości nastąpi to zanikanie.

## <a name="next-steps"></a>Następne kroki

* [Renderowanie jednostronne](single-sided-rendering.md)
* [Zapytania przestrzenne](spatial-queries.md)
