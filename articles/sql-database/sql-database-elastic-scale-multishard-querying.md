---
title: Kwerenda podzielona baza danych
description: Uruchamianie zapytań między fragmentami przy użyciu biblioteki klienta elastycznej bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ae14a9fd8fc8479eac596fb694e12e3e0a9027f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067313"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Wykonywanie zapytań dotyczących wielu fragmentów przy użyciu narzędzi elastycznej bazy danych

## <a name="overview"></a>Omówienie

Za pomocą [narzędzi elastycznej bazy danych](sql-database-elastic-scale-introduction.md)można tworzyć rozwiązania podzielonej bazy danych. **Wykonywanie zapytań z wieloma fragmentami** jest używane do zadań, takich jak zbieranie danych/raportowanie, które wymagają uruchomienia kwerendy, która rozciąga się na kilka fragmentów. (Kontrast to do [routingu zależnego od danych,](sql-database-elastic-scale-data-dependent-routing.md)który wykonuje całą pracę na jednym niezależnego fragmentu.)

1. Pobierz **RangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub **ListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) przy użyciu **TryGetRangeShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), lub **GetShardMap** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Zobacz [Konstruowanie ShardMapManager](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) i [Pobierz RangeShardMap lub ListShardMap](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Utwórz obiekt **MultiShardConnection** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Utwórz **MultiShardStatement lub MultiShardCommand** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Ustaw **właściwość CommandText** [(Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) na polecenie T-SQL.
5. Wykonaj polecenie, wywołując metodę **ExecuteQueryAsync lub ExecuteReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Wyświetlanie wyników przy użyciu klasy **MultiShardResultSet lub MultiShardDataReader** ([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Przykład

Poniższy kod ilustruje użycie wielu niezależnego fragmentu kwerendy przy użyciu danego **ShardMap** o nazwie *myShardMap*.

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

Kluczową różnicą jest budowa połączeń wieloprzęsłowych. Gdy **SqlConnection** działa w pojedynczej bazie danych, **MultiShardConnection** przyjmuje ***kolekcję fragmentów*** jako dane wejściowe. Wypełnij kolekcję fragmentów z mapy niezależnego fragmentu. Kwerenda jest następnie wykonywane na kolekcji fragmentów przy użyciu semantyki **UNION ALL** do montażu pojedynczego wyniku ogólnego. Opcjonalnie nazwa niezależnego fragmentu, z którego pochodzi wiersz, można dodać do danych wyjściowych przy użyciu właściwości **ExecutionOptions** na poleceniu.

Zanotuj wywołanie **myShardMap.GetShards()**. Ta metoda pobiera wszystkie fragmenty z mapy niezależnego fragmentu i zapewnia łatwy sposób uruchamiania kwerendy we wszystkich odpowiednich baz danych. Kolekcja fragmentów dla kwerendy wielodywersowej można udoskonalić dalej, wykonując kwerendę LINQ za pomocą kolekcji zwróconej z wywołania **do myShardMap.GetShards()**. W połączeniu z zasadami wyników częściowych bieżące możliwości w kwerendach wielosprzężowych zostały zaprojektowane tak, aby działały dobrze dla dziesiątek do setek fragmentów.

Ograniczenie z wielu niezależnego fragmentu kwerendy jest obecnie brak sprawdzania poprawności dla fragmentów i shardlets, które są poszukiwane. Routingu zależne od danych sprawdza, czy dany fragment jest częścią mapy niezależnego fragmentu w czasie wykonywania zapytań, zapytania wielowyłamłowe nie wykonują tego sprawdzenia. Może to prowadzić do wielu niezależnego fragmentu kwerend uruchomionych w bazach danych, które zostały usunięte z mapy niezależnego fragmentu.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Zapytania wielodyskowego i operacje dzielenia i scalania

Zapytania wielu fragmentów nie sprawdzić, czy shardlets w bazie danych kwerendy uczestniczą w bieżących operacji scalania podziału. (Zobacz [Skalowanie za pomocą narzędzia scalanie dzielenia elastycznej bazy danych).](sql-database-elastic-scale-overview-split-and-merge.md) Może to prowadzić do niespójności, gdzie wiersze z tego samego shardlet show dla wielu baz danych w tej samej kwerendy wielowyłamków. Należy pamiętać o tych ograniczeniach i należy wziąć pod uwagę opróżnianie bieżących operacji scalania podziału i zmiany na mapie niezależnego fragmentu podczas wykonywania zapytań wielu fragmentów.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]