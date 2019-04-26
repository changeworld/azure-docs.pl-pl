---
title: Tworzenie, zarządzanie serwerami usługi Azure SQL Database i pojedynczych baz danych | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat tworzenia i zarządzania, serwery usługi SQL Database i pojedynczych baz danych.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: bcbed12940b7766d7ffb9e67b7c63931160ba9b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331744"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Tworzenie i zarządzanie nimi, serwery usługi SQL Database i pojedynczych baz danych w usłudze Azure SQL Database

Można tworzyć i zarządzać serwerami bazy danych SQL i pojedynczych baz danych przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Witryna Azure Portal: Zarządzanie serwerami bazy danych SQL i pojedynczych baz danych

Można utworzyć grupę zasobów Azure SQL database wcześniej lub podczas tworzenia serwera. Istnieje wiele metod uzyskiwania nowy formularz serwera SQL, tworząc nowy serwer SQL lub jako część tworzenia nowej bazy danych.

### <a name="create-a-blank-sql-database-server"></a>Utwórz pusty serwer bazy danych SQL

Aby utworzyć serwer bazy danych SQL przy użyciu [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza programu SQL server (serwer logiczny).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Utwórz puste lub przykładowy pojedynczej bazy danych SQL

Aby utworzyć pojedynczą bazę danych Azure SQL przy użyciu [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza SQL Database i podaj wymagane informacje. Można utworzyć grupę zasobów i serwer usługi SQL Database wyprzedzeniem bazy danych Azure SQL lub podczas tworzenia pojedynczej precyzji samej bazy danych. Możesz utworzyć pustą bazę danych lub tworzenie przykładowej bazy danych oparte na Adventure Works LT.

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Więcej informacji o dobieraniu warstwy cenowej bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [utworzysz wystąpienie zarządzane](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Zarządzanie istniejącego serwera bazy danych SQL

Aby zarządzać istniejącego serwera bazy danych SQL, przejdź do serwera przy użyciu szeregu metod — np. informacji od określonej strony bazy danych SQL, **serwerów SQL** strony, lub **wszystkie zasoby** strony.

Aby zarządzać istniejącej bazy danych, przejdź do **baz danych SQL** strony, a następnie kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć, ustawienia zapory na poziomie serwera, bazy danych, z **Przegląd** stron dla bazy danych.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
> [!TIP]
> Dla portalu szybkiego startu platformy Azure, zobacz [utworzyć bazę danych Azure SQL database w witrynie Azure portal](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>Program PowerShell: Zarządzanie serwerami bazy danych SQL i pojedynczych baz danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć i zarządzać serwerów usługi Azure SQL Database, pojedynczej i puli baz danych i zapory serwera bazy danych SQL przy użyciu programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> W przypadku skryptów przykład programu PowerShell, zobacz [Użyj programu PowerShell do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory serwera bazy danych SQL](scripts/sql-database-create-and-configure-database-powershell.md) i [Monitor i skala SQL pojedyncze bazy danych przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) .

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy bazę danych |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera jeden lub więcej baz danych|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości dla bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Tworzy grupę zasobów|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Tworzy serwer|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Zwraca informacje na temat serwerów|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modyfikuje właściwości serwera|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Usuwa serwer|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Tworzy regułę zapory na poziomie serwera |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Pobiera reguły zapory dla serwera|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| New-AzSqlServerVirtualNetworkRule | Tworzy [ *reguły sieci wirtualnej*](sql-database-vnet-service-endpoint-rule-overview.md)zgodnie z podsieci, która jest punkt końcowy usługi sieci wirtualnej. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Interfejs wiersza polecenia platformy Azure: Zarządzanie serwerami bazy danych SQL i pojedynczych baz danych

Tworzenie i zarządzanie nimi, Azure SQL server, baz danych i zapory za pomocą [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Tworzenie i zarządzanie elastycznymi pulami, zobacz [pul elastycznych](sql-database-elastic-pool.md).

> [!TIP]
> W przewodniku Szybki Start wiersza polecenia platformy Azure, zobacz [utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure SQL database pojedynczego](sql-database-cli-samples.md). W przypadku skryptów przykład wiersza polecenia platformy Azure, zobacz [użycia interfejsu wiersza polecenia do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory bazy danych SQL](scripts/sql-database-create-and-configure-database-cli.md) i [użycia interfejsu wiersza polecenia do monitorowania i skalowania pojedynczej bazy danych Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Tworzy bazę danych|
|[AZ sql db list](/cli/azure/sql/db#az-sql-db-list)|Wyświetla listę wszystkich baz danych i magazyny danych na serwerze lub wszystkich baz danych w puli elastycznej|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Cele i limity przestrzeni dyskowej, usługi dostępne listy|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Zwraca bazy danych użycia|
|[AZ sql db show](/cli/azure/sql/db#az-sql-db-show)|Pobiera bazy danych ani na magazyn danych|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje bazę danych|
|[AZ sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az-group-create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Tworzy serwer|
|[AZ sql server list](/cli/azure/sql/server#az-sql-server-list)|Listy serwerów|
|[polecenia az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Zwraca użycia serwera|
|[AZ sql server show](/cli/azure/sql/server#az-sql-server-show)|Pobiera serwera|
|[Aktualizacja az sql server](/cli/azure/sql/server#az-sql-server-update)|Serwer aktualizacji|
|[AZ sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Usuwa serwer|
|[Utwórz az sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Tworzy regułę zapory serwera|
|[AZ sql server reguły zapory na liście](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory az sql server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje regułę zapory|
|[Usuń regułę zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Usuwa reguły zapory|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: Zarządzanie serwerami bazy danych SQL i pojedynczych baz danych

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapory za pomocą instrukcji języka Transact-SQL, użyj następujących poleceń języka T-SQL. Tych poleceń przy użyciu portalu Azure można wydać [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [programu Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który może połączyć się z serwerem usługi Azure SQL Database i przekazać języka Transact-SQL polecenia. Aby zarządzać pul elastycznych, zobacz [pul elastycznych](sql-database-elastic-pool.md).

> [!TIP]
> Aby uzyskać szybki start za pomocą programu SQL Server Management Studio na Microsoft Windows, zobacz [usługi Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md). Aby uzyskać szybki start w systemie macOS, Linux lub Windows przy użyciu programu Visual Studio Code, zobacz [usługi Azure SQL Database: Użyj programu Visual Studio Code do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Tworzy nowy pojedynczej bazy danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modyfikuje usługi Azure SQL database. |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany z główną bazą danych na serwerze usługi Azure SQL Database, zwraca informacje dla wszystkich baz danych. Azure SQL Data Warehouse musisz mieć połączenie z główną bazą danych.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych Azure SQL Database. Dla co 15 sekund, istnieje jeden wiersz, nawet jeśli nie ma działania w bazie danych.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane dotyczące użycia i magazynu procesora CPU dla usługi Azure SQL Database. Dane są zbierane i agregowane w 5 minutowych interwałach.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera dane statystyczne dla zdarzenia połączenia bazy danych SQL Database zawierające przegląd bazy danych połączenia sukcesy i niepowodzenia. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślnego połączenia z bazą danych Azure SQL Database, błędy połączeń i zakleszczenia. Śledzenie lub Rozwiązywanie problemów z usługą SQL Database aktywności bazy danych, można użyć tych informacji.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera bazy danych SQL. Tę procedurę składowaną, jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera. Regułę zapory na poziomie serwera można tworzyć tylko za pomocą języka Transact-SQL, po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnienia na poziomie platformy Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje dotyczące ustawień zapory na poziomie serwera, skojarzonych z usługi Microsoft Azure SQL Database.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwerem usługi SQL Database. Tę procedurę składowaną, jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla usługi Azure SQL Database lub SQL Data Warehouse. Można skonfigurować reguły zapory bazy danych dla bazy danych master i bazach danych użytkowników w bazie danych SQL. Reguły zapory bazy danych są przydatne, gdy za pomocą zawartych użytkowników bazy danych. |
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje dotyczące ustawień zapory na poziomie bazy danych, skojarzonych z usługi Microsoft Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z usługi Azure SQL Database lub SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>INTERFEJS API REST: Zarządzanie serwerami bazy danych SQL i pojedynczych baz danych

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapór, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowego serwera.|
|[Serwery — usuwanie](https://docs.microsoft.com/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Pobiera serwera.|
|[Serwery — lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Zwraca listę serwerów w ramach subskrypcji.|
|[Serwery — listy według grupy zasobów](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — usunięcia](https://docs.microsoft.com/rest/api/sql/databases/delete)|Usuwa bazę danych.|
|[Bazy danych — Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — listę według puli elastycznej](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista przez serwer](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły - zapory Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje reguły zapory.|
|[Reguły zapory - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Usuwa reguły zapory.|
|[Reguły zapory - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista przez serwer](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracja do usługi Azure SQL Database](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
