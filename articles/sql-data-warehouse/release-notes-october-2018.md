---
title: Usługa Azure SQL Data Warehouse — informacje o wersji października 2018 | Dokumentacja firmy Microsoft
description: Informacje o wersji dla usługi Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/14/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 1edac9f7eac1f47974f4c94f3cae5bb3451f92fd
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705382"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Co nowego w usłudze Azure SQL Data Warehouse? Październik 2018 r.
Usługa Azure SQL Data Warehouse odbiera ulepszenia stale. W tym artykule opisano nowe funkcje i zmiany, które zostały wprowadzone w października 2018 roku.

## <a name="devops-for-data-warehousing"></a>Metodyka DevOps na potrzeby magazynowania danych
Wysoce żądanej funkcji usługi SQL Data Warehouse (SQL data Warehouse) jest teraz w wersji zapoznawczej w pomocy technicznej dla programu SQL Server dane narzędzie (SSDT) w programie Visual Studio! Zespoły deweloperów można teraz współpracować nad i kontrolą wersji, pojedynczej bazie kodu i szybkie wdrożenie zmiany do dowolnego wystąpienia na całym świecie. Są Państwo zainteresowani dołączania? Ta funkcja jest obecnie dostępna w wersji zapoznawczej! Możesz zarejestrować, przechodząc [SQL danych magazynu Visual Studio SQL Server Data Tools (SSDT) - formularz rejestracji (wersja zapoznawcza)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Biorąc pod uwagę wysokie zapotrzebowanie, firma Microsoft zarządza akceptacji w wersji zapoznawczej, aby zapewnić najlepsze środowisko dla naszych klientów. Po zarejestrowaniu, naszym celem jest Potwierdź swój status w ciągu siedmiu dni roboczych.

## <a name="row-level-security-generally-available"></a>Zabezpieczenia na poziomie wiersza jest ogólnie dostępna
Usługa Azure SQL Data Warehouse (usługi SQL DW) obsługuje teraz zabezpieczenia na poziomie wiersza (RLS) dodawanie zaawansowanych możliwości, aby zabezpieczyć poufne dane. Wraz z wprowadzeniem zabezpieczenia na poziomie wiersza możesz wdrożyć zasady zabezpieczeń do kontrolowania dostępu do wierszy w tabelach, podobnie jak w, którzy mogą uzyskiwać dostęp do jakich wierszy. Zabezpieczenia na poziomie wiersza Umożliwia to bardziej precyzyjnej kontroli dostępu, bez konieczności zmiany projektu magazynu danych. Zabezpieczenia na poziomie wiersza upraszcza ogólnym modelu zabezpieczeń, ponieważ logika ograniczeń dostępu znajduje się w warstwie bazy danych sam, a nie od danych w innej aplikacji. Zabezpieczenia na poziomie wiersza eliminuje również potrzebę wprowadzenia widoków, aby odfiltrować do wierszy dla zarządzania kontrolą dostępu. Nie ma żadnych dodatkowych kosztów, dla tej funkcji zabezpieczeń przeznaczonych dla przedsiębiorstw dla naszych klientów.

## <a name="advanced-advisors"></a>Zaawansowane doradców
Zaawansowane automatycznego dostrajania dla usługi Azure SQL Data Warehouse (usługi SQL DW) po prostu o wiele prostsze przy użyciu zalecenia dotyczące magazynu dodatkowych danych i metryk. Istnieją dodatkowe zaawansowane zaleceń za pośrednictwem usługi Azure Advisor do dyspozycji użytkownika, w tym:
1.  Adaptacyjne pamięci podręcznej — należy pamiętać podczas skalowania do optymalizacji wykorzystania pamięci podręcznej.
2.  Dystrybucja tabel — umożliwia określenie, kiedy należy replikować tabele, aby zmniejszyć przenoszenia danych i zwiększyć wydajność obciążeń. 
3.  Bazy danych tempdb — omówienie podczas skalowania i konfigurowania zasobów klasy, aby zmniejszyć rywalizację bazy danych tempdb.

Brak głębszą integrację metryki magazynu danych przy użyciu [usługi Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) tym rozszerzone dostosowywalny wykres monitorowania dla niemal w czasie rzeczywistym metryki w bloku Przegląd. Nie trzeba już opuszczać bloku przeglądu magazynu danych, aby uzyskać dostęp do metryk usługi Azure Monitor podczas monitorowania użycia lub weryfikowania i stosowania rekomendacji dotyczących magazynu danych. Ponadto Brak dostępnych nowych metryk, takich jak bazy danych tempdb i wykorzystanie adaptacyjne pamięci podręcznej w celu uzupełnienia Twojego zalecenia dotyczące wydajności.

## <a name="advanced-tuning-with-integrated-advisors"></a>Zaawansowane dostrajania za pomocą zintegrowanego doradców
Zaawansowane automatycznego dostrajania dla usługi Azure SQL Data Warehouse (usługi SQL DW) po prostu o wiele prostsze z zaleceń dotyczących magazynu dodatkowe dane, metryki i przeprojektowana poziomu bloku Przegląd portalu, która zapewnia zintegrowane środowisko za pomocą usługi Azure Advisor i Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Odzyskiwanie bazy danych jej jako przyspieszonej (ADR)
Azure SQL Data magazynu Accelerated bazy danych odzyskiwania (ADR) jest teraz w publicznej wersji zapoznawczej. Reguły ADR jest nowy aparat serwera SQL, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku obecności długotrwałej transakcji, całkowicie przeprojektowanie bieżący proces odzyskiwania od podstaw w górę. Podstawowych zalet reguły ADR są szybkie i spójne bazy danych odzyskiwania i cofnięcie transakcji natychmiastowe.

## <a name="azure-monitor-diagnostics-logs"></a>Dzienniki diagnostyczne usługi Azure Monitor
Usługa SQL Data Warehouse (usługi SQL DW) umożliwia integrowanie bezpośrednio z dzienników diagnostycznych usługi Azure Monitor lepszego wglądu w dane do obciążeń analitycznych. Ta nowa funkcja umożliwia deweloperom analizy zachowania obciążenia przez okres dłuższy czas i podejmowania świadomych decyzji w zakresie zarządzania optymalizacji lub pojemność zapytania. Teraz firma Microsoft wprowadza proces rejestrowania zewnętrznych za pomocą [dzienników diagnostycznych usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) , zapewniania dodatkowego wglądu w obciążenia magazynu danych. Jednym kliknięciem przycisku, jesteś teraz można skonfigurować dzienniki diagnostyczne na potrzeby zapytań historycznych Rozwiązywanie problemów z wydajnością funkcji przy użyciu [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Dzienniki diagnostyczne platformy Azure Monitor obsługuje okresów przechowywania można dostosowywać, zapisując dzienniki do konta magazynu na potrzeby, inspekcji możliwość przesyłanie strumieniowe dzienników do usługi event hubs niemal wgląd w czasie rzeczywistym dane telemetryczne oraz możliwość analizowania dzienników przy użyciu usługi Log Analytics za pomocą [rejestrowania zapytań](). Dzienniki diagnostyczne zawierają widoki telemetrii magazynu danych, będące odpowiednikiem najczęściej używanych widoków DMV dotyczących rozwiązywania problemów z wydajnością usługi SQL Data Warehouse. W przypadku tej początkowej wersji włączyliśmy następujące produkty widoki dla następujących system dynamicznych widoków zarządzania:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Zarządzanie pamięcią magazynu kolumn
W miarę zwiększania liczby grup wierszy magazynu skompresowany kolumny zwiększa ilość pamięci wymaganej do zarządzania metadanych kolumn wewnętrznych segmentu te grupy wierszy.  W rezultacie mogą obniżyć wydajność zapytań i zapytania wykonywane względem niektórych magazynu kolumn dynamicznych widoków zarządzania (DMV).  Ulepszenia wprowadzono w tej wersji Optymalizacja rozmiaru wewnętrznego metadanych w takiej sytuacji prowadzących do udoskonalony interfejs użytkownika i wydajność w przypadku takich zapytań. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integracja z usługą Azure Data Lake Storage Gen2 (GA)
Usługa Azure SQL Data Warehouse (usługi SQL DW) ma teraz natywnej integracji z usługi Azure Data Lake Storage Gen2. Klienci, teraz możesz ładować dane do usługi SQL DW za pomocą tabel zewnętrznych z ABFS. Ta funkcja umożliwia klientom integracji z ich jeziora w Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Poprawki błędów

| Stanowisko | Opis |
|:---|:---|
| **CETAS Parquet błędy w klasach zasobów małych na magazyny danych DW2000 i nie tylko** | Ta poprawka poprawnie identyfikuje odwołanie o wartości null w tworzenie zewnętrznej tabeli jako ścieżkę kodu Parquet. |
|**Wartości kolumny tożsamości może spowodować utratę niektórych operacji CTAS** | Wartość kolumny identyfikowanie mogą nie zostać zachowane podczas CTASed do innej tabeli. Zgłoszone w blogu: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Błąd wewnętrzny w niektórych przypadkach, gdy sesja została przerwana, gdy zapytanie jest nadal uruchomiona.** | Ta poprawka jest wyzwalana InvalidOperationException, jeśli sesja została przerwana, gdy zapytanie jest nadal uruchomiona. |


## <a name="next-steps"></a>Kolejne kroki
Po użytkownik podstawową wiedzę na temat usługi SQL Data Warehouse, Dowiedz się, jak szybko [utworzyć SQL Data Warehouse][create a SQL Data Warehouse]. Jeśli dopiero zaczynasz korzystać z platformy Azure, [słownik platformy Azure][Azure glossary] może pomóc Ci zaznajomić się z nową terminologią. Możesz też zwrócić uwagę na inne zasoby dotyczące usługi SQL Data Warehouse.  

* [Historie sukcesu klientów]
* [Blogi]
* [Żądania funkcji]
* [Filmy wideo]
* [Blogi zespołu doradczego klientów]
* [Forum Stack Overflow]
* [Twitter]


[Blogi]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogi zespołu doradczego klientów]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Historie sukcesu klientów]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Żądania funkcji]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Filmy wideo]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
