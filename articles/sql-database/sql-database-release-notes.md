---
title: Informacje o wersji Azure SQL Database | Microsoft Docs
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach usługi Azure SQL Database oraz w dokumentacji Azure SQL Database
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ed41ccea0754f3eeffdd0248bac567859db1492c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001507"
---
# <a name="sql-database-release-notes"></a>Informacje o wersji SQL Database

W tym artykule wymieniono SQL Database funkcje, które są obecnie dostępne w publicznej wersji zapoznawczej. Aby uzyskać SQL Database aktualizacje i ulepszenia, zobacz [SQL Database Updates](https://azure.microsoft.com/updates/?product=sql-database). Aby uzyskać aktualizacje i ulepszenia dla innych usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Funkcje w publicznej wersji zapoznawczej

### <a name="single-databasetabsingle-database"></a>[Pojedyncza baza danych](#tab/single-database)

| Cecha | Szczegóły |
| ---| --- |
| Szybsze odzyskiwanie bazy danych przy użyciu pojedynczych baz danych i pul elastycznych | Aby uzyskać więcej informacji, zobacz [przyspieszone odzyskiwanie bazy danych](sql-database-accelerated-database-recovery.md).|
|Przybliżona liczba unikatowych|Aby uzyskać więcej informacji, zobacz [Przybliżona liczba odrębna](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Tryb wsadowy w magazynu wierszy (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [Tryb wsadowy w witrynie magazynu wierszy](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Odnajdowanie i klasyfikacja danych  |Aby uzyskać więcej informacji, zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikacji](sql-database-data-discovery-and-classification.md).|
| Zadania Elastic Database | Aby uzyskać więcej informacji, zobacz [Tworzenie i Konfigurowanie zadań elastycznych oraz zarządzanie nimi](elastic-jobs-overview.md). |
| Zapytania elastyczne | Aby uzyskać więcej informacji, zobacz [Omówienie zapytania elastycznego](sql-database-elastic-query-overview.md). |
| Transakcje elastyczne | [Transakcje rozproszone w bazach danych w chmurze](sql-database-elastic-transactions-overview.md). |
|Opinie dotyczące przydzielenia pamięci (tryb wiersza) (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz informacje o [przydzieleniu pamięci (tryb wiersza)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Edytor zapytań w Azure Portal |Aby uzyskać więcej informacji, zobacz [Używanie edytora zapytań SQL Azure Portal do łączenia i wykonywania zapytań dotyczących danych](sql-database-connect-query-portal.md).|
| Usługa R/Uczenie maszynowe z pojedynczymi bazami danych i pulami elastycznymi |Aby uzyskać więcej informacji, zobacz [Machine Learning Services w Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Warstwa bezserwerowych usług obliczeniowych | Aby uzyskać więcej informacji, zobacz [SQL Database bezserwerowe (wersja zapoznawcza)](sql-database-serverless.md).|
|Analiza SQL|Aby uzyskać więcej informacji, zobacz [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Kompilacja odroczona do zmiennej tabeli (w obszarze poziom zgodności 150)|Aby uzyskać więcej informacji, zobacz [kompilacja zmiennej tabeli odroczonej](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

| Cecha | Szczegóły |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Pule wystąpień</a> | Wygodny i ekonomiczny sposób migracji mniejszych wystąpień SQL do chmury. |
| <a href="https://aka.ms/managed-instance-tde-byok">Transparent Data Encryption (TDE) z Bring Your Own Key (BYOK)</a> |Aby uzyskać więcej informacji [, zobacz Usługa Azure SQL transparent Data Encryption z kluczami zarządzanymi przez klienta w programie Azure Key Vault: Obsługa](transparent-data-encryption-byok-azure-sql.md)Bring Your Own Key.|
| <a href="https://aka.ms/managed-instance-failover-groups">Rozproszone geograficznie grupy trybu failover</a> | Zachowaj kopię wystąpienia w innym regionie i upewnij się, że dane będą dostępne nawet w regionalnym scenariuszu awarii. |
| <a href="https://aka.ms/managed-instance-aadlogins">Nazwy główne serwera usługi Azure AD na poziomie wystąpienia (logowania)</a> | Utwórz nazwy logowania na poziomie serwera przy użyciu instrukcji <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN from External Provider</a> . |
| [Replikacja transakcyjna](sql-database-managed-instance-transactional-replication.md) | Replikowanie zmian z tabel do innych baz danych umieszczonych w wystąpieniach zarządzanych, pojedynczych bazach danych lub wystąpieniach SQL Server lub aktualizowanie tabel w przypadku zmiany niektórych wierszy w innych wystąpieniach zarządzanych lub wystąpieniu SQL Server. Aby uzyskać więcej informacji, zobacz [Konfigurowanie replikacji w Azure SQL Database bazie danych wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md). |
| Wykrywanie zagrożeń |Aby uzyskać więcej informacji, zobacz [Konfigurowanie wykrywania zagrożeń w Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-threat-detection.md).|
| Ponowne tworzenie usuniętych baz danych z wystąpieniami zarządzanymi |Aby uzyskać więcej informacji, zobacz [ponowne tworzenie porzuconych baz danych w wystąpieniu zarządzanym usługi Azure SQL](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="fixed-known-issues"></a>Naprawione znane problemy

- **Sie 2019** — zawarte bazy danych są w pełni obsługiwane w wystąpieniu zarządzanym.

## <a name="updates"></a>Aktualizacje

Listę aktualizacji i ulepszeń SQL Database można znaleźć w temacie [SQL Database Updates Service](https://azure.microsoft.com/updates/?product=sql-database).

Aby uzyskać aktualizacje i ulepszenia dla wszystkich usług platformy Azure, zobacz [aktualizacje usługi](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Współtworzenie zawartości

Aby współtworzyć dokumentację Azure SQL Database, zobacz [Przewodnik współautora dokumentów](https://docs.microsoft.com/contribute/).
