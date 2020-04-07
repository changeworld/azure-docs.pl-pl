---
title: Renderowanie konspektu
description: W tym artykule wyjaśniono, jak renderować konspekt zaznaczenia
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680832"
---
# <a name="outline-rendering"></a>Renderowanie konspektu

Zaznaczone obiekty można wyróżnić wizualnie, dodając renderowanie konspektu za pomocą [komponentu Zastępowanie stanu hierarchicznego](../../overview/features/override-hierarchical-state.md). W tym rozdziale wyjaśniono, jak globalne parametry renderowania konspektu są zmieniane za pośrednictwem interfejsu API klienta.

Właściwości konspektu są ustawieniem globalnym. Wszystkie obiekty korzystające z renderowania konspektu będą używać tego samego ustawienia — nie jest możliwe użycie koloru konturu dla obiektu.

## <a name="parameters-for-outlinesettings"></a>Parametry dla`OutlineSettings`

Klasa `OutlineSettings` przechowuje ustawienia związane z właściwościami konspektu globalnego. Udostępnia następujące elementy członkowskie:

| Parametr      | Typ    | Opis                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Kolor4Ub | Kolor używany do rysowania konturu. Część alfa jest ignorowana.         |
| `PulseRateHz`    | float   | Szybkość, z jaką zarys oscyluje na sekundę|
| `PulseIntensity` | float   | Intensywność efektu impulsu konspektu. Musi być między 0,0 dla bez pulsowania i 1,0 dla pełnego pulsowania. Intensywność niejawnie ustawia minimalną krycie `MinOpacity = 1.0 - PulseIntensity`konspektu jako . |

![Konturuje](./media/outlines.png) Efekt zmiany `color` parametru z żółtego (po lewej) `pulseIntensity` na karmazynowy (środek) i od 0 do 0,8 (po prawej).

## <a name="example"></a>Przykład

Poniższy kod przedstawia przykład ustawiania parametrów konspektu za pośrednictwem interfejsu API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Wydajność

Renderowanie konspektu może mieć znaczący wpływ na wydajność renderowania. Wpływ ten różni się w zależności od relacji przestrzennej przestrzeni ekranu między wybranymi i niewybranymi obiektami dla danej klatki.

## <a name="next-steps"></a>Następne kroki

* [Składnik zastępowania stanu hierarchicznego](../../overview/features/override-hierarchical-state.md)
