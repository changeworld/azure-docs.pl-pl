---
title: Zapytanie podzielonej na fragmenty baz danych Azure SQL | Dokumentacja firmy Microsoft
description: Uruchamianie zapytań między fragmentami przy użyciu biblioteki klienta elastycznej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 35759f03d7cf09a4114ca6dca74bd3ee92fdcbfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761696"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Wieloma fragmentami zapytań, za pomocą narzędzi elastycznych baz danych

## <a name="overview"></a>Omówienie

Za pomocą [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md), możesz tworzyć rozwiązania bazy danych podzielonej na fragmenty. **Wykonywanie zapytań w wielu fragmentów** używane do wykonywania zadań, takich jak kolekcja/raportowania danych wymagające uruchomienia zapytania rozciąga kilka fragmentów. (Ten element, aby porównać [routingu zależnego od danych](sql-database-elastic-scale-data-dependent-routing.md), który wykonuje całą pracę na jeden fragment.)

1. Pobierz **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [platformy .NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) przy użyciu **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), lub **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metody. Zobacz [konstruowanie ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) i [RangeShardMap lub ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Tworzenie **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) obiektu.
3. Tworzenie **MultiShardStatement lub MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Ustaw **Właściwość CommandText** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) do polecenia języka T-SQL.
5. Wykonaj polecenie wywołując **ExecuteQueryAsync lub ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) metody.
6. Wyświetl wyniki za pomocą **MultiShardResultSet lub MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) klasy.

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

Najważniejszą różnicą składa się wieloma fragmentami połączeń. Gdzie **SqlConnection** operuje na poszczególnych baz danych, **MultiShardConnection** przyjmuje ***zbiór fragmentów*** jako dane wejściowe. Wypełnianie kolekcji fragmentów z mapy fragmentów. Następnie wykonywania zapytania w kolekcji przy użyciu fragmentów **UNION ALL** semantyki gromadzi pojedynczy wynik ogólny. Opcjonalnie można dodać nazwę fragmentu wiersza, z której pochodzi do danych wyjściowych przy użyciu **ExecutionOptions** właściwość polecenia.

Należy pamiętać, wywołanie **myShardMap.GetShards()** . Ta metoda umożliwia pobranie wszystkich fragmentów z mapy fragmentów i zapewnia łatwy sposób uruchamiania zapytania dla wszystkich odpowiednich baz danych. Zbiór fragmentów dla zapytań wieloma fragmentami może być dostosowany dalsze, wykonując zapytanie LINQ na kolekcji zwrócony z wywołania **myShardMap.GetShards()** . W połączeniu z zasadami wyniki częściowe bieżące możliwości badania wieloma fragmentami został zaprojektowany do działania oraz dziesiątki do setek fragmentów.

Ograniczenie z wieloma fragmentami zapytań jest obecnie brak sprawdzania poprawności dla fragmentów i podfragmentów, który jest badane są tabele. Chociaż routingu zależnego od danych sprawdza, czy dany fragment część mapowania fragmentów w czasie wykonywania zapytań, zapytania z wieloma fragmentami nie należy wykonywać tego sprawdzenia. Może to prowadzić do uruchamiania w bazach danych, które zostały usunięte z mapy fragmentów zapytania z wieloma fragmentami.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Zapytania z wieloma fragmentami i operacji dzielenia i scalania

Zapytania z wieloma fragmentami nie Weryfikuj, czy podfragmentów kwerendy bazy danych uczestniczą w trwających operacji dzielenia i scalania. (Zobacz [skalowanie przy użyciu narzędzia do dzielenia i scalania Elastic Database](sql-database-elastic-scale-overview-split-and-merge.md).) Może to doprowadzić do niespójności gdzie Pokaż wiersze z tego samego podfragmentu dla wielu baz danych w jednym zapytaniu wielu fragmentów. Należy pamiętać o tych ograniczeniach i należy wziąć pod uwagę opróżniania trwających operacji dzielenia i scalania i zmiany do mapy fragmentów podczas wykonywania zapytania z wieloma fragmentami.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]