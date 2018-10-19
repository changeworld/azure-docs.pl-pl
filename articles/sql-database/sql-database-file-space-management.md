---
title: Zarządzanie przestrzenią pliku w usłudze Azure bazy danych SQL Database | Dokumentacja firmy Microsoft
description: Ta strona zawiera opis sposobu zarządzania przestrzenią plików za pomocą usługi Azure SQL Database i zawiera przykłady kodu dotyczące sposobu określenia, czy można zmniejszyć bazy danych również, jak przeprowadzić operację zmniejszania bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: bbcd5de0d6c2b43b220d5010093eccc1c9a76269
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406298"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Zarządzanie przestrzenią pliku w usłudze Azure SQL Database
W tym artykule opisano różne rodzaje miejsca do magazynowania w usłudze Azure SQL Database i czynności, które mogą być wykonywane, gdy przydzielone miejsce plików baz danych i pul elastycznych musi odbywać się jawnie.

## <a name="overview"></a>Przegląd

W usłudze Azure SQL Database są wzorce obciążenia gdzie alokacji podstawowych plików danych dla baz danych może stać się większy niż ilość danych używanych stron. Ten stan może wystąpić, gdy miejsce używane zwiększa i dane są usuwane. Przyczyną jest, ponieważ plik miejsce przydzielone nie jest automatycznie odzyskane po usunięciu danych.

Monitorowanie użycia miejsca na plik lub zmniejszania rozmiaru plików danych może być konieczne w następujących scenariuszach:
- Zezwalaj na wzrostu ilości danych w puli elastycznej, gdy obszar pliku przydzielonych do jego baz danych osiągnie maksymalny rozmiar puli.
- Zezwalaj na zmniejszanie maksymalny rozmiar pojedynczej bazy danych lub elastycznej puli.
- Zezwalaj na zmienianie pojedynczą bazę danych lub elastycznej puli do innej usługi warstwy lub warstwy wydajności z niższym maksymalny rozmiar.

### <a name="monitoring-file-space-usage"></a>Monitorowanie użycia miejsca na plik
Większość metryk miejsce magazynowania wyświetlane w witrynie Azure portal oraz następujące interfejsy API pomiaru tylko rozmiar stron używanych danych:
- Usługa Azure Resource Manager, na podstawie metryk interfejsów API w tym program PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

Jednak następujące interfejsy API pomiaru rozmiar ilość miejsca przydzielonego dla baz danych i elastyczne pule:
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Zmniejszanie rozmiaru plików danych

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
|**Maksymalny rozmiar danych**|Maksymalna ilość miejsca na dane, który może służyć w puli elastycznej dla wszystkich baz danych.|Ilość miejsca przydzielonego dla puli elastycznej nie może przekraczać maksymalny rozmiar puli elastycznej.  Jeśli ten stan występuje, miejsce przydzielone, który jest nieużywany można odzyskać, zmniejszając pliki danych bazy danych.|
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

Zmodyfikuj następujący skrypt programu PowerShell, aby zwrócić tabelę z listą ilość miejsca przydzielonego i nieużywane miejsce przydzielone dla każdej bazy danych w puli elastycznej. Tabeli orders baz danych z tych baz danych z największą ilością nieużywane przydzielone miejsce na najmniejszą ilością nieużywane przydzielone miejsce.  Jednostki wyniki zapytania są w MB.  

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

### <a name="dbcc-shrink"></a>Polecenie DBCC zmniejszania

Po baz danych zostały zidentyfikowane dla odzyskiwaniu nieużywanych przydzielonego miejsca, należy zmodyfikować nazwę bazy danych w poniższym poleceniu zmniejszanie plików danych dla każdej bazy danych.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

To polecenie może wpłynąć na wydajność bazy danych, gdy jest uruchomiona, a jeśli to możliwe powinno być uruchamiane w okresach o niskim użyciu.  

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

### <a name="auto-shrink"></a>Automatycznego zmniejszania

Alternatywnie można włączyć automatycznego zmniejszania dla bazy danych.  Automatycznego zmniejszania zmniejsza złożoność zarządzania pliku i jest mniej wpływie na wydajność bazy danych niż SHRINKDATABASE lub SHRINKFILE.  Automatycznego zmniejszania może być szczególnie przydatne w przypadku Zarządzanie elastycznymi pulami za pomocą wielu baz danych.  Jednak automatycznego zmniejszania może być mniej skuteczny w odzyskiwaniu miejsca niż SHRINKDATABASE i SHRINKFILE.
Aby włączyć automatyczne zmniejszanie, należy zmodyfikować nazwę bazy danych w poniższym poleceniu.


```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Aby uzyskać więcej informacji na temat tego polecenia, zobacz [bazy danych ustaw](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-2017) opcje. 

### <a name="rebuild-indexes"></a>Odbuduj indeksy

Po zmniejszyć są pliki danych bazy danych, indeksów może ulec fragmentacji i utracić ich skuteczność optymalizacji wydajności. Jeśli występuje spadek wydajności, rozważ ponowne tworzenie indeksów bazy danych. Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat maksymalny rozmiar bazy danych zobacz:
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pojedynczej bazy danych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Usługa Azure SQL Database oparty na rdzeniach wirtualnych zakupem modelu limity dla pul elastycznych](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limity zasobów dla pul elastycznych za pomocą modelu zakupu opartego na jednostkach DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Aby uzyskać więcej informacji na temat `SHRINKDATABASE` polecenia, zobacz [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Aby uzyskać więcej informacji na temat fragmentacji i ponowne tworzenie indeksów, zobacz [z opcją Reorganize i odbudowy indeksów](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
