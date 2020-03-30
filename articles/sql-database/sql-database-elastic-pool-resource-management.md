---
title: Zarządzanie zasobami w gęstych basenach elastycznych | Dokumenty firmy Microsoft
description: Zarządzanie zasobami obliczeniowymi w pulach elastycznych SQL platformy Azure z wieloma bazami danych.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473731"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Zarządzanie zasobami w ramach gęstych pul elastycznych

[Pule elastyczne](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) usługi Azure SQL Database to ekonomiczne rozwiązanie do zarządzania wieloma bazami danych o różnym użyciu zasobów. Wszystkie bazy danych w puli elastycznej współużytkują tę samą alokację zasobów, takich jak procesor, pamięć, wątki robocze, miejsce do magazynowania, tempdb, przy założeniu, że **tylko podzbiór baz danych w puli będzie używał zasobów obliczeniowych w danym momencie.** To założenie umożliwia elastyczne pule być opłacalne. Zamiast płacić za wszystkie zasoby, których może potencjalnie potrzebować poszczególne bazy danych, klienci płacą za znacznie mniejszy zestaw zasobów, współużytkowane przez wszystkie bazy danych w puli.

## <a name="resource-governance"></a>Nadzór nad zasobami

Udostępnianie zasobów wymaga systemu do starannego kontrolowania użycia zasobów, aby zminimalizować efekt "hałaśliwy sąsiad", gdzie baza danych o wysokim zużyciu zasobów wpływa na inne bazy danych w tej samej puli elastycznej. Jednocześnie system musi zapewnić wystarczające zasoby dla funkcji, takich jak wysoka dostępność i odzyskiwanie po awarii (HADR), tworzenie kopii zapasowych i przywracanie, monitorowanie, magazyn zapytań, automatyczne dostrajanie itp., aby działać niezawodnie.

Usługa Azure SQL Database osiąga te cele przy użyciu wielu mechanizmów zarządzania zasobami, w tym [obiektów zadań](https://docs.microsoft.com/windows/win32/procthread/job-objects) systemu Windows do zarządzania zasobami na poziomie procesu, Menedżera zasobów serwera plików systemu Windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) do zarządzania przydziałami magazynu oraz zmodyfikowanej i rozszerzonej wersji serwera SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) w celu zaimplementowania zarządzania zasobami w każdym wystąpieniu programu SQL Server uruchomionym w bazie danych SQL Azure.

Głównym celem projektu basenów elastycznych ma być opłacalne. Z tego powodu system celowo umożliwia klientom tworzenie _gęstych_ pul, czyli pul z liczbą zbliżających się baz danych lub z maksymalną dozwoloną wartością, ale z umiarkowaną alokacją zasobów obliczeniowych. Z tego samego powodu system nie rezerwuje wszystkich potencjalnie potrzebnych zasobów dla swoich procesów wewnętrznych, ale umożliwia udostępnianie zasobów między procesami wewnętrznymi i obciążeniami użytkowników.

Takie podejście pozwala klientom korzystać z gęstych elastycznych pul, aby osiągnąć odpowiednią wydajność i znaczne oszczędności kosztów. Jednak jeśli obciążenie w wielu bazach danych w gęstej puli jest wystarczająco intensywne, rywalizacja o zasoby staje się znacząca. Rywalizacja o zasoby zmniejsza wydajność obciążenia użytkownika i może negatywnie wpłynąć na procesy wewnętrzne.

> [!IMPORTANT]
> W gęstych pulach z wieloma aktywnymi bazami danych może nie być możliwe zwiększenie liczby baz danych w puli do maksymalnych wartości udokumentowanych dla puli elastycznej [jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md) i [vCore.](sql-database-vcore-resource-limits-elastic-pools.md)
> 
> Liczba baz danych, które mogą być umieszczane w gęstych pulach bez powodowania rywalizacji o zasoby i problemów z wydajnością zależy od liczby jednocześnie aktywnych baz danych i zużycia zasobów przez obciążenia użytkowników w każdej bazie danych. Liczba ta może ulec zmianie wraz ze zmianą obciążeń użytkowników.

Gdy rywalizacja o zasoby występuje w puli gęsto zapakowane, klienci mogą wybrać jedną lub więcej z następujących akcji, aby go złagodzić:
- Dostrajanie obciążenia zapytania w celu zmniejszenia zużycia zasobów lub rozmieszczania zużycia zasobów w wielu bazach danych w czasie.
- Zmniejsz gęstość puli, przenosząc niektóre bazy danych do innej puli lub tworząc autonomiczne bazy danych.
- Skalowanie w górę puli, aby uzyskać więcej zasobów.

Aby uzyskać sugestie dotyczące sposobu implementacji dwóch ostatnich akcji, zobacz Zalecenia operacyjne w [dalszej](#operational-recommendations) części tego artykułu. Zmniejszenie rywalizacji o zasoby przynosi korzyści zarówno obciążeń użytkowników, jak i procesów wewnętrznych i pozwala systemowi niezawodnie utrzymywać oczekiwany poziom usług.

## <a name="monitoring-resource-consumption"></a>Monitorowanie zużycia zasobów

Aby uniknąć pogorszenia wydajności z powodu rywalizacji o zasoby, klienci korzystający z gęstych pul elastycznych powinni aktywnie monitorować zużycie zasobów i podejmować działania w odpowiednim czasie, jeśli zwiększanie rywalizacji o zasoby zaczyna wpływać na obciążenia. Ciągłe monitorowanie jest ważne, ponieważ użycie zasobów w puli zmienia się wraz z czasem, ze względu na zmiany w obciążeniu użytkownika, zmiany w woluminach danych i dystrybucji, zmiany w gęstości puli i zmiany w usłudze Azure SQL Database. 

Usługa Azure SQL Database zawiera kilka metryk, które są istotne dla tego typu monitorowania. Przekroczenie zalecanej średniej wartości dla każdej metryki wskazuje rywalizację o zasoby w puli i należy rozwiązać przy użyciu jednej z akcji wymienionych wcześniej.

|Nazwa metryki|Opis|Zalecana średnia wartość| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Wykorzystanie procesora CPU procesu programu SQL Server skojarzonego z pulą elastyczną, mierzoną przez podstawowy system operacyjny. Dostępne w widoku [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) w każdej bazie danych i w widoku [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta metryka jest również emitowana do usługi Azure Monitor, gdzie jest [nazwany](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`i można go wyświetlić w witrynie Azure portal. Ta wartość jest taka sama dla każdej bazy danych w tej samej puli elastycznej.|Poniżej 70%. Sporadyczne krótkie skoki do 90% mogą być dopuszczalne.|
|`max_worker_percent`|[Wykorzystanie wątku roboczego.]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) Przewidziane dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity liczby wątków roboczych na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) w każdej bazie danych i w widoku [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta metryka jest również emitowana do usługi Azure Monitor, gdzie jest [nazwany](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`i można go wyświetlić w witrynie Azure portal.|Poniżej 80%. Skoki do 100% spowoduje próby połączenia i kwerendy zakończyć się niepowodzeniem.|
|`avg_data_io_percent`|Wykorzystanie we/wy do odczytu i zapisu fizycznego we/wy. Przewidziane dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity liczby we/wy na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) w każdej bazie danych i w widoku [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta metryka jest również emitowana do usługi Azure Monitor, gdzie jest [nazwany](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`i można go wyświetlić w witrynie Azure portal.|Poniżej 80%. Sporadyczne krótkie skoki do 100% mogą być dopuszczalne.|
|`avg_log_write_percent`|Wykorzystanie przepływności dla dziennika transakcji zapisu we/wy. Przewidziane dla każdej bazy danych w puli, a także dla samej puli. Istnieją różne limity przepływności dziennika na poziomie bazy danych i na poziomie puli, dlatego zaleca się monitorowanie tej metryki na obu poziomach. Dostępne w widoku [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) w każdej bazie danych i w widoku [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta metryka jest również emitowana do usługi Azure Monitor, gdzie jest [nazwany](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`i można go wyświetlić w witrynie Azure portal. Gdy ta metryka jest bliska 100%, wszystkie modyfikacje bazy danych (INSERT, UPDATE, DELETE, MERGE statements, SELECT ... DO, WKŁADKA ZBIORCZA itp.) będzie wolniejszy.|Poniżej 90%. Sporadyczne krótkie skoki do 100% mogą być dopuszczalne.|
|`oom_per_second`|Szybkość błędów braku pamięci (OOM) w puli elastycznej, która jest wskaźnikiem ciśnienia pamięci. Dostępne w widoku [sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Aby obliczyć tę metrykę, zobacz [Przykłady](#examples) przykładowej kwerendy.|0|
|`avg_storage_percent`|Wykorzystanie miejsca do magazynowania na poziomie puli elastycznej. Dostępne w widoku [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) w `master` bazie danych. Ta metryka jest również emitowana do usługi Azure Monitor, gdzie jest [nazwany](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`i można go wyświetlić w witrynie Azure portal.|Poniżej 80%. Można zbliżyć się 100% dla pul bez wzrostu danych.|
|`tempdb_log_used_percent`|Wykorzystanie miejsca w dzienniku `tempdb` transakcji w bazie danych. Mimo że obiekty tymczasowe utworzone w jednej bazie danych nie są `tempdb` widoczne w innych bazach danych w tej samej puli elastycznej, jest zasobem udostępnionym dla wszystkich baz danych w tej samej puli. Długotrwała lub bezczynna transakcja `tempdb` uruchomiona z jednej bazy danych w puli może zużywać dużą część dziennika transakcji i powodować błędy dla kwerend w innych bazach danych w tej samej puli. Dostępne w widoku [sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Ta metryka jest również emitowana do usługi Azure Monitor i można ją wyświetlić w witrynie Azure portal. Zobacz [przykłady](#examples) przykładowej kwerendy, aby zwrócić bieżącą wartość tej metryki.|Poniżej 50%. Sporadyczne skoki do 80% są dopuszczalne.|
|||

Oprócz tych metryk usługa Azure SQL Database udostępnia widok, który zwraca rzeczywiste limity ładu zasobów, a także dodatkowe widoki, które zwracają statystyki wykorzystania zasobów na poziomie puli zasobów i na poziomie grupy obciążenia.

|Wyświetl nazwę|Opis|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Zwraca rzeczywiste ustawienia konfiguracji i pojemności używane przez mechanizmy ładu zasobów w bieżącej bazie danych lub puli elastycznej.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Zwraca informacje o bieżącym stanie puli zasobów, bieżącej konfiguracji pul zasobów i zbiorczych statystykach puli zasobów.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Zwraca zbiorcze statystyki grupy obciążenia i bieżącą konfigurację grupy obciążenia. Ten widok można połączyć z sys.dm_resource_governor_resource_pools `pool_id` w kolumnie, aby uzyskać informacje o puli zasobów.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Zwraca statystyki wykorzystania puli zasobów dla ostatnich 32 minut. Każdy wiersz reprezentuje interwał 20-sekundowy. Kolumny `delta_` zwracają zmiany w każdej statystyce w interwale.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Zwraca statystyki wykorzystania grupy obciążenia dla ostatnich 32 minut. Każdy wiersz reprezentuje interwał 20-sekundowy. Kolumny `delta_` zwracają zmiany w każdej statystyce w interwale.|
|||

Widoki te mogą służyć do monitorowania wykorzystania zasobów i rozwiązywania problemów z rywalizacją o zasoby w czasie zbliżonym do rzeczywistego. Obciążenie użytkownika w podstawowych i czytelnych replikach pomocniczych, w `SloSharedPool1` tym `UserPrimaryGroup.DBId[N]` replikach geograficznych, jest klasyfikowane do puli zasobów i grupy obciążenia, gdzie `N` oznacza wartość identyfikatora bazy danych.

Oprócz monitorowania bieżącego wykorzystania zasobów klienci korzystający z gęstych pul mogą obsługiwać historyczne dane wykorzystania zasobów w oddzielnym magazynie danych. Te dane mogą być używane w analizie predykcyjnej do proaktywnego zarządzania wykorzystaniem zasobów na podstawie trendów historycznych i sezonowych.

## <a name="operational-recommendations"></a>Zalecenia operacyjne

**Zostaw wystarczającą ilość zasobów.** Jeśli występuje rywalizacja o zasoby i obniżenie wydajności, ograniczenie może obejmować przeniesienie niektórych baz danych z puli elastycznej, którego dotyczy problem, lub skalowanie puli, jak wspomniano wcześniej. Jednak te akcje wymagają dodatkowych zasobów obliczeniowych, aby zakończyć. W szczególności dla pul Premium i Business Critical te akcje wymagają przesyłania wszystkich danych dla przenoszonych baz danych lub dla wszystkich baz danych w puli elastycznej, jeśli pula jest skalowana w górę. Transfer danych jest długotrwałą i zasobochłonną operacją. Jeśli pula jest już pod wysokim ciśnieniem zasobów, sama operacja łagodzący jeszcze bardziej obniży wydajność. W skrajnych przypadkach może nie być możliwe rozwiązanie rywalizacji o zasoby za pośrednictwem przenoszenia bazy danych lub skalowania puli w górę, ponieważ wymagane zasoby nie są dostępne. W takim przypadku tymczasowe zmniejszenie obciążenia zapytania na puli elastycznej, którego dotyczy problem, może być jedynym rozwiązaniem.

Klienci korzystający z gęstych pul należy ściśle monitorować trendy wykorzystania zasobów, jak opisano wcześniej i podjąć działania łagodzące, podczas gdy metryki pozostają w zalecanych zakresów i nadal istnieją wystarczające zasoby w puli elastycznej.

Wykorzystanie zasobów zależy od wielu czynników, które zmieniają się w czasie dla każdej bazy danych i każdej puli elastycznej. Osiągnięcie optymalnego stosunku ceny do wydajności w gęstych pulach wymaga ciągłego monitorowania i równoważenia, czyli przenoszenia baz danych z bardziej wykorzystywanych pul do mniej wykorzystywanych pul i tworzenia nowych pul w razie potrzeby w celu uwzględnienia zwiększonego obciążenia.

**Nie należy przenosić "gorących" baz danych**. Jeśli rywalizacja o zasoby na poziomie puli jest przede wszystkim spowodowane przez niewielką liczbę wysoce wykorzystywanych baz danych, może być kuszące, aby przenieść te bazy danych do mniej wykorzystanej puli lub uczynić je autonomicznymi bazami danych. Jednak w ten sposób, gdy baza danych pozostaje wysoce wykorzystywane nie jest zalecane, ponieważ operacja przenoszenia będzie dalsze obniżenie wydajności, zarówno dla bazy danych są przenoszone, jak i dla całej puli. Zamiast tego poczekaj, aż wysokie wykorzystanie ustępuje, albo przenieś mniej wykorzystane bazy danych, zamiast tego zmniejszy presję zasobów na poziomie puli. Ale przenoszenie baz danych o bardzo niskim wykorzystaniu nie zapewnia żadnych korzyści w tym przypadku, ponieważ nie zmniejsza istotnie wykorzystania zasobów na poziomie puli.

**Tworzenie nowych baz danych w puli "kwarantanny"**. W scenariuszach, w których często tworzone są nowe bazy danych, takie jak aplikacje korzystające z modelu dzierżawy na bazę danych, istnieje ryzyko, że nowa baza danych umieszczona w istniejącej puli elastycznej nieoczekiwanie zużywa znaczne zasoby i wpływa na inne bazy danych i procesów wewnętrznych w puli. Aby ograniczyć to ryzyko, należy utworzyć oddzielną pulę "kwarantanny" z dużą alokacją zasobów. Użyj tej puli dla nowych baz danych z jeszcze nieznanych wzorców zużycia zasobów. Gdy baza danych została wstrzymana w tej puli dla cyklu biznesowego, na przykład tydzień lub miesiąc, a jej zużycie zasobów jest znane, można przenieść do puli o wystarczającej pojemności, aby pomieścić to dodatkowe użycie zasobów.

**Unikaj zbyt gęstych serwerów logicznych**. Usługa Azure SQL Database [obsługuje](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) maksymalnie 5000 baz danych na serwer logiczny. Klienci korzystający z pul elastycznych z tysiącami baz danych mogą rozważyć umieszczenie wielu pul elastycznych na jednym serwerze, z całkowitą liczbą baz danych do obsługiwanego limitu. Jednak serwery logiczne z wieloma tysiącami baz danych stwarzają wyzwania operacyjne. Operacje, które wymagają wyliczenia wszystkich baz danych na serwerze, na przykład wyświetlanie baz danych w portalu, będą wolniejsze. Błędy operacyjne, takie jak nieprawidłowa modyfikacja logowania na poziomie serwera lub reguły zapory, będą miały wpływ na większą liczbę baz danych. Przypadkowe usunięcie serwera logicznego będzie wymagało pomocy pomocy ze strony pomocy technicznej firmy Microsoft w celu odzyskania baz danych na usuniętym serwerze i spowoduje długotrwałą awarię wszystkich baz danych, których dotyczy problem.

Zaleca się ograniczenie liczby baz danych na serwer logiczny do mniejszej liczby niż maksymalna obsługiwana. W wielu scenariuszach przy użyciu do 1000-2000 baz danych na serwer jest optymalne. Aby zmniejszyć prawdopodobieństwo przypadkowego usunięcia serwera, zaleca się umieszczenie [blokady usuwania](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) na serwerze logicznym lub jego grupie zasobów.

W przeszłości niektóre scenariusze obejmujące przenoszenie baz danych w, out lub między pulami elastycznymi na tym samym serwerze logicznym były szybsze niż podczas przenoszenia baz danych między serwerami logicznymi. Obecnie wszystkie ruchy bazy danych są wykonywane z tą samą prędkością, niezależnie od źródła i docelowego serwera logicznego.

## <a name="examples"></a>Przykłady

### <a name="monitoring-memory-utilization"></a>Monitorowanie wykorzystania pamięci

Ta kwerenda `oom_per_second` oblicza metrykę dla każdej puli zasobów w ciągu ostatnich 32 minut. Ta kwerenda może być wykonana w dowolnej bazie danych w puli elastycznej.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorowanie `tempdb` wykorzystania miejsca w dzienniku

Ta kwerenda zwraca bieżącą wartość `tempdb_log_used_percent` metryki. Ta kwerenda może być wykonana w dowolnej bazie danych w puli elastycznej.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do pul elastycznych, zobacz [Pule elastyczne ułatwiają zarządzanie wieloma bazami danych SQL platformy Azure i ich skalowanie.](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Aby uzyskać informacje na temat dostrajania obciążeń zapytań w celu zmniejszenia wykorzystania zasobów, zobacz [Monitorowanie i dostrajanie]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)oraz [Monitorowanie i dostrajanie wydajności](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
