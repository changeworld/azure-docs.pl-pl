---
title: Zarządzanie przestrzenią pliku w usłudze Azure bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu zarządzania przestrzenią plików za pomocą usługi Azure SQL Database i zawiera przykłady kodu dotyczące sposobu określenia, czy można zmniejszyć bazy danych również, jak przeprowadzić operację zmniejszania bazy danych.
services: sql-database
author: oslake
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: moslake
ms.openlocfilehash: 498e83e7c312480af6d2eff7d44bd13aee9c55fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055008"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Zarządzanie przestrzenią pliku w usłudze Azure SQL Database
W tym artykule opisano różne rodzaje miejsca do magazynowania w usłudze Azure SQL Database i czynności, które mogą być wykonywane, gdy przydzielone miejsce plików baz danych i pul elastycznych musi odbywać się jawnie.

## <a name="overview"></a>Przegląd

W usłudze Azure SQL Database większość metryk miejsce magazynowania wyświetlane w witrynie Azure portal oraz następujące interfejsy API pomiaru liczba stron używanych danych dla baz danych i pul elastycznych:
- Usługa Azure Resource Manager, na podstawie metryk interfejsów API w tym program PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Brak wzorców obciążenia gdzie alokacji podstawowych plików danych dla baz danych może stać się większy niż ilość danych używanych stron.  Taka sytuacja może wystąpić, gdy miejsce używane zwiększa i dane są usuwane.  Jest to spowodowane pliku miejsce przydzielone nie jest automatycznie odzyskane po usunięciu danych.  W takich scenariuszach przydzielonego miejsca dla bazy danych lub puli może przekracza obsługiwany limit i zapobiec wzrostu ilości danych lub uniemożliwić zmiany warstwy wydajności i wymagają, zmniejszając pliki danych, aby uniknąć.

Usługa SQL DB nie zmniejsza automatycznie plików danych, aby odzyskać nieużywane miejsce przydzielone z powodu potencjalnego wpływu na wydajność bazy danych.  Jednak klientów może spowodować zmniejszenie pliki danych za pomocą samoobsługowej w momencie ich wyboru, wykonując kroki opisane w [odzyskać nieużywane miejsca przydzielonego](#reclaim-unused-allocated-space). 

> [!NOTE]
> W przeciwieństwie do plików danych usługa SQL Database automatycznie zmniejsza pliki dziennika, ponieważ tej operacji nie ma wpływu na wydajność bazy danych. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Opis typów miejsca do magazynowania dla bazy danych

Informacje o następujących ilości miejsca magazynu są ważne w przypadku zarządzanie przestrzenią pliku bazy danych.

|Ilość bazy danych|Definicja|Komentarze|
|---|---|---|
|**Dane miejsca**|Ilość miejsca, używany do przechowywania danych bazy danych na stronach o rozmiarze 8 KB.|Ogólnie rzecz biorąc miejsce używane zwiększa (zmniejsza) na wstawienia (usuwa). W niektórych przypadkach przestrzeni używanej nie zmienia się na operacje wstawiania lub usuwa w zależności od ilości i wzorzec danych zaangażowanych w operację i wszystkie fragmentacji. Na przykład usuwając jeden wiersz z każdej strony danych nie musi zmniejszyć ilość miejsca używanego.|
|**Przydzielone miejsce danych**|Ilość sformatowany plik miejsce dostępne do przechowywania danych bazy danych.|Ilość miejsca przydzielonego powiększa się automatycznie, ale nigdy nie zmniejsza po usuwaniu. Takie zachowanie gwarantuje, przyszłe operacje wstawiania są szybsze, ponieważ miejsce nie musi być ponownie sformatowany.|
|**Przydzielone, ale nieużywana miejsce danych**|Różnica między ilość danych miejsce przydzielone i użyte miejsce danych.|Ta ilość reprezentuje maksymalną ilość wolnego miejsca, które można odzyskać, zmniejszając pliki danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca, który może służyć do przechowywania danych w bazie danych.|Ilość danych miejsce przydzielone nie może rosnąć poza maksymalnego rozmiaru danych.|
||||

Na poniższym diagramie przedstawiono relację między różnymi typami miejsca do magazynowania dla bazy danych.

![relacje i typy miejsce magazynowania](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>Zapytanie dotyczące bazy danych informacji miejsce magazynowania

Następujące zapytania może służyć do określenia ilości miejsca magazynu dla bazy danych.  

### <a name="database-data-space-used"></a>Używane miejsce danych w bazie danych
Zmodyfikuj następujące zapytanie, aby zwrócić ilość miejsca danych bazy danych.  Jednostki wyniki zapytania są w MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Przydzielone miejsce danych w bazie danych i nieużywane miejsce przydzielone
Użyj następującego zapytania, aby zwrócić ilość przydzielonej przestrzeni danych bazy danych i ilość nieużywane miejsce przydzielone.  Jednostki wyniki zapytania są w MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Maksymalny rozmiar danych bazy danych
Zmodyfikuj następujące zapytanie, aby zwrócić maksymalnego rozmiaru bazy danych.  Jednostki wyniki zapytania są w bajtach.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Opis typów miejsca do magazynowania dla puli elastycznej

Informacje o następujących ilości miejsca magazynu są ważne w przypadku zarządzanie przestrzenią pliku elastycznej puli.

|Ilość puli elastycznej|Definicja|Komentarze|
|---|---|---|
|**Dane miejsca**|Podsumowanie przestrzeni danych używany przez wszystkie bazy danych w puli elastycznej.||
|**Przydzielone miejsce danych**|Podsumowanie danych miejsce przydzielone przez wszystkie bazy danych w puli elastycznej.||
|**Przydzielone, ale nieużywana miejsce danych**|Różnica między ilości danych miejsce przydzielone i przestrzeni danych używany przez wszystkie bazy danych w puli elastycznej.|Ta ilość reprezentuje maksymalną ilość miejsca przydzielonego dla puli elastycznej, które można odzyskać, zmniejszając pliki danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca na dane, który może służyć w puli elastycznej dla wszystkich baz danych.|Ilość miejsca przydzielonego dla puli elastycznej nie może przekraczać maksymalny rozmiar puli elastycznej.  Jeśli ten problem wystąpi, miejsce przydzielone, która jest używana można odzyskać, zmniejszając pliki danych bazy danych.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Zapytanie elastycznej puli magazynu miejsca informacji

Następujące zapytania może służyć do określenia ilości miejsca magazynu dla puli elastycznej.  

### <a name="elastic-pool-data-space-used"></a>Używać przestrzeni danych puli elastycznej
Zmodyfikuj następujące zapytanie, aby zwrócić ilość przestrzeni danych puli elastycznej używane.  Jednostki wyniki zapytania są w MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Przydzielonej przestrzeni danych puli elastycznej i nieużywane miejsce przydzielone

Zmodyfikuj następujący skrypt programu PowerShell, aby zwrócić tabelę z listą ilość miejsca przydzielonego i nieużywane miejsce przydzielone dla każdej bazy danych w puli elastycznej. Tabeli orders baz danych, od osób z największą ilością nieużywane przydzielone miejsce na najmniejszą ilością nieużywane przydzielone miejsce.  Jednostki wyniki zapytania są w MB.  

Wyniki zapytania do określania ilość miejsca przydzielonego dla każdej bazy danych w puli można dodać razem do określenia łączne miejsce przydzielone dla puli elastycznej. Przydzielone miejsce puli elastycznej nie może przekraczać maksymalny rozmiar puli elastycznej.  

Skrypt programu PowerShell wymaga modułu programu PowerShell programu SQL Server — zobacz [modułu PowerShell Pobierz](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module?view=sql-server-2017) do zainstalowania.

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Poniższy zrzut ekranu przedstawia przykład danych wyjściowych skryptu:

![Pula elastyczna przydzielone miejsce i przykład nieużywane miejsce przydzielone](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maksymalnego rozmiaru danych dla puli elastycznej

Zmodyfikuj następujące zapytanie T-SQL, aby zwrócić maksymalny rozmiar danych w puli elastycznej.  Jednostki wyniki zapytania są w MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Odzyskać nieużywane miejsce przydzielone

Po baz danych zostały zidentyfikowane dla odzyskiwanie nieużywane miejsce przydzielone, zmodyfikuj następujące polecenie, aby zmniejszyć plików danych dla każdej bazy danych.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

> [!IMPORTANT] 
> Należy rozważyć, czy zmniejszony odbudowywania indeksów bazy danych po pliki danych bazy danych, indeksów może ulec fragmentacji i utracić ich skuteczność optymalizacji wydajności. W takiej sytuacji należy można odbudować indeksy. Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat maksymalny rozmiar bazy danych zobacz:
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pul elastycznych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Aby uzyskać więcej informacji na temat `SHRINKDATABASE` polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
