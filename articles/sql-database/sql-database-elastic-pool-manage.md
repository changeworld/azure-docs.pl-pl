---
title: Zarządzanie pulami elastycznymi
description: Tworzenie pul elastycznych SQL platformy Azure i zarządzanie nimi.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: d8dde76753e58c713763c16230e5461fef43be88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067347"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Zarządzanie pulami elastycznymi w bazie danych SQL usługi Azure

Za pomocą puli elastycznej można określić ilość zasobów, które puli elastycznej wymaga do obsługi obciążenia jego baz danych i ilość zasobów dla każdej puli bazy danych.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portal Azure: zarządzanie pulami elastycznymi i buforowanymi bazami danych

Wszystkie ustawienia puli można znaleźć w jednym miejscu: **Configure pool** blade. Aby uzyskać dostęp do tej lokalizacji, znajdź pulę elastyczną w portalu i kliknij pozycję **Konfiguruj pulę** od góry bloku lub z menu zasobów po lewej stronie.

W tym miejscu można wprowadzić dowolną kombinację następujących zmian i zapisać je wszystkie w jednej partii:

1. Zmienianie warstwy usług puli
2. Skalowanie wydajności (DTU lub vCores) i przechowywanie w górę lub w dół
3. Dodawanie lub usuwanie baz danych do/z puli
4. Ustawianie minimalnego (gwarantowanego) i maksymalnego limitu wydajności baz danych w pulach
5. Przejrzyj podsumowanie kosztów, aby wyświetlić wszelkie zmiany na rachunku w wyniku nowych wyborów

![Elastyczne ostrze konfiguracji puli](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: Zarządzanie pulami elastycznymi i buforowanymi bazami danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć elastyczne pule bazy danych SQL i połączone bazy danych za pomocą programu Azure PowerShell i zarządzać nimi, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli chcesz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Aby utworzyć serwery bazy danych SQL dla puli elastycznej i zarządzać nimi, zobacz [Tworzenie serwerów bazy danych SQL i zarządzanie nimi](sql-database-servers.md). Aby utworzyć reguły zapory i zarządzać nimi, zobacz [Tworzenie reguł zapory i zarządzanie nimi przy użyciu programu PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> W przypadku przykładowych skryptów programu PowerShell zobacz [Tworzenie pul elastycznych i przenoszenie baz danych między pulami i poza nią przy użyciu programu PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) i [programu PowerShell do monitorowania i skalowania puli elastycznej SQL w bazie danych SQL.](scripts/sql-database-monitor-and-scale-pool-powershell.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[Nowy-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Tworzy elastyczną pulę.|
|[Basen Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Pobiera elastyczne pule i ich wartości właściwości.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modyfikuje właściwości puli elastycznej Na przykład użyj **StorageMB** właściwość, aby zmodyfikować maksymalny magazyn puli elastycznej.|
|[Usuń-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Usuwa pulę elastyczną.|
|[Get-AzSqlElasticPoolAkcja](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Pobiera stan operacji na puli elastycznej|
|[Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy nową bazę danych w istniejącej puli lub jako pojedyncza baza danych. |
|[Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do, z lub między pulami elastycznymi.|
|[Usuń-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych.|

> [!TIP]
> Tworzenie wielu baz danych w puli elastycznej może zająć trochę czasu, gdy odbywa się przy użyciu portalu lub poleceń cmdlet programu PowerShell, które tworzą tylko jedną bazę danych naraz. Aby zautomatyzować tworzenie w puli elastycznej, zobacz [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Narzędzie cli platformy Azure: zarządzanie pulami elastycznymi i buforowanymi bazami danych

Aby utworzyć elastyczne pule bazy danych SQL i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure,](/cli/azure)należy użyć następujących poleceń [bazy danych SQL interfejsu wiersza polecenia azure.](/cli/azure/sql/db) Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> W przypadku przykładowych skryptów interfejsu wiersza polecenia platformy Azure zobacz [Przenoszenie bazy danych SQL usługi Azure w puli elastycznej SQL za pomocą interfejsu wiersza](scripts/sql-database-move-database-between-pools-cli.md) polecenia [platformy Azure do skalowania puli elastycznej SQL w bazie danych SQL.](scripts/sql-database-scale-pool-cli.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql elastyczna pula utworzyć](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Tworzy elastyczną pulę.|
|[az sql elastyczna lista puli](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Zwraca listę pul elastycznych na serwerze.|
|[az sql elastyczna lista-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Zwraca listę baz danych w puli elastycznej.|
|[az sql elastyczna pula list-wydania](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Obejmuje również dostępne ustawienia DTU puli, limity miejsca do magazynowania i ustawienia bazy danych. Aby zmniejszyć szczegółowość, domyślnie ukryte są dodatkowe limity magazynowania i ustawienia bazy danych.|
|[az sql elastyczna aktualizacja puli](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje pulę elastyczną.|
|[az sql elastic-pool delete az sql elastic-pool delete az sql elastic-pool delete az](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Usuwa pulę elastyczną.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: Zarządzanie buforowanymi bazami danych

Aby utworzyć i przenieść bazy danych w istniejących pulach elastycznych lub zwrócić informacje o puli elastycznej bazy danych SQL za pomocą funkcji Transact-SQL, należy użyć następujących poleceń T-SQL. Te polecenia można wydać za pomocą portalu Azure, [programu SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio)programu Visual Studio [Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może łączyć się z serwerem bazy danych SQL platformy Azure i przekazywać polecenia Transact-SQL. Aby utworzyć reguły zapory i zarządzać nimi przy użyciu funkcji T-SQL, zobacz [Zarządzanie regułami zapory przy użyciu programu Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Nie można utworzyć, zaktualizować ani usunąć puli elastycznej bazy danych SQL azure przy użyciu usługi Transact-SQL. Można dodać lub usunąć bazy danych z puli elastycznej i można użyć DMV do zwrócenia informacji o istniejących pul elastycznych.
>

| Polecenie | Opis |
| --- | --- |
|[TWORZENIE BAZY DANYCH (usługa Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych w istniejącej puli lub jako pojedyncza baza danych. Aby utworzyć nową bazę danych, musisz być połączony z główną bazą danych.|
| [ALTER DATABASE (usługa Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przenoszenie bazy danych do, z lub między pulami elastycznymi.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.elastic_pool_resource_stats (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Zwraca statystyki użycia zasobów dla wszystkich pul elastycznych na serwerze bazy danych SQL. Dla każdej puli elastycznej jest jeden wiersz dla każdego 15 sekundowego okna raportowania (cztery wiersze na minutę). Obejmuje to procesora CPU, We/Wy, Dziennika, zużycie magazynu i równoczesne wykorzystanie żądań/sesji przez wszystkie bazy danych w puli.|
|[sys.database_service_objectives (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwa cenowa) i nazwę puli elastycznej, jeśli istnieje, dla bazy danych SQL platformy Azure lub usługi Azure SQL Data Warehouse. Jeśli zalogowany do głównej bazy danych na serwerze usługi Azure SQL Database, zwraca informacje o wszystkich bazach danych. W przypadku usługi Azure SQL Data Warehouse musisz mieć połączenie z główną bazą danych.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>INTERFEJS API REST: Zarządzanie pulami elastycznymi i buforowanymi bazami danych

Aby utworzyć elastyczne pule bazy danych SQL i zarządzania nimi, należy użyć tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Pule elastyczne — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Tworzy nową pulę elastyczną lub aktualizuje istniejącą pulę elastyczną.|
|[Pule elastyczne — usuwanie](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Usuwa pulę elastyczną.|
|[Baseny elastyczne - Pobierz](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Pobiera elastyczną pulę.|
|[Pule elastyczne — lista według serwera](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Zwraca listę pul elastycznych na serwerze.|
|[Baseny elastyczne - Aktualizacja](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Aktualizuje istniejącą pulę elastyczną.|
|[Zajęcia z basenu elastycznego](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Zwraca działania puli elastycznej.|
|[Działania bazy danych puli elastycznej](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Zwraca aktywność w bazach danych wewnątrz puli elastycznej.|
|[Bazy danych — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — pobierz](https://docs.microsoft.com/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według puli elastycznej](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według serwera](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Aby uzyskać samouczek SaaS przy użyciu basenów elastycznych, zobacz [Wprowadzenie do aplikacji SaaS Wingtip](sql-database-wtp-overview.md).
