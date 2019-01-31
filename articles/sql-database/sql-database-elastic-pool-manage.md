---
title: Tworzenie i zarządzanie nimi elastyczne pule — usługa Azure SQL database | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie elastycznymi pulami Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 38f0d9cc6f507aa7d521aba0ff737f7bbaf2b211
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468125"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Tworzenie i zarządzanie elastycznymi pulami w usłudze Azure SQL Database

Z puli elastycznej należy określić ilość zasobów, które elastycznej puli wymaga, aby obsłużyć obciążenie z jej baz danych, a ilość zasobów dla każdej puli bazy danych.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: Zarządzanie elastycznych pul i baz danych w puli

Wszystkie ustawienia puli można znaleźć w jednym miejscu: **skonfigurować pulę** bloku. Aby uzyskać w tym miejscu, Znajdź puli elastycznej w portalu i kliknij przycisk **skonfigurować pulę** w górnej części bloku lub zasobu menu po lewej stronie.

W tym miejscu można wprowadzić dowolną kombinację następujących zmian, a następnie zapisz je wszystkie w jednej partii:

1. Zmiana warstwy usługi puli
2. Skalowanie wydajności (jednostki DTU lub rdzeni wirtualnych) i Magazyn w górę lub w dół
3. Dodawanie lub usuwanie baz danych z puli
4. Ustaw minimalny (gwarantowane), a maksymalny limit wydajności dla baz danych w pulach
5. Przejrzyj podsumowanie kosztów, aby wyświetlić wszelkie zmiany na rachunku wyniku nowe opcje

![Blok konfiguracji puli elastycznej](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>Program PowerShell: Zarządzanie elastycznych pul i baz danych w puli

Aby utworzyć i zarządzać nimi, SQL Database elastycznych pul i baz danych w puli za pomocą programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Aby utworzyć i zarządzać serwerami bazy danych SQL dla puli elastycznej, zobacz [tworzenie serwerów bazy danych SQL Database i zarządzanie nimi](sql-database-servers.md). Aby utworzyć i zarządzać regułami zapory, zobacz [tworzenie i zarządzanie regułami zapory za pomocą programu PowerShell](sql-database-firewall-configure.md#manage-firewall-rules-using-azure-powershell).

> [!TIP]
> W przypadku skryptów przykład programu PowerShell, zobacz [Tworzenie pul elastycznych i przenoszenie baz danych między pulami i puli, za pomocą programu PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) i [Użyj programu PowerShell, monitorowanie i skalowanie elastycznej puli SQL w usłudze Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Tworzy pulę elastyczną.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Pobiera pul elastycznych i ich wartości właściwości.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modyfikuje właściwości puli elastycznej, na przykład użyj **StorageMB** właściwości, aby zmodyfikować maksymalny rozmiar magazynu puli elastycznej.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Usuwa puli elastycznej.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Pobiera stan operacji w puli elastycznej|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczej bazy danych. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Pobiera co najmniej jedną bazę danych.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Ustawia właściwości dla bazy danych lub przenosi istniejącą bazę danych do, poza nie lub między elastycznymi pulami.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Usuwa bazę danych.|

> [!TIP]
> Tworzenie wielu baz danych w elastycznej puli może potrwać po zakończeniu za pomocą portalu lub poleceń cmdlet programu PowerShell, który tworzenie tylko jednej bazy danych w danym momencie. Aby zautomatyzować tworzenie w elastycznej puli, zobacz [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Interfejs wiersza polecenia platformy Azure: Zarządzanie elastycznych pul i baz danych w puli

Aby utworzyć i zarządzać elastycznymi pulami SQL Database za pomocą [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> W przypadku skryptów przykład wiersza polecenia platformy Azure, zobacz [użycia interfejsu wiersza polecenia, aby przenieść bazę danych Azure SQL database w elastycznej puli SQL](scripts/sql-database-move-database-between-pools-cli.md) i [Azure Użyj interfejsu wiersza polecenia do skalowania elastycznej puli SQL w usłudze Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[Utwórz az sql elastic-pool](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Tworzy pulę elastyczną.|
|[AZ sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Zwraca listę pule elastyczne na serwerze.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Zwraca listę baz danych w puli elastycznej.|
|[AZ sql elastic-pool list wersje](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Zawiera również ustawień jednostek DTU dostępnej puli, limity przestrzeni dyskowej i ustawienia poszczególnych baz danych. W celu zmniejszenia poziomu szczegółowości, limity dodatkowego miejsca do magazynowania i na bazę danych ustawienia są domyślnie ukryte.|
|[AZ sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje puli elastycznej.|
|[AZ sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Usuwa puli elastycznej.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Zarządzanie bazami danych w puli

Tworzenie i przenoszenie baz danych w ramach istniejących pul elastycznych, lub do zwracania informacji dotyczących puli elastycznej bazy danych SQL za pomocą instrukcji języka Transact-SQL, użyj następujących poleceń języka T-SQL. Tych poleceń przy użyciu portalu Azure można wydać [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [programu Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który może połączyć się z serwerem usługi Azure SQL Database i przekazać języka Transact-SQL polecenia. Aby utworzyć i zarządzać regułami zapory za pomocą języka T-SQL, zobacz [zarządzanie regułami zapory za pomocą języka Transact-SQL](sql-database-firewall-configure.md#manage-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> Nie można utworzyć, zaktualizować ani usunąć pulę elastyczną usługi Azure SQL Database przy użyciu języka Transact-SQL. Można dodawać lub usuwać bazy danych z puli elastycznej i korzystanie z widoków DMV, aby zostały zwrócone informacje o istniejących pul elastycznych.
>

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczej bazy danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przenoszenie bazy danych do, poza nie lub między elastycznymi pulami.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Zwraca statystyki użycia zasobów dla elastycznych pul na serwerze bazy danych SQL. Dla każdej puli elastycznej jest jeden wiersz w każdej sekundzie 15 raportowania okna (cztery wiersze na minutę). W tym procesora CPU, we/wy, Log, wykorzystanie magazynu i wykorzystanie równoczesnych żądań/sesji przez wszystkie bazy danych w puli.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany z główną bazą danych na serwerze usługi Azure SQL Database, zwraca informacje dla wszystkich baz danych. Azure SQL Data Warehouse musisz mieć połączenie z główną bazą danych.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>INTERFEJS API REST: Zarządzanie elastycznych pul i baz danych w puli

Aby utworzyć i zarządzać nimi, SQL Database elastycznych pul i baz danych w puli, należy użyć tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Elastyczne pule — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Powoduje utworzenie nowej puli elastycznej lub aktualizacji istniejącej puli elastycznej.|
|[Elastyczne pule — usuwanie](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Usuwa puli elastycznej.|
|[Elastyczne pule — Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Pobiera puli elastycznej.|
|[Elastyczne pule — lista przez serwer](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Zwraca listę pule elastyczne na serwerze.|
|[Elastyczne pule — aktualizacja](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Aktualizuje istniejący puli elastycznej.|
|[Działania puli elastycznej](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Zwraca działania puli elastycznej.|
|[Działania bazy danych w puli elastycznej](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Zwraca działania baz danych w puli elastycznej.|
|[Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — listę według puli elastycznej](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista przez serwer](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wideo, zobacz [Microsoft Virtual Academy kurs wideo w serwisie elastycznym możliwościom usługi Azure SQL Database](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Samouczek SaaS wykorzystujących pule elastyczne, zobacz [wprowadzenie do aplikacji SaaS o nazwie Wingtip](sql-database-wtp-overview.md).
