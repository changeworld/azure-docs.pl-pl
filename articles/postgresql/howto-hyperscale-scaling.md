---
title: Skalowanie grupy serwerów — Citus — Azure Database for PostgreSQL
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280445"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie rdzeni wirtualnych istniejących węzłów.

## <a name="add-worker-nodes"></a>Dodaj węzły procesu roboczego

Aby dodać węzły, przejdź na kartę **Konfiguracja** w grupie serwerów moja skala (Citus).  Przeciągnięcie suwaka dla **liczby węzłów roboczych** powoduje zmianę wartości.

![Suwaki zasobów](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kliknij przycisk **Zapisz** , aby zmiana wartości zaczęła obowiązywać.

> [!NOTE]
> Po zwiększeniu i zapisaniu liczba węzłów procesu roboczego nie może być obniżona przy użyciu suwaka.

### <a name="rebalance-shards"></a>Ponowne równoważenie fragmentów

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć rozproszoną tabelę [fragmentów](concepts-hyperscale-distributed-data.md#shards), co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Najpierw sprawdź, czy nowi pracownicy pomyślnie ukończyli Inicjowanie obsługi administracyjnej. Następnie należy uruchomić moduł równoważenia fragmentu, łącząc się z węzłem koordynatora klastra z PSQL i uruchamiając następujące polecenie:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkcja `rebalance_table_shards` ponownie równoważy wszystkie tabele w grupie wspólnej [lokalizacji](concepts-hyperscale-colocation.md) tabeli o nazwie w argumencie. W ten sposób nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, po prostu wywołaj ją na reprezentatywnej tabeli z każdej grupy wspólnej lokalizacji.

## <a name="increase-vcores"></a>Zwiększ rdzeni wirtualnych

Oprócz dodawania nowych węzłów można zwiększyć możliwości istniejących węzłów. Ta funkcja jest obecnie dostępna w wersji zapoznawczej — aby zażądać zwiększonych rdzeni wirtualnych dla węzłów w grupie serwerów, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
