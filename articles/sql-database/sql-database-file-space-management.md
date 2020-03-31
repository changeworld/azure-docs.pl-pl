---
title: Zarządzanie przestrzenią plików pojedynczych/pulowanych baz danych
description: Na tej stronie opisano sposób zarządzania przestrzenią plików za pomocą pojedynczych i puli baz danych w usłudze Azure SQL Database i zawiera przykłady kodu, jak określić, czy trzeba zmniejszyć pojedynczą lub pulową bazę danych, a także jak wykonać operację zmniejszania bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420985"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Zarządzanie przestrzenią plików dla pojedynczych i puli baz danych w bazie danych SQL Azure

W tym artykule opisano różne typy miejsca do magazynowania dla pojedynczych i pulowych baz danych w usłudze Azure SQL Database oraz kroki, które można wykonać, gdy miejsce na pliki przydzielone dla baz danych i pul elastycznych musi być jawnie zarządzane.

> [!NOTE]
> Ten artykuł nie dotyczy opcji wdrażania wystąpienia zarządzanego w usłudze Azure SQL Database.

## <a name="overview"></a>Omówienie

Z pojedynczych i puli baz danych w usłudze Azure SQL Database, istnieją wzorce obciążenia, gdzie alokacja podstawowych plików danych dla baz danych może stać się większa niż ilość używanych stron danych. Ten stan może wystąpić, gdy ilość używanego miejsca zwiększy się, a następnie dane zostaną usunięte. Powodem jest to, że przydzielone miejsce na pliki nie jest automatycznie odzyskiwane po usunięciu danych.

Monitorowanie użycia miejsca na pliki i zmniejszania plików danych może być konieczne w następujących scenariuszach:

- Zezwalanie na wzrost ilości danych w elastycznej puli, gdy miejsce na pliki przydzielone dla jej baz danych osiągnie maksymalny rozmiar dla puli.
- Zezwalanie na zmniejszenie maksymalnego rozmiaru pojedynczej bazy danych lub elastycznej puli.
- Zezwalanie na zmianę warstwy usługi lub wydajności dla pojedynczej bazy danych lub elastycznej puli na warstwę obsługującą mniejszy rozmiar maksymalny.

### <a name="monitoring-file-space-usage"></a>Monitorowanie wykorzystania miejsca na pliki

Większość metryk miejsca do magazynowania wyświetlanych w witrynie Azure portal i następujące interfejsy API mierzą tylko rozmiar używanych stron danych:

- Interfejsy API oparte na metrykach usługi Azure Resource Manager, w tym [metryki get-metrics programu](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Jednak następujące interfejsy API również zmierzyć rozmiar miejsca przydzielonego dla baz danych i pul elastycznych:

- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Zmniejszanie plików danych

Usługa bazy danych SQL nie zmniejsza automatycznie plików danych w celu odzyskania niewykorzystanego przydzielonego miejsca ze względu na potencjalny wpływ na wydajność bazy danych.  Klienci mogą jednak zmniejszać pliki danych za pośrednictwem samoobsługi w wybranym przez siebie czasie, wykonując kroki opisane w [celu odzyskania niewykorzystanego przydzielonego miejsca.](#reclaim-unused-allocated-space)

> [!NOTE]
> W przeciwieństwie do plików danych usługa bazy danych SQL automatycznie zmniejsza pliki dziennika, ponieważ ta operacja nie wpływa na wydajność bazy danych.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Opis typów miejsca do magazynowania bazy danych

Zrozumienie następujących ilości miejsca do magazynowania są ważne dla zarządzania przestrzenią plików bazy danych.

|Ilość bazy danych|Definicja|Komentarze|
|---|---|---|
|**Wykorzystano miejsce na dane**|Ilość miejsca używanego do przechowywania danych bazy danych na stronach o rozmiarze 8 KB.|Ogólnie rzecz biorąc, miejsce używane zwiększa (zmniejsza) na wstawia (usuwa). W niektórych przypadkach miejsce używane nie zmienia się na wstawia lub usuwa w zależności od ilości i wzorca danych zaangażowanych w operację i fragmentacji. Na przykład usunięcie jednego wiersza z każdej strony danych nie musi zmniejszać używanego miejsca.|
|**Przydzielone miejsce na dane**|Ilość sformatowanego miejsca na pliki udostępnionego do przechowywania danych bazy danych.|Ilość przydzielonego miejsca zwiększa się automatycznie, ale nigdy nie zmniejsza się po usunięciu. To zachowanie zapewnia, że przyszłe wstawia są szybsze, ponieważ miejsce nie musi być sformatowany.|
|**Miejsce na dane przydzielone, ale nieużywane**|Różnica między ilością przydzielonego miejsca na dane a wykorzystaną przestrzenią danych.|Ta ilość reprezentuje maksymalną ilość wolnego miejsca, które można odzyskać przez zmniejszenie plików danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca, która może służyć do przechowywania danych bazy danych.|Ilość przydzielonego miejsca na dane nie może wzrosnąć poza maksymalny rozmiar danych.|

Na poniższym diagramie przedstawiono relację między różnymi typami miejsca do magazynowania dla bazy danych.

![typy i relacje przestrzeni dyskowej](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Zapytanie o pojedynczą bazę danych w celu uzyskania informacji o przestrzeni dyskowej

Następujące kwerendy mogą służyć do określania ilości miejsca do magazynowania dla pojedynczej bazy danych.  

### <a name="database-data-space-used"></a>Wykorzystano miejsce na dane bazy danych

Zmodyfikuj następującą kwerendę, aby zwrócić ilość używanego miejsca na dane bazy danych.  Jednostki wyniku kwerendy są w MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Przydzielone miejsce na dane bazy danych i niewykorzystane przydzielone miejsce

Użyj następującej kwerendy, aby zwrócić ilość przydzielonego miejsca na dane bazy danych i ilość przydzielonego niewykorzystanego miejsca.  Jednostki wyniku kwerendy są w MB.

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

Zmodyfikuj następującą kwerendę, aby zwrócić maksymalny rozmiar danych bazy danych.  Jednostki wyniku kwerendy są w bajtach.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Opis typów przestrzeni dyskowej dla puli elastycznej

Zrozumienie następujących ilości miejsca do magazynowania są ważne dla zarządzania przestrzenią plików puli elastycznej.

|Ilość puli elastycznej|Definicja|Komentarze|
|---|---|---|
|**Wykorzystano miejsce na dane**|Sumowanie przestrzeni danych używanej przez wszystkie bazy danych w puli elastycznej.||
|**Przydzielone miejsce na dane**|Sumowanie miejsca na dane przydzielonego przez wszystkie bazy danych w puli elastycznej.||
|**Miejsce na dane przydzielone, ale nieużywane**|Różnica między ilością przydzielonego miejsca na dane a przestrzenią danych używaną przez wszystkie bazy danych w puli elastycznej.|Ta ilość reprezentuje maksymalną ilość miejsca przydzielonego dla puli elastycznej, które mogą być odzyskane przez zmniejszenie plików danych bazy danych.|
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca na dane, które mogą być używane przez pulę elastyczną dla wszystkich jego baz danych.|Miejsce przydzielone dla puli elastycznej nie powinno przekraczać maksymalnego rozmiaru puli elastycznej.  Jeśli wystąpi ten warunek, a następnie miejsce przydzielone, które jest nieużywane można odzyskać przez zmniejszenie plików danych bazy danych.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Zapytanie o pulę elastyczną w celu uzyskania informacji o przestrzeni dyskowej

Następujące zapytania mogą służyć do określania ilości miejsca do magazynowania dla puli elastycznej.  

### <a name="elastic-pool-data-space-used"></a>Wykorzystano elastyczną przestrzeń danych puli

Zmodyfikuj następującą kwerendę, aby zwrócić ilość użytego miejsca na dane puli elastycznej.  Jednostki wyniku kwerendy są w MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Przydzielone miejsce na dane puli elastycznej i niewykorzystane przydzielone miejsce

Zmodyfikuj poniższe przykłady, aby zwrócić tabelę zawierającą przydzielone miejsce i niewykorzystane przydzielone miejsce dla każdej bazy danych w puli elastycznej. Tabela porządkuje bazy danych z tych baz danych z największą ilością niewykorzystanego przydzielonego miejsca do najmniejszej ilości niewykorzystanego przydzielonego miejsca.  Jednostki wyniku kwerendy są w MB.  

Wyniki kwerendy do określania miejsca przydzielonego dla każdej bazy danych w puli można dodać razem, aby określić całkowitą ilość miejsca przydzielonego dla puli elastycznej. Przydzielone miejsce w puli elastycznej nie powinno przekraczać maksymalnego rozmiaru puli elastycznej.  

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

Skrypt programu PowerShell wymaga modułu programu SQL Server PowerShell — zobacz [Pobieranie modułu programu PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) do zainstalowania.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

Poniższy zrzut ekranu jest przykładem danych wyjściowych skryptu:

![przykład przydzielonego miejsca w puli elastycznej i nieużywanego przydzielonego miejsca](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Maksymalny rozmiar danych puli elastycznej

Zmodyfikuj następującą kwerendę T-SQL, aby zwrócić maksymalny rozmiar danych puli elastycznej.  Jednostki wyniku kwerendy są w MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Odzyskiwanie niewykorzystanego przydzielonego miejsca

> [!NOTE]
> To polecenie może mieć wpływ na wydajność bazy danych podczas jego pracy i, jeśli to możliwe, powinny być uruchamiane w okresach niskiego użycia.

### <a name="dbcc-shrink"></a>Zmniejszanie DBCC

Po zidentyfikowaniu baz danych w celu odzyskania niewykorzystanego przydzielonego miejsca zmodyfikuj nazwę bazy danych w poniższym poleceniu, aby zmniejszyć pliki danych dla każdej bazy danych.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

To polecenie może mieć wpływ na wydajność bazy danych podczas jego pracy i, jeśli to możliwe, powinny być uruchamiane w okresach niskiego użycia.  

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatyczne zmniejszanie

Alternatywnie można włączyć automatyczne zmniejszanie bazy danych.  Automatyczne zmniejszanie złożoności zarządzania plikami i `SHRINKDATABASE` jest `SHRINKFILE`mniej wpływowe na wydajność bazy danych niż lub .  Automatyczne zmniejszanie może być szczególnie przydatne w zarządzaniu pulami elastycznymi z wieloma bazami danych.  Jednak automatyczne zmniejszanie może być mniej skuteczne `SHRINKDATABASE` `SHRINKFILE`w regeneracji miejsca na pliki niż i .
Aby włączyć automatyczne zmniejszanie, zmodyfikuj nazwę bazy danych w następującym poleceniu.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Aby uzyskać więcej informacji na temat tego polecenia, zobacz opcje [ZESTAWU BAZ DANYCH.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

### <a name="rebuild-indexes"></a>Odbuduj indeksy

Po zmniejszaniu plików danych bazy danych indeksy mogą zostać pofragmentowane i utracić skuteczność optymalizacji wydajności. Jeśli występuje obniżenie wydajności, należy rozważyć odbudowanie indeksów bazy danych. Aby uzyskać więcej informacji na temat fragmentacji i przebudowy indeksów, zobacz [Reorganizacja i odbuduj indeksy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o maksymalnych rozmiarach bazy danych, zobacz:
  - [Limity modeli zakupów opartych na platformie SQL Database dla pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md)
  - [Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupów opartego na jednostce DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Limity modeli zakupów opartych na platformie SQL Database dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Limity zasobów dla pul elastycznych przy użyciu modelu zakupów opartego na UJ](sql-database-dtu-resource-limits-elastic-pools.md)
- Aby uzyskać więcej `SHRINKDATABASE` informacji na temat polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Aby uzyskać więcej informacji na temat fragmentacji i przebudowy indeksów, zobacz [Reorganizacja i odbuduj indeksy](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
