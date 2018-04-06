---
title: Azure pojedynczej bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Zarządzanie warstwy usług, poziom wydajności i ilość storagea dla pojedynczej bazy danych Azure SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 87cd4c565a7429ffb7096034823a1f5ff49df195
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Zarządzanie zasobami dla pojedynczej bazy danych w bazie danych SQL Azure

Z jednej bazy danych należy określić ilość zasobów wymaganych do obsługi jego obciążenia warstwy usług, poziom wydajności i pojemności magazynu, które wymaga bazy danych. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Zarządzanie zasobami pojedynczej bazy danych przy użyciu portalu Azure

Aby ustawić lub zmienić warstwę usługi, poziom wydajności lub wielkość magazynu do nowego lub istniejącego Azure bazy danych SQL przy użyciu portalu Azure, otwórz **skonfigurować wydajności** okna bazy danych, klikając **(warstwy cenowej Skalowanie Dtu)** — jak pokazano na poniższym zrzucie ekranu. 

- Ustawianie lub zmienianie warstwy usług, wybierając warstwy usługi dla obciążenia. 
- Ustaw lub zmień poziom wydajności (**Dtu**) w warstwie usługi za pomocą **jednostek dtu w warstwie** suwaka.
- Ustaw lub zmień wielkość pamięci masowej dla poziomu wydajności za pomocą **magazynu** suwaka. 

![Konfigurowanie usługi warstwę i poziom wydajności](./media/sql-database-single-database-resources/change-service-tier.png)

Kliknij przycisk **omówienie** do monitorowania i/lub Anuluj trwającą operację.

![Anuluj operację](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Przegląd [bieżące ograniczenia P11 i P15 z replikacją baz danych, maksymalny rozmiar 4 TB](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb) podczas wybierania P11 lub P15 warstwy usług.
>

## <a name="manage-single-database-resources-using-powershell"></a>Zarządzanie zasobami pojedynczej bazy danych przy użyciu programu PowerShell

Ustawianie lub zmienianie bazy danych Azure SQL warstwy usług, poziomów wydajności i wielkość magazynu przy użyciu programu PowerShell, należy używać tych poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić programu PowerShell, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Tworzy bazę danych |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Pobiera jeden lub więcej baz danych|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Ustawia właściwości dla bazy danych lub przenosi istniejącą bazę danych do puli elastycznej. Na przykład użyć **MaxSizeBytes** właściwości można ustawić maksymalny rozmiar bazy danych.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Pobiera stan operacji w bazie danych. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Anuluje operację asynchroniczną aktualizacji w bazie danych.|


> [!TIP]
> Aby uzyskać przykładowy skrypt programu PowerShell, który monitoruje metryki wydajności bazy danych, skaluje go na wyższy poziom wydajności i tworzy regułę alertu w jednym z metryki wydajności, zobacz [monitora i skali pojedynczego SQL bazy danych przy użyciu programu PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Zarządzanie zasobami pojedynczej bazy danych przy użyciu wiersza polecenia platformy Azure

Ustawianie lub zmienianie bazy danych Azure SQL warstwy usług, poziomów wydajności i wielkość magazynu przy użyciu wiersza polecenia platformy Azure, użyj tych [bazy danych SQL interfejsu wiersza polecenia Azure](/cli/azure/sql/db) poleceń. Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Do tworzenia i zarządzania pule elastyczne SQL, zobacz [pule elastyczne](sql-database-elastic-pool.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[Utwórz az programu sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Powoduje utworzenie reguły zapory serwera|
|[Lista reguł zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory programu sql server az](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizuje regułę zapory|
|[Usuwanie reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Usuwa regułę zapory|
|[Lista op bazy danych sql az](/cli/azure/sql/db/op?#az_sql_db_op_list)|Pobiera listę operacji wykonywanych na bazie danych.|
|[Anuluj op bazy danych sql az](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Anuluje operację asynchroniczną na bazie danych.|

> [!TIP]
> Aby uzyskać skrypt wiersza polecenia platformy Azure, która może obsłużyć pojedynczej bazy danych Azure SQL do poziomu wydajności różnych po podczas badania informacji rozmiar bazy danych, zobacz [Użyj interfejsu wiersza polecenia, aby monitorować i skalowania pojedynczej bazy danych SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Zarządzanie zasobami pojedynczej bazy danych przy użyciu języka Transact-SQL

Ustawianie lub zmienianie bazy danych Azure SQL warstwy usług, poziomów wydajności i ilości pamięci masowej do języka Transact-SQL, należy używać tych poleceń T-SQL. Te polecenia przy użyciu portalu Azure może wystawiać [programu SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), lub inny program, który może nawiązywać połączenia z serwerem bazy danych SQL Azure i przekazać języka Transact-SQL polecenia. 

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (baza danych Azure SQL)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych. Musisz mieć połączenie z główną bazą danych, aby utworzyć nową bazę danych.|
| [ALTER DATABASE (baza danych Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modyfikuje bazy danych Azure SQL. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edition (warstwy usług), celem usługi (warstwa cenowa) i nazwę puli elastycznej dla bazy danych Azure SQL lub usługi Azure SQL Data Warehouse. Jeśli zalogowany do głównej bazy danych na serwerze bazy danych SQL Azure, zwraca informacje o wszystkich baz danych. Dla usługi Azure SQL Data Warehouse musi być podłączony do bazy danych master.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Wyświetla liczbę, typ i czas trwania baz danych na serwerze bazy danych SQL Azure.|

W poniższym przykładzie przedstawiono maksymalnego rozmiaru bazy danych trwa zmienić za pomocą polecenia ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Zarządzanie zasobami pojedynczej bazy danych przy użyciu interfejsu API REST

Ustawianie lub zmienianie bazy danych Azure SQL warstwy usług, poziomów wydajności i wielkość magazynu Użyj te żądania interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Bazy danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych - Get](/rest/api/sql/databases/get)|Pobiera bazy danych.|
|[Bazy danych — uzyskać za pomocą puli elastycznej](/rest/api/sql/databases/getbyelasticpool)|Pobiera bazy danych w puli elastycznej.|
|[Bazy danych — uzyskać za pomocą zalecana liczba elastycznych pul](/rest/api/sql/databases/getbyrecommendedelasticpool)|Pobiera bazy danych wewnątrz recommented puli elastycznej.|
|[Bazy danych — lista według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według zalecana liczba elastycznych pul](/rest/api/sql/databases/listbyrecommendedelasticpool)|Zwraca listę baz danych wewnątrz recommented puli elastycznej.|
|[Bazy danych — lista przez serwer](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Operacje — listy](/rest/api/sql/Operations/List)|Wyświetla listę wszystkich dostępnych operacji interfejsu API Rest SQL.|



## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat warstwy usług, poziomów wydajności i ilości pamięci masowej, zobacz [warstw usług](sql-database-service-tiers.md).
- Dowiedz się więcej o pule elastyczne, zobacz [pule elastyczne](sql-database-elastic-pool.md).
- Dowiedz się więcej o [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](../azure-subscription-service-limits.md)
