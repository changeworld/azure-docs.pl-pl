---
title: Serwery
description: Dowiedz się więcej o serwerach bazy danych SQL platformy Azure i ich zarządzaniu.
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
ms.openlocfilehash: 7557ed43d9ecb8fc7a584e7e8239bc7ccb972e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647173"
---
# <a name="azure-sql-database-servers-and-their-management"></a>Serwery usługi Azure SQL Database i ich zarządzanie

## <a name="what-is-an-azure-sql-database-server"></a>Co to jest serwer bazy danych SQL platformy Azure

Serwer bazy danych SQL jest konstrukcją logiczną, która działa jako centralny punkt administracyjny dla wielu pojedynczych lub [branych](sql-database-elastic-pool.md) baz danych, [logowania,](sql-database-manage-logins.md) [reguł zapory,](sql-database-firewall-configure.md) [reguł inspekcji,](sql-database-auditing.md) [zasad wykrywania zagrożeń](sql-database-threat-detection.md)i grup trybu [failover](sql-database-auto-failover-group.md) Serwer bazy danych SQL może znajdować się w innym regionie niż jego grupa zasobów. Serwer bazy danych SQL musi istnieć przed utworzeniem bazy danych SQL platformy Azure. Wszystkie bazy danych zarządzane przez serwer bazy danych SQL są tworzone w tym samym regionie co serwer bazy danych SQL.

Serwer bazy danych SQL różni się od wystąpienia programu SQL Server, które może być znane w środowisku lokalnym. W szczególności usługa bazy danych SQL nie daje żadnych gwarancji dotyczących lokalizacji baz danych w odniesieniu do serwera bazy danych SQL, który nimi zarządza i udostępnia nie dostęp na poziomie wystąpienia lub funkcje. Z drugiej strony bazy danych wystąpień w wystąpieniu zarządzanym są zlokalizowane wspólnie — w taki sam sposób, w jaki znasz program SQL Server w środowisku lokalnym.

Podczas tworzenia serwera bazy danych SQL należy podać konto logowania serwera i hasło, które ma prawa administracyjne do głównej bazy danych na tym serwerze i wszystkich baz danych utworzonych na tym serwerze. To konto początkowe jest kontem logowania SQL. Usługa Azure SQL Database obsługuje uwierzytelnianie SQL i uwierzytelnianie usługi Azure Active Directory w celu uwierzytelniania. Aby uzyskać informacje na temat logowania i uwierzytelniania, zobacz [Zarządzanie bazami danych i logowaniami w bazie danych SQL Azure](sql-database-manage-logins.md). Uwierzytelnianie systemu Windows nie jest obsługiwane.

Serwer bazy danych SQL:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Udostępnia obszar nazw dla baz danych, pul elastycznych i magazynów danych
- Jest kontenerem logicznym z silną semantyką okresu istnienia — usuwa serwer i usuwa zawarte bazy danych, pule elastyczne i magazyny danych
- Uczestniczy w [kontroli dostępu opartej na rolach platformy Azure (RBAC)](/azure/role-based-access-control/overview) — bazy danych, pule elastyczne i magazyny danych na serwerze dziedziczą prawa dostępu z serwera
- Jest elementem wysokiego rzędu tożsamości baz danych, pul elastycznych i magazynów danych do celów zarządzania zasobami platformy Azure (zobacz schemat adresów URL dla baz danych i pul)
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (`<serverName>`.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych
- Udostępnia zakres zasad zarządzania, które mają zastosowanie do jego baz danych - loginy, zapora, inspekcja, wykrywanie zagrożeń i takie
- Jest ograniczona przez przydział w ramach subskrypcji nadrzędnej (domyślnie sześć serwerów na subskrypcję — [zobacz Limity subskrypcji tutaj)](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Udostępnia zakres przydziału bazy danych i przydziału DTU lub vCore dla zasobów, które zawiera (takich jak 45 000 identyfikatorów DTU)
- Czy zakres przechowywania wersji dla możliwości włączonych w zamkniętych zasobach
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać logowania podobne do tych w lokalnych wystąpieniach programu SQL Server, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze, i może otrzymać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](sql-database-manage-logins.md).
- Domyślne sortowanie dla wszystkich baz danych utworzonych na serwerze bazy danych SQL jest `SQL_LATIN1_GENERAL_CP1_CI_AS`, gdzie `LATIN1_GENERAL` jest angielski (Stany `CP1` Zjednoczone), jest strona kodowa 1252, `CI` jest bez uwzględniania wielkości liter i `AS` jest akcentem.

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzanie serwerami, bazami danych i zaporami SQL platformy Azure za pomocą witryny Azure portal

Można utworzyć grupę zasobów bazy danych SQL platformy Azure z wyprzedzeniem lub podczas tworzenia samego serwera. Istnieje wiele metod uzyskiwania do nowego formularza serwera SQL, przez utworzenie nowego serwera SQL lub w ramach tworzenia nowej bazy danych.

### <a name="create-a-blank-sql-database-server"></a>Tworzenie pustego serwera bazy danych SQL

Aby utworzyć serwer bazy danych SQL Azure (bez bazy danych) przy użyciu [portalu Azure,](https://portal.azure.com)przejdź do pustego formularza serwera SQL (serwera logicznego).  

### <a name="create-a-blank-or-sample-sql-database"></a>Tworzenie pustej lub przykładowej bazy danych SQL

Aby utworzyć bazę danych SQL platformy Azure przy użyciu [portalu Azure portal,](https://portal.azure.com)przejdź do pustego formularza bazy danych SQL i podaj żądane informacje. Można utworzyć grupę zasobów bazy danych SQL platformy Azure i serwer bazy danych SQL z wyprzedzeniem lub podczas tworzenia samej bazy danych. Można utworzyć pustą bazę danych lub utworzyć przykładową bazę danych na podstawie programu Adventure Works LT.

  ![tworzenie bazy danych 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej bazy danych, zobacz [model zakupów oparty na jednostkach DTU](sql-database-service-tiers-dtu.md) i model zakupu oparty na [łanie wirtualnej](sql-database-service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](sql-database-managed-instance-get-started.md)

### <a name="manage-an-existing-sql-server"></a>Zarządzanie istniejącym serwerem SQL

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu wielu metod — takich jak strona określonej bazy danych SQL, strona **serwerów SQL** lub strona **Wszystkie zasoby.**

Aby zarządzać istniejącą bazą danych, przejdź do strony **bazy danych SQL** i kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych ze strony **Przegląd** bazy danych.

   ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności bazy danych, zobacz [model zakupu oparty na UTU](sql-database-service-tiers-dtu.md) i model zakupu oparty na [racji wirtualnej](sql-database-service-tiers-vcore.md).
> [!TIP]
> Aby zapoznać się z przewodnikiem szybki start w portalu Azure, zobacz [Tworzenie bazy danych SQL platformy Azure w witrynie Azure.](sql-database-single-database-get-started.md)

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-powershell"></a>Zarządzanie serwerami, bazami danych i zapory SQL platformy Azure przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi za pomocą programu Azure PowerShell, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli chcesz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-az-ps). Aby utworzyć baseny elastyczne i zarządzać nimi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

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

> [!TIP]
> Aby uzyskać szybki start programu PowerShell, zobacz [Tworzenie pojedynczej bazy danych sql platformy Azure przy użyciu programu PowerShell](sql-database-single-database-get-started.md). W przypadku przykładowych skryptów programu PowerShell zobacz [Tworzenie pojedynczej bazy danych usługi Azure SQL za pomocą programu PowerShell oraz konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-powershell.md) i [monitora oraz skalowanie pojedynczej bazy danych usługi Azure SQL przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzanie serwerami, bazami danych i zaporami sql platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi za pomocą [interfejsu wiersza polecenia Platformy Azure,](/cli/azure)należy użyć następujących poleceń [bazy danych SQL platformy Azure.](/cli/azure/sql/db) Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby utworzyć baseny elastyczne i zarządzać nimi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

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

> [!TIP]
> Aby uzyskać szybki start interfejsu wiersza polecenia platformy Azure, zobacz [Tworzenie pojedynczej bazy danych sql platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](sql-database-cli-samples.md). W przypadku przykładowych skryptów interfejsu wiersza polecenia platformy Azure zobacz [Tworzenie pojedynczej bazy danych sql platformy Azure i konfigurowanie reguły zapory](scripts/sql-database-create-and-configure-database-cli.md) oraz [Monitorowanie i skalowanie pojedynczej bazy danych usługi Azure SQL](scripts/sql-database-monitor-and-scale-database-cli.md)za pomocą interfejsu wiersza polecenia.
>

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzanie serwerami, bazami danych i zaporami sql platformy Azure przy użyciu funkcji Transact-SQL

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi za pomocą funkcji Transact-SQL, należy użyć następujących poleceń T-SQL. Te polecenia można wydać za pomocą portalu Azure, [programu SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio)programu Visual Studio [Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może łączyć się z serwerem bazy danych SQL platformy Azure i przekazywać polecenia Transact-SQL. Aby zarządzać pulami elastycznymi, zobacz [Pule elastyczne](sql-database-elastic-pool.md).

> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu funkcji Transact-SQL.
>

| Polecenie | Opis |
| --- | --- |
|[TWORZENIE BAZY DANYCH (usługa Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Aby utworzyć nową bazę danych, musisz być połączony z główną bazą danych.|
| [ALTER DATABASE (usługa Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje bazę danych SQL platformy Azure. |
|[ALTER DATABASE (usługa Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modyfikuje magazyn danych SQL platformy Azure.|
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

> [!TIP]
> Aby uzyskać przewodnik Szybki start przy użyciu programu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Baza danych SQL Azure: Łączenie i wykonywanie zapytań](sql-database-connect-query-ssms.md)o dane za pomocą programu SQL Server Management Studio. Aby uzyskać przewodnik Szybki start przy użyciu programu Visual Studio Code w systemach macOS, Linux lub Windows, zobacz [Baza danych SQL Azure: Łączenie i wykonywanie zapytań o dane za pomocą programu Visual Studio.](sql-database-connect-query-vscode.md)

## <a name="manage-azure-sql-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzanie serwerami, bazami danych i zaporami SQL platformy Azure przy użyciu interfejsu API REST

Aby utworzyć serwer SQL, bazy danych i zapory platformy Azure SQL i zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](https://docs.microsoft.com/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery - Pobierz](https://docs.microsoft.com/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery - Lista](https://docs.microsoft.com/rest/api/sql/servers/list)|Zwraca listę serwerów.|
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
