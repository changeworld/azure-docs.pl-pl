---
title: Limity zasobów Azure SQL Database | Microsoft Docs
description: Ten artykuł zawiera omówienie limitów zasobów Azure SQL Database dla pojedynczych baz danych i pul elastycznych. Zawiera również informacje dotyczące tego, co się dzieje po osiągnięciu lub przekroczeniu limitów zasobów.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: fa41649e002bd4845b95e787c1d0589ed1987588
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255927"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limity zasobów SQL Database i zarządzanie zasobami

Ten artykuł zawiera omówienie limitów zasobów SQL Database dla serwera SQL Database, który zarządza pojedynczymi bazami danych i pulami elastycznymi. Zawiera informacje o tym, co się stanie w przypadku osiągnięcia lub przekroczenia limitów zasobów oraz opis mechanizmów ładu zasobów używanych do wymuszania tych limitów.

> [!NOTE]
> Aby uzyskać ograniczenia wystąpienia zarządzanego, zobacz [SQL Database limitów zasobów dla wystąpień zarządzanych](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maksymalne limity zasobów

| Zasób | Limit |
| :--- | :--- |
| Bazy danych na serwer | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Przydział jednostek DTU/eDTU na serwer | 54,000 |  
| przydział rdzeń wirtualny na serwer/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczone przez liczbę DTU lub rdzeni wirtualnych. Na przykład jeśli każda pula ma 1000 DTU, serwer może obsługiwać pule 54.|
|||

> [!IMPORTANT]
> Ponieważ liczba baz danych zbliża się do limitu na serwer SQL Database, mogą wystąpić następujące elementy:
>
> - Zwiększanie opóźnień w uruchamianiu zapytań względem bazy danych Master.  Obejmuje to widoki statystyk wykorzystania zasobów, takich jak sys. resource_stats.
> - Zwiększenie opóźnienia w operacjach zarządzania i portalu renderowania, który obejmuje wyliczanie baz danych na serwerze.

> [!NOTE]
> Aby uzyskać więcej wartości przydziału jednostek DTU/eDTU, przydziału rdzeń wirtualny lub większej liczby serwerów niż domyślna, Prześlij nowe żądanie obsługi w Azure Portal. Aby uzyskać więcej informacji, zobacz [zwiększenie przydziału żądań dla Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Rozmiar magazynu

W przypadku magazynów zasobów o pojedynczej bazie danych zapoznaj się z limitami zasobów [opartymi](sql-database-dtu-resource-limits-single-databases.md) na jednostkach DTU lub limitami [zasobów opartymi na rdzeń wirtualny](sql-database-vcore-resource-limits-single-databases.md) dla limitów rozmiaru magazynu na warstwę cenową.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych

### <a name="compute-dtus-and-edtus--vcores"></a>Obliczenia (DTU i jednostek eDTU/rdzeni wirtualnych)

Gdy użycie obliczeniowe bazy danych (mierzone przez DTU i jednostek eDTU, lub rdzeni wirtualnych) stanie się wysokie, opóźnienia zapytań rosną, a kwerendy mogą nawet przekroczyć limit czasu. W tych warunkach zapytania mogą być umieszczane w kolejce przez usługę i są udostępniane zasoby do wykonania, ponieważ zasoby stają się bezpłatne.
W przypadku wystąpienia dużej mocy obliczeniowej opcje ograniczenia obejmują:

- Zwiększenie rozmiaru obliczeniowego bazy danych lub puli elastycznej w celu zapewnienia bazy danych większej ilości zasobów obliczeniowych. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizowanie zapytań w celu zmniejszenia wykorzystania zasobów dla każdego zapytania. Aby uzyskać więcej informacji, zobacz [dostrajanie/podpowiedzi zapytań](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Gdy używane miejsce na bazę danych osiągnie limit rozmiaru, wstawia i aktualizuje bazę danych, która zwiększa niepowodzenie rozmiaru danych, a klienci odbierają [komunikat o błędzie](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Instrukcje SELECT i DELETE kontynuują się pomyślnie.

W przypadku wystąpienia dużej ilości miejsca, opcje ograniczenia obejmują:

- Zwiększenie maksymalnego rozmiaru bazy danych lub elastycznej puli lub dodanie większej ilości miejsca do magazynowania. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Jeśli baza danych znajduje się w puli elastycznej, można przenieść bazę danych poza pulę, aby jej miejsce do magazynowania nie było udostępniane innym baz danych.
- Zmniejsz rozmiar bazy danych, aby odzyskać nieużywane miejsce. Aby uzyskać więcej informacji, zobacz [Zarządzanie obszarem plików w Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sesje i procesy robocze (żądania)

Maksymalna liczba sesji i procesów roboczych zależy od warstwy usług i rozmiaru obliczeń (DTU/jednostek eDTU lub rdzeni wirtualnych. Nowe żądania są odrzucane, gdy osiągnięto limity sesji lub procesu roboczego, a klienci otrzymają komunikat o błędzie. Liczba dostępnych połączeń może być kontrolowana przez aplikację, ale liczba współbieżnych procesów roboczych często jest trudniejsza do oszacowania i kontroli. Jest to szczególnie ważne w przypadku szczytowych okresów ładowania, gdy osiągnięto limity zasobów bazy danych i procesy robocze są ustawiane z powodu dłuższych przebiegów zapytań, dużych łańcuchów blokowania lub nadmiernej współbieżności zapytań.

W przypadku wystąpienia wysokiego poziomu sesji lub procesu wyłączania, opcje ograniczenia obejmują:

- Zwiększenie warstwy usług lub rozmiaru obliczeniowego bazy danych lub puli elastycznej. Zobacz [skalowanie zasobów pojedynczych baz danych](sql-database-single-database-scale.md) i [skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizowanie zapytań w celu ograniczenia wykorzystania zasobów dla każdego zapytania, jeśli przyczyną zwiększonego wykorzystania procesów roboczych jest rywalizacja o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [dostrajanie/podpowiedzi zapytań](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Nadzór nad zasobami

Aby wymusić limity zasobów, Azure SQL Database używa implementacji ładu zasobów, która jest oparta na SQL Server [gubernatora zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), modyfikowana i rozszerzona w celu uruchomienia usługi bazy danych SQL Server na platformie Azure. W każdym wystąpieniu SQL Server w usłudze istnieje wiele [pul zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) i [grup obciążeń](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)z limitami zasobów ustawionymi na poziomie puli i grup, aby zapewnić [zrównoważoną bazę danych jako usługę](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Obciążenie użytkownikami i obciążenia wewnętrzne są klasyfikowane do oddzielnych pul zasobów i grup obciążeń. Obciążenie użytkownikami na głównych i czytelnych replikach pomocniczych, łącznie z replikami geograficznymi, jest klasyfikowane do puli zasobów `SloSharedPool1` i `UserPrimaryGroup.DBId[N]` grupy obciążeń, gdzie `N` oznacza wartość identyfikatora bazy danych. Ponadto istnieje wiele pul zasobów i grup obciążeń dla różnych obciążeń wewnętrznych.

Oprócz korzystania z regulatora zasobów w celu zarządzania zasobami w ramach procesu SQL Server Azure SQL Database również korzysta z [obiektów zadań](https://docs.microsoft.com/windows/win32/procthread/job-objects) systemu Windows dla ładu zasobów na poziomie procesu i [Menedżer zasobów serwera plików systemu Windows (Menedżer zasobów)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) na potrzeby zarządzanie przydziałami magazynu.

Azure SQL Database Zarządzanie zasobami ma charakter hierarchiczny. Od góry do dołu limity są wymuszane na poziomie systemu operacyjnego i na poziomie woluminu magazynu przy użyciu mechanizmów i zarządców zasobów systemu operacyjnego, a następnie na poziomie puli zasobów przy użyciu prezesa zasobów, a następnie na poziomie grupy obciążeń przy użyciu Zarządca zasobów. Limity zarządzania zasobami obowiązujące w przypadku bieżącej bazy danych lub puli elastycznej są wyświetlane w widoku [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . 

### <a name="data-io-governance"></a>Zarządzanie we/wy danych

Zarządzanie we/wy danych to proces w Azure SQL Database używany do ograniczania fizycznych operacji we/wy odczytu i zapisu do plików danych bazy danych. Limity liczby IOPS są ustawiane dla każdego poziomu usługi, aby zminimalizować efekt "zakłócenia", aby zapewnić sprawiedliwość alokacji zasobów w usłudze z wieloma dzierżawcami i pozostać w ramach możliwości podstawowego sprzętu i magazynu.

W przypadku pojedynczych baz danych limity grup obciążeń są stosowane do wszystkich operacji we/wy magazynu związanych z bazą danych, podczas gdy limity puli zasobów dotyczą wszystkich baz danych w tym samym wystąpieniu SQL Server, w tym `tempdb` Database. W przypadku pul elastycznych limity grup obciążeń dotyczą każdej bazy danych w puli, a limit puli zasobów ma zastosowanie do całej puli elastycznej, łącznie z `tempdb` bazą danych, która jest współużytkowana przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc, limity puli zasobów mogą nie być osiągalne w przypadku obciążenia dla bazy danych (pojedyncza lub w puli), ponieważ limity grup obciążeń są mniejsze niż limity puli zasobów i wkrótce ograniczają liczbę operacji we/wy na sekundę. Jednak limity puli mogą być osiągane przez połączone obciążenie dla wielu baz danych w tym samym wystąpieniu SQL Server.

Na przykład, jeśli zapytanie generuje 1000 IOPS bez żadnego ładu zasobów we/wy, ale limit liczby IOPS dla grupy obciążeń jest ustawiony na 900 IOPS, zapytanie nie będzie w stanie generować więcej niż 900 operacji wejścia/wyjścia. Jeśli jednak limit liczby IOPS dla puli zasobów jest ustawiony na 1500 operacji we/wy na sekundę, a łączny stan wejścia/wyjścia ze wszystkich grup obciążeń skojarzonych z pulą zasobów przekracza 1500 operacji we/wy, wówczas ta sama kwerenda może zostać zmniejszona poniżej limitu grupy roboczej dla 900 IOPS.

Liczba operacji we/wy na sekundę i przepływności zwracanych przez [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) widoku pełni rolę limitów/wersalików, a nie jako gwarancje. Ponadto zarządzanie zasobami nie gwarantuje żadnych opóźnień związanych z magazynem. Najlepsze osiągalne opóźnienia, operacje we/wy i przepływność dla danego obciążenia użytkownika zależą nie tylko od limitów nadzoru zasobów we/wy, ale również w przypadku różnych użytych rozmiarów we/wykorzystaniu i na możliwościach magazynu bazowego. SQL Server używa systemu IOs, który różni się od 512 KB i 4 MB. W celu wymuszenia limitów IOPS każda operacja we/wy jest uwzględniana niezależnie od jej rozmiaru, z wyjątkiem baz danych z plikami danych w usłudze Azure Storage. W takim przypadku system IOs o rozmiarze większym niż 256 KB jest rozliczany jako wiele 256 KB systemu IOs, aby dostosować je do usługi Azure Storage we/wy.

W przypadku baz danych w warstwach Podstawowa, standardowa i Ogólnego przeznaczenia, które korzystają z plików danych w usłudze Azure Storage, `primary_group_max_io` wartość może nie być osiągalna, jeśli baza danych nie ma wystarczającej ilości plików danych do skumulowanego dostarczania tej liczby operacji we/wy, lub jeśli dane nie są dystrybuowane równomiernie między plikami, lub jeśli warstwa wydajności bazowych obiektów BLOB ogranicza liczbę IOPS/przepływność poniżej limitu Podobnie w przypadku małego systemu IOs wygenerowanego przez częste zatwierdzanie transakcji wartość `primary_max_log_rate` może być nieosiągalna przez obciążenie wynikające z limitu liczby IOPS w źródłowym obiekcie blob usługi Azure Storage.

Wartości wykorzystania zasobów, takie jak `avg_data_io_percent` i `avg_log_write_percent`, raportowane w widokach [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , są obliczane jako wartości procentowe maksymalnego limitu zarządzania zasobami. W związku z tym, gdy czynniki inne niż zarządzanie zasobami ograniczają liczbę operacji we/wy, można zobaczyć, że operacje we/wy na sekundę i opóźnienia zwiększają się wraz ze wzrostem obciążenia, nawet jeśli zgłoszone użycie zasobów pozostanie poniżej 100%. 

Aby zobaczyć operacje we/wy odczytu i zapisu w pliku bazy danych, użyj funkcji [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Ta funkcja wyświetla wszystkie operacje we/wy względem bazy danych, w tym w tle we/wy, która nie jest uwzględniona w `avg_data_io_percent`, ale używa IOPS i przepływności magazynu bazowego, a także może mieć wpływ na zaobserwowane opóźnienie magazynu. Funkcja także umieszcza dodatkowe opóźnienia, które mogą zostać wprowadzone przez ładu zasobów we/wy dla operacji odczytu i zapisu w kolumnach `io_stall_queued_read_ms` i `io_stall_queued_write_ms`.

### <a name="transaction-log-rate-governance"></a>Ocena szybkości dziennika transakcji

Współczynnik dziennika transakcji ładu to proces w Azure SQL Database używany do ograniczania wysokich szybkości pozyskiwania dla obciążeń takich jak wstawianie zbiorcze, wybieranie do i kompilacje indeksów. Limity te są śledzone i wymuszane na poziomie drugim do szybkości generowania rekordów dziennika, ograniczając przepływność bez względu na to, ile systemu IOs można wystawić dla plików danych.  Stawki generowania dziennika transakcji są obecnie skalowane liniowo do punktu, który jest zależny od sprzętu, a maksymalna szybkość rejestrowania może być 96 MB/s z modelem zakupów rdzeń wirtualny. 

> [!NOTE]
> Rzeczywiste fizyczne pliki dziennika transakcji w systemie IOs nie podlegają ograniczeniom ani nie są ograniczone.

Stawki dzienników są ustawiane tak, aby można je było osiągnąć i utrzymywać w różnych scenariuszach, podczas gdy ogólny system może utrzymywać swoją funkcjonalność przy minimalnym wpływie na obciążenie użytkownikami. Częstotliwość rejestrowania gwarantuje, że kopie zapasowe dziennika transakcji pozostają w ramach opublikowanej umowy SLA.  To zarządzanie uniemożliwia również nadmierne zaległości w replikach pomocniczych.

Ponieważ rekordy dziennika są generowane, każda operacja jest oceniana i oceniana pod kątem tego, czy należy opóźnić, aby zachować maksymalny żądany współczynnik dziennika (MB/s na sekundę). Opóźnienia nie są dodawane, gdy rekordy dziennika są opróżniane do magazynu, podczas generowania stawki dziennika są stosowane zasady zarządzania.

Rzeczywiste stawki za generowanie dzienników nałożone w czasie wykonywania mogą również mieć wpływ na mechanizmy przesyłania opinii, tymczasowo zmniejszając dopuszczalne stawki dzienników, dzięki czemu system może być stabilny. Zarządzanie miejscem w pliku dziennika, unikanie uruchamiania w warunkach wolnego miejsca w dzienniku i mechanizmów replikacji grupy dostępności może tymczasowo obniżyć ogólne limity systemu.

Współczynnik dziennika: kształtowanie ruchu gubernatora jest przedstawiane przy użyciu następujących typów oczekiwania (uwidocznionych w [tabeli sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ oczekiwania | Uwagi |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Ograniczanie bazy danych |
| POOL_LOG_RATE_GOVERNOR | Ograniczanie puli |
| INSTANCE_LOG_RATE_GOVERNOR | Ograniczanie poziomu wystąpienia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Kontrola opinii, replikacja fizyczna grupy dostępności w warstwie Premium/Krytyczne dla działania firmy nie jest zachowywana |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Kontrola opinii, ograniczanie stawek aby uniknąć braku miejsca w dzienniku |
|||

W przypadku napotkania limitu szybkości rejestrowania, który hamuje pożądaną skalowalność, należy wziąć pod uwagę następujące opcje:
- Skalowanie w górę do wyższego poziomu usługi w celu uzyskania maksymalnej liczby dzienników 96 MB/s. 
- Jeśli ładowane dane są przejściowe, takie jak dane przejściowe w procesie ETL, można je załadować do bazy danych tempdb (co jest zarejestrowana w minimalnym stopniu). 
- W przypadku scenariuszy analitycznych Załaduj do tabeli objętej usługą magazynu kolumn. Zmniejsza to wymaganą szybkość rejestrowania z powodu kompresji. Ta technika zwiększa wykorzystanie procesora CPU i ma zastosowanie tylko do zestawów danych, które korzystają z klastrowanych indeksów magazynu kolumn. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat DTU i jednostek eDTU, zobacz [DTU i jednostek eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiaru bazy danych tempdb, zobacz [tempdb w Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
