---
title: Limity zasobów serwera w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie serwera Azure SQL Database limity zasobów dla pojedynczych baz danych i pul elastycznych. Umożliwia także informacja, co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 4e4c0a6cd25587b33c06526b57e6acdbebb69c8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445633"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limity zasobów bazy danych SQL dla serwera Azure SQL Database

Ten artykuł zawiera omówienie limitów zasobów bazy danych SQL dla serwera bazy danych SQL, który zarządza pojedynczych baz danych i pul elastycznych. Umożliwia także informacja, co się stanie, gdy te limity zasobów są osiągnięty lub przekroczony.

> [!NOTE]
> Limity wystąpień zarządzanych, zobacz [limity zasobów bazy danych SQL dla wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limity zasobów maksymalna

| Resource | Limit |
| :--- | :--- |
| Baz danych na jednym serwerze | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Jednostka DTU / przydziału liczby jednostek eDTU na serwerze | 54,000 |  
| Rdzeń wirtualny przydziału na server/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczone przez liczbę jednostek Dtu lub rdzeni wirtualnych. Na przykład jeśli każda pula ma 1000 jednostek Dtu, następnie serwera obsługują 54 pul.|
|||

> [!NOTE]
> Aby uzyskać więcej /eDTU limitu przydziału, przydziału pamięci rdzeń wirtualny lub większej liczby serwerów niż domyślny, można przesłać nowe żądanie pomocy technicznej w witrynie Azure portal dla subskrypcji z typem problemu "Limit przydziału". Wartość DTU / limitu przydziału i bazy danych na serwer liczby jednostek eDTU ogranicza liczbę pule elastyczne na serwerze.
> [!IMPORTANT]
> Jak liczba baz danych zbliża się limit dla bazy danych programu SQL server, mogą wystąpić następujące czynności:
> - Zwiększenie opóźnienia w uruchamianiu zapytań bazy danych master.  Dotyczy to widoków dane statystyczne wykorzystania zasobów, takich jak sys.resource_stats.
> - Zwiększenie opóźnienia w operacji zarządzania i renderowanie portalu punkty widzenia, obejmujące wyliczanie baz danych na serwerze.

### <a name="storage-size"></a>Rozmiar magazynu
- Pojedyncze bazy danych rources można znaleźć na stronie albo [limity zasobów na podstawie jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) lub [limity zasobów opartych na rdzeniach wirtualnych](sql-database-vcore-resource-limits-single-databases.md) limitów rozmiaru magazynu na warstwy cenowej.

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

## <a name="transaction-log-rate-governance"></a>Zarządzanie współczynnik dziennika transakcji 
Nadzoru współczynnik dziennik transakcji jest procesu w usłudze Azure SQL Database, używana do ograniczania szybkości pozyskiwania wysokiej dla obciążeń, takich jak zbiorczo wstawić, SELECT INTO, i tworzy indeks. Te limity są śledzone i wymuszane na poziomie sekundy, aby szybkość generowania rekordów dziennika, ograniczanie przepływności, niezależnie od tego, ile z systemem IOs mogą być wydawane względem danych plików.  Stawki Generowanie dziennika transakcji obecnie skalowane liniowo do momentu, która jest zależna od sprzętu, w dzienniku maksymalną szybkość dozwolone przy 96 MB/s z zakupem modelu rdzeni wirtualnych. 

> [!NOTE]
> Rzeczywiste fizycznych z systemem IOs do plików dziennika transakcji nie są zarządzane lub ograniczone. 

Stawki dziennika są ustawiane w taki sposób, że mogą być osiągnięte i przez dłuższy w różnych scenariuszach, podczas gdy cały system może zachować jego działanie w trybie zminimalizowanym wpływu na obciążenia użytkownika. Dziennik współczynnik Zarządzanie zapewnia, że tego dziennika transakcji, których kopie zapasowe pozostać w opublikowanej odzysku umowy SLA.  To ładu zapobiega także nadmierne zaległości w replikach pomocniczych.

Jak rekordy dziennika są generowane, każda operacja jest obliczane i oceny pod kątem tego, czy powinno zostać opóźnione utrzymane, gdy wskaźnik maksymalną żądaną dziennika (MB/s na sekundę). Opóźnienia nie są dodawane po rekordów dziennika zostały przesłane do magazynu, zamiast nadzoru współczynnik dziennika jest stosowany podczas generowania współczynnik dziennika, sam.

Generowanie dziennika rzeczywiste stawki nałożone w czasie wykonywania mogą również mieć wpływ mechanizmy przesyłania opinii, tymczasowo zmniejszenie szybkości dopuszczalny rozmiar dziennika, dzięki czemu można ustabilizować system. Zarządzanie przestrzenią pliku dziennika, unikając kończy do warunków miejsca w dzienniku i grupy dostępności mechanizmów replikacji może tymczasowo obniżyć limity całego systemu. 

Kształtowania ruchu zarządcy współczynnik dziennika jest udostępniane za pośrednictwem następujących typów oczekiwania (w [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Poczekaj typu | Uwagi |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Ograniczenie bazy danych |
| POOL_LOG_RATE_GOVERNOR | Ograniczanie puli |
| INSTANCE_LOG_RATE_GOVERNOR | Ograniczenie poziomu wystąpienia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Kontrolka opinii, replikacja fizycznych grupy dostępności w warstwie Premium/krytyczne dla działania firmy nie nadążają |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Kontrolka opinii, ograniczania szybkości, aby uniknąć stan miejsca na dziennik braku |
|||

Gdy wystąpią limit szybkości dziennika, który hamuje odpowiednią skalowalność, należy wziąć pod uwagę następujące opcje:
- Skalowanie w górę do warstwy większa Aby uzyskać maksymalną szybkość dziennika 96 MB/s. 
- Jeśli dane są ładowane jest przejściowy, czyli przemieszczania danych w proces ETL może być załadowany do bazy danych tempdb, (które są rejestrowane w minimalnym). 
- W przypadku scenariuszy analitycznych załadować do tabeli klastrowanego magazynu kolumn objętych usługą. Pozwala to zmniejszyć częstotliwość wymagane dzienników z powodu stosowania kompresji. Ta technika zwiększenie wykorzystania procesora CPU i ma zastosowanie tylko do zestawów danych, które korzystają z klastrowane indeksy magazynu kolumn. 

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat ogólne limity platformy Azure, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).
- Aby uzyskać informacji na temat jednostek Dtu i Edtu, zobacz [jednostek Dtu i Edtu](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiarów bazy danych tempdb, zobacz [bazy danych TempDB w usłudze Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
