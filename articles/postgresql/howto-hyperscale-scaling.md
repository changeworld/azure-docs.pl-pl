---
title: Skalowanie grupy serwerów — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Dostosowywanie zasobów pamięci, dysku i procesora cpu grupy serwerów w celu radzenia sobie ze zwiększonym obciążeniem
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063112"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skalowanie grupy serwerów w skali hiperskali (Citus)

Usługa Azure Database for PostgreSQL — hiperskali (Citus) zapewnia skalowanie samoobsługowe w celu radzenia sobie ze zwiększonym obciążeniem. Portal Azure ułatwia dodawanie nowych węzłów procesu roboczego i zwiększanie owo wirtualnych istniejących węzłów.

## <a name="add-worker-nodes"></a>Dodawanie węzłów procesu roboczego

Aby dodać węzły, przejdź do karty **Konfigurowanie** w grupie serwerów w skali hiperskali (Citus).  Przeciąganie suwaka dla **liczby węzłów procesu roboczego** zmienia wartość.

![Suwaki zasobów](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Kliknij przycisk **Zapisz,** aby zmieniona wartość została uwzględnina.

> [!NOTE]
> Po zwiększeniu i zapisaniu nie można zmniejszyć liczby węzłów procesu roboczego za pomocą suwaka.

### <a name="rebalance-shards"></a>Równoważenie odłamków

Aby skorzystać z nowo dodanych węzłów, należy ponownie zrównoważyć fragmenty tabeli [rozproszonej,](concepts-hyperscale-distributed-data.md#shards)co oznacza przeniesienie niektórych fragmentów z istniejących węzłów do nowych. Najpierw sprawdź, czy nowi pracownicy pomyślnie zakończyli inicjowanie obsługi administracyjnej. Następnie uruchom balastator niezależnego fragmentu, łącząc się z węzłem koordynatora klastra z psql i uruchomiony:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funkcja `rebalance_table_shards` równoważy wszystkie tabele w grupie [kolokacji](concepts-hyperscale-colocation.md) tabeli o nazwie w jej argumentie. W związku z tym nie trzeba wywoływać funkcji dla każdej tabeli rozproszonej, wystarczy wywołać ją w reprezentatywnej tabeli z każdej grupy kolokacji.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Zwiększanie lub zmniejszanie korli wirtualnych w węzłach

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Aby zażądać zmiany w polach wirtualnych dla węzłów w grupie serwerów, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Oprócz dodawania nowych węzłów, można zwiększyć możliwości istniejących węzłów. Dostosowywanie pojemności obliczeniowej w górę i w dół może być przydatne w przypadku eksperymentów wydajności, a także krótko- lub długoterminowych zmian w wymaganiach ruchu.

Aby zmienić pola wirtualne dla wszystkich węzłów procesu roboczego, dostosuj suwak **pola wirtualne** w obszarze **Konfiguracja (węzeł pracownika).** Bramki wirtualne węzła koordynatora można regulować niezależnie. Kliknij łącze **Zmień konfigurację** w **węźle koordynatora**. Pojawi się okno dialogowe z suwakami dla liczników wirtualnych i pojemności magazynowej koordynatora. Zmień suwaki zgodnie z potrzebami i wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [opcjach wydajności](concepts-hyperscale-configuration-options.md)grupy serwerów .
