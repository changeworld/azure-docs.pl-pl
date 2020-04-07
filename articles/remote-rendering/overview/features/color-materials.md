---
title: Materiały kolorowe
description: Opisuje typ materiału koloru.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 7cbcaefcc087c9f1c7c09668a27fbdef9a4802d3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681079"
---
# <a name="color-materials"></a>Materiały kolorowe

*Materiały kolorów* są jednym z obsługiwanych [typów materiałów](../../concepts/materials.md) w renderowaniu zdalnym platformy Azure. Są one używane do [oczek,](../../concepts/meshes.md) które nie powinny otrzymywać żadnego rodzaju oświetlenia, ale raczej być pełną jasność przez cały czas. Może tak być w przypadku materiałów "świecących", takich jak deski rozdzielcze samochodów, żarówki lub dane, które już zawierają oświetlenie statyczne, takie jak modele uzyskane za pomocą [fotogrametrii.](https://en.wikipedia.org/wiki/Photogrammetry)

Materiały kolorowe są bardziej wydajne do renderowania niż [materiały PBR](pbr-materials.md) ze względu na ich prostszy model cieniowania. Obsługują one również różne tryby przezroczystości.

## <a name="common-material-properties"></a>Wspólne właściwości materiału

Właściwości te są wspólne dla wszystkich materiałów:

* **albedoColor:** Ten kolor jest mnożony z innymi kolorami, takimi jak *albedoMap* lub *kolory wierzchołków*. Jeśli *przezroczystość* jest włączona na materiale, kanał alfa jest `1` używany do regulacji `0` krycia, co oznacza, że jest w pełni nieprzezroczysty i oznacza w pełni przezroczysty. Wartość domyślna to biały.

  > [!NOTE]
  > Ponieważ materiały kolorowe nie odzwierciedlają środowiska, w pełni przezroczysty materiał kolorowy staje się niewidoczny. Inaczej jest w przypadku [materiałów PBR.](pbr-materials.md)

* **albedoMap:** [Tekstura 2D](../../concepts/textures.md) dla wartości albedo na piksel.

* **alphaClipEnabled** i **alphaClipThreshold:** Jeśli *alphaClipEnabled* jest prawdziwe, wszystkie piksele, w których wartość alfa albedo jest niższa niż *alfaClipThreshold* nie zostaną narysowane. Przycinanie alfa może być używane nawet bez włączania przezroczystości i jest znacznie szybsze do renderowania. Alpha obcięte materiały są nadal wolniejsze do renderowania niż w pełni nieprzezroczyste materiały, choć. Domyślnie przycinanie alfa jest wyłączone.

* **textureCoordinateScale** i **textureCoordinateOffset:** Skala jest mnożona do współrzędnych tekstury UV, do niej dodaje się przesunięcie. Może być używany do rozciągania i przesuwania tekstur. Domyślna skala to (1, 1), a przesunięcie to (0, 0).

* **useVertexColor:** Jeśli siatka zawiera kolory wierzchołków i ta opcja jest włączona, kolory wierzchołków siatki są mnożone do *albedoColor* i *albedoMap*. Domyślnie kolory wierzchołków są wyłączone.

* **isDoubleSided:** Jeśli dwustronność jest ustawiona na true, trójkąty z tym materiałem są renderowane, nawet jeśli aparat patrzy na ich tylne ściany. Domyślnie ta opcja jest wyłączona. Zobacz też [Renderowanie jednostronne](single-sided-rendering.md).

## <a name="color-material-properties"></a>Właściwości materiału koloru

Następujące właściwości są specyficzne dla materiałów kolorowych:

* **wierzchołekMik:** Ta wartość `0` `1` między i określa, jak silnie kolor wierzchołka w [siatce](../../concepts/meshes.md) przyczynia się do ostatecznego koloru. Przy wartości domyślnej 1 kolor wierzchołka jest w pełni mnożony do koloru albedo. Przy wartości 0 kolory wierzchołków są całkowicie ignorowane.

* **transparencyMode:** W przeciwieństwie do [materiałów PBR,](pbr-materials.md)materiały kolorowe rozróżniają różne tryby przezroczystości:

  1. **Nieprzezroczyste:** Tryb domyślny wyłącza przezroczystość. Przycinanie alfa jest nadal możliwe, choć, i powinny być preferowane, jeśli wystarczy.
  
  1. **AlphaBlended:** Ten tryb jest podobny do trybu przezroczystości dla materiałów PBR. Powinien być stosowany do przezroczystych materiałów, takich jak szkło.

  1. **Dodatek:** Ten tryb jest najprostszym i najbardziej wydajnym trybem przezroczystości. Wkład materiału jest dodawany do renderowanego obrazu. W tym trybie można symulować świecące (ale nadal przezroczyste) obiekty, takie jak znaczniki używane do wyróżniania ważnych obiektów.

## <a name="next-steps"></a>Następne kroki

* [Materiały PBR](pbr-materials.md)
* [Tekstury](../../concepts/textures.md)
* [Siatki](../../concepts/meshes.md)
