---
title: Zarządzanie przestrzenią pliku w usłudze Azure bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu zarządzania przestrzenią plików za pomocą usługi Azure SQL Database i zawiera przykłady kodu dotyczące sposobu określenia, czy można zmniejszyć bazy danych również, jak przeprowadzić operację zmniejszania bazy danych.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415148"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Zarządzanie przestrzenią pliku w usłudze Azure SQL Database

W tym artykule opisano różne rodzaje miejsca do magazynowania w usłudze Azure SQL Database i czynności, które mogą być wykonywane, gdy przydzielone miejsce plików baz danych i pul elastycznych, musi być zarządzane przez klienta.

## <a name="overview"></a>Przegląd

W usłudze Azure SQL Database metryki rozmiar magazynu wyświetlany w witrynie Azure portal i następujące interfejsy API pomiaru liczba stron używanych danych dla baz danych i pul elastycznych:
- Usługa Azure Resource Manager, na podstawie metryk interfejsów API w tym program PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Brak wzorców obciążenia, w których przydzielenie miejsca w podstawowych plików danych dla bazy danych przekroczy liczbę stron używanych danych w plikach danych. Ten scenariusz może wystąpić, gdy miejsce używane rośnie, a następnie dane są usuwane. Po usunięciu danych pliku ilość miejsca przydzielonego jest nie automatycznie odzyskane po usunięciu danych. W takich scenariuszach przydzielonego miejsca dla bazy danych lub puli może być dłuższe niż zestaw obsługiwanych maksymalnych limitów (lub obsługiwany) dla bazy danych, a w rezultacie zapobiec wzrostu ilości danych lub uniemożliwiają zmiany warstwy wydajności, mimo że faktycznie wykorzystanego miejsca w bazie danych jest mniejsza niż maksymalna limit miejsca. Aby rozwiązać problem, może być konieczne zmniejszyć bazy danych, aby ograniczyć miejsce przydzielone, ale nieużywane w bazie danych.

Usługa SQL Database nie automatyczne zmniejszanie plików bazy danych mogą odzyskać nieużywane miejsce przydzielone z powodu potencjalnego wpływu na wydajność bazy danych. Jednakże, można zmniejszyć pliku w bazie danych w momencie, wykonując kroki opisane w [odzyskać nieużywane miejsca przydzielonego](#reclaim-unused-allocated-space). 

> [!NOTE]
> W przeciwieństwie do plików danych usługa SQL Database automatycznie zmniejsza pliki dziennika, ponieważ tej operacji nie ma wpływu na wydajność bazy danych.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Opis typów miejsca do magazynowania dla bazy danych

Do zarządzania przestrzenią pliku, należy zrozumieć następujące pojęcia związane z bazy danych magazynu dla obu pojedynczej bazy danych i puli elastycznej.

|Określenie miejsca magazynu|Definicja|Komentarze|
|---|---|---|
|**Dane miejsca**|Ilość miejsca, używany do przechowywania danych bazy danych na stronach o rozmiarze 8 KB.|Ogólnie rzecz biorąc to miejsce używane zwiększa (zmniejsza) na wstawienia (usuwa). W niektórych przypadkach przestrzeni używanej nie zmienia się na operacje wstawiania lub usuwa w zależności od ilości i wzorzec danych zaangażowanych w operację i wszystkie fragmentacji. Na przykład usuwając jeden wiersz z każdej strony danych nie musi zmniejszyć ilość miejsca używanego.|
|**Przydzielone miejsce**|Ilość sformatowany plik miejsce dostępne do przechowywania danych bazy danych|Ilość miejsca przydzielonego powiększa się automatycznie, ale nigdy nie zmniejsza po usuwaniu. Takie zachowanie gwarantuje, przyszłe operacje wstawiania są szybsze, ponieważ miejsce nie musi być ponownie sformatowany.|
|**Miejsce przydzielone, ale nieużywana**|Ilość miejsca na plik nieużywanych danych, o które przydzielone dla bazy danych.|Ta ilość jest różnica między ilość miejsca przydzielonego i użyte miejsce i przedstawia maksymalną ilość miejsca, które można odzyskać, zmniejszając pliki bazy danych.|
|**Maksymalny rozmiar**|Maksymalna ilość miejsca na dane mogą być używane przez bazy danych.|Przydzielone miejsce danych nie może rosnąć poza maksymalnego rozmiaru danych.|
||||

Na poniższym diagramie przedstawiono relację między tymi typami miejsca do magazynowania.

![relacje i typy miejsce magazynowania](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Zapytanie dotyczące bazy danych informacji miejsce magazynowania

Aby określić, jeśli zostały przydzielone, ale nieużywana przestrzeni danych dla poszczególnych baz danych czy może chcesz odzyskać, użyj następujących zapytań:

### <a name="database-data-space-used"></a>Używane miejsce danych w bazie danych
Zmodyfikuj następujące zapytanie, aby zwrócić ilość miejsca danych bazy danych w MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Dane z bazy danych przydzielane i przydzielone miejsce nieużywane
Zmodyfikuj następujące zapytanie, aby zwrócić ilości danych bazy danych przydzielone i przydzielone miejsce nieużywane.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Maksymalny rozmiar bazy danych
Zmodyfikuj następujące zapytanie, aby zwrócić maksymalnego rozmiaru bazy danych w bajtach.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Zapytanie elastycznej puli magazynu miejsca informacji

Aby określić, jeśli zostały przydzielone, ale nieużywana przestrzeni danych w puli elastycznej i dla każdej puli bazy danych czy może chcesz odzyskać, użyj następujących zapytań:

### <a name="elastic-pool-data-space-used"></a>Używać przestrzeni danych puli elastycznej
Zmodyfikuj następujące zapytanie, aby zwrócić ilość miejsca danych elastycznej puli w Megabajtach.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Dane puli elastycznej przydzielone i przydzielone miejsce nieużywane

Zmodyfikuj poniższy skrypt programu PowerShell, aby zwrócić tabelę z listą łączne miejsce przydzielone i nieużywane miejsce przydzielone dla każdej bazy danych w puli elastycznej. Tabeli orders baz danych z tych z największą ilość miejsca przydzielonego nieużywanych do najmniejszej ilość miejsca przydzielonego nieużywane.  

Wyniki zapytania do określania ilość miejsca przydzielonego dla każdej bazy danych w puli mogą być dodawane razem określić przydzielone miejsce w puli elastycznej. Przydzielone miejsce puli elastycznej nie może przekraczać maksymalny rozmiar puli elastycznej.  

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
# Requires SQL Server PowerShell module – see here to install.  
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

### <a name="elastic-pool-max-size"></a>Maksymalny rozmiar puli elastycznej

Użyj następującego zapytania T-SQL, aby zwrócić maksymalny rozmiar elastycznych baz danych w MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Odzyskać nieużywane miejsce przydzielone

Po określeniu, czy masz nieużywane miejsce przydzielone, który chcesz odzyskać, użyj następującego polecenia, aby zmniejszyć przydzielone miejsca w bazie danych. 

> [!IMPORTANT]
> W przypadku baz danych w elastycznej puli baz danych przy użyciu najbardziej miejsce przydzielone nieużywane powinno zmniejszyć najpierw na najbardziej szybko odzyskać miejsce pliku.  

Aby zmniejszyć wszystkie pliki danych w określonej bazy danych, użyj następującego polecenia:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Należy rozważyć, czy zmniejszony odbudowywania indeksów bazy danych po pliki danych bazy danych, indeksów może ulec fragmentacji i utracić ich skuteczność optymalizacji wydajności. W takiej sytuacji należy można odbudować indeksy. Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat maksymalnych rozmiarów zobacz:
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pul elastycznych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Aby uzyskać więcej informacji na temat `SHRINKDATABASE` polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).