---
title: Informacje o wersji
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach usługi Azure SQL Database oraz w dokumentacji Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: sstein
ms.openlocfilehash: bdea9af04008ef68578ff8c136760f2493a2ae35
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279229"
---
# <a name="sql-database-release-notes"></a>Informacje o wersji SQL Database

W tym artykule wymieniono SQL Database funkcje, które są obecnie dostępne w publicznej wersji zapoznawczej. Aby uzyskać SQL Database aktualizacje i ulepszenia, zobacz [SQL Database Updates](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać aktualizacje i ulepszenia dla innych usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="single-databasetabsingle-database"></a>[Pojedyncza baza danych](#tab/single-database)

| Funkcja | Szczegóły |
| ---| --- |
| Nowe generacja sprzętu serii Fsv2 i serii M| Aby uzyskać więcej informacji, zobacz [generacja sprzętu](sql-database-service-tiers-vcore.md#hardware-generations).|
| [Prywatny link do platformy Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| Łącze prywatne upraszcza architekturę sieci i zabezpiecza połączenie między punktami końcowymi na platformie Azure, zachowując dane w sieci platformy Azure i eliminując ekspozycję z Internetu. Łącze prywatne pozwala też tworzyć i renderować własne usługi na platformie Azure. |
| Szybsze odzyskiwanie bazy danych przy użyciu pojedynczych baz danych i pul elastycznych | Aby uzyskać więcej informacji, zobacz [przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md).|
|Przybliżona liczba unikatowych|Aby uzyskać więcej informacji, zobacz [Przybliżona liczba odrębna](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Tryb wsadowy w magazynu wierszy (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [Tryb wsadowy w witrynie magazynu wierszy](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Odnajdowanie i klasyfikacja danych  |Aby uzyskać więcej informacji, zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikacji](sql-database-data-discovery-and-classification.md).|
| Zadania elastycznych baz danych | Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie zadań elastycznych oraz zarządzanie nimi](elastic-jobs-overview.md). |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [Omówienie zapytania elastycznego](sql-database-elastic-query-overview.md). |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze](sql-database-elastic-transactions-overview.md). |
|Opinie dotyczące przydzielenia pamięci (tryb wiersza) (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz informacje o [przydzieleniu pamięci (tryb wiersza)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Edytor zapytań w Azure Portal |Aby uzyskać więcej informacji, zobacz [Używanie edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych](sql-database-connect-query-portal.md).|
| Usługa R/Uczenie maszynowe z pojedynczymi bazami danych i pulami elastycznymi |Aby uzyskać więcej informacji, zobacz [Machine Learning Services w Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
|Analiza SQL|Aby uzyskać więcej informacji, zobacz [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Kompilacja odroczona do zmiennej tabeli (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [kompilacja zmiennej tabeli odroczonej](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

| Funkcja | Szczegóły |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Konfiguracja podsieci z obsługą usług</a> | Bezpieczny i wygodny sposób zarządzania konfiguracją podsieci. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pule wystąpień</a> | Wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparent Data Encryption (TDE) z Bring Your Own Key (BYOK)</a> |Aby uzyskać więcej informacji, zobacz temat [usługa Azure SQL transparent Data Encryption z kluczami zarządzanymi przez klienta w programie Azure Key Vault: obsługa Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).|
| <a href="https://aka.ms/managed-instance-aadlogins">Nazwy główne serwera usługi Azure AD na poziomie wystąpienia (logowania)</a> | Utwórz nazwy logowania na poziomie serwera przy użyciu instrukcji <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN from External Provider</a> . |
| [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) | Replikowanie zmian z tabel do innych baz danych umieszczonych w wystąpieniach zarządzanych, pojedynczych bazach danych lub wystąpieniach SQL Server lub aktualizowanie tabel w przypadku zmiany niektórych wierszy w innych wystąpieniach zarządzanych lub wystąpieniu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji w Azure SQL Database bazie danych wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md). |
| Wykrywanie zagrożeń |Aby uzyskać więcej informacji, zobacz [Konfigurowanie wykrywania zagrożeń w Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-threat-detection.md).|
| Ponowne tworzenie usuniętych baz danych z wystąpieniami zarządzanymi |Aby uzyskać więcej informacji, zobacz [ponowne tworzenie porzuconych baz danych w wystąpieniu zarządzanym usługi Azure SQL](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Nowe funkcje

### <a name="managed-instance-h2-2019-updates"></a>Zarządzane wystąpienie H2 2019 aktualizacje

- [Grupy autotrybu failover](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) umożliwiają replikację wszystkich baz danych z wystąpienia podstawowego do wystąpienia dodatkowego w innym regionie.
- Skonfiguruj zachowanie wystąpienia zarządzanego przy użyciu [globalnych flag śledzenia](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Wystąpienia zarządzane — aktualizacje 2019

W modelu wdrażania wystąpienia zarządzanego w H1 2019 są włączone następujące funkcje:
  - Obsługa subskrypcji za pomocą <a href="https://aka.ms/sql-mi-visual-studio-subscribers">miesięcznych środków na korzystanie z platformy Azure dla subskrybentów programu Visual Studio</a> i zwiększonych [granic regionalnych](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
  - Obsługa <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">programów SharePoint 2016 i SharePoint 2019</a> oraz <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">usługi Dynamics 365 Business Central</a>
  - Utwórz wystąpienia z wybranym <a href="https://aka.ms/managed-instance-collation">sortowaniem na poziomie serwera</a> i <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">strefą czasową</a> .
  - Zarządzane wystąpienia są teraz chronione za pomocą <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">wbudowanej zapory</a>.
  - Skonfiguruj wystąpienia do korzystania z [publicznych punktów końcowych](sql-database-managed-instance-public-endpoint-configure.md), połączenia [zastąpień serwera proxy](sql-database-connectivity-architecture.md#connection-policy) w celu uzyskania lepszej wydajności sieci, <a href="https://aka.ms/four-cores-sql-mi-update">4 rdzeni wirtualnych na generowanie sprzętu 5 rdzeń</a> lub <a href="https://aka.ms/managed-instance-configurable-backup-retention">Skonfiguruj przechowywanie kopii zapasowych do 35 dni</a> w przypadku przywracania do punktu w czasie. Długoterminowe przechowywanie kopii zapasowych (do 10 lat) nadal nie jest włączone, aby można było użyć <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">kopii zapasowych tylko do kopiowania</a> jako alternatywy.
  - Nowe funkcje umożliwiają <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">przeprzywracanie geograficznej bazy danych do innego centrum danych przy użyciu programu PowerShell</a>, [zmiana nazwy bazy danych](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [usunięcie klastra wirtualnego](sql-database-managed-instance-delete-virtual-cluster.md).
  - Nowa [rola współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) wbudowanego wystąpienia umożliwia rozdzielenie cła (SOD) z zasadami zabezpieczeń i zgodności z normami przedsiębiorstwa.
  - Wystąpienie zarządzane jest dostępne w następujących Azure Government regionach do GA (US Gov Teksas, US Gov Arizona), a także w Chiny Północne 2 i Chiny Wschodnie 2. Jest ona również dostępna w następujących regionach publicznych: Australia Środkowa, Australia Środkowa 2, Brazylia Południowa, Francja Południowa, Europa Środkowa, Płn. Zjednoczone Emiraty Arabskie, Północna Republika Południowej Afryki, Zachodnia Republika Południowej Afryki.

## <a name="fixed-known-issues"></a>Naprawione znane problemy

- **Sie 2019** — zawarte bazy danych są w pełni obsługiwane w wystąpieniu zarządzanym.
- **Oct 2019** -wbudowane przywracanie bazy danych w punkcie w czasie z warstwy krytyczne dla działania firmy do warstwy ogólnego przeznaczenia nie powiedzie się, jeśli źródłowa baza danych zawiera obiekty OLTP w pamięci.
- **Oct 2019** — można użyć funkcji **poczta bazy danych** z zewnętrznymi serwerami poczty (poza platformą Azure) przy użyciu bezpiecznego połączenia.
- **Lis 2019** — spójność bazy danych jest weryfikowana przy użyciu `DBCC CHECKDB` po przywróceniu bazy danych z usługi Azure Blob Storage.

## <a name="updates"></a>Aktualizacje

Listę aktualizacji i ulepszeń SQL Database można znaleźć w temacie [SQL Database Updates Service](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać aktualizacje i ulepszenia dla wszystkich usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby współtworzyć dokumentację Azure SQL Database, zobacz [Przewodnik współautora dokumentów](https://docs.microsoft.com/contribute/).
