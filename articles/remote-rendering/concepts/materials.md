---
title: Materiały
description: Renderowanie opisu materiału i właściwości materiału
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.openlocfilehash: 8551e17ddd71e76aca0c85b9768f564ae0e5f049
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681846"
---
# <a name="materials"></a>Materiały

Materiały są [zasobami udostępnionymi,](../concepts/lifetime.md) które definiują sposób renderowania [siatek.](meshes.md) Materiały służą do określania, które [tekstury](textures.md) mają być stosowane, czy obiekty mają być przezroczyste i jak będzie obliczane oświetlenie.

Materiały są tworzone automatycznie podczas [konwersji modelu](../how-tos/conversion/model-conversion.md) i są dostępne w czasie wykonywania. Można również tworzyć materiały niestandardowe na podstawie kodu i zastąpić istniejące. Ten scenariusz ma szczególnie sens, jeśli chcesz udostępnić ten sam materiał w wielu siatce. Ponieważ modyfikacje materiału są widoczne na każdej siatce, która się do niego odwołuje, metoda ta może być używana do łatwego stosowania zmian.

> [!NOTE]
> Niektóre przypadki użycia, takie jak wyróżnianie wybranego obiektu, można wykonać, modyfikując materiały, ale są znacznie łatwiejsze do osiągnięcia za pomocą [hierarchicznego komponentu .](../overview/features/override-hierarchical-state.md)

## <a name="material-types"></a>Rodzaje materiałów

Renderowanie zdalne platformy Azure ma dwa różne typy materiałów:

* [Materiały PBR](../overview/features/pbr-materials.md) są stosowane do powierzchni, które powinny być renderowane jako fizycznie poprawne, jak to możliwe. Realistyczne oświetlenie jest obliczane dla tych materiałów przy użyciu *renderowania opartego fizycznie* (PBR). Aby w pełni wykorzystać ten rodzaj materiału, ważne jest, aby zapewnić wysokiej jakości dane wejściowe, takie jak chropowatość i normalne mapy.

* [Materiały kolorowe](../overview/features/color-materials.md) są stosowane w przypadkach, w których nie jest wymagane dodatkowe oświetlenie. Materiały te są zawsze w pełni jasne i łatwiejsze w konfiguracji. Kolorowe materiały są używane do danych, które powinny albo nie mieć oświetlenia w ogóle, lub już zawiera oświetlenie statyczne, takie jak modele uzyskane za pomocą [fotogrametrii](https://en.wikipedia.org/wiki/Photogrammetry).

## <a name="mesh-vs-meshcomponent-material-assignment"></a>Przypisanie materiału siatki a siatki

[Siatki](meshes.md) mają jeden lub więcej podmeshes. Każdy submesh odwołuje się do jednego materiału. Można zmienić materiał, który ma być używany bezpośrednio w siatce, lub można zastąpić materiał, który ma być używany dla podmesh na [MeshComponent](meshes.md#meshcomponent).

Podczas modyfikowania materiału bezpośrednio na zasób siatki, ta zmiana dotyczy wszystkich wystąpień tej siatki. Zmiana go na MeshComponent, jednak wpływa tylko na to jedno wystąpienie siatki. Metoda jest bardziej odpowiednie zależy od żądanego zachowania, ale modyfikowanie MeshComponent jest bardziej powszechne podejście.

## <a name="material-classes"></a>Klasy materiałów

Wszystkie materiały dostarczone przez INTERFEJS API `Material`pochodzą z klasy podstawowej . Ich typ można zbadać `Material.MaterialSubType` za pośrednictwem lub rzucając je bezpośrednio:

``` cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if( pbrMat!= null )
    {
        PbrMaterial pbrMaterial = material.PbrMaterial.Value;
        pbrMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

## <a name="next-steps"></a>Następne kroki

* [Materiały PBR](../overview/features/pbr-materials.md)
* [Materiały kolorowe](../overview/features/color-materials.md)
