---
title: Dodawanie fragmentu przy użyciu narzędzi elastycznej bazy danych
description: Jak używać interfejsów API skali elastycznej, aby dodać nowe fragmenty do zestawu niezależnego fragmentu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823710"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Dodawanie fragmentu przy użyciu narzędzi elastycznej bazy danych

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Aby dodać fragment dla nowego zakresu lub klucza

Aplikacje często trzeba dodać nowe fragmenty do obsługi danych, które oczekuje się od nowych kluczy lub zakresów kluczy, dla mapy niezależnego fragmentu, który już istnieje. Na przykład aplikacja podzielona na fragmenty przez identyfikator dzierżawy może być konieczne aprowizowanie nowego niezależnego fragmentu dla nowej dzierżawy lub dane podzielonej co miesiąc może wymagać nowego niezależnego fragmentu aprowizacji przed rozpoczęciem każdego nowego miesiąca.

Jeśli nowy zakres wartości klucza nie jest już częścią istniejącego mapowania, można łatwo dodać nowy fragment i skojarzyć nowy klucz lub zakres do tego fragmentu.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Przykład: dodawanie fragmentu i jego zasięgu do istniejącej mapy niezależnego fragmentu

W tym przykładzie użyto trygetshard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metody i tworzy wystąpienie ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)). W poniższym przykładzie baza danych o nazwie **sample_shard_2** i wszystkie niezbędne obiekty schematu wewnątrz niego zostały utworzone do przechowywania zakresu [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

W przypadku wersji .NET można również użyć programu PowerShell jako alternatywy do utworzenia nowego Menedżera map niezależnego fragmentu. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Aby dodać fragment dla pustej części istniejącego zakresu

W niektórych okolicznościach może już mapowane zakres do niezależnego fragmentu i częściowo wypełnione go danymi, ale teraz chcesz, aby nadchodzące dane mają być kierowane do innego fragmentu. Na przykład można niezależnego fragmentu według zakresu dnia i już przydzielone 50 dni do niezależnego fragmentu, ale w dniu 24, chcesz przyszłych danych do lądowania w innym niezależnego fragmentu. [Narzędzie do scalania podzielonej elastycznej](sql-database-elastic-scale-overview-split-and-merge.md) bazy danych może wykonać tę operację, ale jeśli przenoszenie danych nie jest konieczne (na przykład dane dla zakresu dni [25, 50), czyli dzień 25 włącznie z 50 wyłącznymi, jeszcze nie istnieje) można wykonać to całkowicie za pomocą interfejsów API zarządzania mapami niezależnego fragmentu.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Przykład: dzielenie zakresu i przypisywanie pustej części do nowo dodanego fragmentu

Baza danych o nazwie "sample_shard_2" i wszystkie niezbędne obiekty schematu wewnątrz niego zostały utworzone.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Ważne:** Tej techniki należy używać tylko wtedy, gdy masz pewność, że zakres zaktualizowanego mapowania jest pusty.  Powyższe metody nie sprawdzają danych dla zakresu, który jest przenoszony, dlatego najlepiej jest uwzględnić kontrole w kodzie.  Jeśli wiersze istnieją w zakresie są przenoszone, rzeczywista dystrybucja danych nie będzie odpowiadać zaktualizowanej mapy niezależnego fragmentu. Użyj [narzędzia scalania podziału,](sql-database-elastic-scale-overview-split-and-merge.md) aby wykonać operację zamiast w tych przypadkach.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
