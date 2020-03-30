---
title: Tworzenie serwerów i pojedynczych baz danych, zarządzanie nimi
description: Dowiedz się więcej o tworzeniu serwerów bazy danych SQL i pojedynczych bazach danych oraz zarządzaniu nimi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268550"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Tworzenie serwerów bazy danych SQL i pojedynczych baz danych i zarządzanie nimi w bazie danych SQL Azure

Serwery bazy danych SQL i pojedyncze bazy danych można tworzyć i zarządzać nimi za pomocą witryny Azure portal, PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Portal Azure: Zarządzanie serwerami bazy danych SQL i pojedynczymi bazami danych

Można utworzyć grupę zasobów bazy danych SQL platformy Azure z wyprzedzeniem lub podczas tworzenia samego serwera. Istnieje wiele metod uzyskiwania do nowego formularza serwera SQL, przez utworzenie nowego serwera SQL lub w ramach tworzenia nowej bazy danych.

### <a name="create-a-blank-sql-database-server"></a>Tworzenie pustego serwera bazy danych SQL

Aby utworzyć serwer bazy danych SQL przy użyciu [portalu Azure portal](https://portal.azure.com), przejdź do pustego formularza serwera SQL (serwera logicznego).  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Tworzenie pustej lub przykładowej pojedynczej bazy danych SQL

Aby utworzyć pojedynczą bazę danych sql platformy Azure przy użyciu [portalu Azure portal,](https://portal.azure.com)przejdź do pustego formularza bazy danych SQL i podaj żądane informacje. Można utworzyć grupę zasobów bazy danych SQL platformy Azure i serwer bazy danych SQL z wyprzedzeniem lub podczas tworzenia pojedynczej bazy danych. Można utworzyć pustą bazę danych lub utworzyć przykładową bazę danych na podstawie programu Adventure Works LT.

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej bazy danych, zobacz [model zakupów oparty na jednostkach DTU](sql-database-service-tiers-dtu.md) i model zakupu oparty na [łanie wirtualnej](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Zarządzanie istniejącym serwerem bazy danych SQL

Aby zarządzać istniejącym serwerem bazy danych SQL, przejdź do serwera przy użyciu wielu metod — takich jak strona określonej bazy danych SQL, strona **serwerów SQL** lub strona **Wszystkie zasoby.**

Aby zarządzać istniejącą bazą danych, przejdź do strony **bazy danych SQL** i kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych ze strony **Przegląd** bazy danych.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [model zakupu oparty na UTU](sql-database-service-tiers-dtu.md) i model zakupu oparty na [racji wirtualnej](sql-database-service-tiers-vcore.md).
> [!TIP]
> Aby zapoznać się z przewodnikiem szybki start w portalu Azure, zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure.](sql-database-single-database-get-started.md)

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: Zarządzanie serwerami bazy danych SQL i pojedynczymi bazami danych

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć serwery bazy danych SQL i zarządzać nimi, pojedyncze i bufory baz danych oraz zapory serwera bazy danych SQL za pomocą programu Azure PowerShell, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli chcesz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> W przypadku przykładowych skryptów programu PowerShell zobacz [Tworzenie pojedynczej bazy danych usługi Azure SQL za pomocą programu PowerShell oraz konfigurowanie reguły zapory serwera bazy danych SQL](scripts/sql-database-create-and-configure-database-powershell.md) i [monitora oraz skalowanie pojedynczej bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy bazę danych |
|[Baza danych Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera jedną lub więcej baz danych|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
|[Usuń-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Tworzy grupę zasobów|
|[Nowy serwer AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Tworzy serwer|
|[Serwer Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Zwraca informacje o serwerach|
|[Serwer set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modyfikuje właściwości serwera|
|[Usuń-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Usuwa serwer|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Tworzy regułę zapory na poziomie serwera |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Pobiera reguły zapory dla serwera|
|[Zestaw-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Usuń-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| Nowy-AzSqlServerVirtualNetworkRule | Tworzy [*regułę sieci wirtualnej*](sql-database-vnet-service-endpoint-rule-overview.md), na podstawie podsieci, która jest punktem końcowym usługi sieci wirtualnej. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>Narzędzie interfejsu wiersza polecenia platformy Azure: zarządzanie serwerami bazy danych SQL i pojedynczymi bazami danych

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi za pomocą [interfejsu wiersza polecenia Azure CLI,](/cli/azure)należy użyć następujących poleceń [bazy danych SQL platformy Azure.](/cli/azure/sql/db) Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby utworzyć baseny elastyczne i zarządzać nimi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

> [!TIP]
> Aby uzyskać szybki start interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie pojedynczej bazy danych sql platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md). W przypadku przykładowych skryptów interfejsu wiersza polecenia platformy Azure zobacz [Tworzenie pojedynczej bazy danych sql usługi Azure SQL i konfigurowanie reguły zapory bazy danych SQL oraz](scripts/sql-database-create-and-configure-database-cli.md) Monitorowanie i skalowanie pojedynczej bazy danych usługi Azure SQL za pomocą interfejsu [wiersza polecenia.](scripts/sql-database-monitor-and-scale-database-cli.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Tworzy bazę danych|
|[az sql db lista](/cli/azure/sql/db#az-sql-db-list)|Wyświetla listę wszystkich baz danych i magazynów danych na serwerze lub wszystkich baz danych w puli elastycznej|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Wyświetla listę dostępnych celów usług i limitów magazynowania|
|[az sql db list-usages az sql db list-usages az sql db list-usages az](/cli/azure/sql/db#az-sql-db-list-usages)|Zwraca użycie bazy danych|
|[az sql db pokaż](/cli/azure/sql/db#az-sql-db-show)|Pobiera bazę danych lub magazyn danych|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje bazę danych|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az-group-create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Tworzy serwer|
|[az sql lista serwerów](/cli/azure/sql/server#az-sql-server-list)|Wyświetla listę serwerów|
|[az sql serwer list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Zwraca użycia serwera|
|[az sql serwer pokaż](/cli/azure/sql/server#az-sql-server-show)|Pobiera serwer|
|[az sql aktualizacja serwera](/cli/azure/sql/server#az-sql-server-update)|Aktualizuje serwer|
|[az sql server delete az sql server delete az sql server delete az](/cli/azure/sql/server#az-sql-server-delete)|Usuwa serwer|
|[az sql server firewall-rule create az sql server firewall-rule create az sql server firewall-rule create az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Tworzy regułę zapory serwera|
|[az sql server firewall-rule lista](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Wyświetla listę reguł zapory na serwerze|
|[az sql server firewall-rule show az sql server firewall-rule show az sql server firewall-rule show az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Pokazuje szczegóły reguły zapory|
|[az sql server firewall-rule update az sql server firewall-rule update az sql server firewall-rule](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje regułę zapory|
|[az sql server firewall-rule delete az sql server firewall-rule delete az sql server firewall-rule delete az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Usuwa regułę zapory|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: Zarządzanie serwerami bazy danych SQL i pojedynczymi bazami danych

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi za pomocą funkcji Transact-SQL, należy użyć następujących poleceń T-SQL. Te polecenia można wydać za pomocą portalu Azure, [programu SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio)programu Visual Studio [Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może łączyć się z serwerem bazy danych SQL platformy Azure i przekazywać polecenia Transact-SQL. Aby zarządzać pulami elastycznymi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

> [!TIP]
> Aby uzyskać przewodnik Szybki start przy użyciu programu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Baza danych SQL Azure: Łączenie i wykonywanie zapytań](sql-database-connect-query-ssms.md)o dane za pomocą programu SQL Server Management Studio. Aby uzyskać przewodnik Szybki start przy użyciu programu Visual Studio Code w systemach macOS, Linux lub Windows, zobacz [Baza danych SQL Azure: Łączenie i wykonywanie zapytań o dane za pomocą programu Visual Studio.](sql-database-connect-query-vscode.md)
> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu funkcji Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[TWORZENIE BAZY DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Tworzy nową pojedynczą bazę danych. Aby utworzyć nową bazę danych, musisz być połączony z główną bazą danych.|
| [ALTER DATABASE (usługa Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modyfikuje bazę danych SQL platformy Azure. |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwa cenowa) i nazwę puli elastycznej, jeśli istnieje, dla bazy danych SQL platformy Azure lub usługi Azure SQL Data Warehouse. Jeśli zalogowany do głównej bazy danych na serwerze usługi Azure SQL Database, zwraca informacje o wszystkich bazach danych. W przypadku usługi Azure SQL Data Warehouse musisz mieć połączenie z główną bazą danych.|
|[sys.dm_db_resource_stats (usługa Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca zużycie procesora CPU, we/wy i pamięci dla bazy danych usługi Azure SQL Database. Jeden wiersz istnieje co 15 sekund, nawet jeśli nie ma żadnych działań w bazie danych.|
|[sys.resource_stats (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora i magazynu dla bazy danych SQL Azure. Dane są zbierane i agregowane w odstępach pięciu minut.|
|[sys.database_connection_stats (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera statystyki zdarzeń łączności bazy danych SQL Database, zapewniając przegląd sukcesów i błędów połączenia z bazą danych. |
|[sys.event_log (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne połączenia bazy danych usługi Azure SQL Database, błędy połączeń i zakleszczenia. Te informacje można użyć do śledzenia lub rozwiązywania problemów z aktywnością bazy danych za pomocą bazy danych SQL Database.|
|[sp_set_firewall_rule (baza danych SQL azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera bazy danych SQL. Ta procedura składowana jest dostępna tylko w głównej bazie danych do logowania głównego na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć tylko przy użyciu funkcji Transact-SQL po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure|
|[sys.firewall_rules (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z bazą danych SQL platformy Microsoft Azure.|
|[sp_delete_firewall_rule (usługa Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwera bazy danych SQL. Ta procedura składowana jest dostępna tylko w głównej bazie danych do logowania głównego na poziomie serwera.|
|[sp_set_database_firewall_rule (baza danych SQL azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla usługi Azure SQL Database lub USŁUGI SQL Data Warehouse. Reguły zapory bazy danych można skonfigurować dla głównej bazy danych i dla baz danych użytkowników w bazie danych SQL. Reguły zapory bazy danych są przydatne podczas korzystania z użytkowników zawartej bazy danych. |
|[sys.database_firewall_rules (usługa Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych skojarzonych z bazą danych SQL platformy Microsoft Azure. |
|[sp_delete_database_firewall_rule (baza danych SQL azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z usługi Azure SQL Database lub USŁUGI SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>INTERFEJS API REST: Zarządzanie serwerami bazy danych SQL i pojedynczymi bazami danych

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](https://docs.microsoft.com/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery - Pobierz](https://docs.microsoft.com/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Zwraca listę serwerów w ramach subskrypcji.|
|[Serwery — lista według grupy zasobów](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](https://docs.microsoft.com/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — usuwanie](https://docs.microsoft.com/rest/api/sql/databases/delete)|Usuwa bazę danych.|
|[Bazy danych — pobierz](https://docs.microsoft.com/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według puli elastycznej](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według serwera](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły zapory — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje regułę zapory.|
|[Reguły zapory — usuwanie](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Usuwa regułę zapory.|
|[Reguły zapory zapory — Pobierz](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista według serwera](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o migrowaniu bazy danych programu SQL Server na platformę Azure, zobacz [Migrowanie do bazy danych SQL Azure](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
