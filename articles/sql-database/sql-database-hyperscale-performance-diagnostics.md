---
title: Diagnostyka wydajności w hiperskali
description: W tym artykule opisano sposób rozwiązywania problemów z wydajnością hiperskali w usłudze Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615059"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnostyka rozwiązywania problemów z wydajnością sql hyperscale

Aby rozwiązać problemy z wydajnością w bazie danych w hiperskali, [ogólne metodologie dostrajania wydajności](sql-database-monitor-tune-overview.md) w węźle obliczeniowym bazy danych SQL platformy Azure jest punktem wyjścia badania wydajności. Jednak biorąc pod uwagę [rozproszoną architekturę](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) hiperskali, dodatkowa diagnostyka została dodana do pomocy. W tym artykule opisano dane diagnostyczne specyficzne dla hiperskalii.

## <a name="log-rate-throttling-waits"></a>Oczekiwanie ograniczania szybkości dziennika

Każdy poziom usługi Usługi Azure SQL Database ma limity szybkości generowania dzienników wymuszane za pomocą [zarządzania szybkością dziennika.](sql-database-resource-limits-database-server.md#transaction-log-rate-governance) W hiperskali limit generowania dziennika jest obecnie ustawiony na 100 MB/s, niezależnie od poziomu usługi. Istnieją jednak czasy, gdy szybkość generowania dziennika w podstawowej repliki obliczeń musi być ograniczona w celu utrzymania ujednoliciwalów SLA odzyskiwalności. To ograniczanie występuje, gdy [serwer strony lub inna replika obliczeniowa](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) jest znacznie za zastosowanie nowych rekordów dziennika z usługi Dziennika.

Następujące typy oczekiwania (w [pliku sys.dm_os_wait_stats)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)opisują powody, dla których szybkość dziennika może być ograniczona do podstawowej repliki obliczeń:

|Typ oczekiwania    |Opis                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Występuje, gdy szybkość generowania podstawowego węzła obliczeniowego węzła obliczeniowego bazy danych jest ograniczana z powodu opóźnionego zużycia dziennika na serwerach stron.         |
|RBIO_RG_DESTAGE        | Występuje, gdy szybkość generowania węzła obliczeniowego węzła danych hiperskali jest ograniczana z powodu opóźnionego zużycia dziennika przez magazyn dziennika długoterminowego.         |
|RBIO_RG_REPLICA        | Występuje, gdy szybkość generowania modułu obliczeniowego węzła obliczeniowego bazy danych jest ograniczana z powodu opóźnionego zużycia dziennika przez czytelne repliki pomocnicze.         |
|RBIO_RG_LOCALDESTAGE   | Występuje, gdy szybkość generowania węzła obliczeniowego węzła danych hiperskali jest ograniczana z powodu opóźnionego zużycia dziennika przez usługę dziennika.         |

## <a name="page-server-reads"></a>Odczyty serwera strony

Repliki obliczeniowe nie buforują pełnej kopii bazy danych lokalnie. Dane lokalne do repliki obliczeniowej są przechowywane w puli buforów (w pamięci) i w lokalnej pamięci podręcznej Rozszerzenie puli buforów odpornych (RBPEX), która jest częściową (nie obejmującą) pamięci podręcznej stron danych. Ta lokalna pamięć podręczna RBPEX jest dopasowywać się proporcjonalnie do rozmiaru obliczeń i jest trzykrotnie większa od pamięci warstwy obliczeniowej. RBPEX jest podobny do puli buforów, ponieważ ma najczęściej używane dane. Każdy serwer strony, z drugiej strony, ma pokrycie pamięci podręcznej RBPEX dla części bazy danych, która utrzymuje.
 
Gdy odczyt jest wystawiany w replice obliczeniowej, jeśli dane nie istnieją w puli buforów lub lokalnej pamięci podręcznej RBPEX, wywoływane jest wywołanie funkcji getPage(pageId, LSN), a strona jest pobierana z odpowiedniego serwera strony. Odczyty z serwerów stron są odczytami zdalnymi i dlatego są wolniejsze niż odczyty z lokalnego RBPEX. Podczas rozwiązywania problemów z wydajnością związanych z we/wy, musimy być w stanie stwierdzić, ile iOs zostały wykonane za pośrednictwem stosunkowo wolniej zdalnego serwera strony odczytuje.

Kilka dmv i rozszerzone zdarzenia mają kolumny i pola, które określają liczbę odczytów zdalnych z serwera strony, które mogą być porównywane z całkowitą odczyty. Query store przechwytuje również zdalne odczyty jako część statystyk czasu wykonywania kwerendy.

- Kolumny do odczytów serwera strony raportu są dostępne w realizacji dmv i widoków katalogu, takich jak:
    - [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [Sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [Sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Odczyty serwera strony są dodawane do następujących zdarzeń rozszerzonych:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - store_execution_runtime_info zapytania
- ActualPageServerReads/ActualPageServerReadAheads są dodawane do planu kwerend XML dla rzeczywistych planów. Przykład:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Aby wyświetlić te atrybuty w oknie właściwości planu kwerendy, wymagany jest system SSMS 18.3 lub nowszy.

## <a name="virtual-file-stats-and-io-accounting"></a>Statystyki plików wirtualnych i księgowanie we/wy

W usłudze Azure SQL Database plik DMF [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) jest podstawowym sposobem monitorowania we/wy programu SQL Server. Charakterystyka we/wy w hiperskali są różne ze względu na [jego architekturę rozproszoną.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) W tej sekcji koncentrujemy się na We/Wy (odczyty i zapisy) do plików danych, jak widać w tym DMF. W hiperskali każdy plik danych widoczny w tym pliku DMF odpowiada serwerowi strony zdalnej. Pamięć podręczna RBPEX, o których mowa w tym miejscu, jest lokalną pamięcią podręczną opartą na SSD, która jest nieobrywającą pamięci podręcznej w replice obliczeniowej.

### <a name="local-rbpex-cache-usage"></a>Użycie lokalnej pamięci podręcznej RBPEX

Lokalna pamięć podręczna RBPEX istnieje w replice obliczeniowej na lokalnym magazynie SSD. W związku z tym we/wy w stosunku do tej pamięci podręcznej jest szybszy niż we/wy względem serwerów strony zdalnej. Obecnie [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) w bazie danych hiperskali ma specjalny wiersz raportowania we/wy dla lokalnej pamięci podręcznej RBPEX w replice obliczeniowej. Ten wiersz ma wartość 0 `database_id` `file_id` dla obu i kolumn. Na przykład poniższa kwerenda zwraca statystyki użycia RBPEX od momentu uruchomienia bazy danych.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Stosunek odczytów odbywa się na RBPEX do zagregowanych odczytów odbywa się na wszystkich innych plików danych zapewnia RBPEX cache hit ratio.

### <a name="data-reads"></a>Odczyty danych

- Gdy odczyty są wydawane przez aparat programu SQL Server w replice obliczeniowej, mogą być obsługiwane przez lokalną pamięć podręczną RBPEX lub zdalne serwery stron lub przez kombinację dwóch, jeśli czytanie wielu stron.
- Gdy replika obliczeniowa odczytuje niektóre strony z określonego pliku, na przykład file_id 1, jeśli te dane znajdują się wyłącznie w lokalnej pamięci podręcznej RBPEX, wszystkie we/wy dla tego odczytu są rozliczane z file_id 0 (RBPEX). Jeśli część tych danych znajduje się w lokalnej pamięci podręcznej RBPEX, a część znajduje się na serwerze strony zdalnej, we/wy jest rozliczana w file_id 0 dla części obsługiwanej przez RBPEX, a część obsługiwana z serwera strony zdalnej jest rozliczana w kierunku file_id 1. 
- Gdy replika obliczeniowa żąda strony w określonym [numerze LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) z serwera strony, jeśli serwer strony nie został przechwycony do żądanej sieci LSN, odczyt na replice obliczeniowej będzie czekać, aż serwer strony dogoni, zanim strona zostanie zwrócona do repliki obliczeniowej. Dla każdego odczytu z serwera strony na replice obliczeniowej, zobaczysz typ oczekiwania PAGEIOLATCH_ *, jeśli oczekuje na tej we/wy. W hiperskali ten czas oczekiwania obejmuje zarówno czas, aby nadrobić żądaną stronę na serwerze strony do LSN wymagane, a czas potrzebny do przeniesienia strony z serwera strony do repliki obliczeniowej.
- Duże odczyty, takie jak odczyt z wyprzedzeniem, są często wykonywane za pomocą ["Scatter-Gather" Odczyty](/sql/relational-databases/reading-pages/). Umożliwia to odczyty do 4 MB stron naraz, uważany za pojedynczy odczyt w silniku programu SQL Server. Jednak gdy dane są odczytywane w RBPEX, te odczyty są rozliczane jako wiele indywidualnych odczytów 8 KB, ponieważ pula buforów i RBPEX zawsze używają stron 8 KB. W rezultacie liczba odczytu IO widoczne w RBPEX może być większa niż rzeczywista liczba IOs wykonywane przez silnik.

### <a name="data-writes"></a>Zapisy danych

- Podstawowa replika obliczeniowa nie zapisuje bezpośrednio na serwerach stron. Zamiast tego rekordy dziennika z usługi Dziennik są odtwarzane na odpowiednich serwerach strony. 
- Zapisy, które mają miejsce w replice obliczeniowej są głównie zapisuje do lokalnego RBPEX (file_id 0). W przypadku zapisów w plikach logicznych, które są większe niż 8 KB, innymi słowy te wykonywane przy użyciu [gather-write,](/sql/relational-databases/writing-pages/)każda operacja zapisu jest tłumaczona na wiele indywidualnych zapisów 8 KB do RBPEX, ponieważ pula buforów i RBPEX zawsze używają stron 8 KB. W rezultacie liczba zapisu IO widoczne dla RBPEX może być większa niż rzeczywista liczba IOs wykonywane przez silnik.
- Pliki inne niż RBPEX lub pliki danych inne niż file_id 0, które odpowiadają serwerom stron, również pokazują zapisy. W warstwie usług hiperskali te zapisy są symulowane, ponieważ repliki obliczeniowe nigdy nie zapisują bezpośrednio na serwerach stron. Zapis We/Wy i przepływność są rozliczane w miarę ich występowania w replice obliczeniowej, ale opóźnienie plików danych innych niż file_id 0 nie odzwierciedla rzeczywistego opóźnienia zapisów serwera strony.

### <a name="log-writes"></a>Zapisy dziennika

- W obliczeniach podstawowych zapis dziennika jest rozliczany w file_id 2 sys.dm_io_virtual_file_stats. Zapis dziennika na podstawowym obliczeń jest zapis do strefy docelowej dziennika.
- Rekordy dziennika nie są hartowane w replice pomocniczej w zatwierdzeniu. W hiperskali dziennik jest stosowany przez usługę Log do replik pomocniczych asynchronicznie. Ponieważ zapisy dziennika w rzeczywistości nie występują w replikach pomocniczych, wszelkie księgowanie we/wy dzienników w replikach pomocniczych służy tylko do śledzenia.

## <a name="data-io-in-resource-utilization-statistics"></a>We/wy we dane w statystykach wykorzystania zasobów

W bazie danych bez hiperskali połączone odczytywanie i zapisywanie we/wy dla plików danych względem limitu IOPS danych [zarządzania zasobami](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) są zgłaszane w `avg_data_io_percent` [widokach sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) w kolumnie. Ta sama wartość jest zgłaszana w portalu jako _procent we/wy danych_. 

W bazie danych w hiperskali ta kolumna raportuje wykorzystanie we/wy danych względem limitu tylko dla magazynu `tempdb`lokalnego w replice obliczeniowej, w szczególności we/wy względem RBPEX i . Wartość 100% w tej kolumnie wskazuje, że zarządzanie zasobami ogranicza usługi we/wy magazynu lokalnego. Jeśli jest to skorelowane z problemem wydajności, dostroić obciążenie, aby wygenerować mniej we/wy lub zwiększyć cel usługi bazy danych, aby zwiększyć [limit](sql-database-vcore-resource-limits-single-databases.md) _IOPS max danych_ zarządzania zasobami . W przypadku zarządzania zasobami odczytów i zapisów RBPEX system zlicza poszczególne 8 KB we/wy, a nie większe we/wy, które mogą być wystawiane przez aparat programu SQL Server. 

We/Wy danych względem zdalnych serwerów stron nie są zgłaszane w widokach wykorzystania zasobów ani w portalu, ale są zgłaszane w [pliku DMF sys.dm_io_virtual_file_stats(),](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) jak wspomniano wcześniej.


## <a name="additional-resources"></a>Zasoby dodatkowe

- Limity zasobów w ykonie dla pojedynczej bazy danych hiperskali zobacz [Limity vcore warstwy usług hiperskali, zobacz Limity czeków wirtualnych warstwy usług hiperskali](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- W przypadku dostrajania wydajności usługi Azure SQL Database zobacz [Wydajność kwerendy w bazie danych SQL usługi Azure](sql-database-performance-guidance.md)
- Aby dostroić wydajność przy użyciu Magazynu zapytań, zobacz [Monitorowanie wydajności przy użyciu magazynu zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- W przypadku skryptów monitorowania DMV zobacz [Monitorowanie wydajności usługi Azure SQL Database przy użyciu dynamicznych widoków zarządzania](sql-database-monitoring-with-dmvs.md)
