---
title: Konfigurowanie wysokiej dostępności — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Jak włączyć lub wyłączyć wysoką dostępność
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977669"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurowanie wysokiej dostępności w hiperskali (Citus)

Usługa Azure Database for PostgreSQL — hiperskali (Citus) zapewnia wysoką dostępność (HA), aby uniknąć przestojów bazy danych. Po włączeniu wysokiej usługi każdy węzeł w grupie serwerów otrzyma stan wstrzymania. Jeśli oryginalny węzeł stanie się w złej kondycji, jego rezerwy zostanie awansowany do zastąpienia go.

> [!IMPORTANT]
> Ponieważ ha podwaja liczbę serwerów w grupie, będzie również podwoić koszt.

Włączenie wysokiej wartości administracyjnej jest możliwe podczas tworzenia grupy serwerów lub później na karcie **Konfigurowanie** dla grupy serwerów w witrynie Azure portal. Interfejs użytkownika wygląda podobnie w obu przypadkach. Przeciągnij suwak, **aby uzyskać wysoką dostępność,** do POZIOMU TAK:

![ha suwak](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kliknij przycisk **Zapisz,** aby zastosować wybór. Włączenie zasobów administracyjnych może zająć trochę czasu, ponieważ rezerwy grupy serwerów są w stanie gotowości i przesyła do nich dane.

Karta **Przegląd** dla grupy serwerów wyświetli listę wszystkich węzłów i ich rezerw, wraz z kolumną **Wysoka dostępność** wskazującą, czy funkcja ha została pomyślnie włączona dla każdego węzła.

![kolumna ha w przeglądzie grupy serwerów](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wysokiej dostępności](concepts-hyperscale-high-availability.md).
