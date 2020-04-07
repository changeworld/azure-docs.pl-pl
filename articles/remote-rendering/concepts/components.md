---
title: Składniki
description: Definicja składników w zakresie zdalnego renderowania platformy Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681898"
---
# <a name="components"></a>Składniki

Renderowanie zdalne platformy Azure używa wzorca [systemu składników jednostki.](https://en.wikipedia.org/wiki/Entity_component_system) Podczas gdy [jednostki](entities.md) reprezentują położenie i hierarchiczny skład obiektów, składniki są odpowiedzialne za implementowanie zachowania.

Najczęściej używane typy komponentów to [komponenty siatki,](meshes.md)które dodają siatki do potoku renderowania. Podobnie, [lekkie komponenty](../overview/features/lights.md) są używane do dodawania oświetlenia, a [elementy płaszczyzny cięcia](../overview/features/cut-planes.md) są używane do cięcia otwartych siatek.

Wszystkie te komponenty używają transformacji (położenie, obrót, skala) elementu, do którego są dołączone, jako punkt odniesienia.

## <a name="working-with-components"></a>Praca z komponentami

Komponenty można łatwo dodawać, usuwać i manipulować komponentami programowo:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Składnik jest dołączony do jednostki w czasie tworzenia. Nie można go później przenieść do innego podmiotu. Składniki są jawnie `Component.Destroy()` usuwane z lub automatycznie, gdy jednostka właściciela składnika jest niszczona.

Tylko jedno wystąpienie każdego typu składnika mogą być dodawane do jednostki w czasie.

## <a name="unity-specific"></a>Specyficzne dla jedności

Integracja Unity ma dodatkowe funkcje rozszerzenia do interakcji ze składnikami. Zobacz [Obiekty i składniki gry Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Następne kroki

* [Granice obiektu](object-bounds.md)
* [Siatki](meshes.md)
