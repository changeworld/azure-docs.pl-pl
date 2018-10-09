---
title: Zapytanie podzielonej na fragmenty baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Uruchamianie zapytań między fragmentami przy użyciu biblioteki klienta elastycznej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 93408b266a239e897b49ab2482818a5221742685
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870406"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Wieloma fragmentami zapytań, za pomocą narzędzi elastycznych baz danych

## <a name="overview"></a>Przegląd

Za pomocą [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), możesz tworzyć rozwiązania bazy danych podzielonej na fragmenty. **Wykonywanie zapytań w wielu fragmentów** używane do wykonywania zadań, takich jak kolekcja/raportowania danych wymagające uruchomienia zapytania rozciąga kilka fragmentów. (Ten element, aby porównać [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md), który wykonuje całą pracę na jeden fragment.) 

1. Pobierz **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) lub **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [platformy .NET ](https://msdn.microsoft.com/library/azure/dn807370.aspx)) przy użyciu **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), lub **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metody. Zobacz **[konstruowanie ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager)** i  **[RangeShardMap lub ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)**.
2. Tworzenie **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_connection), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)) obiektu.
3. Tworzenie **MultiShardStatement lub MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)). 
4. Ustaw **Właściwość CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)) do polecenia języka T-SQL.
5. Wykonaj polecenie wywołując **ExecuteQueryAsync lub ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_statement.executeQueryAsync), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)) metody.
6. Wyświetl wyniki za pomocą **MultiShardResultSet lub MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard._multi_shard_result_set), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)) klasy. 

## <a name="example"></a>Przykład

Poniższy kod ilustruje użycie wielu fragmentów, wykonywanie zapytań przy użyciu danego **ShardMap** o nazwie *myShardMap*. 

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString)) 
{ 
    using (MultiShardCommand cmd = conn.CreateCommand())
    { 
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
        cmd.CommandType = CommandType.Text; 
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

        using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
        { 
            while (sdr.Read())
            { 
                var c1Field = sdr.GetString(0); 
                var c2Field = sdr.GetFieldValue<int>(1); 
                var c3Field = sdr.GetFieldValue<Int64>(2);
            } 
        } 
    } 
} 
```

Najważniejszą różnicą składa się wieloma fragmentami połączeń. Gdzie **SqlConnection** operuje na pojedynczej bazy danych **MultiShardConnection** przyjmuje ***zbiór fragmentów*** jako dane wejściowe. Wypełnianie kolekcji fragmentów z mapy fragmentów. Następnie wykonywania zapytania w kolekcji przy użyciu fragmentów **UNION ALL** semantyki gromadzi pojedynczy wynik ogólny. Opcjonalnie można dodać nazwę fragmentu wiersza, z której pochodzi do danych wyjściowych przy użyciu **ExecutionOptions** właściwość polecenia. 

Należy pamiętać, wywołanie **myShardMap.GetShards()**. Ta metoda umożliwia pobranie wszystkich fragmentów z mapy fragmentów i zapewnia łatwy sposób uruchamiania zapytania dla wszystkich odpowiednich baz danych. Zbiór fragmentów dla zapytań wieloma fragmentami może być dostosowany dalsze, wykonując zapytanie LINQ na kolekcji zwrócony z wywołania **myShardMap.GetShards()**. W połączeniu z zasadami wyniki częściowe bieżące możliwości badania wieloma fragmentami został zaprojektowany do działania oraz dziesiątki do setek fragmentów.

Ograniczenie z wieloma fragmentami zapytań jest obecnie brak sprawdzania poprawności dla fragmentów i podfragmentów, który jest badane są tabele. Chociaż routingu zależnego od danych sprawdza, czy dany fragment część mapowania fragmentów w czasie wykonywania zapytań, zapytania z wieloma fragmentami nie należy wykonywać tego sprawdzenia. Może to prowadzić do uruchamiania w bazach danych, które zostały usunięte z mapy fragmentów zapytania z wieloma fragmentami.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Zapytania z wieloma fragmentami i operacji dzielenia i scalania

Zapytania z wieloma fragmentami nie Weryfikuj, czy podfragmentów kwerendy bazy danych uczestniczą w trwających operacji dzielenia i scalania. (Zobacz [skalowanie przy użyciu narzędzia do dzielenia i scalania Elastic Database](sql-database-elastic-scale-overview-split-and-merge.md).) Może to doprowadzić do niespójności gdzie Pokaż wiersze z tego samego podfragmentu dla wielu baz danych w jednym zapytaniu wielu fragmentów. Należy pamiętać o tych ograniczeniach i należy wziąć pod uwagę opróżniania trwających operacji dzielenia i scalania i zmiany do mapy fragmentów podczas wykonywania zapytania z wieloma fragmentami.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]