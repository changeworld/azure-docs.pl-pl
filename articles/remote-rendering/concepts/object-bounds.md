---
title: Granice obiektu
description: Wyjaśnia, w jaki sposób można wyszukiwać granice obiektów przestrzennych
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681716"
---
# <a name="object-bounds"></a>Granice obiektu

Granice obiektu reprezentują wolumin, który zajmuje [encja](entities.md) i jej element podrzędny. W usłudze Azure Remote Rendering granice obiektów są zawsze podane jako *pola ograniczające wyrównane osi* (AABB). Granice obiektu mogą znajdować się w *przestrzeni lokalnej* lub w *przestrzeni światowej.* Tak czy inaczej, są one zawsze wyrównane do osi, co oznacza, że zakresy i objętość mogą się różnić między reprezentacją przestrzeni lokalnej i światowej.

## <a name="querying-object-bounds"></a>Wykonywanie zapytań o granice obiektu

Lokalny AABB [siatki](meshes.md) można zbadać bezpośrednio z zasobu siatki. Te granice mogą zostać przekształcone w przestrzeń lokalną lub przestrzeń świata jednostki przy użyciu transformacji jednostki.

Istnieje możliwość obliczenia granic całej hierarchii obiektów w ten sposób, ale wymaga przechodzenia przez hierarchię, kwerendy granice dla każdej siatki i połączyć je ręcznie. Ta operacja jest zarówno żmudne i nieefektywne.

Lepszym sposobem jest `QueryLocalBoundsAsync` wywołanie lub `QueryWorldBoundsAsync` na jednostkę. Obliczenia są następnie odciążane do serwera i zwracane z minimalnym opóźnieniem.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>Następne kroki

* [Zapytania przestrzenne](../overview/features/spatial-queries.md)
