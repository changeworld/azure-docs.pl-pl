---
title: Tworzenie i zarządzanie serwerami Azure SQL & bazy danych | Dokumentacja firmy Microsoft
description: Informacje o serwerze bazy danych SQL Azure i pojęcia dotyczące bazy danych i dotyczących tworzenia i zarządzania serwerami i bazami danych.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/10/2018
ms.author: carlrab
ms.openlocfilehash: 3ffae541020a2672affab774ee6da2a8c707745f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-azure-sql-database-servers-and-databases"></a>Utwórz i Zarządzaj serwerami bazy danych SQL Azure i baz danych

Baza danych SQL oferuje trzy typy baz danych:

- Pojedyncza baza danych utworzona w ciągu [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) z [łączyć zestaw zasobów obliczeniowych i przestrzeni dyskowej](sql-database-service-tiers-dtu.md) lub [niezależnie od skalowania zasobów obliczeniowych i przestrzeni dyskowej](sql-database-service-tiers-vcore.md). Baza danych Azure SQL jest skojarzony z serwera logicznego bazy danych SQL Azure, który jest tworzony w określonym regionie Azure.
- Bazy danych utworzonej w ramach [puli baz danych](sql-database-elastic-pool.md) w [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) z [łączyć zestaw zasobów obliczeniowych i magazynu (w oparciu o jednostek dtu w warstwie)](sql-database-service-tiers-dtu.md) lub [niezależnie od skalowania zasobów obliczeniowych i magazynu (w oparciu o vCore)](sql-database-service-tiers-vcore.md) współdzielenia wszystkie bazy danych w puli. Baza danych Azure SQL jest skojarzony z serwera logicznego bazy danych SQL Azure, który jest tworzony w określonym regionie Azure.
- [Wystąpienia programu SQL server](sql-database-managed-instance.md) (zarządzane) tworzone wystąpienie w ramach [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) ze zdefiniowanym zestawem zasobów obliczeniowych i magazynu dla wszystkich baz danych w tym wystąpieniu serwera. Zarządzane wystąpienie zawiera systemowych i użytkownika bazy danych. Zarządzane wystąpienia zaprojektowana w celu umożliwienia bazy danych przyrostu i shift do w pełni zarządzane rozwiązanie typu PaaS, bez zmiany projektu aplikacji. Zarządzane wystąpienia zapewnia wysoką zgodność z modelem programowania lokalnego programu SQL Server i obsługuje dużych większość funkcji programu SQL Server i towarzyszące narzędzi i usług.  

Baza danych SQL Azure Microsoft obsługuje danych tabelarycznych (TDS) protokół klienta wersja strumienia 7.3 lub nowszym i zezwala na tylko zaszyfrowanego połączenia TCP/IP.

> [!IMPORTANT]
> Wystąpienia zarządzane bazy danych SQL w publicznej wersji zapoznawczej, oferuje pojedynczej warstwie usługi ogólnego przeznaczenia. Aby uzyskać więcej informacji, zobacz [Wystąpienie zarządzane usługi SQL Database](sql-database-managed-instance.md). W dalszej części tego artykułu, nie ma zastosowania do zarządzanego wystąpienia.

## <a name="what-is-an-azure-sql-logical-server"></a>Co to jest serwer logiczny Azure SQL?

Serwer logiczny działa jako centralny punkt administracyjny wielu jednym lub [puli](sql-database-elastic-pool.md) baz danych, [logowania](sql-database-manage-logins.md), [reguły zapory](sql-database-firewall-configure.md), [zasadyinspekcji](sql-database-auditing.md), [zasady wykrywania zagrożeń](sql-database-threat-detection.md), i [trybu failover grupy](sql-database-geo-replication-overview.md). W regionie innym niż jego grupa zasobów może być serwerem logicznym. Serwer logiczny musi istnieć przed przystąpieniem do tworzenia bazy danych Azure SQL. Wszystkie bazy danych na serwerze są tworzone w tym samym regionie co serwer logiczny.

Serwer logiczny jest konstrukcją logiczną, która różni się od wystąpienia programu SQL Server, które można zapoznać się z na świecie lokalnych. W szczególności usługa SQL Database nie udziela żadnych gwarancji dotyczących lokalizacji baz danych w odniesieniu do ich serwerów logicznych i nie uwidacznia dostępu ani żadnych funkcji na poziomie wystąpienia. Z kolei server w wystąpieniu zarządzane bazy danych SQL jest podobny do wystąpienia programu SQL Server, które można zapoznać się z na świecie lokalnych.

Tworzenie serwera logicznego, należy podać serwer, konto logowania i hasło, które ma prawa administracyjne do głównej bazy danych na tym serwerze i wszystkich baz danych utworzonych na tym serwerze. To konto początkowej jest konto logowania SQL. Baza danych SQL Azure obsługuje uwierzytelnianie SQL i uwierzytelniania usługi Azure Active Directory do uwierzytelniania. Aby uzyskać informacje dotyczące logowania i uwierzytelniania, zobacz [Zarządzanie bazami danych i Logowaniami w bazie danych SQL Azure](sql-database-manage-logins.md). Uwierzytelnianie systemu Windows nie jest obsługiwane. 

> [!TIP]
> Aby uzyskać prawidłowy zasób grupy i nazw serwerów, zobacz [nazewnictwa reguły i ograniczenia](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).
>

Serwer logiczny bazy danych Azure:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Obejmuje przestrzeń nazw dla bazy danych, pule elastyczne i magazyny danych
- Jest kontenerem logicznym z semantyki silne istnienia — usuwanie serwera i usuwa zawartych baz danych, pule elastyczne i magazyny danych
- Uczestniczy w [kontroli dostępu opartej na rolach na platformie Azure (RBAC)](/azure/role-based-access-control/overview) -baz danych, pule elastyczne i hurtowni danych w ramach serwera dziedziczą prawa dostępu z serwera
- Jest elementem znaczących tożsamości baz danych, pule elastyczne i magazynów danych dla zasobów platformy Azure (Zobacz Schemat adresu URL dla baz danych i pul) do celów zarządzania
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (<serverName>.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych 
- Określa zakres dla zasad zarządzania, które dotyczą bazy danych — logowania, zapory, inspekcji, zagrożenia wykrywania itp. 
- Jest ograniczony przez przydział w ramach subskrypcji nadrzędnej (sześciu serwerów na subskrypcję domyślnie - [Zobacz subskrypcji w tym miejscu ogranicza](../azure-subscription-service-limits.md))
- Zapewnia zakres limit przydziału bazy danych i limit przydziału jednostek dtu w warstwie lub vCore zasoby, które zawiera (na przykład DTU 45 000)
- Zakres przechowywania wersji dla możliwościami włączonymi na zasobach ograniczonego 
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać logowania podobne do tych w lokalnych wystąpieniach programu SQL Server, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze, i może otrzymać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](sql-database-manage-logins.md).
- Jest sortowanie domyślne dla wszystkich baz danych użytkowników utworzone na serwerze logicznym `SQL_LATIN1_GENERAL_CP1_CI_AS`, gdzie `LATIN1_GENERAL` jest angielski (Stany Zjednoczone), `CP1` jest strona kodowa 1252, `CI` nie uwzględnia wielkości liter, a `AS` jest akcentów.

## <a name="azure-sql-databases-protected-by-sql-database-firewall"></a>Chronione przez zaporę bazy danych SQL Azure bazy danych SQL

Aby ułatwić ochronę danych, [zapory bazy danych SQL](sql-database-firewall-configure.md) uniemożliwia dostęp do serwera bazy danych lub dowolny z jej baz danych z poza połączenie z serwerem bezpośrednio za pośrednictwem połączenia subskrypcji platformy Azure. Aby włączyć dodatkowe łączności, należy najpierw [utworzyć co najmniej jedną regułę zapory](sql-database-firewall-configure.md#creating-and-managing-firewall-rules). Do tworzenia i zarządzania pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzaj serwerami oraz baz danych Azure SQL a zapory przy użyciu portalu Azure

Można utworzyć grupy zasobów bazy danych Azure SQL wcześniejsze lub podczas tworzenia serwera. Istnieje wiele metod pobierania do nowego formularza serwera SQL, tworząc nowy serwer SQL lub podczas tworzenia nowej bazy danych. 

### <a name="create-a-blank-sql-server-logical-server"></a>Utwórz puste SQL server (serwer logiczny)

Do utworzenia serwera (bez bazy danych) bazy danych SQL Azure przy użyciu [portalu Azure](https://portal.azure.com), przejdź do pustego formularza programu SQL server (serwer logiczny).  

### <a name="create-a-blank-or-sample-sql-database"></a>Utwórz puste lub przykładowa baza danych SQL

Do utworzenia bazy danych Azure SQL przy użyciu [portalu Azure](https://portal.azure.com), przejdź do pusty formularz bazy danych SQL i podaj żądane informacje. Można utworzyć grupy zasobów i serwer logiczny wcześniejsze lub podczas tworzenia bazy danych bazy danych Azure SQL. Można utworzyć pustą bazę danych lub utworzyć oparty na Adventure Works LT. przykładowej bazy danych 

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje dotyczące wybierania warstwy cenowej bazy danych, zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzanych, zobacz [utworzyć wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md)

### <a name="manage-an-existing-sql-server"></a>Zarządzanie istniejącego serwera SQL

Aby zarządzać istniejącego serwera, przejdź do serwera przy użyciu metod — przykład wyświetlanie określonej strony bazy danych SQL, **serwerów SQL** strony, lub **wszystkie zasoby** strony. 

Do zarządzania istniejącą bazę danych, przejdź do **baz danych SQL** i kliknij przycisk bazy danych chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć, ustawienia zapory poziomu serwera bazy danych z **omówienie** stron dla bazy danych. 

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md).
>

> [!TIP]
> Dla platformy Azure Szybki Start portalu, zobacz [tworzenie bazy danych Azure SQL w portalu Azure](sql-database-get-started-portal.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Zarządzaj serwerami oraz baz danych Azure SQL a zapory przy użyciu programu PowerShell

Tworzenie i zarządzanie nimi Azure SQL server, baz danych i zapory przy użyciu programu Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). Do tworzenia i zarządzania pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Tworzy bazę danych |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Pobiera jeden lub więcej baz danych|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Ustawia właściwości dla bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Usuwa z bazy danych|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Tworzy grupę zasobów]
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Tworzy serwer|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Zwraca informacje na temat serwerów|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modyfikuje właściwości serwera|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Usuwa serwer|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Tworzy regułę zapory poziomu serwera |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Pobiera reguły zapory serwera|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| New-AzureRmSqlServerVirtualNetworkRule | Tworzy [ *reguły sieci wirtualnej*](sql-database-vnet-service-endpoint-rule-overview.md), na podstawie podsieci, która jest punkt końcowy usługi sieci wirtualnej. |

> [!TIP]
> PowerShell — Szybki Start dla [tworzenia pojedynczej bazy danych Azure SQL przy użyciu programu PowerShell](sql-database-get-started-portal.md). Dla programu PowerShell przykładowe skrypty, zobacz [Użyj programu PowerShell do tworzenia pojedynczej bazy danych Azure SQL i skonfigurować regułę zapory](scripts/sql-database-create-and-configure-database-powershell.md) i [monitora i skali pojedynczego SQL bazy danych przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzaj serwerami oraz baz danych Azure SQL a zapory przy użyciu wiersza polecenia platformy Azure

Tworzenie i zarządzanie nimi Azure SQL server, baz danych i zapory z [interfejsu wiersza polecenia Azure](/cli/azure), należy użyć następującego [bazy danych SQL interfejsu wiersza polecenia Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Do tworzenia i zarządzania pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Tworzy bazę danych|
|[Lista bazy danych sql az](/cli/azure/sql/db#az_sql_db_list)|Wyświetla listę wszystkich baz danych i magazynów danych na serwerze lub wszystkie bazy danych w puli elastycznej|
|[Lista wersje az bazy danych sql](/cli/azure/sql/db#az_sql_db_list_editions)|Wyświetla dostępne usługi cele i limity magazynu|
|[bazy danych sql az listy użycia](/cli/azure/sql/db#az_sql_db_list_usages)|Zwraca bazy danych użycia|
|[Pokaż bazy danych sql az](/cli/azure/sql/db#az_sql_db_show)|Pobiera Magazyn bazy danych lub danych|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Aktualizuje bazę danych|
|[Usuwanie bazy danych sql az](/cli/azure/sql/db#az_sql_db_delete)|Usuwa z bazy danych|
|[az group create](/cli/azure/group#az_group_create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Tworzy serwer|
|[Lista serwerów sql az](/cli/azure/sql/server#az_sql_server_list)|Wyświetla serwery|
|[Serwer sql az listy użycia](/cli/azure/sql/server#az_sql_server_list_usages)|Zwraca użycia serwera|
|[Pokaż serwera sql az](/cli/azure/sql/server#az_sql_server_show)|Pobiera serwera|
|[Aktualizacja programu sql server az](/cli/azure/sql/server#az_sql_server_update)|Serwer aktualizacji|
|[Usuń serwer sql az](/cli/azure/sql/server#az_sql_server_delete)|Usuwa serwer|
|[Utwórz az programu sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Powoduje utworzenie reguły zapory serwera|
|[Lista reguł zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory programu sql server az](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizuje regułę zapory|
|[Usuwanie reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Usuwa regułę zapory|

> [!TIP]
> Aby uzyskać szybki start wiersza polecenia platformy Azure, zobacz [tworzenia pojedynczej bazy danych Azure SQL przy użyciu interfejsu wiersza polecenia Azure](sql-database-get-started-cli.md). Dla interfejsu wiersza polecenia Azure przykładowe skrypty, zobacz [Użyj interfejsu wiersza polecenia do tworzenia pojedynczej bazy danych Azure SQL i skonfigurować regułę zapory](scripts/sql-database-create-and-configure-database-cli.md) i [Użyj interfejsu wiersza polecenia, aby monitorować i skalowania pojedynczej bazy danych SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzaj serwerami oraz baz danych Azure SQL a zapory przy użyciu języka Transact-SQL

Tworzenie i zarządzanie nimi Azure SQL server, baz danych i zapór z Transact-SQL, użyj następujących poleceń T-SQL. Te polecenia przy użyciu portalu Azure może wystawiać [programu SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), lub inny program, który może nawiązywać połączenia z serwerem bazy danych SQL Azure i przekazać języka Transact-SQL polecenia. Do zarządzania pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nie można utworzyć ani usunąć z serwerem przy użyciu języka Transact-SQL.
>

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (baza danych Azure SQL)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje bazy danych Azure SQL. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modyfikuje magazyn danych Azure SQL.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa z bazy danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany do głównej bazy danych na serwerze bazy danych SQL Azure, zwraca informacje o wszystkich baz danych. Dla usługi Azure SQL Data Warehouse musi być podłączony do bazy danych master.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca zużycie procesora CPU, we/wy i pamięci dla bazy danych z bazy danych SQL Azure. Dla co 15 s istnieje jeden wiersz, nawet jeśli nic się nie w bazie danych.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca procesora CPU, użycia i magazynu danych dla bazy danych SQL Azure. Dane są zbierane i agregowane w ciągu 5 minut.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera dane statystyczne dla zdarzenia łączności bazy danych SQL Database przedstawiające przegląd bazy danych połączenia sukcesy i niepowodzenia. |
|[sys.event_log (baza danych SQL Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślnego połączenia z bazą danych usługi Azure SQL Database, błędy połączeń i zakleszczenia. Te informacje można użyć do śledzenia i rozwiązywanie problemów z działania bazy danych z bazy danych SQL.|
|[procedurę składowaną sp_set_firewall_rule (baza danych SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory poziomu serwera dla serwera bazy danych SQL. Ta procedura składowana jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera. Reguły zapory poziomu serwera można tworzyć tylko za pomocą języka Transact-SQL, po utworzeniu pierwszej reguły zapory poziomu serwera przez użytkownika z uprawnienia na poziomie Azure|
|[sys.firewall_rules (baza danych SQL Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje na temat ustawień zapory na poziomie serwera, skojarzonych z bazy danych SQL Microsoft Azure.|
|[sp_delete_firewall_rule (baza danych SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory poziomu serwera z serwerem bazy danych SQL. Ta procedura składowana jest dostępna tylko w bazie danych master do głównego identyfikatora logowania poziomu serwera.|
|[sp_set_database_firewall_rule (baza danych SQL Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory poziomu bazy danych dla bazy danych SQL Azure lub usługi SQL Data Warehouse. Można skonfigurować reguł zapory bazy danych dla bazy danych master i baz danych użytkowników w bazie danych SQL. Reguły zapory bazy danych są przydatne, gdy przy użyciu zawarte bazy danych użytkowników. |
|[sys.database_firewall_rules (baza danych SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje na temat ustawień zapory na poziomie bazy danych, skojarzonych z bazy danych SQL Microsoft Azure. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Ustawienia zapory na poziomie bazy danych usuwa z bazy danych SQL Azure lub SQL Data Warehouse. |


> [!TIP]
> Aby uzyskać szybki start Microsoft Windows za pomocą programu SQL Server Management Studio, zobacz [bazy danych SQL Azure: Użyj SQL Server Management Studio do nawiązywania połączeń i zapytania na danych](sql-database-connect-query-ssms.md). Aby uzyskać szybki start za pomocą programu Visual Studio Code na macOS, Linux lub Windows, zobacz [bazy danych SQL Azure: Użyj Visual Studio Code do nawiązywania połączeń i zapytania na danych](sql-database-connect-query-vscode.md).

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzaj serwerami oraz baz danych Azure SQL a zapory przy użyciu interfejsu API REST

Tworzenie i zarządzanie nimi Azure SQL server, baz danych i zapór, użyj te żądania interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Tworzenie lub aktualizowanie](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowego serwera.|
|[Serwery — Delete](/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery — Get](/rest/api/sql/servers/get)|Pobiera serwera.|
|[Serwery — listy](/rest/api/sql/servers/list)|Zwraca listę serwerów.|
|[Serwery — Lista według grupy zasobów](/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych - Get](/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — uzyskać za pomocą puli elastycznej](/rest/api/sql/databases/getbyelasticpool)|Pobiera bazy danych w puli elastycznej.|
|[Bazy danych — uzyskać za pomocą zalecana liczba elastycznych pul](/rest/api/sql/databases/getbyrecommendedelasticpool)|Pobiera bazy danych wewnątrz recommented puli elastycznej.|
|[Bazy danych — lista według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według zalecana liczba elastycznych pul](/rest/api/sql/databases/listbyrecommendedelasticpool)|Zwraca listę baz danych wewnątrz zalecanej puli elastycznej.|
|[Bazy danych — lista przez serwer](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Zapory reguł — Tworzenie lub aktualizowanie](/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje regułę zapory.|
|[Reguły zapory - Delete](/rest/api/sql/firewallrules/delete)|Usuwa regułę zapory.|
|[Reguły zapory - Get](/rest/api/sql/firewallrules/get)|Pobiera reguły zapory.|
|[Reguły zapory - listy przez serwer](/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Kolejne kroki

- Informacje na temat migracji bazy danych programu SQL Server na platformie Azure, zobacz [migracji do usługi Azure SQL Database](sql-database-cloud-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
