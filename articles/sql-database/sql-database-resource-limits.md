---
title: Zasób platformy Azure SQL Database ogranicza — omówienie | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie zasobów usługi Azure SQL Database ogranicza i zawiera informacje dotyczące co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.
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
ms.date: 09/19/2018
ms.openlocfilehash: 582e0cc8dd48921bbbef188cb712a156e128e4fe
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159588"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Limity zasobów usługi Azure SQL Database — omówienie 

Ten artykuł zawiera omówienie zasobów usługi Azure SQL Database ogranicza i zawiera informacje dotyczące co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Co to jest maksymalna liczba serwerów i baz danych?

| Maksimum | Serwer logiczny | Wystąpienie zarządzane |
| :--- | :--- | :--- |
| Baz danych na serwerze/wystąpienie | 5000 | 100 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 | ND |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 | ND | 
| Jednostka DTU / przydziału liczby jednostek eDTU na serwerze | 54,000 | ND |  
| Rdzeń wirtualny przydziału na server/wystąpienie | 540 | 80 |
| Maksymalna liczba pul na serwer | ograniczone przez liczbę jednostek Dtu lub rdzeni wirtualnych | ND |
||||

> [!NOTE]
> Aby uzyskać więcej /eDTU limitu przydziału, przydziału pamięci rdzeń wirtualny lub większej liczby serwerów niż domyślny, można przesłać nowe żądanie pomocy technicznej w witrynie Azure portal dla subskrypcji z typem problemu "Limit przydziału". Wartość DTU / limitu przydziału i bazy danych na serwer liczby jednostek eDTU ogranicza liczbę pule elastyczne na serwerze. 

> [!IMPORTANT]
> Jak liczba baz danych zbliża się do limitu na serwer, mogą wystąpić następujące czynności:
> - Zwiększenie opóźnienia w uruchamianiu zapytań bazy danych master.  Dotyczy to widoków dane statystyczne wykorzystania zasobów, takich jak sys.resource_stats.
> - Zwiększenie opóźnienia w operacji zarządzania i renderowanie portalu punkty widzenia, obejmujące wyliczanie baz danych na serwerze.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych?

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
- Aby uzyskać informacji na temat jednostek Dtu i Edtu, zobacz [jednostek Dtu i Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Aby uzyskać informacje na temat limitów rozmiarów bazy danych tempdb, zobacz [bazy danych TempDB w usłudze Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
