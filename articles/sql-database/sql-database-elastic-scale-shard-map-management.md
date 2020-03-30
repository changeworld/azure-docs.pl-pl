---
title: Skalowanie w poziomie bazy danych
description: Jak korzystać z biblioteki klienta elastycznej bazy danych ShardMapManager
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256265"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skalowanie w poziomie baz danych za pomocą menedżera map niezależnego fragmentu

Aby łatwo skalować w poziomie baz danych na platformie SQL Azure, należy użyć menedżera map niezależnego fragmentu. Menedżer mapy niezależnego fragmentu to specjalna baza danych, która przechowuje globalne informacje o mapowaniu wszystkich fragmentów (baz danych) w zestawie niezależnego fragmentu. Metadane umożliwiają aplikacji łączenie się z poprawną bazą danych na podstawie wartości **klucza dzielenia na fragmenty.** Ponadto każdy fragment w zestawie zawiera mapy śledzące lokalne dane niezależnego fragmentu (znane jako **shardlets).**

![Zarządzanie mapami fragmentów](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Zrozumienie, jak te mapy są konstruowane jest niezbędne do zarządzania mapami fragmentów. Odbywa się to przy użyciu ShardMapManager klasy ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), znaleźć w [bibliotece klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) do zarządzania mapami niezależnego fragmentu.  

## <a name="shard-maps-and-shard-mappings"></a>Mapy niezależnego fragmentu i mapowania fragmentów

Dla każdego fragmentu należy wybrać typ mapy niezależnego fragmentu do utworzenia. Wybór zależy od architektury bazy danych:

1. Pojedyncza dzierżawa na bazę danych  
2. Wielu dzierżawców na bazę danych (dwa typy):
   1. Mapowanie listy
   2. Mapowanie zasięgu

W przypadku modelu z jedną dzierżawą utwórz mapę niezależnego **fragmentu mapowania listy.** Model z jedną dzierżawą przypisuje jedną bazę danych na dzierżawę. Jest to skuteczny model dla deweloperów SaaS, ponieważ upraszcza zarządzanie.

![Mapowanie listy][1]

Model wielodostępny przypisuje kilku dzierżawców do pojedynczej bazy danych (i można dystrybuować grupy dzierżawców w wielu bazach danych). Użyj tego modelu, gdy oczekujesz, że każda dzierżawa ma małe potrzeby danych. W tym modelu należy przypisać zakres dzierżawców do bazy danych przy użyciu **mapowania zakresu**.

![Mapowanie zasięgu][2]

Lub można zaimplementować model bazy danych z wieloma dzierżawami przy użyciu *mapowania listy,* aby przypisać wielu dzierżawców do pojedynczej bazy danych. Na przykład DB1 służy do przechowywania informacji o identyfikatorze dzierżawy 1 i 5, a DB2 przechowuje dane dla dzierżawy 7 i dzierżawy 10.

![Wielu dzierżawców w jednej db][3]

### <a name="supported-types-for-sharding-keys"></a>Obsługiwane typy dla klawiszy dzielenia na fragmenty

Skala elastyczna obsługuje następujące typy jako klawisze dzielenia na fragmenty:

| .NET | Java |
| --- | --- |
| liczba całkowita |liczba całkowita |
| long |long |
| Identyfikator GUID |uuid |
| bajt[]  |bajt[] |
| datetime | sygnatura czasowa |
| Timespan | czas trwania|
| datetimeoffset |czas przesunięcia |

### <a name="list-and-range-shard-maps"></a>Mapy odłamków listy i zakresu

Mapy niezależnego fragmentu mogą być konstruowane przy użyciu **list poszczególnych wartości klucza dzielenia na fragmenty**lub mogą być konstruowane przy użyciu **zakresów wartości klucza dzielenia na fragmenty.**

### <a name="list-shard-maps"></a>Lista map niezależnego fragmentu

**Fragmenty** zawierają **shardlets** i mapowanie shardlets do fragmentów jest obsługiwany przez mapę niezależnego fragmentu. **Mapa niezależnego fragmentu listy** jest skojarzeniem między poszczególnymi wartościami klucza, które identyfikują shardlets i baz danych, które służą jako fragmenty.  **Mapowania list** są jawne i różne wartości kluczy mogą być mapowane do tej samej bazy danych. Na przykład wartość klucza 1 jest mapowana do bazy danych A, a wartości kluczy 3 i 6 są mapone do bazy danych B.

| Klucz | Lokalizacja odłamka |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapy odłamków zasięgu

Na **mapie niezależnego fragmentu zakresu**zakres kluczy jest opisany przez parę **[Niska wartość, wysoka wartość),** gdzie *niska wartość* jest kluczem minimalnym w zakresie, a wysoka *wartość* jest pierwszą wartością wyższą niż zakres.

Na przykład **[0, 100)** zawiera wszystkie liczby całkowite większe lub równe 0 i mniej niż 100. Należy zauważyć, że wiele zakresów może wskazywać tę samą bazę danych, a zakresy rozłączne są obsługiwane (na przykład [100 200) i [400 600) zarówno wskazują na bazę danych C w poniższym przykładzie.)

| Klucz | Lokalizacja odłamka |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Każda z tabel pokazano powyżej jest przykładem koncepcyjnym **ShardMap** obiektu. Każdy wiersz jest uproszczonym przykładem indywidualnego **mapowania punktów** (dla mapy niezależnego fragmentu listy) lub **rangemappingu** (dla mapy niezależnego fragmentu zakresu).

## <a name="shard-map-manager"></a>Menedżer map fragmentów

W bibliotece klienta menedżer mapy niezależnego fragmentu jest kolekcją map niezależnego fragmentu. Dane zarządzane przez wystąpienie **ShardMapManager** są przechowywane w trzech miejscach:

1. **Globalna mapa niezależnego fragmentu (GSM)**: Należy określić bazę danych, która ma służyć jako repozytorium dla wszystkich map i mapowań niezależnego fragmentu. Specjalne tabele i procedury przechowywane są tworzone automatycznie w celu zarządzania informacjami. Zazwyczaj jest to mała baza danych i niechebranie dostęp i nie powinny być używane do innych potrzeb aplikacji. Tabele znajdują się w specjalnym schemacie o nazwie **__ShardManagement**.
2. **Lokalna mapa niezależnego fragmentu (LSM)**: Każda baza danych, która określisz jako niezależnego fragmentu, jest modyfikowana w celu zawierającego kilka małych tabel i specjalnych procedur przechowywanych, które zawierają informacje mapy niezależnego fragmentu i zarządzają nimi, specyficzne dla tego fragmentu. Te informacje są zbędne z informacjami w GSM i umożliwia aplikacji sprawdzanie poprawności informacji o mapie fragmentów pamięci podręcznej bez umieszczania obciążenia na GSM; aplikacja używa LSM, aby ustalić, czy buforowane mapowanie jest nadal prawidłowe. Tabele odpowiadające LSM na każdym niezależnuzyku są również w schemacie **__ShardManagement**.
3. **Pamięć podręczna aplikacji:** Każde wystąpienie aplikacji uzyskujące dostęp do obiektu **ShardMapManager** przechowuje lokalną pamięć podręczną w pamięci swoich mapowań. Przechowuje informacje o routingu, które zostały niedawno pobrane.

## <a name="constructing-a-shardmapmanager"></a>Konstruowanie ShardMapManager

Obiekt **ShardMapManager** jest konstruowany przy użyciu wzorca fabrycznego[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) Metoda **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) przyjmuje poświadczenia (w tym nazwę serwera i nazwę bazy danych zawierającą GSM) w postaci **PołączeniaString** i zwraca wystąpienie **ShardMapManager**.  

**Uwaga:** **ShardMapManager** powinny być tworzone tylko raz na domenę aplikacji, w kodzie inicjowania dla aplikacji. Tworzenie dodatkowych wystąpień ShardMapManager w tej samej domenie aplikacji powoduje zwiększenie wykorzystania pamięci i procesora CPU aplikacji. **A ShardMapManager** może zawierać dowolną liczbę map niezależnego fragmentu. Podczas gdy mapa pojedynczego fragmentu może być wystarczająca dla wielu aplikacji, istnieją chwile, gdy różne zestawy baz danych są używane do różnych schematów lub do unikatowych celów; w takich przypadkach preferowane może być wiele map niezależnego fragmentu.

W tym kodzie aplikacja próbuje otworzyć istniejący **ShardMapManager** z TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metoda. Jeśli obiekty reprezentujące global **shardMapManager** (GSM) nie istnieją jeszcze wewnątrz bazy danych, biblioteka klienta tworzy je przy użyciu Metody CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

W przypadku wersji .NET można użyć programu PowerShell do utworzenia nowego Menedżera map niezależnego fragmentu. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Pobierz RangeShardMap lub ListShardMap

Po utworzeniu menedżera map niezależnego fragmentu można uzyskać mapę RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) przy użyciu metody TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) lub GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)).

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Poświadczenia administracji mapą niezależnego fragmentu

Aplikacje, które administrują i manipulują mapami niezależnego fragmentu różnią się od tych, które używają map niezależnego fragmentu do kierowania połączeń.

Aby administrować mapami niezależnego fragmentu (dodawanie lub zmienianie fragmentów, mapy fragmentów, mapowania fragmentów itp.) należy utworzyć wystąpienie **programu ShardMapManager** przy użyciu **poświadczeń, które mają uprawnienia do odczytu/zapisu zarówno w bazie danych GSM, jak i w każdej bazie danych, która służy jako fragment.** Poświadczenia muszą zezwalać na zapisy względem tabel w GSM i LSM jako informacje o mapie niezależnego fragmentu jest wprowadzany lub zmieniany, a także do tworzenia tabel LSM na nowe fragmenty.  

Zobacz [Poświadczenia używane do uzyskiwania dostępu do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Dotyczy to tylko metadanych, których dotyczy problem

Metody używane do wypełniania lub zmiany danych **ShardMapManager** nie zmieniają danych użytkownika przechowywanych w samych fragmentach. Na przykład metody takie jak **CreateShard**, **DeleteShard**, **UpdateMapping**itp. Nie usuwają, nie dodają ani nie zmieniają danych użytkownika zawartych w fragmentach. Zamiast tego te metody są przeznaczone do użycia w połączeniu z oddzielnych operacji, które można wykonać w celu utworzenia lub usunięcia rzeczywistych baz danych lub przenieść wiersze z jednego niezależnego fragmentu do drugiego, aby ponownie zrównoważyć środowisko podzielonego na fragment.  (Narzędzie **do scalania podziału** dołączone do narzędzi elastycznej bazy danych korzysta z tych interfejsów API wraz z organizowaniem rzeczywistego przenoszenia danych między fragmentami). Zobacz [Skalowanie przy użyciu narzędzia scalanie podzielonej bazy danych elastycznej](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Routing zależny od danych

Menedżer mapy niezależnego fragmentu jest używany w aplikacjach, które wymagają połączeń z bazą danych do wykonywania operacji danych specyficznych dla aplikacji. Te połączenia muszą być skojarzone z poprawną bazą danych. Jest to nazywane **routingu zależnego od danych**. W przypadku tych aplikacji wystąpienia obiektu menedżera map niezależnego fragmentu z fabryki przy użyciu poświadczeń, które mają dostęp tylko do odczytu w bazie danych GSM. Poszczególne żądania dla późniejszych połączeń dostarczają poświadczenia niezbędne do nawiązania połączenia z odpowiednią bazą danych niezależnego fragmentu.

Należy zauważyć, że te aplikacje (przy użyciu **ShardMapManager** otwarty z poświadczeniami tylko do odczytu) nie można wprowadzać zmian na mapach lub mapowaniach. W przypadku tych potrzeb utwórz aplikacje specyficzne dla administracyjnych lub skrypty programu PowerShell, które dostarczają poświadczenia o wyższych uprawnieniach, jak wspomniano wcześniej. Zobacz [Poświadczenia używane do uzyskiwania dostępu do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

Aby uzyskać więcej informacji, zobacz [Routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modyfikowanie mapy niezależnego fragmentu

Mapa niezależnego fragmentu można zmienić na różne sposoby. Wszystkie z następujących metod zmodyfikować metadane opisujące fragmenty i ich mapowania, ale nie fizycznie modyfikować dane w ramach fragmentów, ani nie tworzą lub usunąć rzeczywiste bazy danych.  Niektóre operacje na mapie niezależnego fragmentu opisane poniżej może być konieczne koordynacja z działań administracyjnych, które fizycznie przenieść dane lub które dodają i usuwają bazy danych służąc jako fragmenty.

Te metody współpracują ze sobą jako bloki konstrukcyjne dostępne do modyfikowania ogólnego rozkładu danych w środowisku podzielonej bazy danych.  

* Aby dodać lub usunąć fragmenty: użyj **createshard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) i **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) klasy Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    Serwer i baza danych reprezentująca obiekt docelowy musi już istnieć dla tych operacji do wykonania. Te metody nie mają żadnego wpływu na same bazy danych, tylko na metadane na mapie niezależnego fragmentu.
* Aby utworzyć lub usunąć punkty lub zakresy mapowane na fragmenty: należy użyć **createrangemapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) klasy RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) z listShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Wiele różnych punktów lub zakresów można odwzorować na ten sam fragment. Te metody mają wpływ tylko na metadane — nie wpływają na żadne dane, które mogą być już obecne we fragmentach. Jeśli dane muszą zostać usunięte z bazy danych, aby były zgodne z **deletemapping** operacji, należy wykonać te operacje oddzielnie, ale w połączeniu z przy użyciu tych metod.  
* Aby podzielić istniejące zakresy na dwa lub scalić sąsiednie zakresy w jeden: użyj **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) i **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Należy zauważyć, że operacje dzielenia i scalania **nie zmieniają fragmentu, na który są mapowane wartości klucza.** Podział dzieli istniejący zakres na dwie części, ale pozostawia zarówno jako mapowane do tego samego fragmentu. Scalanie działa na dwóch sąsiednich zakresach, które są już mapowane na ten sam fragment, scalając je w jeden zakres.  Ruch punktów lub zakresów się między fragmentami musi być koordynowany przy użyciu **UpdateMapping** w połączeniu z rzeczywistego przenoszenia danych.  Za pomocą usługi **Podziel/Scalanie,** która jest częścią narzędzi elastycznej bazy danych, można koordynować zmiany mapy niezależnego fragmentu z przenoszeniem danych, gdy konieczne jest przesunięcie.
* Aby ponownie zamapować (lub przenieść) poszczególne punkty lub zakresy do różnych fragmentów: użyj **updatemapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Ponieważ dane mogą wymagać przeniesienia z jednego niezależnego fragmentu do drugiego, aby były zgodne z operacjami **UpdateMapping,** należy wykonać ten ruch oddzielnie, ale w połączeniu z przy użyciu tych metod.

* Aby przejmować mapowania w trybie online i offline: użyj **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) do kontrolowania stanu online mapowania.
  
    Niektóre operacje mapowania fragmentów są dozwolone tylko wtedy, gdy mapowanie jest w stanie "offline", w tym **UpdateMapping** i **DeleteMapping**. Gdy mapowanie jest w trybie offline, żądanie zależne od danych na podstawie klucza zawartego w tym mapowaniu zwraca błąd. Ponadto po pierwszym przełączeniu zakresu w tryb offline wszystkie połączenia z fragmentem, którego dotyczy problem, są automatycznie zabijane, aby zapobiec niespójnym lub niekompletnym wynikom dla zapytań skierowanych przeciwko zmianie zakresów.

Mapowania są niezmienne obiekty w .Net.  Wszystkie powyższe metody, które zmieniają mapowania również unieważnić wszelkie odwołania do nich w kodzie. Aby ułatwić wykonywanie sekwencji operacji, które zmieniają stan mapowania, wszystkie metody, które zmieniają mapowanie zwracają nowe odwołanie do mapowania, dzięki czemu operacje mogą być połączone łańcuchem. Na przykład, aby usunąć istniejące mapowanie w shardmap sm, który zawiera klucz 25, można wykonać następujące czynności:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Dodawanie fragmentu

Aplikacje często trzeba dodać nowe fragmenty do obsługi danych, które oczekuje się od nowych kluczy lub zakresów kluczy, dla mapy niezależnego fragmentu, który już istnieje. Na przykład aplikacja podzielona na fragmenty przez identyfikator dzierżawy może być konieczne aprowizowanie nowego niezależnego fragmentu dla nowej dzierżawy lub dane podzielonej co miesiąc może wymagać nowego niezależnego fragmentu aprowizacji przed rozpoczęciem każdego nowego miesiąca.

Jeśli nowy zakres wartości klucza nie jest już częścią istniejącego mapowania i nie jest konieczne przenoszenie danych, można łatwo dodać nowy fragment i skojarzyć nowy klucz lub zakres do tego fragmentu. Aby uzyskać szczegółowe informacje na temat dodawania nowych fragmentów, zobacz [Dodawanie nowego fragmentu](sql-database-elastic-scale-add-a-shard.md).

W scenariuszach, które wymagają przenoszenia danych, jednak narzędzie scalania podziału jest potrzebne do organizowania przenoszenia danych między fragmentami w połączeniu z niezbędnymi aktualizacjami mapy niezależnego fragmentu. Aby uzyskać szczegółowe informacje na temat korzystania z narzędzia scalania podziału, zobacz [Omówienie scalania dzielonego](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
