---
title: Informacje o wersji bazy danych usługi Azure SQL | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o nowe funkcje i ulepszenia w usłudze Azure SQL Database, a w dokumentacji usługi Azure SQL Database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: carlrab
ms.openlocfilehash: 9a461f84577997ff976956b7f2f50500e9b3d323
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548068"
---
# <a name="sql-database-release-notes"></a>Informacje o wersji bazy danych SQL

Ten artykuł zawiera listę nowych funkcji i ulepszeń, w ramach usługi SQL Database i w dokumentacji usługi SQL Database. Ulepszeń usługi SQL Database, zobacz też [aktualizacji usługi SQL Database](https://azure.microsoft.com/updates/?product=sql-database). Ulepszenia do innych usług platformy Azure, można zobaczyć [aktualizowaniem usługi](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>Marca 2019 r

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
| Wkrótce ||
| &nbsp; |

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Udoskonalenia dokumentacji | Szczegóły |
| --- | --- |
| Dodano dziennika limity dla pojedynczych baz danych|Aby uzyskać więcej informacji, zobacz [pojedynczy limity zasobów na rdzeniach wirtualnych bazy danych](sql-database-vcore-resource-limits-single-databases.md).|
| Dodano dziennika limity dla pul elastycznych i baz danych w puli|Aby uzyskać więcej informacji, zobacz [vCore zasobów limity pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md).|
| Dodano nadzoru współczynnik dziennika transakcji| Dodano nową zawartość dotyczącą [nadzoru współczynnik dziennika transakcji](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Zaktualizowano przykłady programu PowerShell dla pojedynczych baz danych i pul elastycznych, aby użyć modułu az.sql | Aby uzyskać więcej informacji, zobacz [przykłady programu PowerShell dla pojedynczych baz danych i pul elastycznych](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>Lutego 2019 r

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
|Tworzenie z możliwością wznowienia indeksu w trybie online jest teraz ogólnie dostępna| Aby uzyskać więcej informacji, zobacz [Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Wystąpienie zarządzane Obsługa tabel tras, ulepszone| Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieciowej](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Zmień nazwę bazy danych obsługiwane w wystąpieniu zarządzanym | Aby uzyskać więcej informacji, zobacz [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) i [sp_rename](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) składni.|
|Baza danych SQL jako źródła danych referencyjnych dla usługi Stream Analytics. | Aby uzyskać więcej informacji, zobacz [usługi Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Data Migration Assistant dodaje obsługę dla wystąpienia zarządzanego. |Aby uzyskać więcej informacji, zobacz [What's new in DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew?view=sql-server-2017).|
|SQL Server Migration Assistant alokowanej oceny gotowości docelowego dla wystąpienia zarządzanego. | Aby uzyskać więcej informacji, zobacz [Asystenta migracji programu SQL Server](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Usługa migracji danych obsługuje migrację z usług RDS Amazon do wystąpienia zarządzanego | Aby uzyskać więcej informacji, zobacz [Samouczek: Migrowanie usług pulpitu zdalnego programu SQL Server do usługi Azure SQL Database lub wystąpienia zarządzanego usługi Azure SQL Database i online przy użyciu usługi DMS](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Udoskonalenia dokumentacji | Szczegóły |
| --- | --- |
|Dodawanie zarządzanych wyjaśnienia opcji wdrożenia wystąpienia|Zaktualizowano wiele artykułów, aby wyjaśnić, ma zastosowanie do pojedynczej bazy danych, pul elastycznych i opcji wdrożenia wystąpienia zarządzanego. |
|Zaktualizowano bazę danych tempdb rozmiary modelu zakupu opartego na jednostkach DTU | Aby uzyskać więcej informacji, zobacz [bazy danych Tempdb w bazie danych SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).|
|Zaktualizowano importowanie i eksportowanie z pliku bacpac do obsługi wystąpienia zarządzanego| Aby uzyskać więcej informacji, zobacz [Importuj z pliku BACPAC](sql-database-import.md) i [wyeksportować do pliku BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>Styczeń 2019

### <a name="service-improvements"></a>Udoskonalenia usługi

| Udoskonalenia usługi | Szczegóły |
| --- | --- |
| Opcje dodatkowe stopień szczegółowości za zasoby obliczeniowe | Ogólnego przeznaczenia i krytyczne dla działania usługi warstwy [pojedyncze bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md) mają teraz więcej opcji obliczeniowych szczegółowych.|
| Wyświetlanie rekordów inspekcji dla wystąpienia zarządzanego w witrynie Azure portal | Wyświetlanie [rekordy dla wystąpienia zarządzanego inspekcji](sql-database-managed-instance-auditing.md) w witrynie Azure portal jest teraz obsługiwane.|
| Funkcja wykrywania zagrożeń wcześniejszym zmieniona na zaawansowane zabezpieczenia danych | Funkcja wykrywania zagrożeń wcześniejszym zmieniona na [zaawansowanych danych](sql-advanced-threat-protection.md) dla pojedynczych baz danych, pul elastycznych i wystąpienia zarządzanego. |
| &nbsp; |

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Udoskonalenia dokumentacji | Szczegóły |
| --- | --- |
| Wystąpienia zarządzanego i replikacji transakcyjnej | Dodano artykuł na temat przy użyciu [replikacji transakcyjnej za pomocą wystąpienia zarządzanego](replication-with-sql-database-managed-instance.md) |
| Dodano usługi Azure AD z samouczkiem wystąpienia zarządzanego | To [usługi Azure AD za pomocą wystąpienia zarządzanego](sql-database-managed-instance-aad-security-tutorial.md) samouczku przedstawiono, które trzeba skonfigurować i przetestować zarządzane wystąpienia zabezpieczeń za pomocą logowania do usługi Azure AD. |
| Zaktualizowano zawartość dla zadań usługi automation przy użyciu skryptów języka Transact-SQL | Zaktualizowane i wyjaśniono zawartości przy użyciu [zadań usługi automation przy użyciu skryptów języka Transact-SQL](sql-database-job-automation-overview.md) dla pojedynczych baz danych, pul elastycznych i wystąpienia zarządzanego |
| Zawartość dotycząca zabezpieczeń dla zarządzanego wystąpienia zaktualizowane | Zaktualizowane i wyjaśniono zawartość [model zabezpieczeń dla zarządzanego wystąpienia](sql-database-security-overview.md)i odróżniające się przy użyciu modelu zabezpieczeń dla pojedynczych baz danych i pul elastycznych |
| Odświeżane wszystkie przewodników Szybki Start i samouczki | Wszystkie przewodników Szybki Start i samouczków w [dokumentacji](https://docs.microsoft.com/azure/sql-database) zostały zaktualizowane i odświeżyć, aby dopasować zmiany w witrynie Azure portal |
| Przewodniki Przegląd dodano szybkiego startu | Dodano omówienie Przewodnik Szybki Start dla [pojedyncze bazy danych](sql-database-quickstart-guide.md) i [wystąpienia zarządzane](sql-database-managed-instance-quickstart-guide.md) |
| Dodano bazę danych SQL słownik pojęć | To [słownik terminów](sql-database-glossary-terms.md) artykuł zawiera listę ostatecznych terminów bazy danych SQL i łącza do głównej strony koncepcyjnej wyjaśnia pojęcie w kontekście. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Przyczynia się do poprawy jakości zawartości

Zestaw dokumentacji usługi Azure SQL jest "open source". Praca w środowisku otwartym oferuje następujące korzyści:

- Planowanie repozytoriów typu open source w środowisku otwartym, aby uzyskać opinie na temat najbardziej potrzebnych dokumentów, jakie.
- "Open source" Przeglądanie repozytoriów w środowisku otwartym w celu publikowania najbardziej użytecznej zawartości w naszej pierwszej wersji.
- Aktualizowanie repozytoriów typu open source w środowisku otwartym w celu ułatwienia stałego udoskonalania zawartości.

Aby współtworzyć zawartość dokumentacji usługi Azure SQL Database, zobacz [przewodnik współautora Microsoft Docs — omówienie](https://docs.microsoft.com/en-us/contribute/). Środowisko użytkownika w [docs.microsoft.com](https://docs.microsoft.com/) integruje [GitHub](https://github.com/) przepływy pracy, bezpośrednio po to, aby jeszcze bardziej ułatwiał. Rozpocznij od [edytowania przeglądanego dokumentu](https://docs.microsoft.com/en-us/contribute/#quick-edits-to-existing-documents). Lub Pomóż [recenzować nowe tematy](https://docs.microsoft.com/en-us/contribute/#review-open-prs), lub [tworzenie problemów z jakością](https://docs.microsoft.com/en-us/contribute/#create-quality-issues).
