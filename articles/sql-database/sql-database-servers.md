---
title: Serwery usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Więcej informacji na temat serwerów usługi Azure SQL Database oraz zarządzania nimi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 21d4e7bd3bf7453d2c770034728aedfdaa5ab85e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790167"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Serwery usługi Azure SQL Database i zarządzanie nimi

## <a name="what-is-an-azure-sql-database-server"></a>Co to jest serwer usługi Azure SQL Database

Serwer bazy danych SQL jest konstrukcją logiczną, która działa jako centralny punkt administracyjny dla wielu pojedynczego lub [puli](sql-database-elastic-pool.md) baz danych, [logowania](sql-database-manage-logins.md), [reguły zapory](sql-database-firewall-configure.md), [zasady inspekcji](sql-database-auditing.md), [zasady wykrywania zagrożeń](sql-database-threat-detection.md), i [grupy trybu failover](sql-database-auto-failover-group.md) bazy danych programu SQL server może być w innym regionie niż jego grupie zasobów. Serwer bazy danych SQL musi istnieć przed utworzeniem bazy danych Azure SQL. Wszystkie bazy danych, zarządza serwer bazy danych SQL są tworzone w tym samym regionie co serwer bazy danych SQL.

Serwer bazy danych SQL różni się od wystąpienia programu SQL Server, który można zapoznać się z w środowisku lokalnym. W szczególności usługa SQL Database nie udziela żadnych gwarancji dotyczących lokalizacji baz danych w odniesieniu do serwera bazy danych SQL, który zarządza nimi, a ujawnia nie dostęp na poziomie wystąpienia lub funkcji. Z kolei wystąpienia bazy danych w wystąpieniu zarządzanym są wszystkie wspólnie — w ten sam sposób, że jesteś zaznajomiony z programem SQL Server w środowisku lokalnym.

Podczas tworzenia serwera usługi SQL Database, podaj serwer konto logowania i hasło, które ma prawa administracyjne do głównej bazy danych na tym serwerze i wszystkimi bazami danych, utworzony na tym serwerze. To konto początkowej jest konto logowania SQL. Usługa Azure SQL Database obsługuje uwierzytelnianie SQL i uwierzytelniania usługi Azure Active Directory do uwierzytelniania. Aby uzyskać informacji na temat nazw logowania i uwierzytelniania, zobacz [Zarządzanie bazami danych i nazwami logowania w usłudze Azure SQL Database](sql-database-manage-logins.md). Uwierzytelnianie systemu Windows nie jest obsługiwane.

Serwer bazy danych SQL:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Zapewnia przestrzeń nazw dla baz danych, pul elastycznych i magazynów danych
- Jest kontenerem logicznym z silną semantyką okresu istnienia — usuwanie serwera, a następnie usuwane zawartych baz danych, pul elastycznych i magazynów danych
- Uczestniczy w [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](/azure/role-based-access-control/overview) -baz danych, pul elastycznych i magazynów danych na serwerze dziedziczą uprawnienia dostępu z serwera
- Jest elementem wyższego rzędu tożsamości baz danych, pul elastycznych i magazynów danych dla zasobów platformy Azure do celów zarządzania (Zobacz Schemat adresu URL dla baz danych i pul)
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (`<serverName>`.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych
- Określa zakres dla zasad zarządzania, które są stosowane do jego baz danych — identyfikatory logowania, zapory, inspekcji, wykrywanie zagrożeń i takie
- Jest ograniczony przez limit przydziału w ramach subskrypcji nadrzędnej (sześć serwerów na subskrypcję domyślnie - [Zobacz tutaj limity subskrypcji](../azure-subscription-service-limits.md))
- Określa zakres dla limitu przydziału bazy danych i limit przydziału jednostek DTU i rdzeniach wirtualnych dla zasobów, które zawiera (np. 45 000 jednostek DTU)
- Zakres przechowywania wersji dla możliwości włączonych w zawartych zasobach
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać logowania podobne do tych w lokalnych wystąpieniach programu SQL Server, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze, i może otrzymać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](sql-database-manage-logins.md).
- Sortowanie domyślne dla wszystkich baz danych utworzonych na serwerze bazy danych SQL jest `SQL_LATIN1_GENERAL_CP1_CI_AS`, gdzie `LATIN1_GENERAL` oznacza język angielski (Stany Zjednoczone) `CP1` oznacza stronę kodową 1252, `CI` jest rozróżniana wielkość liter, i `AS` jest akcentów.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzanie serwerami usługi Azure SQL, bazy danych i zapory za pomocą witryny Azure portal

Można utworzyć grupę zasobów Azure SQL database wcześniej lub podczas tworzenia serwera. Istnieje wiele metod uzyskiwania nowy formularz serwera SQL, tworząc nowy serwer SQL lub jako część tworzenia nowej bazy danych.

### <a name="create-a-blank-sql-database-server"></a>Utwórz pusty serwer bazy danych SQL

Do utworzenia serwera (bez bazy danych) usługi Azure SQL Database przy użyciu [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza programu SQL server (serwer logiczny).  

### <a name="create-a-blank-or-sample-sql-database"></a>Tworzenie pustego lub przykładowy bazy danych SQL

Aby utworzyć bazę danych Azure SQL za pomocą [witryny Azure portal](https://portal.azure.com), przejdź do pustego formularza SQL Database i podaj wymagane informacje. Można utworzyć grupę zasobów i serwer usługi SQL Database wcześniej lub podczas tworzenia bazy danych Azure SQL database. Możesz utworzyć pustą bazę danych lub tworzenie przykładowej bazy danych oparte na Adventure Works LT.

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Więcej informacji o dobieraniu warstwy cenowej bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Zarządzanie istniejącego serwera SQL

Aby zarządzać istniejącego serwera, przejdź do serwera przy użyciu szeregu metod — np. informacji od określonej strony bazy danych SQL, **serwerów SQL** strony, lub **wszystkie zasoby** strony.

Aby zarządzać istniejącej bazy danych, przejdź do **baz danych SQL** strony, a następnie kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć, ustawienia zapory na poziomie serwera, bazy danych, z **Przegląd** stron dla bazy danych.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
> [!TIP]
> Dla portalu szybkiego startu platformy Azure, zobacz [utworzyć bazę danych Azure SQL database w witrynie Azure portal](sql-database-single-database-get-started.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Zarządzanie serwerami Azure SQL, bazy danych i zapory przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć i zarządzać nimi, Azure SQL server, baz danych i zapory za pomocą programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Tworzenie i zarządzanie elastycznymi pulami, zobacz [pul elastycznych](sql-database-elastic-pool.md).

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

> [!TIP]
> Dla programu PowerShell przewodnika Szybki Start zobacz [Utwórz bazę pojedynczego Azure SQL przy użyciu programu PowerShell](sql-database-single-database-get-started.md). W przypadku skryptów przykład programu PowerShell, zobacz [Użyj programu PowerShell do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md) i [Monitor i skala usługi Azure SQL pojedyncze bazy danych przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzanie serwerami usługi Azure SQL, bazy danych i zapory, korzystając z wiersza polecenia platformy Azure

Tworzenie i zarządzanie nimi, Azure SQL server, baz danych i zapory za pomocą [wiersza polecenia platformy Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Tworzenie i zarządzanie elastycznymi pulami, zobacz [pul elastycznych](sql-database-elastic-pool.md).

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

> [!TIP]
> W przewodniku Szybki Start wiersza polecenia platformy Azure, zobacz [utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure SQL database pojedynczego](sql-database-cli-samples.md). W przypadku skryptów przykład wiersza polecenia platformy Azure, zobacz [użycia interfejsu wiersza polecenia do tworzenia pojedynczej bazy danych Azure SQL i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-cli.md) i [użycia interfejsu wiersza polecenia do monitorowania i skalowania pojedynczej bazy danych Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzanie serwerami Azure SQL, bazy danych i zapory, korzystając z instrukcji Transact-SQL

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapory za pomocą instrukcji języka Transact-SQL, użyj następujących poleceń języka T-SQL. Tych poleceń przy użyciu portalu Azure można wydać [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [programu Visual Studio Code](https://code.visualstudio.com/docs), lub innego programu, który może połączyć się z serwerem usługi Azure SQL Database i przekazać języka Transact-SQL polecenia. Aby zarządzać pul elastycznych, zobacz [pul elastycznych](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu języka Transact-SQL.
>

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [Instrukcja ALTER DATABASE (baza danych SQL platformy Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje usługi Azure SQL database. |
|[Instrukcja ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modyfikuje usługi Azure SQL Data Warehouse.|
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

> [!TIP]
> Aby uzyskać szybki start za pomocą programu SQL Server Management Studio na Microsoft Windows, zobacz [usługi Azure SQL Database: Użyj programu SQL Server Management Studio do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-ssms.md). Aby uzyskać szybki start w systemie macOS, Linux lub Windows przy użyciu programu Visual Studio Code, zobacz [usługi Azure SQL Database: Użyj programu Visual Studio Code do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzanie serwerami, bazy danych i zapory za pomocą interfejsu API REST usługi Azure SQL

Tworzenie i zarządzanie nimi w usłudze Azure SQL server, baz danych i zapór, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowego serwera.|
|[Serwery — usuwanie](https://docs.microsoft.com/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|Pobiera serwera.|
|[Serwery — lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Zwraca listę serwerów.|
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
