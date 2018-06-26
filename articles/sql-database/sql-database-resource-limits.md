---
title: Zasobów bazy danych SQL Azure ogranicza omówienie | Dokumentacja firmy Microsoft
description: Na tej stronie opisano niektóre typowe limity zasobów na podstawie jednostek DTU dla pojedynczych baz danych w bazie danych SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 6806b0c5b5e5ac5e1189f628786f0c8f9b223395
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750955"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Limity zasobów bazy danych SQL Azure — omówienie 

Ten artykuł zawiera omówienie zasobów bazy danych SQL Azure ogranicza oraz zawiera informacje dotyczące co się stanie, gdy te limity zasobów są trafień lub przekroczony.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Co to jest maksymalna liczba serwerów i baz danych?

| Maksimum | Wartość |
| :--- | :--- |
| Baz danych na serwerze | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |
|||

> [!NOTE]
> Aby uzyskać więcej przydział serwera niż domyślny, w portalu Azure dla subskrypcji o problem typu "Przydziału" można przesłać nowe żądanie pomocy technicznej.

> [!IMPORTANT]
> Jak liczba baz danych zbliża się do limitu na serwerze, mogą wystąpić następujące czynności:
> - Zwiększanie opóźnienie w wykonywanie zapytań bazy danych master.  Dotyczy to widoków dane statystyczne wykorzystania zasobów, takich jak sys.resource_stats.
> - Zwiększenie opóźnienia podczas wykonywania operacji zarządzania i renderowania portalu punkt widzenia obejmujących wyliczanie baz danych na serwerze.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitu zasobów bazy danych?

### <a name="compute-dtus-and-edtus--vcores"></a>Obliczenia bazy danych (Dtu a Edtu / vCores)

Gdy wzrośnie wykorzystania obliczeń bazy danych (mierzonej w Dtu a Edtu lub vCores), opóźnienia zapytania zwiększa i może nawet limit czasu. W tych warunkach zapytania mogą być umieszczone w kolejce przez usługę i są pod warunkiem, że zwolnić zasobów do wykonania jako zasobu.
Gdy wystąpią wykorzystania wysokiej obliczeń, są następujące opcje środki zaradcze:

- Zwiększenie poziomu wydajności bazy danych lub puli elastycznej, aby zapewnić większą ilością zasobów obliczeniowych bazy danych. Zobacz [skalowania pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [puli elastycznej zasoby są skalowane](sql-database-elastic-pool-scale.md).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Gdy miejsca w bazie danych używany przez nią miejsce osiągnie limit maksymalnego rozmiaru bazy danych wstawia i aktualizacji, które zwiększają rozmiar danych nie powiodło się i klienci odbierają [komunikat o błędzie](sql-database-develop-error-messages.md). Baza danych WYBIERA i USUWA nadal się powieść.

Gdy wystąpią wykorzystanie miejsca wysoka, są następujące opcje środki zaradcze:

- Zwiększyć maksymalny rozmiar bazy danych lub elastyczna pula lub Dodaj więcej pamięci masowej. Zobacz [skalowania pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [puli elastycznej zasoby są skalowane](sql-database-elastic-pool-scale.md).
- W przypadku bazy danych w puli elastycznej następnie również bazy danych może zostać przeniesiona poza puli tak, aby jej miejsca do magazynowania nie są współużytkowane z innych baz danych.

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (liczba żądań) 

Maksymalna liczba sesji, jak i pracownicy są określane przez warstwę i poziom wydajności usługi (Dtu a Edtu). Nowe żądania są odrzucane po osiągnięciu limitu sesji lub proces roboczy, a klienci odbierają komunikat o błędzie. Gdy liczbę połączeń dostępnych może być kontrolowane przez aplikację, liczbę równoczesnych procesów roboczych jest często przeszkodę oszacować i sterowanie nią. Jest to szczególnie istotne podczas okresów obciążenia szczytowego po osiągnięciu limitu zasobów bazy danych i pracowników, ustawianie z powodu dłużej uruchomione zapytania. 

Gdy wystąpią wysokie użycie sesji lub procesu roboczego, są następujące opcje środki zaradcze:
- Zwiększenie poziomu warstwy lub wydajności usługi bazy danych lub elastyczna pula. Zobacz [skalowania pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [puli elastycznej zasoby są skalowane](sql-database-elastic-pool-scale.md).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania, jeśli przyczyną zwiększone wykorzystanie wynika z rywalizacji o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

Gdy wystąpią wysokie użycie sesji lub procesu roboczego, są następujące opcje środki zaradcze:
- Zwiększa poziom wydajności ani warstwy usługi bazy danych. Zobacz [skalowania pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [puli elastycznej zasoby są skalowane](sql-database-elastic-pool-scale.md).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdego zapytania, jeśli przyczyną zwiększone wykorzystanie wynika z rywalizacji o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [bazy danych SQL — często zadawane pytania](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Informacje ogólne limity Azure, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Informacje o Dtu a Edtu, zobacz [Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Informacje o limity rozmiaru bazy danych tempdb, zobacz https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
