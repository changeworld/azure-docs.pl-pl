---
title: Limity zasobów bazy danych SQL platformy Azure — serwer logiczny | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie serwera logicznego usługi Azure SQL Database limity zasobów dla pojedynczych baz danych i baz danych w puli przy użyciu pul elastycznych. Umożliwia także informacja, co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 11/13/2018
ms.openlocfilehash: a423f5c112faa615b7888dacfa20f9ff8f6a595a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51620902"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases"></a>Limity zasobów bazy danych SQL Database dla pojedynczych i puli baz danych

Ten artykuł zawiera omówienie limitów zasobów bazy danych SQL Database dla pojedynczych i puli baz danych na serwerze logicznym. Umożliwia także informacja, co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.

> [!NOTE]
> Limity wystąpienia zarządzanego, zobacz [limity zasobów bazy danych SQL dla wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limity zasobów maksymalna

| Zasób | Limit |
| :--- | :--- |
| Baz danych na jednym serwerze | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Jednostka DTU / przydziału liczby jednostek eDTU na serwerze | 54,000 |  
| Rdzeń wirtualny przydziału na server/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczone przez liczbę jednostek Dtu lub rdzeni wirtualnych. Na przykład jeśli każda pula ma 1000 jednostek Dtu, następnie pojedynczego serwera obsługują 54 pul.|
||||

> [!NOTE]
> Aby uzyskać więcej /eDTU limitu przydziału, przydziału pamięci rdzeń wirtualny lub większej liczby serwerów niż domyślny, można przesłać nowe żądanie pomocy technicznej w witrynie Azure portal dla subskrypcji z typem problemu "Limit przydziału". Wartość DTU / limitu przydziału i bazy danych na serwer liczby jednostek eDTU ogranicza liczbę pule elastyczne na serwerze.
> [!IMPORTANT]
> Ponieważ liczba baz danych zbliża się do limitu na każdym serwerze logicznym, mogą wystąpić następujące czynności:
> - Zwiększenie opóźnienia w uruchamianiu zapytań bazy danych master.  Dotyczy to widoków dane statystyczne wykorzystania zasobów, takich jak sys.resource_stats.
> - Zwiększenie opóźnienia w operacji zarządzania i renderowanie portalu punkty widzenia, obejmujące wyliczanie baz danych na serwerze.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych

### <a name="compute-dtus-and-edtus--vcores"></a>Obliczeniowe (jednostki Dtu i Edtu / rdzeni wirtualnych)

Gdy wzrośnie wykorzystanie obliczeń bazy danych (według liczby jednostek Dtu i Edtu lub rdzenie wirtualne), kwerendami zwiększa i może nawet przekroczyć limit czasu. W tych warunkach zapytania mogą być umieszczane w kolejce przez usługę i są pod warunkiem, że zwolnić zasoby do wykonania jako zasób.
Gdy wystąpią wykorzystanie dużą mocą obliczeniową, opcje środki zaradcze:

- Zwiększenie rozmiaru obliczeń bazy danych lub elastycznej puli w celu zapewnienia bazy danych przy użyciu większej ilości zasobów obliczeniowych. Zobacz [skalowanie pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).
- Optymalizowanie zapytań, aby zmniejszyć wykorzystanie zasobów każdej kwerendy. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Używane miejsce w bazie danych osiągnie limit maksymalnego rozmiaru, wstawia bazy danych i aktualizacji, które zwiększają rozmiar danych nie powiodło się i klienci otrzymują [komunikat o błędzie](sql-database-develop-error-messages.md). Baza danych WYBIERA i USUWA nadal pomyślnie tworzone.

Gdy wystąpią wykorzystania miejsca wysoka, opcje środki zaradcze:

- Zwiększenie maksymalnego rozmiaru bazy danych lub elastycznej puli, lub Dodaj więcej pamięci masowej. Zobacz [skalowanie pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).
- W przypadku bazy danych w puli elastycznej następnie też bazy danych można przenosić poza pulę, aby jej miejsca do magazynowania nie jest współużytkowane z innymi bazami danych.
- Zmniejszanie bazy danych mogą odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [zarządzania miejsca na pliki w usłudze Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sesje i procesów roboczych (żądań)

Maksymalna liczba sesji i procesy robocze są określane przez warstwę usług i obliczenia rozmiaru (jednostki Dtu i Edtu). Nowe żądania są odrzucane po osiągnięciu limitu sesji lub proces roboczy, a klienci otrzymują komunikat o błędzie. Chociaż można kontrolować liczbę połączeń, które są dostępne przez aplikację, liczba współbieżnych procesów roboczych jest często trudniejsze do oszacowania i sterowania. Jest to szczególnie istotne w okresach szczytowego obciążenia po osiągnięciu limitów zasobów bazy danych i procesów roboczych, ustawianie ze względu na dłużej uruchomionych zapytań.

Gdy wystąpią wysokie wykorzystanie sesji lub proces roboczy, opcje środki zaradcze:

- Zwiększenie usługi warstwy lub obliczenia rozmiaru bazy danych lub elastycznej puli. Zobacz [skalowanie pojedynczej bazy danych zasobów](sql-database-single-database-scale.md) i [skalowanie elastycznej puli zasobów](sql-database-elastic-pool-scale.md).
- Optymalizacja zapytania, aby zmniejszyć wykorzystanie zasobów każdej kwerendy, jeśli przyczyną zwiększonej wykorzystanie jest z powodu rywalizacji o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [zapytania dostrajania/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [— często zadawane pytania dla bazy danych SQL](sql-database-faq.md) odpowiedzi na często zadawane pytania.
- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat jednostek Dtu i Edtu, zobacz [jednostek Dtu i Edtu](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiarów bazy danych tempdb, zobacz [bazy danych TempDB w usłudze Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
