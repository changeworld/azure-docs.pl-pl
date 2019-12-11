---
title: Skalowanie grupy serwerów — Citus — Azure Database for PostgreSQL
description: Dostosowywanie pamięci grupy serwerów, dysku i zasobów procesora CPU w celu zwiększenia obciążenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977560"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów ze skalą (Citus)

Azure Database for PostgreSQL-Citus) oferuje skalowanie samoobsługowe umożliwiające zwiększenie obciążenia. Azure Portal ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie pojemności istniejących węzłów.

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

## <a name="increase-vcores-or-storage-space"></a>Zwiększ rdzeni wirtualnych lub miejsce do magazynowania

Oprócz dodawania nowych węzłów można zwiększyć możliwości istniejących węzłów. Przejdź do karty **Konfiguracja** w grupie serwerów moja skala (Citus), a następnie przeciągnij suwak dla pozycji **rdzeni wirtualnych** i **Storage** , aby zmienić te wartości dla wszystkich węzłów procesu roboczego. Kliknij przycisk **Zapisz** , aby zastosować zmiany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów.
