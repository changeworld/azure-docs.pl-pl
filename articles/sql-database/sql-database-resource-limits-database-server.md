---
title: Limity zasobów usługi Azure SQL Database | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie limitów zasobów usługi Azure SQL Database dla pojedynczych baz danych i pul elastycznych. Zawiera również informacje dotyczące tego, co się dzieje, gdy te limity zasobów zostaną trafione lub przekroczone.
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
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067265"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Limity zasobów bazy danych SQL i zarządzanie zasobami

Ten artykuł zawiera omówienie limitów zasobów bazy danych SQL dla serwera bazy danych SQL, który zarządza pojedynczymi bazami danych i pulami elastycznymi. Zawiera informacje o tym, co się dzieje, gdy te limity zasobów zostaną trafione lub przekroczone, i opisuje mechanizmy ładu zasobów używane do wymuszania tych limitów.

> [!NOTE]
> W przypadku limitów wystąpienia zarządzanego zobacz [Limity zasobów bazy danych SQL dla wystąpień zarządzanych](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maksymalne limity zasobów

| Zasób | Limit |
| :--- | :--- |
| Bazy danych na serwer | 5000 |
| Domyślna liczba serwerów na subskrypcję w dowolnym regionie | 20 |
| Maksymalna liczba serwerów na subskrypcję w dowolnym regionie | 200 |  
| Przydział identyfikatorów DTU / eDTU na serwer | 54,000 |  
| Przydział vCore na serwer/wystąpienie | 540 |
| Maksymalna liczba pul na serwer | Ograniczona liczbą DTU lub vCores. Na przykład jeśli każda pula jest 1000 DTU, serwer może obsługiwać 54 pule.|
|||

> [!IMPORTANT]
> Ponieważ liczba baz danych zbliża się do limitu na serwer bazy danych SQL, mogą wystąpić następujące czynności:
>
> - Zwiększenie opóźnienia w uruchamianiu zapytań względem głównej bazy danych.  Obejmuje to widoki statystyk wykorzystania zasobów, takich jak sys.resource_stats.
> - Zwiększenie opóźnienia w operacjach zarządzania i renderowania punktów widzenia portalu, które obejmują wyliczanie baz danych na serwerze.

> [!NOTE]
> Aby uzyskać więcej przydziału DTU/eDTU, przydziału vCore lub więcej serwerów niż kwota domyślna, prześlij nowe żądanie pomocy technicznej w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Żądanie zwiększenia przydziału dla usługi Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Rozmiar magazynu

W przypadku rozmiarów magazynu zasobów pojedynczych baz danych można znaleźć w odniesieniu do [limitów zasobów opartych na jednostkach DTU](sql-database-dtu-resource-limits-single-databases.md) lub [limitów zasobów opartych na nikach wirtualnych](sql-database-vcore-resource-limits-single-databases.md) dla limitów rozmiaru magazynu dla warstwy cenowej.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co się stanie po osiągnięciu limitów zasobów bazy danych

### <a name="compute-dtus-and-edtus--vcores"></a>Obliczenia (DTU i eDTU / vCores)

Gdy wykorzystanie obliczeń bazy danych (mierzone przez jednostek DTU i eDTU lub vCore) staje się wysokie, zwiększa się opóźnienie kwerendy, a kwerendy mogą nawet limit czasu. W tych warunkach zapytania mogą być umieszczane w kolejce przez usługę i są dostarczane zasoby do wykonania, jak zasoby stają się wolne.
W przypadku wystąpienia wysokiego wykorzystania obliczeń opcje ograniczania ryzyka obejmują:

- Zwiększenie rozmiaru obliczeń bazy danych lub puli elastycznej w celu zapewnienia bazy danych więcej zasobów obliczeniowych. Zobacz [Skalowanie zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) i [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizacja kwerend w celu zmniejszenia wykorzystania zasobów każdej kwerendy. Aby uzyskać więcej informacji, zobacz [Dostrajanie/podpowiedzi kwerendy](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Magazyn

Gdy miejsce w bazie danych osiągnie maksymalny limit rozmiaru, baza danych wstawia i aktualizuje, które zwiększają rozmiar danych, a klienci otrzymują [komunikat o błędzie](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Instrukcje SELECT i DELETE nadal powiodą się pomyślnie.

W przypadku wystąpienia wysokiego wykorzystania miejsca opcje ograniczania ryzyka obejmują:

- Zwiększenie maksymalnego rozmiaru bazy danych lub puli elastycznej lub dodanie większej ilości miejsca do magazynowania. Zobacz [Skalowanie zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) i [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Jeśli baza danych znajduje się w puli elastycznej, a następnie alternatywnie bazy danych można przenieść poza pulę, tak aby jego miejsce do magazynowania nie jest współużytkowane z innymi bazami danych.
- Zmniejszanie bazy danych w celu odzyskania nieużytego miejsca. Aby uzyskać więcej informacji, zobacz [Zarządzanie przestrzenią plików w bazie danych SQL usługi Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sesje i pracownicy (żądania)

Maksymalna liczba sesji i pracowników są określane przez warstwę usług i rozmiar obliczeń (DTU/eDTU lub vCores). Nowe żądania są odrzucane po osiągnięciu limitów sesji lub procesu roboczego, a klienci otrzymują komunikat o błędzie. Podczas gdy liczba dostępnych połączeń może być kontrolowana przez aplikację, liczba równoczesnych pracowników jest często trudniejsze do oszacowania i kontroli. Jest to szczególnie ważne w okresach szczytowego obciążenia, gdy limity zasobów bazy danych są osiągane, a pracownicy piętrzą się z powodu dłuższych uruchomionych zapytań, dużych łańcuchów blokowania lub nadmiernego równoległości zapytań.

W przypadku wystąpienia wysokiego wykorzystania sesji lub pracownika opcje łagodzenia skutków obejmują:

- Zwiększenie warstwy usług lub rozmiar obliczeń bazy danych lub puli elastycznej. Zobacz [Skalowanie zasobów pojedynczej bazy danych](sql-database-single-database-scale.md) i [Skalowanie zasobów puli elastycznej](sql-database-elastic-pool-scale.md).
- Optymalizacja kwerend w celu zmniejszenia wykorzystania zasobów każdej kwerendy, jeśli przyczyną zwiększonego wykorzystania procesu roboczego jest ze względu na rywalizację o zasoby obliczeniowe. Aby uzyskać więcej informacji, zobacz [Dostrajanie/podpowiedzi kwerendy](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Zmniejszenie ustawienia [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maksymalny stopień równoległości).
- Optymalizacja obciążenia zapytania w celu zmniejszenia liczby wystąpień i czasu trwania blokowania kwerend.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Zużycie zasobów przez obciążenia użytkowników i procesy wewnętrzne

Zużycie procesora CPU i pamięci przez obciążenia użytkowników w każdej bazie danych jest zgłaszane w [widokach sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) i [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) w `avg_cpu_percent` i `avg_memory_usage_percent` kolumnach. W przypadku pul elastycznych zużycie zasobów na poziomie puli jest zgłaszane w widoku [sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) Zużycie procesora CPU obciążenia `cpu_percent` użytkownika jest również zgłaszane za pośrednictwem metryki usługi Azure Monitor dla [pojedynczych baz danych](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) i [pul elastycznych](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) na poziomie puli.

Usługa Azure SQL Database wymaga zasobów obliczeniowych w celu wdrożenia podstawowych funkcji usługi, takich jak wysoka dostępność i odzyskiwanie po awarii, tworzenie kopii zapasowych i przywracanie bazy danych, monitorowanie, magazyn zapytań, automatyczne dostrajanie itp. System odstawi pewną ograniczoną część ogólnych zasobów dla tych procesów wewnętrznych przy użyciu mechanizmów [zarządzania zasobami,](#resource-governance) dzięki czemu pozostała część zasobów jest dostępna dla obciążeń użytkowników. Czasami, gdy procesy wewnętrzne nie są przy użyciu zasobów obliczeniowych, system udostępnia je do obciążeń użytkowników.

Całkowite zużycie procesora CPU i pamięci przez obciążenia użytkowników i procesy wewnętrzne w wystąpieniu programu SQL Server obsługującym pojedynczą bazę danych `avg_instance_cpu_percent` `avg_instance_memory_percent` lub pulę elastyczną jest zgłaszane w [widokach sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) i [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) w i kolumnach. Te dane są również `sqlserver_process_core_percent` `sqlserver_process_memory_percent` zgłaszane za pośrednictwem i Azure Monitor metryki, dla [pojedynczych baz danych](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) i [pul elastycznych](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) na poziomie puli.

Bardziej szczegółowy podział ostatniego zużycia zasobów według obciążeń użytkowników i procesów wewnętrznych jest raportowy w [widokach sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) i [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Aby uzyskać szczegółowe informacje na temat pul zasobów i grup obciążeń, do których odwołuje się te widoki, zobacz [Zarządzanie zasobami](#resource-governance). Te widoki raport wykorzystania zasobów przez obciążenia użytkowników i określonych procesów wewnętrznych w pul skojarzonych zasobów i grup obciążenia.

W kontekście monitorowania wydajności i rozwiązywania problemów ważne jest, aby wziąć pod uwagę zarówno **zużycie procesora CPU przez użytkownika** (`avg_cpu_percent`, `cpu_percent`), jak i całkowite zużycie **procesora** przez obciążenia użytkowników i procesy wewnętrzne (`avg_instance_cpu_percent`,`sqlserver_process_core_percent`).

**Zużycie procesora CPU użytkownika** jest obliczane jako procent limitów obciążenia użytkownika w każdym celu usługi. **Wykorzystanie procesora CPU użytkownika** w 100% wskazuje, że obciążenie użytkownika osiągnęło limit celu usługi. Jednak gdy **całkowite zużycie procesora CPU** osiągnie zakres 70-100%, możliwe jest, aby zobaczyć spłaszczenie przepływności obciążenia użytkownika i zwiększenie opóźnienia zapytania, nawet jeśli zgłoszone zużycie **procesora CPU przez użytkownika** pozostaje znacznie poniżej 100%. Jest to bardziej prawdopodobne podczas korzystania z mniejszych celów usługi z umiarkowaną alokacją zasobów obliczeniowych, ale stosunkowo intensywne obciążenia użytkowników, takie jak w [gęstych pulach elastycznych.](sql-database-elastic-pool-resource-management.md) Może to również wystąpić przy mniejszych celów usługi, gdy procesy wewnętrzne tymczasowo wymagają dodatkowych zasobów, na przykład podczas tworzenia nowej repliki bazy danych.

Gdy **całkowite zużycie procesora CPU** jest wysokie, opcje łagodzenia są takie same, jak wspomniano wcześniej i obejmują zwiększenie celu usługi i/lub optymalizację obciążenia użytkownika.

## <a name="resource-governance"></a>Nadzór nad zasobami

Aby wymusić limity zasobów, usługa Azure SQL Database używa implementacji ładu zasobów opartej na [gubernatorze zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)programu SQL Server, zmodyfikowanej i rozszerzonej w celu uruchomienia usługi bazy danych programu SQL Server na platformie Azure. W każdym wystąpieniu programu SQL Server w usłudze istnieje wiele pul zasobów i [grup obciążeń,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)z [limitami](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) zasobów ustawionymi zarówno na poziomie puli, jak i grupy, aby zapewnić [zrównoważoną bazę danych jako usługę](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Obciążenie użytkowników i obciążenia wewnętrzne są klasyfikowane do oddzielnych pul zasobów i grup obciążeń. Obciążenie użytkownika w podstawowych i czytelnych replikach pomocniczych, w `SloSharedPool1` tym `UserPrimaryGroup.DBId[N]` replikach geograficznych, jest klasyfikowane do puli zasobów i grupy obciążenia, gdzie `N` oznacza wartość identyfikatora bazy danych. Ponadto istnieje wiele pul zasobów i grup obciążenia dla różnych obciążeń wewnętrznych.

Oprócz używania Gubernator zasobów do regulowania zasobów w ramach procesu SQL Server, usługa Azure SQL Database używa również [obiektów zadań](https://docs.microsoft.com/windows/win32/procthread/job-objects) systemu Windows do zarządzania zasobami na poziomie procesu i Menedżera zasobów serwera plików systemu Windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) do zarządzania przydziałami magazynu.

Zarządzanie zasobami usługi Azure SQL Database ma charakter hierarchiczny. Od góry do dołu limity są wymuszane na poziomie systemu operacyjnego i na poziomie woluminu magazynu przy użyciu mechanizmów zarządzania zasobami systemu operacyjnego i Resource Governor, następnie na poziomie puli zasobów przy użyciu Resource Governor, a następnie na poziomie grupy obciążenia przy użyciu Gubernator zasobów. Limity ładu zasobów obowiązujące dla bieżącej bazy danych lub puli elastycznej są wyświetlane w widoku [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Zarządzanie we/wy danych

Zarządzanie we/wy danych jest procesem w usłudze Azure SQL Database używanym do ograniczania zarówno odczytu, jak i zapisu fizycznego we/wy w plikach danych bazy danych. Limity operacji We/Wy są ustawiane dla każdego poziomu usługi, aby zminimalizować efekt "hałaśliwy sąsiad", aby zapewnić sprawiedliwość alokacji zasobów w usłudze wielodostępnej i pozostać w zasięgu możliwości podstawowego sprzętu i magazynu.

W przypadku pojedynczych baz danych limity grupy obciążenia są stosowane do wszystkich we/wy magazynu w bazie danych, podczas gdy `tempdb` limity puli zasobów mają zastosowanie do wszystkich we/wy magazynu dla wszystkich baz danych w tym samym wystąpieniu programu SQL Server, w tym bazy danych. W przypadku pul elastycznych limity grupy obciążenia mają zastosowanie do każdej bazy danych w `tempdb` puli, podczas gdy limit puli zasobów ma zastosowanie do całej puli elastycznej, w tym bazy danych, która jest współużytkowana przez wszystkie bazy danych w puli. Ogólnie rzecz biorąc limity puli zasobów mogą nie być osiągalne przez obciążenie w bazie danych (pojedyncze lub połączone), ponieważ limity grupy obciążenia są niższe niż limity puli zasobów i wcześniej ograniczyć przepływność we/ops/przepływności. Jednak limity puli mogą być osiągnięte przez połączone obciążenie dla wielu baz danych w tym samym wystąpieniu programu SQL Server.

Na przykład jeśli kwerenda generuje 1000 operacji We/Wy bez zarządzania zasobami we/wy, ale maksymalny limit operacji We/Wy grupy obciążenia jest ustawiony na 900 Operacji We/Wy, kwerenda nie będzie mogła wygenerować więcej niż 900 operacji We/Wy. Jeśli jednak maksymalny limit we/wy puli zasobów jest ustawiony na 1500 we/wy, a całkowita liczba we/wy ze wszystkich grup obciążeń skojarzonych z pulą zasobów przekracza 1500 we/wy we/wy, we/wy tej samej kwerendy może zostać zmniejszona poniżej limitu grupy roboczej 900 we/wy.

Wartości We/Wysch i przepływność min/max zwrócone przez widok [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) działają jako limity/limity, a nie jako gwarancje. Ponadto zarządzanie zasobami nie gwarantuje żadnych określonych opóźnień magazynu. Najlepsze osiągalne opóźnienie, operacji We/Wy i przepływność dla danego obciążenia użytkownika zależą nie tylko od limitów zarządzania zasobami we/wy, ale także od kombinacji używanych rozmiarów operacji we/wy oraz od możliwości magazynu źródłowego. Sql Server używa obiektów WE, które różnią się rozmiarem od 512 KB do 4 MB. Na potrzeby wymuszania limitów we/wy wszystkie we/wy są rozliczane niezależnie od jego rozmiaru, z wyjątkiem baz danych z plikami danych w usłudze Azure Storage. W takim przypadku we/wy większe niż 256 KB są rozliczane jako wiele 256 KB we/wy, aby wyrównać z kontem księgowaniem we/wy usługi Azure Storage.

W przypadku baz danych podstawowego, standardowego i ogólnego przeznaczenia, które używają plików danych w usłudze Azure Storage, `primary_group_max_io` wartość może nie być osiągalna, jeśli baza danych nie ma wystarczającej liczby plików danych, aby zbiorczo podać tę liczbę usług We/Wy lub jeśli dane nie są rozłożone równomiernie między plikami lub jeśli warstwa wydajności podstawowych obiektów blob ogranicza liczbę wyświetleń/przepływność poniżej limitu zarządzania zasobami. Podobnie z małych we/wy dziennika ios `primary_max_log_rate` generowane przez częste zatwierdzanie transakcji, wartość może nie być osiągalne przez obciążenie ze względu na limit IOPS na podstawowej platformy Azure magazynu obiektu blob.

Wartości wykorzystania zasobów, `avg_data_io_percent` `avg_log_write_percent`takie jak i , zgłoszone w [widokach sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys.elastic_pool_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) są obliczane jako wartości procentowe maksymalnych limitów zarządzania zasobami. W związku z tym gdy czynniki inne niż ograniczenie zarządzania zasobami IOPS/przepływność, jest możliwe, aby zobaczyć IOPS/przepływność spłaszczenie i opóźnienia wzrasta wraz ze wzrostem obciążenia, mimo że zgłaszane wykorzystanie zasobów pozostaje poniżej 100%. 

Aby wyświetlić odczyt i zapis we/wy, przepływność i opóźnienie na plik bazy danych, należy użyć funkcji [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Ta funkcja powierzchni wszystkie we/wy do bazy danych, w `avg_data_io_percent`tym we/wy w tle, który nie jest rozliczany do , ale używa we/wy i przepływności podstawowej pamięci masowej i może mieć wpływ obserwowane opóźnienie magazynu. Funkcja również powierzchni dodatkowe opóźnienie, które mogą być wprowadzone przez zarządzanie zasobami `io_stall_queued_read_ms` we/wy dla odczytów i zapisów, w i `io_stall_queued_write_ms` kolumny odpowiednio.

### <a name="transaction-log-rate-governance"></a>Zarządzanie kursem dziennika transakcji

Zarządzanie szybkością dziennika transakcji jest procesem w usłudze Azure SQL Database używanym do ograniczania wysokich stawek pozyskiwania dla obciążeń, takich jak zbiorcze wstawianie, wybierz do i kompilacje indeksów. Te limity są śledzone i wymuszane na poziomie podsekund do szybkości generowania rekordów dziennika, ograniczając przepływność niezależnie od liczby usług we/wy mogą być wydawane dla plików danych.  Szybkość generowania dziennika transakcji jest obecnie skalowana liniowo do punktu zależnego od sprzętu, przy czym maksymalna szybkość dziennika jest dozwolona na poziomie 96 MB/s w przypadku modelu zakupu vCore. 

> [!NOTE]
> Rzeczywiste fizyczne operacje we/wy do plików dziennika transakcji nie są regulowane lub ograniczone.

Stawki dziennika są ustawione w taki sposób, że mogą być osiągnięte i trwałe w różnych scenariuszach, podczas gdy ogólny system może utrzymać swoją funkcjonalność z zminimalizowanym wpływem na obciążenie użytkownika. Zarządzanie szybkością dziennika zapewnia, że kopie zapasowe dziennika transakcji pozostają w opublikowanych logowania do sla.  To zarządzanie zapobiega również nadmierne zaległości w replikach pomocniczych.

Jak rekordy dziennika są generowane, każda operacja jest oceniane i oceniane pod kątem tego, czy należy opóźnić w celu utrzymania maksymalnej żądanej szybkości dziennika (MB/s na sekundę). Opóźnienia nie są dodawane, gdy rekordy dziennika są opróżniane do magazynu, a zarządzanie szybkością dziennika jest stosowany podczas generowania stawki dziennika.

Rzeczywiste szybkości generowania dziennika nałożone w czasie wykonywania mogą być również pod wpływem mechanizmów sprzężenia zwrotnego, tymczasowo zmniejszając dopuszczalne stawki dziennika, dzięki czemu system może się ustabilizować. Zarządzanie przestrzenią plików dziennika, unikanie wbiegania z warunków miejsca dziennika i mechanizmów replikacji grupy dostępności może tymczasowo zmniejszyć ogólne limity systemowe.

Kształtowanie ruchu regulatora szybkości dzienników jest widoczne za pośrednictwem następujących typów oczekiwania (ujawnionych w [pliku sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ oczekiwania | Uwagi |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Ograniczanie bazy danych |
| POOL_LOG_RATE_GOVERNOR | Ograniczenie puli |
| INSTANCE_LOG_RATE_GOVERNOR | Ograniczanie poziomu wystąpienia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Kontrola opinii, fizyczna replikacja grupy dostępności w wersji Premium/Business Critical nie nadąża za |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Kontrola sprzężenia zwrotnego, ograniczenie szybkości w celu uniknięcia stanu przestrzeni poza dziennikiem |
|||

W przypadku napotkania limitu szybkości dziennika, który utrudnia żądaną skalowalność, należy wziąć pod uwagę następujące opcje:
- Skalowanie do wyższego poziomu usług w celu uzyskania maksymalnej szybkości dziennika 96 MB/s lub przełączenie się do innej warstwy usług. Warstwa usługi [hiperskali](sql-database-service-tier-hyperscale.md) zapewnia szybkość dziennika 100 MB/s niezależnie od wybranego poziomu usług.
- Jeśli dane są ładowane jest przejściowy, takich jak dane tymczasowe w procesie ETL, może być ładowany do tempdb (który jest minimalnie rejestrowane). 
- W przypadku scenariuszy analitycznych załaduj do tabeli objętej klastrowanym magazynem kolumn. Zmniejsza to wymaganą szybkość dziennika z powodu kompresji. Ta technika zwiększa wykorzystanie procesora CPU i ma zastosowanie tylko do zestawów danych, które korzystają z indeksów klastrowanego magazynu kolumn. 

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat ogólnych limitów platformy Azure, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Aby uzyskać informacje na temat procesorów DTU i eDTU, zobacz [Dtu i eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Aby uzyskać informacje na temat limitów rozmiaru bazy danych tempdb, zobacz [TempDB w bazie danych SQL Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
