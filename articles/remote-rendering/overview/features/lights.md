---
title: Lampki
description: Opis i właściwości źródła światła
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680949"
---
# <a name="lights"></a>Lampki

Domyślnie zdalnie renderowane obiekty są oświetlone za pomocą [światła nieba](sky.md). W przypadku większości aplikacji jest to już wystarczające, ale można dodać do sceny kolejne źródła światła.

> [!IMPORTANT]
> Źródła światła mają wpływ wyłącznie na [materiały PBR.](pbr-materials.md) [Kolorowe materiały](color-materials.md) zawsze wydają się w pełni jasne.

> [!NOTE]
> Rzutowanie cieni nie jest obecnie obsługiwane. Renderowanie zdalne platformy Azure jest zoptymalizowane do renderowania ogromnych ilości geometrii, wykorzystując w razie potrzeby wiele procesorów GPU. Tradycyjne podejścia do rzucania cieni nie działają dobrze w takich scenariuszach.

## <a name="common-light-component-properties"></a>Wspólne właściwości komponentu światła

Wszystkie typy światła pochodzą z `LightComponent` abstrakcyjnej klasy podstawowej i współużytkują następujące właściwości:

* **Kolor:** Kolor światła w [przestrzeni Gamma](https://en.wikipedia.org/wiki/SRGB). Alfa jest ignorowana.

* **Intensywność:** Jasność światła. W przypadku świateł punktowych i punktowych intensywność określa również, jak daleko świeci światło.

## <a name="point-light"></a>Światło punktowe

W usłudze Azure `PointLightComponent` Remote Rendering można nie tylko emitować światło z jednego punktu, ale także z małej kuli lub małej rurki, aby symulować bardziej miękkie źródła światła.

### <a name="pointlightcomponent-properties"></a>Właściwości PointLightComponent

* **Promień:** Domyślny promień wynosi zero, w którym to przypadku światło działa jako światło punktowe. Jeśli promień jest większy od zera, działa jako sferyczne źródło światła, które zmienia wygląd świateł lustrzanych.

* **Długość:** Jeśli `Length` oba `Radius` i nie są zerowe, światło działa jak światło rury. Może to być wykorzystane do symulacji neonów.

* **TłumienieCutoff:** Pozostawienie (0,0) tłumienia światła zależy tylko od `Intensity`jego . Można jednak podać niestandardowe odległości min/max, na których intensywność światła jest skalowana liniowo do 0. Ta funkcja może służyć do wymuszania mniejszego zakresu wpływu określonego światła.

* **ProjectedCubemap:** Jeśli ustawiona jest prawidłowa [mapa modułu,](../../concepts/textures.md)tekstura jest rzutowana na otaczającą światło geometrię. Kolor mapy kostki jest modulowany kolorem światła.

## <a name="spot-light"></a>Światło punktowe

Jest `SpotLightComponent` podobny do `PointLightComponent` ale światło jest ograniczone do kształtu stożka. Orientacja stożka jest definiowana przez *ujemną oś Z jednostki właściciela*.

### <a name="spotlightcomponent-properties"></a>Właściwości SpotLightComponent

* **Promień:** Tak samo `PointLightComponent`jak w przypadku .

* **SpotAngleDeg:** Interwał ten określa wewnętrzny i zewnętrzny kąt stożka, mierzony w stopniu. Wszystko w wewnętrznym kącie jest oświetlone z pełną jasnością. Spadek jest stosowany w kierunku kąta zewnętrznego, który generuje efekt podobny do penumbry.

* **FalloffExponent:** Określa, jak ostro przechodzi spadek między wewnętrznym i zewnętrznym kątem stożka. Wyższa wartość skutkuje ostrzejszym przejściem. Wartość domyślna 1.0 powoduje przejście liniowe.

* **TłumienieCutoff:** Tak samo `PointLightComponent`jak w przypadku .

* **Rzut2dTexture:** Jeśli ustawiona jest prawidłowa [tekstura 2D,](../../concepts/textures.md)obraz jest rzutowany na geometrię, na którą świeci światło. Kolor tekstury jest modulowany kolorem światła.

## <a name="directional-light"></a>Światło kierunkowe

Symuluje `DirectionalLightComponent` źródło światła, które jest nieskończenie daleko. Światło świeci w kierunku *ujemnej osi Z jednostki właściciela*. Pozycja jednostki jest ignorowana.

Nie ma żadnych dodatkowych właściwości.

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Źródła światła mają znaczący wpływ na wydajność renderowania. Używaj ich ostrożnie i tylko wtedy, gdy jest to wymagane przez aplikację. Każdy statyczny globalny stan oświetlenia, w tym statyczny komponent kierunkowy, można osiągnąć za pomocą [niestandardowej tekstury nieba,](sky.md)bez dodatkowych kosztów renderowania.

## <a name="next-steps"></a>Następne kroki

* [Materiały](../../concepts/materials.md)
* [Niebo](sky.md)
