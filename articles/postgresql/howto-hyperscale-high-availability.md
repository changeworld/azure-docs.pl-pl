---
title: Konfigurowanie wysokiej dostępności dla grupy serwerów Azure Database for PostgreSQL-Citus)
description: Jak włączyć lub wyłączyć wysoką dostępność
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5ed29be1e890ddf2c4208ce9c03f01ce44f0e0d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515917"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Konfigurowanie wysokiej dostępności (Citus)

Azure Database for PostgreSQL-Citus) zapewnia wysoką dostępność (HA), aby uniknąć przestoju bazy danych. W przypadku włączenia wysokiej dostępności każdy węzeł w grupie serwerów będzie miał stan wstrzymania. Jeśli oryginalny węzeł ulegnie złej kondycji, jego stan wstrzymania zostanie podwyższony do jego zastąpienia.

> [!IMPORTANT]
> Ze względu na to, że HA podwaja liczbę serwerów w grupie, zostanie również podwojony koszt.

Włączenie wysokiej dostępności jest możliwe podczas tworzenia grupy serwerów lub później na karcie **Konfiguracja** dla grupy serwerów w Azure Portal. Interfejs użytkownika wygląda podobnie w każdym przypadku. Przeciągnij suwak, aby zapewnić **wysoką dostępność** , Włącz lub Wyłącz:

![suwak ha](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Kliknij przycisk **Zapisz** , aby zastosować wybór. Włączenie wysokiej dostępności może zająć trochę czasu, ponieważ Grupa serwerów inicjuje rezerwę i przesyła strumieniowo do nich dane.

Na karcie **Przegląd** dla grupy serwerów zostaną wystawione wszystkie węzły i ich wstrzymania wraz z kolumną **o wysokiej dostępności** , która wskazuje, czy ha została pomyślnie włączona dla każdego węzła.

### <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wysokiej dostępności](concepts-hyperscale-high-availability.md).
