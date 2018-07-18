---
title: Porównanie funkcji usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera porównanie funkcji programu SQL Server, które są dostępne w różnymi odmianami bazy danych SQL Azure.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/02/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 0901abe6f973d525220c948f8c32c0b4f342b11a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092090"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Porównanie funkcji: Azure SQL Database a baza danych programu SQL Server 

Usługa Azure SQL Database udostępnia wspólnej bazy kodu z programem SQL Server. Funkcje programu SQL Server obsługiwana przez usługę Azure SQL Database zależą od rodzaju bazy danych Azure SQL, którą tworzysz. Usługa Azure SQL Database, możesz utworzyć bazę danych jako część [wystąpienia zarządzanego](sql-database-managed-instance.md) (obecnie dostępna w publicznej wersji zapoznawczej) lub można utworzyć bazy danych, która jest częścią serwera logicznego i opcjonalnie umieszczone w puli elastycznej. 

Firma Microsoft nadal dodawania funkcji do usługi Azure SQL Database. Odwiedź strony poświęconej aktualizacjom usług dla platformy Azure, aby uzyskać najnowsze aktualizacje przy użyciu tych filtrów:

* Włączony filtr [usługi SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Włączony filtr [ogłoszeń dotyczących ogólnej dostępności](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) funkcji usługi SQL Database.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Obsługa funkcji programu SQL Server w usłudze Azure SQL Database

Poniższej tabeli przedstawiono główne funkcje programu SQL Server i zawiera informacje dotyczące tego, czy ta funkcja jest częściowo lub całkowicie obsługiwana i łącza do dodatkowych informacji o funkcji. 

| **Funkcja SQL** | **Obsługiwane na serwerze bazy danych/logicznym usługi Azure SQL** | **Obsługiwane w usłudze Azure SQL Database/wystąpienia zarządzanego (wersja zapoznawcza)** |
| --- | --- | --- |
| [Zawsze szyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Tak — zobacz [magazyn certyfikatów](sql-database-always-encrypted.md) i [usługi Key vault](sql-database-always-encrypted-azure-key-vault.md) | Tak — zobacz [magazyn certyfikatów](sql-database-always-encrypted.md) i [usługi Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Zawsze włączone grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) | [Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) |
| [Dołączanie bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nie | Nie |
| [Role aplikacji](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Yes | Yes |
|[Inspekcja](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Tak](sql-database-auditing.md)| [Tak](sql-database-managed-instance-auditing.md) |
| [Automatyczne tworzenie kopii zapasowych](sql-database-automated-backups.md) | Yes | Yes |
| [Automatyczne dostrajanie (wymuszanie planu)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Tak](sql-database-automatic-tuning.md)| [Tak](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatyczne dostrajanie (indeksów)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Tak](sql-database-automatic-tuning.md)| Nie |
| [Plik BACPAC (Eksportowanie)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Tak — zobacz [eksportowania bazy danych SQL](sql-database-export.md) | Nie |
| [Plik BACPAC (importowanie)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Tak — zobacz [importu bazy danych SQL](sql-database-import.md) | Nie |
| [Polecenia Kopia zapasowa](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nie, tylko inicjowanych przez system automatycznych kopii zapasowych — Zobacz [automatyczne kopie zapasowe](sql-database-automated-backups.md) | Inicjowanych przez system z automatycznie tworzonymi kopiami zapasowymi i użytkownik zainicjował kopie zapasowe tylko do kopiowania — zobacz [kopii zapasowej różnice](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funkcje wbudowane](https://docs.microsoft.com/sql/t-sql/functions/functions) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [składowane, funkcje, wyzwala różnice](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Przechwytywanie zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nie | Yes |
| [Śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Yes |Yes |
| [Instrukcje sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations) | Yes | Yes |
| [Indeksy magazynu kolumn](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Tak — [w warstwie Premium, w warstwie standardowa — S3 i nowszych warstwa ogólnego przeznaczenia i krytyczne dla działania firmy warstw](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Yes |
| [Środowisko uruchomieniowe języka wspólnego (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nie | Tak — zobacz [różnice CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Zawarte bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Yes | Yes |
| [Zawartych użytkowników](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Yes | Yes |
| [Kontrolka słowa kluczowe języka przepływu](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Yes | Yes |
| [Zapytania wielu baz danych](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie — zobacz [zapytania elastyczne](sql-database-elastic-query-overview.md) | Tak, a także [zapytania elastyczne](sql-database-elastic-query-overview.md) |
| [Transakcje między bazami danych](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie | Tak — zobacz [połączony serwer różnice](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Kursory](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Yes |Yes | 
| [Kompresja danych](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Yes |Yes |
| [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nie | Yes |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Yes | Yes |
| [Funkcja dublowania baz danych](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nie | Nie |
| [Ustawienia konfiguracji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Yes | Yes |
| [Usług Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nie | Nie |
| [Migawki baz danych](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nie | Nie |
| [Typy danych](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Yes |Yes |
| [Instrukcje DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Większość — Zobacz pojedyncze instrukcje | Tak — zobacz [DBCC różnice](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instrukcje języka DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Większość — Zobacz pojedyncze instrukcje | Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Wyzwalacze DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Tylko bazę danych |  Yes |
| [Widoki rozproszone partycji](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nie | Yes |
| [Transakcje rozproszone - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nie — zobacz [transakcje elastyczne](sql-database-elastic-transactions-overview.md) |  Nie — zobacz [transakcje elastyczne](sql-database-elastic-transactions-overview.md) |
| [Instrukcje DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Yes | Yes |
| [Wyzwalacze języka DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Większość — Zobacz pojedyncze instrukcje |  Yes |
| [Dynamiczne widoki zarządzania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Większość — Zobacz pojedyncze dynamiczne widoki zarządzania |  Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Tak](sql-database-dynamic-data-masking-get-started.md)| [Tak](sql-database-dynamic-data-masking-get-started.md) |
| [Elastyczne pule](sql-database-elastic-pool.md) | Yes | Wbudowane — pojedyncze wystąpienie zarządzane mogą mieć wiele baz danych, które współużytkują ten sam puli zasobów |
| [Powiadomienia o zdarzeniach](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nie — zobacz [alertów](sql-database-insights-alerts-portal.md) | Yes |
| [Wyrażenia](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Yes | Yes |
| [Zdarzenia rozszerzone](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Niektóre — zobacz [rozszerzonych zdarzeń w bazie danych SQL](sql-database-xevent-db-diff-from-svr.md) | Tak — zobacz [różnice zdarzenia rozszerzone ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Rozszerzone procedury składowane](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nie | Nie |
[Pliki i grupy plików](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Podstawowa grupa plików tylko | Yes |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nie | Nie |
| [Wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Moduły dzielenia wyrazów innych firm nie są obsługiwane. |Moduły dzielenia wyrazów innych firm nie są obsługiwane. |
| [Funkcje](https://docs.microsoft.com/sql/t-sql/functions/functions) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [składowane, funkcje, wyzwala różnice](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Funkcja przywracania geograficznego](sql-database-recovery-using-backups.md#geo-restore) | Yes | Nie można przywrócić COPY_ONLY Zobacz pełne kopie zapasowe, okresowo - przyjmujące [kopii zapasowej różnice](sql-database-managed-instance-transact-sql-information.md#backup) i [przywrócić różnice](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Geo-replication](sql-database-geo-replication-overview.md) (Replikacja geograficzna) | Yes | Nie |
| [Przetwarzanie wykresów](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Yes | Yes |
| [Optymalizacja w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Tak — [tylko w warstwach Premium i krytyczne dla działania firmy](sql-database-in-memory.md) | Nie |
| [Obsługa danych JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Elementy języka](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Większość — Zobacz poszczególne elementy |  Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Połączone serwery](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie — zobacz [zapytania elastycznego](sql-database-elastic-query-horizontal-partitioning.md) | Tylko do programu SQL Server i bazy danych SQL |
| [Wysyłanie dziennika](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) |[Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nie | Nie |
| [Rejestrowanie minimalne w imporcie zbiorczym](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nie | Nie |
| [Modyfikowanie danych systemowych](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nie | Yes |
| [Operacje indeksowania w trybie online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Yes | Yes |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nie|Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Yes|Yes|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nie|Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Nie|Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Yes|Yes|
| [Operatory](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Większość — Zobacz pojedyncze operatory |Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Partycjonowanie](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Yes | Yes |
| [Punkt w czasie przywracania bazy danych](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Tak — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Tak — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Program Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nie | Nie |
| [Zarządzanie oparte na zasadach](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nie | Nie |
| [Predykaty](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Yes | Yes |
| [Usługi języka R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Wersja zapoznawcza; zobacz [co nowego w usłudze machine learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nie |
| [Zarządca zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nie | Yes |
| [Instrukcje RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nie | Tak — zobacz [przywrócić różnice](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Przywracanie bazy danych z kopii zapasowej](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Automatyczne kopie zapasowe tylko — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md) | Automatycznych kopii zapasowych — Zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md) i pełne kopie zapasowe — zobacz [kopii zapasowej różnice](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Yes | Yes |
| [Wyszukiwanie semantyczne](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nie | Nie |
| [Numery sekwencyjne](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Yes | Yes |
| [Usługa Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nie | Tak — zobacz [różnice programu Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Ustawienia konfiguracji serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nie | Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Instrukcje SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Większość — Zobacz pojedyncze instrukcje | Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Yes | Yes |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Yes | Yes |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Yes | Nie |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Yes | Yes |
| [Program SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nie — zobacz [zadań elastycznych](sql-database-elastic-jobs-getting-started.md) | Tak — zobacz [różnice programu SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nie — zobacz [usług Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Nie — zobacz [usług Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Inspekcja serwera SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nie — zobacz [inspekcji usługi SQL Database](sql-database-auditing.md) | Tak — zobacz [inspekcji różnice](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes | Yes |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Tak, przy użyciu zarządzanych usług SSIS w środowisku usługi Azure Data Factory (ADF), gdy pakiety są przechowywane w bazy danych SSISDB hostowaną przez usługę Azure SQL Database i wykonane na platformie Azure SSIS Integration Runtime (IR), zobacz [tworzenie środowiska Azure-SSIS IR w usłudze ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Aby porównać funkcji SSIS w bazie danych SQL i wystąpienia zarządzanego, zobacz [Porównaj bazy danych SQL i wystąpienia zarządzanego (wersja zapoznawcza)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). | Tak, przy użyciu zarządzanych usług SSIS w środowisku usługi Azure Data Factory (ADF), gdy pakiety są przechowywane w SSISDB pracujących na wystąpieniu zarządzanym i wykonywany na platformie Azure SSIS Integration Runtime (IR), zobacz [tworzenie środowiska Azure-SSIS IR w usłudze ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Aby porównać funkcji SSIS w bazie danych SQL i wystąpienia zarządzanego, zobacz [Porównaj bazy danych SQL i wystąpienia zarządzanego (wersja zapoznawcza)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Yes | Yes |
| [Program SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Yes | Yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nie — zobacz [zdarzeniom rozszerzonym](sql-database-xevent-db-diff-from-svr.md) | Yes |
| [Replikacji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Tylko dla subskrybentów replikacji transakcyjnej i replikacji migawki](sql-database-cloud-migrate.md) | Tak — [replikację za pomocą wystąpienia zarządzanego SQL Database (publiczna wersja zapoznawcza)](http://review.docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nie — [zobaczyć usługę Power BI](https://docs.microsoft.com/power-bi/) | Nie — [zobaczyć usługę Power BI](https://docs.microsoft.com/power-bi/) |
| [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Yes | Yes |
| [Systemowe funkcje składowane](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [składowane, funkcje, wyzwala różnice](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedury składowane w systemie](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Niektóre — Zobacz poszczególne procedur składowanych | Tak — zobacz [składowane, funkcje, wyzwala różnice](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tabele systemowe](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Niektóre — Zobacz poszczególnych tabel | Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Widoki wykazu systemu](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Niektóre — Zobacz pojedyncze widoki | Tak — zobacz [różnice w języku T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tabele tymczasowe](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Globalne tabele tymczasowe lokalnych i w zakresie bazy danych | Lokalne i zakresu wystąpienie globalne tabele tymczasowe |
| [Tabele danych czasowych](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Wykrywanie zagrożeń|  [Tak](sql-database-threat-detection.md)|[Tak](sql-database-managed-instance-threat-detection.md)|
| [Flagi śledzenia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nie | Nie |
| [Zmienne](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Yes | Yes |
| [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Yes | Partial tylko w przypadku szyfrowania zarządzanego przez usługę |
[Sieć wirtualna](../virtual-network/virtual-networks-overview.md) | Częściowe — zobacz [punkty końcowe sieci Wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) | Tak, tylko w modelu usługi Resource Manager |
| [Windows Server Failover Clustering](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) | [Wysoka dostępność](sql-database-high-availability.md) jest dołączony do każdej bazy danych. Odzyskiwanie po awarii została omówiona w [omówienie ciągłości działania za pomocą usługi Azure SQL Database](sql-database-business-continuity.md) |
| [Indeksy XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Yes | Yes |

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat usługi Azure SQL Database, zobacz [Co to jest SQL Database?](sql-database-technical-overview.md)
- Aby uzyskać informacje na temat wystąpienia zarządzanego, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md).
