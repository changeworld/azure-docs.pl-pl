---
title: Porównanie funkcji w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera porównanie funkcji programu SQL Server, które są dostępne w różnych odmian bazy danych SQL Azure.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: c48a0f078da8e56038ec74ee398bd26fe1dc2718
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054759"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Porównanie funkcji: Azure SQL Database i programu SQL Server 

Baza danych SQL Azure udostępnia typowe bazy kodu z programem SQL Server. Funkcje programu SQL Server obsługiwana przez bazę danych SQL Azure są zależne od typu bazy danych Azure SQL, które można utworzyć. Z bazy danych SQL Azure, możesz utworzyć bazę danych jako część [zarządzanego wystąpienia](sql-database-managed-instance.md) (obecnie w publicznej wersji zapoznawczej) lub można utworzyć bazy danych, która jest częścią logicznego serwera i opcjonalnie umieszczony w puli elastycznej. 

Firma Microsoft w dalszym ciągu dodawania funkcji do bazy danych SQL Azure. Odwiedź stronę sieci Web usługi aktualizacji dla platformy Azure najnowsze aktualizacje przy użyciu tych filtrów:

* Włączony filtr [usługi SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Włączony filtr [ogłoszeń dotyczących ogólnej dostępności](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) funkcji usługi SQL Database.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Obsługa funkcji programu SQL Server w bazie danych SQL Azure

Poniższa tabela zawiera listę głównych funkcji programu SQL Server oraz informacje na temat tego, czy funkcja ta jest częściowo lub całkowicie obsługiwana i łącza do dodatkowych informacji na temat funkcji. 

| **Funkcja SQL** | **Obsługiwane przez serwer bazy danych/logiczny Azure SQL** | **Obsługiwane w wystąpieniu bazy danych/zarządzane SQL Azure (wersja zapoznawcza)** |
| --- | --- | --- |
| [Zawsze zaszyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Tak — zobacz [magazynu certyfikatów](sql-database-always-encrypted.md) i [magazyn kluczy](sql-database-always-encrypted-azure-key-vault.md) | Tak — zobacz [magazynu certyfikatów](sql-database-always-encrypted.md) i [magazyn kluczy](sql-database-always-encrypted-azure-key-vault.md) |
| [Zawsze włączone grupy dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) | [Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) |
| [Dołączanie bazy danych](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nie | Nie |
| [Role aplikacji](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Yes | Yes |
|[Inspekcja](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Tak](sql-database-auditing.md)| [Tak](sql-database-managed-instance-auditing.md) |
| [Automatyczne kopie zapasowe](sql-database-automated-backups.md) | Yes | Yes |
| [Automatycznego dostrajania (wymuszanie planu)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Tak](sql-database-automatic-tuning.md)| [Tak](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatycznego dostrajania (indeksów)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Tak](sql-database-automatic-tuning.md)| Nie |
| [Plik pliku BACPAC (Eksportuj)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Tak — zobacz [eksportu bazy danych SQL](sql-database-export.md) | Nie |
| [Plik pliku BACPAC (Importuj)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Tak — zobacz [importu bazy danych SQL](sql-database-import.md) | Nie |
| [Polecenie tworzenia kopii zapasowej](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nie, tylko inicjowanych przez system automatycznego tworzenia kopii zapasowych — Zobacz [automatycznego tworzenia kopii zapasowych](sql-database-automated-backups.md) | Zainicjowane przez system automatycznych kopii zapasowych i użytkownik zainicjował kopie zapasowe tylko do kopiowania — zobacz [kopii zapasowej różnic](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funkcje wbudowane](https://docs.microsoft.com/sql/t-sql/functions/functions) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [procedury przechowywane i funkcje, wyzwala różnic](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Przechwytywania zmian danych](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nie | Yes |
| [Śledzenie zmian](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Yes |Yes |
| [Instrukcje sortowania](https://docs.microsoft.com/sql/t-sql/statements/collations) | Yes | Yes |
| [Indeksy magazynu kolumn](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Tak — [warstwy Premium, warstwy standardowa - S3 i powyżej warstwy ogólnego przeznaczenia i warstwy biznesowej krytyczne](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Yes |
| [Środowisko uruchomieniowe języka wspólnego (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nie | Tak — zobacz [różnice CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Zawartych baz danych](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Yes | Yes |
| [Zawartych użytkowników](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Yes | Yes |
| [Formant słów kluczowych języka przepływu](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Yes | Yes |
| [Zapytania między bazami danych](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie — zobacz [elastycznej zapytań](sql-database-elastic-query-overview.md) | Tak, oraz [elastycznej zapytań](sql-database-elastic-query-overview.md) |
| [Transakcje między bazami danych](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie | Tak — zobacz [połączony serwer różnic](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [Kursory](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Yes |Yes | 
| [Kompresja danych](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Yes |Yes |
| [Poczta bazy danych](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nie | Yes |
| [Usługa migracji danych (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Yes | Yes |
| [Funkcja dublowania baz danych](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nie | Nie |
| [Ustawienia konfiguracji bazy danych](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Yes | Yes |
| [Usług Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nie | Nie |
| [Migawki baz danych](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nie | Nie |
| [Typy danych](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Yes |Yes |
| [Instrukcji DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Większość — zobacz instrukcje poszczególnych | Tak — zobacz [DBCC różnic](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instrukcji DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Większość — zobacz instrukcje poszczególnych | Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Wyzwalaczy DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Tylko bazy danych |  Yes |
| [Widoki rozproszone partycji](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nie | Yes |
| [Transakcje rozproszone - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nie — zobacz [elastycznej transakcji](sql-database-elastic-transactions-overview.md) |  Nie — zobacz [elastycznej transakcji](sql-database-elastic-transactions-overview.md) |
| [Instrukcje DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Yes | Yes |
| [Wyzwalacze DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Większość — zobacz instrukcje poszczególnych |  Yes |
| [Dynamiczne widoki zarządzania](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Większość — Zobacz poszczególnych widoków DMV |  Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Dynamiczne maskowanie danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Tak](sql-database-dynamic-data-masking-get-started.md)| [Tak](sql-database-dynamic-data-masking-get-started.md) |
| [Elastyczne pule](sql-database-elastic-pool.md) | Yes | Wbudowane-pojedynczego wystąpienia zarządzane mogą mieć wiele baz danych, które mają tej samej puli zasobów |
| [Powiadomienia o zdarzeniach](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nie — zobacz [alertów](sql-database-insights-alerts-portal.md) | Yes |
| [Wyrażenia](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Yes | Yes |
| [Zdarzenia rozszerzone](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Niektóre — zobacz [rozszerzony zdarzeń w bazie danych SQL](sql-database-xevent-db-diff-from-svr.md) | Tak — zobacz [rozszerzony różnice zdarzenia ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Rozszerzone procedury składowane](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nie | Nie |
[Pliki i grup plików](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Tylko grupy plik podstawowy | Yes |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nie | Nie |
| [Wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Moduły dzielenia wyrazów innej firmy nie są obsługiwane. |Moduły dzielenia wyrazów innej firmy nie są obsługiwane. |
| [Funkcje](https://docs.microsoft.com/sql/t-sql/functions/functions) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [procedury przechowywane i funkcje, wyzwala różnic](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Geograficzne](sql-database-recovery-using-backups.md#geo-restore) | Yes | Nie — można przywrócić COPY_ONLY pełne kopie zapasowe wykonywane okresowo — zobacz [kopii zapasowej różnice](sql-database-managed-instance-transact-sql-information.md#backup) i [przywrócić różnice](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Geo-replication](sql-database-geo-replication-overview.md) (Replikacja geograficzna) | Yes | Nie |
| [Przetwarzanie wykresu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Yes | Yes |
| [Optymalizacja w pamięci](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Tak — [tylko warstwy Premium i biznesowe krytyczne](sql-database-in-memory.md) | Nie |
| [Obsługa danych JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [Elementy języka](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Większość — Zobacz poszczególne elementy |  Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Połączonych serwerów](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nie — zobacz [elastycznej zapytania](sql-database-elastic-query-horizontal-partitioning.md) | Tylko do programu SQL Server i bazy danych SQL |
| [Wysyłanie dziennika](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) |[Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) |
| [Usług Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nie | Nie |
| [Minimalny logowania za pomocą importowania zbiorczego](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nie | Nie |
| [Modyfikowanie danych systemowych](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nie | Yes |
| [Operacje indeksu w trybie online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Yes | Yes |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nie|Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Yes|Yes|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nie|Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Nie|Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Yes|Yes|
| [Operatory](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Większość — Zobacz poszczególnych operatorów |Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Podział na partycje](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Yes | Yes |
| [Punktu w czasie przywracania bazy danych](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Tak — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Tak — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Program Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nie | Nie |
| [Zarządzanie oparte na zasadach](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nie | Nie |
| [Predykaty](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Yes | Yes |
| [Usługi języka R](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Wersja zapoznawcza; zobacz [What's new in uczenia maszynowego](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nie |
| [Zarządcy zasobów](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nie | Yes |
| [Przywróć — instrukcje](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nie | Tak — zobacz [przywrócić różnic](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Przywróć bazę danych z kopii zapasowej](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Automatyczne kopie zapasowe tylko — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md) | Automatyczne kopie zapasowe — zobacz [odzyskiwanie bazy danych SQL](sql-database-recovery-using-backups.md) i pełne kopie zapasowe — zobacz temat [kopii zapasowej różnic](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Yes | Yes |
| [Semantycznego wyszukiwania](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nie | Nie |
| [Numerów sekwencji.](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Yes | Yes |
| [Usługa Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nie | Tak — zobacz [różnice Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Ustawienia konfiguracji serwera](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nie | Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Set — instrukcje](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Większość — zobacz instrukcje poszczególnych | Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Yes | Yes |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Yes | Yes |
| [Synchronizacja danych SQL](sql-database-get-started-sql-data-sync.md) | Yes | Nie |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Yes | Yes |
| [Program SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nie — zobacz [zadania elastyczne](sql-database-elastic-jobs-getting-started.md) | Tak — zobacz [różnice programu SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nie — zobacz [usług Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Nie — zobacz [usług Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Inspekcja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nie — zobacz [inspekcji bazy danych SQL](sql-database-auditing.md) | Tak — zobacz [inspekcji różnic](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Program SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes | Yes |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Tak, z zarządzanego SSIS w środowisku fabryki danych Azure (ADF), gdy pakiety są przechowywane w SSISDB obsługiwana przez bazę danych SQL Azure i wykonywane na Azure SSIS integrację środowiska uruchomieniowego (IR), zobacz [utworzyć IR Azure SSIS w ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). | Tak, z zarządzanego SSIS w środowisku fabryki danych Azure (ADF), gdy pakiety są przechowywane w SSISDB hostowane przez zarządzane wystąpienia i wykonywane na Azure SSIS integrację środowiska uruchomieniowego (IR), zobacz [utworzyć IR Azure SSIS w ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Yes | Yes |
| [SQL Server w programie PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Yes | Yes |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nie — zobacz [rozszerzone zdarzenia](sql-database-xevent-db-diff-from-svr.md) | Yes |
| [Replikacja programu SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Tylko dla subskrybentów replikacji transakcyjnej i replikacji migawki](sql-database-cloud-migrate.md) | Nie |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nie - [Zobacz usługi Power BI](https://docs.microsoft.com/power-bi/) | Nie - [Zobacz usługi Power BI](https://docs.microsoft.com/power-bi/) |
| [Procedury składowane](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Yes | Yes |
| [Funkcje przechowywana w systemie](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Większość — Zobacz poszczególnych funkcji | Tak — zobacz [procedury przechowywane i funkcje, wyzwala różnic](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedury składowane w systemie](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Niektóre — Zobacz poszczególnych procedury składowane | Tak — zobacz [procedury przechowywane i funkcje, wyzwala różnic](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tabele systemowe](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Niektóre — Zobacz poszczególnych tabel | Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Widoków wykazu systemu](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Niektóre — Zobacz poszczególnych widoków | Tak — zobacz [różnice T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tabele tymczasowe](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Lokalne i o zakresie bazy danych globalnych tabel tymczasowych | Lokalne i zakres wystąpienia globalne tabele tymczasowe |
| [Tabele danych czasowych](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [Tak](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|Wykrywanie zagrożeń|  [Tak](sql-database-threat-detection.md)|[Tak](sql-database-managed-instance-threat-detection.md)|
| [Flagi śledzenia](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nie | Nie |
| [Zmienne](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Yes | Yes |
| [Przezroczystego szyfrowania danych (funkcji TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Yes | Partial tylko z szyfrowaniem zarządzane przez usługę |
[Sieć wirtualna](../virtual-network/virtual-networks-overview.md) | Częściowe — zobacz [punktów końcowych sieci Wirtualnej](sql-database-vnet-service-endpoint-rule-overview.md) | Tak — tylko modelu Resource Manager |
| [Klaster trybu Failover serwera systemu Windows](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) | [Wysoka dostępność](sql-database-high-availability.md) znajduje się w każdej bazie danych. Odzyskiwanie po awarii jest omówiona w [omówienie ciągłości działalności biznesowej z bazy danych SQL Azure](sql-database-business-continuity.md) |
| [Indeksów XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Yes | Yes |

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat usługi Azure SQL Database, zobacz [Co to jest SQL Database?](sql-database-technical-overview.md)
- Informacje o wystąpieniu zarządzanych, zobacz [co to jest wystąpieniem zarządzane?](sql-database-managed-instance.md).
