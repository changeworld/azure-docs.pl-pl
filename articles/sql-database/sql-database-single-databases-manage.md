---
title: Tworzenie, zarządzanie serwerami usługi Azure SQL i pojedynczych baz danych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat tworzenia i zarządzania serwerami logicznymi i pojedynczych baz danych.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: single-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/07/2018
ms.author: carlrab
ms.openlocfilehash: 20039c32ed7bb740ba5d1185d195d7590cff39e2
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051259"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Tworzenie i zarządzanie serwerami logicznymi i pojedynczych baz danych w usłudze Azure SQL Database 

Można tworzyć i zarządzać serwerach logicznych Azure SQL Database i pojedynczych baz danych przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Witryna Azure portal: Zarządzanie serwerów logicznych i baz danych

Można utworzyć grupę zasobów Azure SQL database wcześniej lub podczas tworzenia serwera. Istnieje wiele metod uzyskiwania nowy formularz serwera SQL, tworząc nowy serwer SQL lub jako część tworzenia nowej bazy danych. 

### <a name="create-a-blank-sql-server-logical-server"></a>Tworzenie pustego serwera SQL (serwer logiczny)

Do utworzenia serwera (bez bazy danych) usługi Azure SQL Database przy użyciu [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza programu SQL server (serwer logiczny).  

### <a name="create-a-blank-or-sample-sql-database"></a>Tworzenie pustego lub przykładowy bazy danych SQL

Aby utworzyć bazę danych Azure SQL za pomocą [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza SQL Database i podaj wymagane informacje. Można utworzyć grupę zasobów i serwer logiczny wcześniej lub podczas tworzenia bazy danych Azure SQL database. Możesz utworzyć pustą bazę danych lub tworzenie przykładowej bazy danych oparte na Adventure Works LT. 

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Więcej informacji o dobieraniu warstwy cenowej bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Zarządzanie istniejącego serwera SQL

Aby zarządzać istniejącego serwera, przejdź do serwera przy użyciu szeregu metod — np. informacji od określonej strony bazy danych SQL, **serwerów SQL** strony, lub **wszystkie zasoby** strony. 

Aby zarządzać istniejącej bazy danych, przejdź do **baz danych SQL** strony, a następnie kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć, ustawienia zapory na poziomie serwera, bazy danych, z **Przegląd** stron dla bazy danych. 

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
>

> [!TIP]
> Dla portalu szybkiego startu platformy Azure, zobacz [utworzyć bazę danych Azure SQL database w witrynie Azure portal](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>Program PowerShell: Zarządzanie serwerów logicznych i baz danych

Aby utworzyć i zarządzać nimi, Azure SQL server, baz danych i zapory za pomocą programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!TIP]
> Dla programu PowerShell przewodnika Szybki Start zobacz [tworzenie pojedynczej bazy danych Azure SQL przy użyciu programu PowerShell](sql-database-get-started-portal.md). W przypadku skryptów przykład programu PowerShell, zobacz [Użyj programu PowerShell do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md) i [Monitor i skalowanie pojedynczej SQL bazy danych przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Tworzy bazę danych |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Pobiera jeden lub więcej baz danych|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Ustawia właściwości dla bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Usuwa bazę danych|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Tworzy grupę zasobów|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Tworzy serwer|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Zwraca informacje na temat serwerów|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modyfikuje właściwości serwera|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Usuwa serwer|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Tworzy regułę zapory na poziomie serwera |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Pobiera reguły zapory dla serwera|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| New-AzureRmSqlServerVirtualNetworkRule | Tworzy [ *reguły sieci wirtualnej*](sql-database-vnet-service-endpoint-rule-overview.md)zgodnie z podsieci, która jest punkt końcowy usługi sieci wirtualnej. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Interfejs wiersza polecenia platformy Azure: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi, Azure SQL server, baz danych i zapory za pomocą [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Tworzenie i zarządzanie elastycznymi pulami, zobacz [pul elastycznych](sql-database-elastic-pool.md).

> [!TIP]
> W przewodniku Szybki Start wiersza polecenia platformy Azure, zobacz [tworzenie pojedynczej bazy danych Azure SQL przy użyciu wiersza polecenia platformy Azure](sql-database-cli-samples.md). W przypadku skryptów przykład wiersza polecenia platformy Azure, zobacz [użycia interfejsu wiersza polecenia do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-cli.md) i [użycia interfejsu wiersza polecenia do monitorowania i skalowania pojedynczej bazy danych SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Tworzy bazę danych|
|[AZ sql db list](/cli/azure/sql/db#az_sql_db_list)|Wyświetla listę wszystkich baz danych i magazyny danych na serwerze lub wszystkich baz danych w puli elastycznej|
|[AZ sql db list wersje](/cli/azure/sql/db#az_sql_db_list_editions)|Cele i limity przestrzeni dyskowej, usługi dostępne listy|
|[polecenia az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Zwraca bazy danych użycia|
|[AZ sql db show](/cli/azure/sql/db#az_sql_db_show)|Pobiera bazy danych ani na magazyn danych|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Aktualizuje bazę danych|
|[AZ sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az_group_create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Tworzy serwer|
|[AZ sql server list](/cli/azure/sql/server#az_sql_server_list)|Listy serwerów|
|[polecenia az sql server list-usages](/cli/azure/sql/server#az_sql_server_list_usages)|Zwraca użycia serwera|
|[AZ sql server show](/cli/azure/sql/server#az_sql_server_show)|Pobiera serwera|
|[Aktualizacja az sql server](/cli/azure/sql/server#az_sql_server_update)|Serwer aktualizacji|
|[AZ sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Usuwa serwer|
|[Utwórz az sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Tworzy regułę zapory serwera|
|[AZ sql server reguły zapory na liście](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory az sql server](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizuje regułę zapory|
|[Usuń regułę zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Usuwa reguły zapory|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Języka Transact-SQL: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapory za pomocą instrukcji języka Transact-SQL, użyj następujących poleceń języka T-SQL. Tych poleceń przy użyciu portalu Azure można wydać [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [programu Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który może połączyć się z serwerem usługi Azure SQL Database i przekazać języka Transact-SQL polecenia. Aby zarządzać pul elastycznych, zobacz [pul elastycznych](sql-database-elastic-pool.md).


> [!TIP]
> Aby uzyskać szybki start za pomocą programu SQL Server Management Studio na Microsoft Windows, zobacz [usługi Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md). Aby uzyskać szybki start w systemie macOS, Linux lub Windows przy użyciu programu Visual Studio Code, zobacz [usługi Azure SQL Database: Użyj programu Visual Studio Code do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-vscode.md).

> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu języka Transact-SQL.
>

| Polecenie | Opis |
| --- | --- |
|[Tworzenie bazy danych (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje usługi Azure SQL database. |
|[Instrukcja ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modyfikuje usługi Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany z główną bazą danych na serwerze usługi Azure SQL Database, zwraca informacje dla wszystkich baz danych. Azure SQL Data Warehouse musisz mieć połączenie z główną bazą danych.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych Azure SQL Database. Dla co 15 sekund, istnieje jeden wiersz, nawet jeśli nie ma działania w bazie danych.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane dotyczące użycia i magazynu procesora CPU dla usługi Azure SQL Database. Dane są zbierane i agregowane w 5 minutowych interwałach.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera dane statystyczne dla zdarzenia połączenia bazy danych SQL Database zawierające przegląd bazy danych połączenia sukcesy i niepowodzenia. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślnego połączenia z bazą danych Azure SQL Database, błędy połączeń i zakleszczenia. Śledzenie lub Rozwiązywanie problemów z usługą SQL Database aktywności bazy danych, można użyć tych informacji.|
|[procedurę składowaną sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera bazy danych SQL. Tę procedurę składowaną, jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera. Regułę zapory na poziomie serwera można tworzyć tylko za pomocą języka Transact-SQL, po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnienia na poziomie platformy Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje dotyczące ustawień zapory na poziomie serwera, skojarzonych z usługi Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwerem usługi SQL Database. Tę procedurę składowaną, jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla usługi Azure SQL Database lub SQL Data Warehouse. Można skonfigurować reguły zapory bazy danych dla bazy danych master i bazach danych użytkowników w bazie danych SQL. Reguły zapory bazy danych są przydatne, gdy za pomocą zawartych użytkowników bazy danych. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje dotyczące ustawień zapory na poziomie bazy danych, skojarzonych z usługi Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z usługi Azure SQL Database lub SQL Data Warehouse. |



## <a name="rest-api-manage-logical-servers-and-databases"></a>Interfejs API REST: Zarządzanie serwerów logicznych i baz danych

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapór, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowego serwera.|
|[Serwery — usuwanie](/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery - Get](/rest/api/sql/servers/get)|Pobiera serwera.|
|[Serwery — lista](/rest/api/sql/servers/list)|Zwraca listę serwerów.|
|[Serwery — listy według grupy zasobów](/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — Get](/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — listę według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista przez serwer](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły - zapory Utwórz lub zaktualizuj](/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje reguły zapory.|
|[Reguły zapory - Delete](/rest/api/sql/firewallrules/delete)|Usuwa reguły zapory.|
|[Reguły zapory - Get](/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista przez serwer](/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracja do usługi Azure SQL Database](sql-database-cloud-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
