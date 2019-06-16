---
title: Skalowanie w poziomie bazą danych Azure SQL database | Dokumentacja firmy Microsoft
description: Jak używać ShardMapManager, biblioteka kliencka elastic database
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
ms.openlocfilehash: a9c857ab9e9a3cfc0d1314600b612c4e6293173d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332317"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skalowanie w poziomie bazy danych z Menedżera map fragmentów

Aby łatwo skalować w poziomie bazy danych SQL Azure, użyj Menedżera mapowań fragmentów. Menedżera mapowań fragmentów to specjalne bazy danych, która utrzymuje Mapowanie globalne informacje o wszystkich fragmentów (baz danych) w zestawie fragmentu. Metadane temu aplikacja może nawiązać połączenie z odpowiednią bazą danych na podstawie wartości **klucz fragmentowania**. Ponadto każdy fragment w zestawie zawiera mapowania, które śledzą dane lokalne fragmentów (nazywane **podfragmentów**).

![Zarządzanie mapami fragmentów](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Zrozumienie, jak te mapowania są konstruowane jest niezbędne do procesu zarządzania mapą fragmentów. Odbywa się przy użyciu klasy ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)znajdujące się w [Biblioteka kliencka Elastic Database](sql-database-elastic-database-client-library.md) Zarządzanie mapowań fragmentów.  

## <a name="shard-maps-and-shard-mappings"></a>Mapowania fragmentów i map fragmentów

Dla każdego fragmentu musisz wybrać typ mapowania fragmentów do utworzenia. Wybór zależy od architektury bazy danych:

1. Pojedynczej dzierżawy na bazę danych  
2. Wiele dzierżaw na bazę danych (dwóch typów):
   1. Mapowanie list
   2. Mapowanie zakresu

W przypadku modelu jednej dzierżawy, utworzyć **mapowanie list** mapowania fragmentów. Modelu jednej dzierżawy przypisze jedną bazę danych na dzierżawę. Jest to skuteczne modelu dla deweloperów SaaS, ponieważ upraszcza zarządzanie.

![Mapowanie list][1]

Modelu wielodostępnym przypisuje kilka dzierżaw poszczególnych baz danych (i grup dzierżawców można rozpowszechniać w wielu bazach danych). Jeśli oczekujesz, że każdego dzierżawcy, aby korzystać z danych w małych, należy użyć tego modelu. W tym modelu należy przypisać określonego zakresu dzierżaw, bazę danych za pomocą **mapowania zakresu**.

![Mapowanie zakresu][2]

Lub możesz zaimplementować modelu wielodostępną bazą danych przy użyciu *mapowanie list* można przypisać wielu dzierżaw do poszczególnych baz danych. Na przykład DB1 służy do przechowywania informacji o dzierżawie ID 1 i 5, a bazy danych DB2 przechowuje dane dla dzierżawy 7 i dzierżawy 10.

![Wielu dzierżaw w pojedynczej bazy danych][3]

### <a name="supported-types-for-sharding-keys"></a>Obsługiwane typy kluczy fragmentowania

Elastyczne skalowanie obsługuje następujące typy jako klucze dzielenia na fragmenty:

| .NET | Java |
| --- | --- |
| liczba całkowita |liczba całkowita |
| long |long |
| Identyfikator GUID |uuid |
| byte[]  |byte[] |
| datetime | timestamp |
| TimeSpan | Czas trwania|
| Datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapowań fragmentów w postaci listy i zakresu

Mapowań fragmentów w postaci można skonstruować przy użyciu **list fragmentowania poszczególne wartości klucza**, lub może być tworzona, przy użyciu **wartości klucza zakresów fragmentowania**.

### <a name="list-shard-maps"></a>Mapowań fragmentów w postaci listy

**Fragmenty** zawierają **podfragmentów** i mapowanie podfragmentów do fragmentów jest obsługiwana przez mapowania fragmentów w postaci. A **mapowanie fragmentów w postaci listy** jest skojarzeniem między poszczególne wartości kluczy, które identyfikują podfragmentach i baz danych, które służą jako fragmenty.  **Lista mapowań** mają jawne i różne kluczowe wartości mogą zostać zmapowane do tej samej bazy danych. Na przykład wartość klucza 1 mapuje A bazy danych i wartości klucza, 3 i 6 zarówno mapy do bazy danych B.

| Klucz | Lokalizacja fragmentu |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| Przyciski ... |Przyciski ... |

### <a name="range-shard-maps"></a>Zakres dzielenie map na fragmenty

W **mapowania fragmentów w zakresie**, zakres kluczy jest opisana przez parę **[wartość niska, wysoka wartość)** gdzie *niska wartość* jest klucz minimum zakresu i *wysoka Wartość* jest wyższy niż zakres to pierwsza wartość.

Na przykład **[0, 100)** obejmuje wszystkie liczby całkowite większy lub równy 0 i mniejsza niż 100. Należy zauważyć, że wiele zakresów można wskazać w tej samej bazy danych, a zakresy rozłączne są obsługiwane (na przykład [100,200) i [400,600) wskazują bazy danych w języku C w poniższym przykładzie.)

| Klucz | Lokalizacja fragmentu |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| Przyciski ... |Przyciski ... |

Każdej z tabel przedstawionych powyżej jest koncepcyjny przykładem **ShardMap** obiektu. Każdy wiersz jest uproszczony przykład osoba **PointMapping** (w przypadku mapowania fragmentów w listy) lub **RangeMapping** (w przypadku mapowania fragmentów zakres) obiektu.

## <a name="shard-map-manager"></a>Menedżera map fragmentów

W bibliotece klienta Menedżera mapowań fragmentów jest kolekcją mapowań fragmentów. Danych zarządzanych przez **ShardMapManager** wystąpienia są przechowywane w trzech miejscach:

1. **Mapowanie fragmentów w postaci globalnych (GSM)** : Należy określić bazę danych jako repozytorium dla wszystkich mapowań fragmentów i mapowania. Specjalne tabel i procedur składowanych są tworzone automatycznie do zarządzania informacjami. Zazwyczaj jest małej bazy danych, a niezbyt często używane i nie należy jej używać do innych potrzeb aplikacji. Tabele są w schemacie specjalne o nazwie **__ShardManagement**.
2. **Mapowanie fragmentów w postaci lokalnej (LSM) tak,** : Każda baza danych, wskazanym jako fragment zostanie zmodyfikowany na potrzeby zawiera kilka małe tabele i szczególnych procedur przechowywanych, które zawierają i zarządzanie nimi specyficzne dla tego fragmentu informacji mapowania fragmentów. Niniejsze informacje mają charakter nadmiarowe informacje w usłudze GSM i umożliwia aplikacji weryfikowanie informacji mapowania fragmentów w pamięci podręcznej bez wprowadzania żadnych obciążenia na GSM; Aplikacja używa LSM, aby ustalić, czy mapowanie pamięci podręcznej jest nadal prawidłowa. Tabele odpowiadający LSM w poszczególnych fragmentach znajdują się również w schemacie **__ShardManagement**.
3. **Pamięć podręczną aplikacji**: Każda aplikacja wystąpienia dostęp do **ShardMapManager** obiekt zachowuje lokalnej pamięci podręcznej w pamięci jego mapowań. Przechowuje informacje routingu, który niedawno został pobrany.

## <a name="constructing-a-shardmapmanager"></a>Konstruowanie ShardMapManager

A **ShardMapManager** obiekt jest konstruowany przy użyciu ustawień fabrycznych ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) wzorca. **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoda przyjmuje poświadczenia (w tym nazwę serwera i nazwę bazy danych, zawierający GSM) rodzaj **ConnectionString** i zwraca wystąpienie **ShardMapManager**.  

**Uwaga:** **ShardMapManager** były tworzone tylko raz dla domeny aplikacji, w kodzie inicjowania aplikacji. Tworzenie dodatkowych wystąpień ShardMapManager w tej samej domenie aplikacji powoduje znacznie większą ilość pamięci i procesora aplikacji. A **ShardMapManager** może zawierać dowolną liczbę mapowań fragmentów. Mapa pojedynczy fragment może być wystarczające dla wielu aplikacji, są razy, gdy różne zestawy baz danych są używane do innego schematu lub do celów unikatowy; w takich przypadkach wiele mapowań fragmentów może być korzystniejsze.

W tym kodzie aplikacja próbuje otworzyć istniejący **ShardMapManager** z TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metody. Jeśli obiekty reprezentujące Global **ShardMapManager** (GSM) nie obsługują jeszcze istnieje w bazie danych, Biblioteka klienta tworzy je przy użyciu CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [platformy.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) Metoda.

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

Dla wersji platformy .NET można użyć programu PowerShell do utworzenia nowego Menedżera mapowań fragmentów. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Pobierz RangeShardMap lub ListShardMap

Po utworzeniu fragmentu menedżera map, możesz uzyskać RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) lub ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) przy użyciu TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), lub GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metody.

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

### <a name="shard-map-administration-credentials"></a>Poświadczenia administracyjne mapy fragmentów

Aplikacje, służących do administrowania i manipulowania mapowań fragmentów w postaci różnią się od tych, które korzystają z mapowań fragmentów w postaci do połączeń trasy.

Aby administrować mapowań fragmentów w postaci (Dodawanie lub zmienianie fragmentów, mapowań fragmentów w postaci, mapowania fragmentów, itp.) trzeba utworzyć **ShardMapManager** przy użyciu **odczytu/zapisu na poświadczenia, które mają uprawnienia na bazie usługi GSM i na każdym bazy danych, która służy jako fragmentu**. Poświadczenia muszą zezwalać na operacje zapisu dla tabel w usłudze GSM i LSM zgodnie z wprowadzaniem lub zmienione, a także jak w przypadku tworzenia tabel LSM na nowych fragmentów informacji mapowania fragmentów.  

Zobacz [poświadczenia umożliwiają dostęp do biblioteki klienckiej Elastic Database](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Tylko metadane, których to dotyczy

Metody używane do wypełniania lub zmiana **ShardMapManager** dane nie zostaną zmienione dane użytkownika przechowywane we fragmentach, samodzielnie. Na przykład metody takie jak **CreateShard**, **DeleteShard**, **UpdateMapping**itd wpływa na tylko metadane mapy fragmentów. Nie usuwaj, dodać ani zmienić dane użytkownika zawartych we fragmentach. Zamiast tego te metody są przeznaczone do użycia w połączeniu z osobne operacje, które należy wykonać, aby utworzyć lub usuń rzeczywiste baz danych, lub że przenoś wiersze z jednego fragmentu do innej, aby ponownie zrównoważyć środowisku podzielonej na fragmenty.  ( **Dzielenia i scalania** narzędzie dołączane za pomocą narzędzi elastycznych baz danych korzysta z tych interfejsów API oraz organizowanie rzeczywiste przeniesienie danych między fragmentami.) Zobacz [skalowanie przy użyciu narzędzia do dzielenia i scalania Elastic Database](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Routing zależny od danych

Menedżera mapowań fragmentów jest używany w aplikacjach, które wymagają połączenia z bazą danych do wykonywania operacji dane specyficzne dla aplikacji. Te połączenia musi być skojarzony z odpowiednią bazą danych. Jest to nazywane **Routing zależny od danych**. W przypadku tych aplikacji Utwórz wystąpienie obiektu Menedżera mapy fragmentów z fabryki przy użyciu poświadczeń, które mają dostęp tylko do odczytu w bazie danych usługi GSM. Poszczególnych żądań połączeń nowsze podać poświadczenia niezbędne do łączenia z bazą danych odpowiedniego fragmentu.

Należy pamiętać, że aplikacje te (przy użyciu **ShardMapManager** otwarty przy użyciu poświadczeń tylko do odczytu) nie może wprowadzać zmian w społeczności maps lub mapowania. Te potrzeby utwórz administracyjnych określonych aplikacji lub skryptów programu PowerShell, które wprowadzać poświadczeń wyższych uprawnieniach, zgodnie z wcześniejszym opisem. Zobacz [poświadczenia umożliwiają dostęp do biblioteki klienckiej Elastic Database](sql-database-elastic-scale-manage-credentials.md).

Aby uzyskać więcej informacji, zobacz [routing zależny od danych](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modyfikowanie mapowania fragmentów w postaci

Można zmienić mapowania fragmentów w postaci na różne sposoby. Wszystkie z następujących metod modyfikowania metadane opisujące fragmenty i ich mapowań, ale są fizycznie modyfikują dane w ramach fragmenty, ani mogą tworzyć ani nie usuwaj rzeczywiste baz danych.  Niektóre operacje na mapowania fragmentów, w opisany poniżej może być konieczne z działania administracyjne, które fizycznym przeniesieniu danych lub, dodawanie i usuwanie baz danych, służąc jako fragmenty.

Te metody współpracują ze sobą jako bloków konstrukcyjnych dostępne do modyfikowania ogólny rozkład danych w środowisku bazy danych podzielonej na fragmenty.  

* Aby dodać lub usunąć fragmentów: Użyj **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) i **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) z shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)) klasy.
  
    Serwer i bazę danych, reprezentujący fragmentu docelowy musi już istnieć dla tych operacji do wykonania. Te metody nie ma żadnego wpływu na samych bazach danych, tylko na metadanych w ramach mapowania fragmentów.
* Aby utworzyć lub usunąć punkty lub zakresy, które są mapowane na fragmenty: Użyj **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) z RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) klasy, a **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) z ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) klasy.
  
    Wiele różnych punktach lub zakresy mogą być mapowane na tym samym fragmencie. Metody te dotyczą tylko metadane — nie wpływają na wszystkie dane, które mogą już być obecne we fragmentach. Jeśli dane muszą zostać usunięte z bazy danych, aby były zgodne z **DeleteMapping** operacji, wykonywać te operacje oddzielnie, ale w połączeniu z przy użyciu tych metod.  
* Dzielenie na dwie kolumny istniejących zakresów lub scalić sąsiednich zakresach w jednym: Użyj **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) i **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Należy zauważyć, że dzielenie i scalanie operacje **nie zmieniaj fragmentów, do której są mapowane wartości klucza**. Podział dzieli istniejący zakres na dwie części, ale pozostawia zarówno jako mapowane na tym samym fragmencie. Scalanie działa w dwóch sąsiednich zakresach, które już są mapowane na tym samym fragmencie, łączenie ich w jednym zakresem.  Przemieszczanie punkty lub zakresy się między fragmentami musi być koordynowane za pomocą **UpdateMapping** w połączeniu z rzeczywiste przeniesienie danych.  Możesz użyć **dzielenia i scalania** usługi oznacza to część pakietu narzędzi elastycznej bazy danych do koordynowania zmiany mapy fragmentów za pomocą przenoszenia danych, gdy przepływ będzie potrzebny.
* Ponowne mapowanie (lub Przenieś) poszczególnych punktów lub zakresy do różnych fragmentów: Użyj **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Ponieważ dane mogą muszą zostać przeniesiona z jednego fragmentu do innego, aby były zgodne z **UpdateMapping** operacji, trzeba wykonywać tego przepływu, oddzielnie, ale w połączeniu z przy użyciu tych metod.

* Aby móc mapowania online i offline: Użyj **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) i **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) do sterowania stanem online mapowania.
  
    Niektóre operacje na mapowania fragmentów są dozwolone tylko, gdy mapowanie jest w stanie "offline", w tym **UpdateMapping** i **DeleteMapping**. Żądanie zależne od danych na podstawie objęte mapowania klucza zwraca błąd, gdy mapowanie jest w trybie offline. Ponadto gdy zakres jest najpierw przełączony w tryb offline, wszystkich połączeń, do których to dotyczy fragmentu są automatycznie zabite aby zapobiec niespójne lub niekompletne wyniki zapytań skierowany przeciwko zakresów zmieniany.

Mapowania są niezmienne obiektów na platformie .net.  Unieważnienie wszystkich powyższych metod, które zmieniają mapowania również wszelkie odwołania do nich w kodzie. Aby ułatwić wykonania sekwencji operacji, które zmieniają stan mapowania, wszystkie metody, które zmieniają się mapowanie zwrócenie nowe odwołanie mapowania w celu operacje można łączyć. Na przykład aby usunąć istniejące mapowanie programie sm shardmap, który zawiera klucz 25, można wykonywać następujące czynności:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Dodawanie fragmentu

Aplikacje często konieczne dodanie nowych fragmentów do obsługi danych, która oczekuje się od nowych kluczy lub kluczy zakresy, mapy fragmentów, która już istnieje. Na przykład może być konieczne aprowizowanie nowych fragmentów dla nowej dzierżawy aplikacji podzielonej na fragmenty według Identyfikatora dzierżawy lub miesięczny podzielonej na fragmenty danych może wymagać nowego fragmentu aprowizowane przed rozpoczęciem każdego nowego miesiąca.

Jeśli nowy zakres wartości klucza nie jest już częścią istniejącego mapowania i nie przenoszenie danych jest niezbędne, jest prosty do dodawania nowych fragmentów i Skojarz nowy klucz lub zakresu do tego fragmentu. Aby uzyskać więcej informacji na temat dodawania nowych fragmentów, zobacz [Dodawanie nowych fragmentów](sql-database-elastic-scale-add-a-shard.md).

W scenariuszach, które wymagają przenoszenia danych jednak narzędzie do dzielenia i scalania jest potrzebny do aranżowania przenoszenia danych między fragmentami w połączeniu z aktualizacjami mapy fragmentów niezbędne. Aby uzyskać więcej informacji na temat korzystania z narzędzia do dzielenia i scalania, zobacz [Przegląd dzielenia i scalania](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
