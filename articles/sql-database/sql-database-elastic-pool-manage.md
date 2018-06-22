---
title: Tworzenie i zarządzanie nimi pule elastyczne — usługa Azure SQL database | Dokumentacja firmy Microsoft
description: Utwórz i Zarządzaj pule elastyczne Azure SQL.
keywords: wiele baz danych, bazy danych zasobów, wydajność bazy danych
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313325"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Tworzenie i zarządzanie nimi pul elastycznych w bazie danych SQL Azure

Z puli elastycznej należy określić ilość zasobów wymaganych do obsługi obciążeń z jej baz danych puli elastycznej i ilość zasobów dla każdej puli bazy danych. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portalu Azure: Zarządzanie pule elastyczne i puli baz danych

Wszystkie ustawienia puli, znajdują się w jednym miejscu: **Konfigurowanie puli** bloku. Można uzyskać w tym miejscu, należy znaleźć puli elastycznej portalu i kliknij przycisk **Konfigurowanie puli** w górnej części bloku lub z menu po lewej stronie zasobów.

W tym miejscu można wprowadzić dowolne z następujących zmian, a następnie zapisz je w jednej partii:
1. Zmiana warstwy usług puli
2. Skalowanie wydajności (DTU lub vCores) i magazynu
3. Dodawanie lub usuwanie baz danych z puli
4. Ustaw min (gwarantowane), a maksymalny limit wydajności baz danych w puli
5. Przejrzyj podsumowanie kosztów, aby wyświetlić wszystkie zmiany na rachunku wyniku nowe opcje

![Blok konfiguracji puli elastycznej](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>Środowiska PowerShell: Zarządzanie pule elastyczne i puli baz danych 

Tworzenie i zarządzanie nimi pule elastyczne bazy danych SQL i puli baz danych przy użyciu programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Aby utworzyć i zarządzać baz danych, serwerów i reguły zapory, zobacz [Utwórz i Zarządzaj serwerami bazy danych SQL Azure i baz danych przy użyciu programu PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell).

> [!TIP]
> Dla programu PowerShell przykładowe skrypty, zobacz [tworzenie elastycznych pul i przenoszenia baz danych między pulami i z puli przy użyciu programu PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) i [Użyj programu PowerShell, aby monitorować i skalowania puli elastycznej SQL w bazie danych SQL Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Tworzy elastycznej puli baz danych na serwerze logicznym SQL.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Pobiera pule elastyczne i ich wartości właściwości na serwerze logicznym SQL.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modyfikuje właściwości elastycznej puli baz danych na serwerze logicznym SQL. Na przykład użyć **StorageMB** właściwości, aby zmodyfikować max magazynu elastycznej puli.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Usuwa elastycznej puli baz danych na serwerze logicznym SQL.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Pobiera stan operacji dla puli elastycznej na serwerze logicznym SQL.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczej bazy danych. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Pobiera co najmniej jedną bazę danych.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Ustawia właściwości dla bazy danych lub istniejącą bazę danych są przenoszone do z i między pule elastyczne.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Usuwa z bazy danych.|


> [!TIP]
> Tworzenie wielu baz danych w puli elastycznej może trwać, jeśli odbywa się za pomocą portalu lub poleceń cmdlet programu PowerShell, który tworzenie tylko jednej bazy danych w czasie. Aby zautomatyzować tworzenie w puli elastycznej, zobacz [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: Zarządzanie pule elastyczne i puli baz danych

Tworzenie i zarządzanie nimi pule elastyczne bazy danych SQL z [interfejsu wiersza polecenia Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> Dla interfejsu wiersza polecenia Azure przykładowe skrypty, zobacz [Użyj interfejsu wiersza polecenia, aby przenieść bazę danych Azure SQL w puli elastycznej SQL](scripts/sql-database-move-database-between-pools-cli.md) i [Użyj wiersza polecenia platformy Azure do skalowania puli elastycznej SQL w bazie danych SQL Azure](scripts/sql-database-scale-pool-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[Tworzenie puli elastyczna az sql](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Tworzy puli elastycznej.|
|[Lista elastyczna pula sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Zwraca listę pul elastycznych na serwerze.|
|[AZ pula elastyczna listy-baz danych sql](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Zwraca listę baz danych w puli elastycznej.|
|[Elastyczna pula sql az listy — wersje](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Również obejmuje ustawienia jednostek dtu w warstwie dostępnej puli, limity magazynu, a dla ustawienia bazy danych. Aby zmniejszyć poziom szczegółowości, limity dodatkowego miejsca do magazynowania i na bazę danych ustawienia są domyślnie ukryte.|
|[Aktualizacja elastyczna pula sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Aktualizuje puli elastycznej.|
|[Usuń elastyczna pula sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Usuwa puli elastycznej.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Zarządzanie bazami danych w puli

Aby utworzyć i przenoszenia baz danych w istniejącej puli elastycznej lub do zwracania informacji dotyczących puli elastycznej bazy danych SQL z Transact-SQL, użyj następujących poleceń T-SQL. Te polecenia przy użyciu portalu Azure może wystawiać [programu SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), lub inny program, który może nawiązywać połączenia z serwerem bazy danych SQL Azure i przekazać języka Transact-SQL polecenia. Aby utworzyć i zarządzać baz danych, serwerów i reguły zapory, zobacz [Utwórz i Zarządzaj serwerami bazy danych SQL Azure i baz danych przy użyciu języka Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Nie można tworzyć, aktualizować lub usuwać puli elastycznej bazy danych SQL Azure przy użyciu języka Transact-SQL. Można dodać lub usunąć z puli elastycznej bazy danych i widoków DMV służy do zwracania informacji dotyczących istniejących pul elastycznych.
>

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (baza danych Azure SQL)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczej bazy danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przenoszenie bazy danych do z lub między elastyczne pule.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa z bazy danych.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Zwraca statystyki użycia zasobów dla wszystkich pul elastycznych baz danych serwera logicznego. Dla każdej puli elastycznej bazy danych jest jeden wiersz dla każdej sekundzie 15 raportowania okna (cztery wiersze na minutę). W tym procesora CPU, we/wy, dziennika, użycia magazynu i użycie równoczesnych żądań/sesji przez wszystkie bazy danych w puli.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany do głównej bazy danych na serwerze bazy danych SQL Azure, zwraca informacje o wszystkich baz danych. Dla usługi Azure SQL Data Warehouse musi być podłączony do bazy danych master.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>Interfejs API REST: Zarządzanie pule elastyczne i puli baz danych

Aby utworzyć i zarządzać nimi pule elastyczne bazy danych SQL i baz danych w puli, użyj te żądania interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Pule elastyczne — tworzenie lub aktualizowanie](/rest/api/sql/elasticpools/createorupdate)|Tworzy nową pulę elastyczną lub aktualizuje istniejącej puli elastycznej.|
|[Pule elastyczne - Delete](/rest/api/sql/elasticpools/delete)|Usuwa puli elastycznej.|
|[Pule elastyczne - Get](/rest/api/sql/elasticpools/get)|Pobiera puli elastycznej.|
|[Pule elastyczne — lista przez serwer](/rest/api/sql/elasticpools/listbyserver)|Zwraca listę pul elastycznych na serwerze.|
|[Pule elastyczne - aktualizacji](/rest/api/sql/elasticpools/update)|Aktualizuje istniejącej puli elastycznej.|
|[Zalecana liczba elastycznych pul - Get](/rest/api/sql/recommendedelasticpools/get)|Pobiera zalecanej puli elastycznej.|
|[Zalecana liczba elastycznych pul — lista przez serwer](/rest/api/sql/recommendedelasticpools/listbyserver)|Zwraca zalecana liczba elastycznych pul.|
|[Zalecana liczba elastycznych pul - metryki listy](/rest/api/sql/recommendedelasticpools/listmetrics)|Zwraca zalecane metryki puli elastycznej.|
|[Działania puli elastycznej](/rest/api/sql/elasticpoolactivities)|Zwraca działania puli elastycznej.|
|[Działania bazy danych w puli elastycznej](/rest/api/sql/elasticpooldatabaseactivities)|Zwraca działania bazy danych, w ramach puli elastycznej.|
|[Bazy danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych - Get](/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — uzyskać za pomocą puli elastycznej](/rest/api/sql/databases/getbyelasticpool)|Pobiera bazy danych w puli elastycznej.|
|[Bazy danych — uzyskać za pomocą zalecana liczba elastycznych pul](/rest/api/sql/databases/getbyrecommendedelasticpool)|Pobiera bazy danych wewnątrz zalecanej puli elastycznej.|
|[Bazy danych — lista według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według zalecana liczba elastycznych pul](/rest/api/sql/databases/listbyrecommendedelasticpool)|Zwraca listę baz danych wewnątrz zalecanej puli elastycznej.|
|[Bazy danych — lista przez serwer](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wideo, zobacz [Microsoft Virtual Academy kursu wideo na możliwości elastycznej bazy danych SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Samouczek SaaS wykorzystujących pule elastyczne, zobacz [wprowadzenie do aplikacji Wingtip SaaS](sql-database-wtp-overview.md).
