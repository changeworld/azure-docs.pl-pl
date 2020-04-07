---
title: Siatki
description: Definicja siatek w zakresie zdalnego renderowania platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681768"
---
# <a name="meshes"></a>Siatki

## <a name="mesh-resource"></a>Zasób siatki

Siatki są niezmiennym [zasobem udostępnionym,](../concepts/lifetime.md)który można tworzyć tylko poprzez [konwersję modelu.](../how-tos/conversion/model-conversion.md) Siatki zawierają jeden lub wiele *podmeshes*. Każdy podmesh odwołuje się do [materiału,](materials.md) z którym powinien być renderowany domyślnie. Aby umieścić siatkę w przestrzeni 3D, dodaj [siatkęsą](#meshcomponent) do [elementu](entities.md).

### <a name="mesh-resource-properties"></a>Właściwości zasobów siatki

Właściwości `Mesh` klasy to:

* **Materiały:** Tablica materiałów. Każdy materiał jest używany przez inny submesh. Wiele wpisów w tablicy może odwoływać się do tego samego [materiału](materials.md). Tych danych nie można modyfikować w czasie wykonywania.

* **Granice:** Obwiednia osi lokalnej (AABB) wierzchołków siatki.

## <a name="meshcomponent"></a>MeshComponent (Szesedastywent

Klasa `MeshComponent` jest używana do umieszczania wystąpienia zasobu siatki. Każdy MeshComponent odwołuje się do pojedynczej siatki. Może zastąpić, które materiały są używane do renderowania każdego podmesh.

### <a name="meshcomponent-properties"></a>Właściwości MeshComponent

* **Siatka:** Zasób siatki używany przez ten składnik.

* **Materiały:** Tablica materiałów określona w samym komponencie siatki. Tablica będzie zawsze miała taką samą długość jak tablica *Materiały* w zasobie siatki. Materiały, które nie zostaną zastąpione z domyślnej siatki, są ustawione na *wartość null* w tej tablicy.

* **UżywaneMateriały:** Tablica faktycznie używanych materiałów dla każdego podmesh. Będzie identyczna z danymi w *tablicy Materials* dla wartości innych niż null. W przeciwnym razie zawiera wartość z *tablicy Materials* w wystąpieniu siatki.

## <a name="next-steps"></a>Następne kroki

* [Materiały](materials.md)
