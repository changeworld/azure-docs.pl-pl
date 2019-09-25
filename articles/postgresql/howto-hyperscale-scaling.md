---
title: Skalowanie grupy serwerów Azure Database for PostgreSQL-Citus)
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262494"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego.

W tym celu przejdź na kartę **Konfiguracja** w grupie serwerów moja skala (Citus).
Przeciągnij suwak dla pozycji **Liczba węzłów procesu roboczego** , aby zmienić wartość.

![Suwaki zasobów](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kliknij przycisk "Zapisz", aby zmienić wartość efektu.

> [!NOTE]
> Po zwiększeniu i zapisaniu liczba węzłów procesu roboczego nie może być obniżona przy użyciu suwaka.
>
> Ponadto rdzeni wirtualnych i magazyn nie mogą zostać dostosowane do koordynatora lub procesów roboczych z tym interfejsem użytkownika. Otwórz bilet pomocy technicznej, jeśli jest wymagany skalowanie obliczeniowe na koordynatorze lub w węzłach procesu roboczego.

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Aby uruchomić moduł równoważenia fragmentu, Połącz się z węzłem koordynatora klastra z PSQL i uruchom polecenie:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkcja ponownie równoważy wszystkie tabele w grupie wspólnej lokalizacji tabeli o nazwie w jej argumencie. [](concepts-hyperscale-colocation.md) `rebalance_table_shards` W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
