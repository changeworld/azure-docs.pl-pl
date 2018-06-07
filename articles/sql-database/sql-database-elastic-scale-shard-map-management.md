---
title: Skalowania bazy danych Azure SQL | Dokumentacja firmy Microsoft
description: Jak używać ShardMapManager, biblioteki klienta elastycznej bazy danych
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: sstein
ms.openlocfilehash: 7e156142a68b30471646ea3a9181ce7d0097e626
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646997"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skalowanie w poziomie baz danych przy użyciu Menedżera mapy niezależnego fragmentu
Aby łatwe skalowanie bazy danych SQL Azure, użyj Menedżera map niezależnego fragmentu. Menedżer mapy niezależnego fragmentu jest specjalne bazy danych, która przechowuje Mapowanie globalne informacje o wszystkich odłamków (bazy danych) w zestawie niezależnego fragmentu. Metadane umożliwia aplikacji do nawiązania połączenia z poprawną bazą danych na podstawie wartości z **klucza dzielenia na fragmenty**. Ponadto każdy identyfikator niezależnego fragmentu w zestawie zawiera map, które śledzą dane lokalne niezależnego fragmentu (nazywane **shardlets**). 

![Identyfikator niezależnego fragmentu mapy zarządzania](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Zrozumienie, jak te mapowania są konstruowane jest niezbędne do zarządzania mapy niezależnego fragmentu. Jest to realizowane przy użyciu klasy ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), liczba znalezionych w [biblioteki klienta elastycznej bazy danych](sql-database-elastic-database-client-library.md) do zarządzania mapy niezależnego fragmentu.  

## <a name="shard-maps-and-shard-mappings"></a>Mapy niezależnego fragmentu i niezależnego fragmentu mapowania
Dla każdego niezależnego fragmentu należy wybrać typ mapy niezależnego fragmentu, aby utworzyć. Wybór zależy od architektury bazy danych: 

1. Pojedynczej dzierżawy na bazę danych  
2. Wiele dzierżaw dla jednej bazy danych (dwa typy):
   1. Mapowanie list
   2. Mapowanie zakresu

Model pojedynczego dzierżawcy można utworzyć **mapowania listy** mapy niezależnego fragmentu. Model pojedynczej dzierżawy przypisuje jednej bazy danych dla każdego dzierżawcy. Jest to efektywne modelu dla deweloperów SaaS, ponieważ takie rozwiązanie upraszcza zarządzanie.

![Mapowanie list][1]

Modelu wielodostępnym przypisuje kilka dzierżaw do pojedynczej bazy danych (i grup dzierżawcy mogą rozpowszechniają wielu baz danych). Jeśli potrzebujesz każdego dzierżawcy, aby korzystać z niewielkie zbiory danych, należy użyć tego modelu. W tym modelu Przypisz zakres dzierżawcy do bazy danych przy użyciu **mapowanie zakresu**. 

![Mapowanie zakresu][2]

Lub możesz wdrożyć model bazy danych wielu dzierżawców przy użyciu *mapowanie list* można przypisać wielu dzierżawców do pojedynczej bazy danych. Na przykład DB1 jest używany do przechowywania informacji na temat dzierżawy ID 1 i 5 i bazy danych DB2 przechowuje dane dla dzierżawy 7 i dzierżawcy 10. 

![Wiele dzierżaw dla jednej bazy danych][3] 

### <a name="supported-types-for-sharding-keys"></a>Obsługiwane typy kluczy dzielenia na fragmenty
Elastyczne skalowanie obsługuje następujące typy jako klucze dzielenia na fragmenty:

| .NET | Java |
| --- | --- |
| liczba całkowita |liczba całkowita |
| dł. |dł. |
| Identyfikator GUID |Identyfikator UUID |
| byte[]  |byte[] |
| datetime | sygnatura czasowa |
| Zakres czasu | czas trwania|
| Datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapuje niezależnych listy i zakresu
Mapy niezależnych można skonstruować przy użyciu **list dzielenia na fragmenty poszczególne wartości klucza**, lub może być skonstruowany, za pomocą **wartości klucza zakresy dzielenia na fragmenty**. 

### <a name="list-shard-maps"></a>Lista niezależnych mapy
**Odłamków** zawierają **shardlets** i mapowanie shardlets do odłamków jest obsługiwana przez mapy niezależnego fragmentu. A **mapy niezależnego fragmentu listy** jest skojarzenie między poszczególnych wartości kluczy, które identyfikują shardlets i baz danych, które stanowią fragmentów.  **Lista mapowań** kluczowych jawne i innej wartości może być zmapowana do tej samej bazy danych. Na przykład klucz 1 mapuje A bazą danych i wartości kluczy, 3 i 6 odwołania B. bazy danych

| Klucz | Identyfikator niezależnego fragmentu lokalizacji |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| Przyciski ... |Przyciski ... |

### <a name="range-shard-maps"></a>Mapuje niezależnych zakresu
W **mapy niezależnego fragmentu zakresu**, zakresem kluczy jest opisane przez parę **[wartość niska, wysoka wartość)** gdzie *niska wartość* jest klucz minimum zakresu i *wysokiej wartości* jest to pierwsza wartość większą niż zakres. 

Na przykład **[0, 100)** obejmuje wszystkie liczby całkowite większe niż lub równa 0 i mniejsza niż 100. Należy pamiętać, że wielu zakresów może wskazywać tę samą bazę danych, a zakresy rozłącznych są obsługiwane (na przykład [100,200) i [400,600) wskazują C bazy danych w poniższym przykładzie.)

| Klucz | Identyfikator niezależnego fragmentu lokalizacji |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| Przyciski ... |Przyciski ... |

Każdej z tabel wymienionych powyżej to przykład koncepcyjnej **ShardMap** obiektu. Każdy wiersz jest uproszczony przykład osoba **PointMapping** (dla listy map niezależnego fragmentu) lub **RangeMapping** (dla zakresu map niezależnego fragmentu) obiektu.

## <a name="shard-map-manager"></a>Menedżer mapy niezależnego fragmentu
W bibliotece klienta Menedżera map niezależnego fragmentu to kolekcja map niezależnego fragmentu. Danych zarządzanych przez **ShardMapManager** wystąpienia jest przechowywany w trzech miejscach: 

1. **Globalne mapy niezależnego fragmentu (GSM)**: Określ bazę danych jako repozytorium dla wszystkich map niezależnego fragmentu i mapowania. Specjalne tabele i procedury składowane są tworzone automatycznie do zarządzania informacjami. Zazwyczaj jest mała baza danych, a lekkim dostępne i nie powinna być używana na potrzeby innych aplikacji. Tabele są w schemacie specjalne o nazwie **__ShardManagement**. 
2. **Lokalny identyfikator niezależnego fragmentu mapy (LSM) tak**: każdej bazy danych przez użytkownika jako niezależnego fragmentu są modyfikowane w celu zawiera kilka tabel małe i specjalnych procedur składowanych, zawierające i zarządzanie niezależnego fragmentu mapy informacje specyficzne dla tego niezależnego fragmentu. Te informacje jest nadmiarowy wraz z informacjami w GSM i umożliwia aplikacji weryfikowanie niezależnych buforowanych informacji mapy bez wprowadzania żadnych obciążenia na GSM; Aplikacja używa LSM w celu ustalenia, czy mapowanie pamięci podręcznej jest nadal ważny. Tabele odpowiadający LSM na każdym niezależnego fragmentu są również w schemacie **__ShardManagement**.
3. **Pamięci podręcznej aplikacji**: każda aplikacja wystąpienia dostęp do **ShardMapManager** obiekt zachowuje lokalnej pamięci podręcznej w pamięci z jego mapowanie. Przechowuje informacje routingu, która została ostatnio pobrana. 

## <a name="constructing-a-shardmapmanager"></a>Konstruowanie ShardMapManager
A **ShardMapManager** obiekt jest tworzony przy użyciu fabryki ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) wzorca. **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoda przyjmuje poświadczenia (w tym nazwę serwera i nazwę bazy danych zawierający GSM) formę **ConnectionString** i zwraca wystąpienie klasy **ShardMapManager**.  

**Uwaga:** **ShardMapManager** były tworzone tylko raz dla domeny aplikacji w kodzie inicjowania aplikacji. Tworzenie wystąpień dodatkowe ShardMapManager w tej samej domenie aplikacji powoduje znacznie większą ilość pamięci i użycie procesora CPU przez aplikację. A **ShardMapManager** może zawierać dowolną liczbę niezależnych mapy. Mapa jednego niezależnego fragmentu może być wystarczające dla wielu aplikacji, istnieją momenty, gdy różne zestawy baz danych są używane do innego schematu lub do celów unikatowe; w takich przypadkach może być preferowana wielu map niezależnego fragmentu. 

W tym kodzie aplikacja próbuje otworzyć istniejące **ShardMapManager** z TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metody. Jeśli obiekty reprezentujące Global **ShardMapManager** (GSM) nie zostały jeszcze istnieje w bazie danych, Biblioteka klienta tworzy je tam przy użyciu CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET ](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metody.

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

Dla programu .NET w wersji można utworzyć nowego Menedżera mapy niezależnego fragmentu za pomocą programu PowerShell. Przykład jest dostępny [tutaj](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Pobierz RangeShardMap lub ListShardMap
Po utworzeniu niezależnych menedżera map, możesz uzyskać RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) lub ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) przy użyciu TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), lub GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metody.

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

### <a name="shard-map-administration-credentials"></a>Poświadczenia administrowania niezależnego fragmentu mapy
Aplikacje, które administrowania i manipulowania mapy niezależnego fragmentu różnią się od używające map niezależnego fragmentu do połączeń trasy. 

Do administrowania mapy niezależnego fragmentu (Dodawanie lub zmienianie odłamków, mapy niezależnego fragmentu, niezależnego fragmentu mapowania, itp.) musi utworzyć wystąpienia **ShardMapManager** przy użyciu **odczytu/zapisu poświadczenia, które mają uprawnienia w bazie GSM i w każdej bazie danych, która służy jako identyfikator niezależnego fragmentu**. Poświadczenia muszą zezwalać na dla operacji zapisu dla tabel w GSM i LSM niezależnego fragmentu mapy informacji jest wprowadzona lub zmienić również podobnie jak w przypadku tworzenia tabel LSM na nowych fragmentów.  

Zobacz [poświadczenia umożliwiające dostęp do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Tylko metadane, których to dotyczy
Metody używane do zapełniania lub zmiany **ShardMapManager** danych nie należy zmieniać dane użytkownika przechowywane w odłamków, samodzielnie. Na przykład metod, takich jak **CreateShard**, **DeleteShard**, **UpdateMapping**, itd. wpływa na tylko metadane mapy niezależnego fragmentu. Nie należy usuwać, Dodaj lub alter zawarte w liczbie fragmentów danych użytkownika. Zamiast tego te metody są przeznaczone do użycia w połączeniu z oddzielnych operacje, które należy wykonać w celu utworzenia lub usuń rzeczywiste baz danych, lub że przenoszeniu wierszy z jednego niezależnego fragmentu do rebalance podzielonej środowiska.  ( **Scalania podziału** narzędzia dołączonego do narzędzi elastycznej bazy danych korzysta z poniższych interfejsów API oraz organizowanie rzeczywiste przeniesienie danych między odłamków.) Zobacz [odbierającej za pomocą narzędzia do scalania podziału elastycznej bazy danych](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Routing zależny od danych
Menedżer mapy niezależnego fragmentu jest używany w aplikacjach, które wymagają połączenia z bazą danych w celu wykonania operacji dane specyficzne dla aplikacji. Te połączenia musi być skojarzony z poprawną bazą danych. Jest to nazywane **danych zależnych routingu**. Dla tych aplikacji tworzy obiekt menedżera niezależnego fragmentu mapy z fabryki przy użyciu poświadczeń, które mają dostęp tylko do odczytu w bazie danych GSM. Poszczególnych żądań w połączeniach nowszej Podaj poświadczenia niezbędne do łączenia z bazą danych odpowiedni identyfikator niezależnego fragmentu.

Należy pamiętać, że te aplikacje (przy użyciu **ShardMapManager** otwarty przy użyciu poświadczeń tylko do odczytu) nie można zmienić mapowania i mapy. W przypadku tych potrzeb utworzyć administracyjne dotyczące aplikacji i skryptów programu PowerShell, zapewniających poświadczenia z niskimi uprawnieniami, zgodnie z wcześniejszym opisem. Zobacz [poświadczenia umożliwiające dostęp do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

Aby uzyskać więcej informacji, zobacz [danych zależnych routingu](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Modyfikowanie mapy niezależnego fragmentu
Mapa niezależnych można zmienić na różne sposoby. Wszystkie z następujących metod modyfikowania metadane opisujące odłamków i ich mapowań, ale ich nie należy fizycznie modyfikować danych w ramach odłamków ani ich tworzyć ani nie usuwaj bazy danych rzeczywistych.  Niektóre z tych operacji na mapie niezależnego fragmentu opisanych poniżej może być konieczne z działania administracyjne, które fizycznie przenieść dane lub dodać i usunąć bazy danych służy jako niezależne.

Te metody współdziałają ze sobą jako bloków konstrukcyjnych dostępne modyfikowania ogólną rozkład danych w środowisku podzielonej bazy danych.  

* Aby dodać lub usunąć odłamków: Użyj **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) i **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)) z Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) klasy. 
  
    Serwer i reprezentujący niezależnych docelowej bazy danych musi już istnieć na wykonywanie tych operacji. Te metody nie mają wpływu na samych bazach danych, tylko na metadanych w mapie niezależnego fragmentu.
* Tworzenie lub usuwanie punktów lub zakresy, które są mapowane na odłamków: Użyj **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)) z RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) klasy, a **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)) z ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) klasy.
  
    Wiele różnych punktach lub zakresy mogą być mapowane na sam identyfikator niezależnego fragmentu. Te metody mają wpływ tylko na metadanych — nie wpływają na dane, które mogą już być obecne w fragmentów. Jeśli dane mają być usunięte z bazy danych, aby były spójne z **DeleteMapping** operacje, wykonywać te operacje oddzielnie, ale w połączeniu z przy użyciu tych metod.  
* Podziel istniejących zakresów na dwa lub scalanie sąsiadujących zakresów w jednym: Użyj **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) i **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Należy pamiętać, że dzielenie i scalanie operacji **nie należy zmieniać niezależnego fragmentu, do którego wartości klucza są mapowane**. Podział dzieli istniejący zakres na dwie części, ale pozostawia zarówno jako mapowany na sam identyfikator niezależnego fragmentu. Scalanie działa na dwóch sąsiadujących zakresów, które już są mapowane na sam identyfikator niezależnego fragmentu, łączenie ich do jednego zakresu.  Przenoszenie punktów lub zakresy się między odłamków musi koordynowane przy użyciu **UpdateMapping** w połączeniu z rzeczywiste przeniesienie danych.  Można użyć **podziału/Merge** usługi będący częścią narzędzi elastycznej bazy danych do koordynowania niezależnego fragmentu mapy zmian z przepływu danych, gdy potrzebny jest przepływu. 
* Mapowane ponownie (lub Przenieś) poszczególnych punktów lub zakresów do fragmentów różne: Użyj **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Ponieważ danych może być konieczne można przenosić z jednego niezależnego fragmentu do innego, aby były spójne z **UpdateMapping** operacji, należy wykonać ten przepływ oddzielnie, ale w połączeniu z przy użyciu tych metod.
* Podjęcie mapowania online i offline: Użyj **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) i **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) do sterowania stanem online mapowania. 
  
    Niektóre operacje na niezależnego fragmentu mapowania są dozwolone tylko podczas mapowania jest w stanie "offline", w tym **UpdateMapping** i **DeleteMapping**. Podczas mapowania jest w trybie offline, żądanie zależne od danych oparte na kluczu zawarte w tym mapowania zwraca błąd. Ponadto gdy zakres jest najpierw przełączona w tryb offline, wszystkie połączenia z odpowiednim niezależnego fragmentu są automatycznie skasowane zapobiec niespójne lub niekompletne wyniki zapytania skierowanej zakresy zmieniane. 

Mapowania są niezmienne obiekty w środowisku .net.  Wszystkie metody powyżej, które spowodują zmianę mapowania również unieważnienie odwołań do ich w kodzie. Aby ułatwić wykonać sekwencje działań, które spowodują zmianę stanu mapowania, wszystkie metody, które spowodują zmianę mapowania zwrócenie nowe odwołanie mapowania w celu operacje można powiązać. Na przykład aby usunąć istniejące mapowanie w sm shardmap, który zawiera klucz 25, można wykonywać następujące czynności: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Dodawanie niezależnego fragmentu
Aplikacje często konieczne dodanie nowych odłamków do obsługi danych, która oczekuje się od nowych kluczy lub kluczy zakresów, mapy niezależnego fragmentu, która już istnieje. Na przykład aplikację podzielonej na podstawie Identyfikatora dzierżawcy może być konieczne obsługi administracyjnej nowych niezależnych dla nowej dzierżawy lub co miesiąc podzielonej danych może być konieczne nowy identyfikator niezależnego fragmentu udostępnione przed rozpoczęciem każdego nowego miesiąca. 

Jeśli nowy zakres wartości klucza nie jest już częścią istniejące mapowanie i niezbędne jest nie przenoszenia danych, jest proste dodać nowy identyfikator niezależnego fragmentu i Skojarz nowy klucz lub zakresu do tego niezależnego fragmentu. Aby uzyskać więcej informacji na temat dodawania nowych fragmentów, zobacz [Dodawanie nowych niezależnego fragmentu](sql-database-elastic-scale-add-a-shard.md).

W scenariuszach, wymagających przenoszenia danych jednak narzędzia do scalania podziału jest wymagane do organizowania przenoszenia danych między odłamków w połączeniu z aktualizacjami konieczne niezależnych mapy. Aby uzyskać więcej informacji na temat używania narzędzia do scalania podziału, zobacz [omówienie scalania podziału](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
