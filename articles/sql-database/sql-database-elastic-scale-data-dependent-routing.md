---
title: Dane zależne routingu w usłudze Azure SQL Database | Dokumentacja firmy Microsoft
description: Sposób użycia klasy ShardMapManager w aplikacjach .NET dla danych zależne od routingu, funkcja podzielonej baz danych w bazie danych SQL Azure
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 715b6e55b053b3f999f3bd938c14d72a8e20ad1a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646885"
---
# <a name="data-dependent-routing"></a>Routing zależny od danych
**Dane zależne routingu** jest możliwość użycia danych w zapytaniu można przekierować żądania do odpowiedniej bazy danych. Podczas pracy z bazy danych podzielonej jest wzorzec podstawowych. Kontekst żądania może również trasy żądania, zwłaszcza, jeśli klucz dzielenia na fragmenty nie jest częścią zapytania. Każdej określonej kwerendy lub transakcji w aplikacji przy użyciu routingu zależnych danych jest ograniczone do uzyskiwania dostępu do pojedynczej bazy danych na żądanie. Dla narzędzi elastycznej bazy danych SQL Azure to routingu jest realizowane za pomocą **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)) klasy.

Aplikacji nie musi śledzić różne parametry połączenia lub skojarzone z inną wycinków danych w środowisku podzielonej lokalizacji bazy danych. Zamiast tego [Menedżera Map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) otwiera połączenia z poprawną baz danych, w razie potrzeby na podstawie danych w planie niezależnego fragmentu i wartość klucza dzielenia na fragmenty, który jest miejscem docelowym żądania aplikacji. Klucz jest zwykle *customer_id*, *tenant_id*, *date_key*, lub niektóre określonym identyfikatorze, który jest parametrem podstawowym żądania bazy danych. 

Aby uzyskać więcej informacji, zobacz [skalowanie limit programu SQL Server z routingiem zależnych danych](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Pobierz biblioteki klienta
Aby pobrać:
* wersja języka Java biblioteki, zobacz [Maven centralnym repozytorium](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET w wersji biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Przy użyciu ShardMapManager w danych zależnych aplikacji routingu
Aplikacje powinny wystąpienia **ShardMapManager** podczas inicjowania przy użyciu wywołania fabryki **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)). W tym przykładzie zarówno **ShardMapManager** i określony **ShardMap** zawierający są zainicjowane. W tym przykładzie pokazano GetSqlShardMapManager i GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/dn824173.aspx)) metod.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Użyj najniższy poświadczeń uprawnień możliwe do uzyskania mapy niezależnego fragmentu
Jeśli aplikacja nie jest manipulowanie mapy niezależnego fragmentu, poświadczenia używane w metodzie fabryki powinni mieć uprawnienia tylko do odczytu **globalne mapy niezależnego fragmentu** bazy danych. Te poświadczenia różnią się zwykle z poświadczeniami używanymi do otwarcia połączenia z menedżerem mapy niezależnego fragmentu. Zobacz też [poświadczenia umożliwiające dostęp do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md). 

## <a name="call-the-openconnectionforkey-method"></a>Wywołaj metodę OpenConnectionForKey
**Metody ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkey), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)) zwraca gotowe do wydania poleceń odpowiednią bazę danych na podstawie wartości z połączeniem **klucza** parametru. Identyfikator niezależnego fragmentu informacje są buforowane w aplikacji przez **ShardMapManager**, więc te żądania nie obejmują zazwyczaj wyszukiwania w bazie danych przed **globalne mapy niezależnego fragmentu** bazy danych. 

```Java
// Syntax: 
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax: 
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```
* **Klucza** parametr jest używany jako klucz wyszukiwania w planie niezależnego fragmentu ustalenie odpowiedniej bazy danych dla żądania. 
* **ConnectionString** używany do przekazywania tylko poświadczenia użytkownika dla żądanego połączenia. Nie nazwy bazy danych lub nazwa serwera jest zawarta w to *connectionString* ponieważ metoda pozwala bazy danych i serwera przy użyciu **ShardMap**. 
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._connection_options), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)) powinien być ustawiony na **ConnectionOptions.Validate** Jeśli środowisku, gdzie niezależnego fragmentu mapuje maja zmiany i wiersze mogą przenosić do innych baz danych w wyniku operacji dzielenie i scalanie. Obejmuje to krótkie zapytania do mapy niezależnych lokalnych w celu bazy danych (nie do mapy niezależnych globalne) przed połączenia jest dostarczany do aplikacji. 

W przypadku niepowodzenia weryfikacji względem na mapie lokalnych niezależnego fragmentu (co oznacza, że pamięć podręczna jest nieprawidłowe) Manager mapy niezależnego fragmentu zapytania mapy globalne niezależnych uzyskania nowego poprawną wartość do wyszukiwania, zaktualizuj pamięć podręczną i uzyskać i zwracać połączenie z odpowiednią bazą danych . 

Użyj **ConnectionOptions.None** tylko podczas zmiany mapowania niezależnego fragmentu nie powinny podczas, gdy aplikacja jest w trybie online. W takim przypadku buforowane wartości można założyć, że zawsze być poprawne, a następnie bezpiecznie pominięte wywołania bardzo obustronne weryfikacji do docelowej bazy danych. Która zmniejsza ruch bazy danych. **ConnectionOptions** również mogą zostać ustawione za pomocą wartości, w pliku konfiguracji, aby wskazać, czy zmiany dzielenia na fragmenty są oczekiwane lub nie w okresie czasu.  

W tym przykładzie użyto wartości klucza **CustomerID**za pomocą **ShardMap** obiektu o nazwie **customerShardMap**.  

```Java 
int customerId = 12345; 
int productId = 4321; 
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

// Connect to the shard for that customer ID. No need to call a SqlConnection 
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
{ 
    // Execute a simple command. 
    SqlCommand cmd = conn.CreateCommand(); 
    cmd.CommandText = @"UPDATE Sales.Customer 
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID"; 

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
    cmd.ExecuteNonQuery(); 
}  
```

**OpenConnectionForKey** metoda zwraca nowe połączenie już otwarty z poprawną bazą danych. Połączenia używane w ten sposób nadal w pełni korzystać z puli połączeń. 

**Metody OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper._list_shard_mapper.openconnectionforkeyasync), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)) jest również dostępna w przypadku aplikacji sprawia, że użycie programowania asynchronicznego.

## <a name="integrating-with-transient-fault-handling"></a>Integrowanie z obsługi błędu przejściowego
Upewnij się, że błędów przejściowych są przechwytywane przez aplikację i operacje są zwalniane kilka razy przed generowaniem błędu jest najlepszym rozwiązaniem w tworzeniu aplikacji dostęp do danych w chmurze. Wystąpienia błędu przejściowego obsługi dla aplikacji w chmurze została szczegółowo opisana w obsługi błędów przejściowych ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://msdn.microsoft.com/library/dn440719\(v=pandp.60\).aspx)). 

Obsługa błędu przejściowego mogą współistnieć naturalnie z wzorcem danych zależnych routingu. Decydujące znaczenie jest, aby ponowić próbę, w tym żądania dostępu do danych **przy użyciu** bloku uzyskiwanej połączenia routingu zależne od danych. Ponownie poprzedniego przykładu można zapisać w następujący sposób. 

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Przykład — danych zależnych routingu z obsługą błędu przejściowego
```Java 
int customerId = 12345; 
int productId = 4321; 
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345; 
int newPersonId = 4321; 

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt; 
{
    // Connect to the shard for a customer ID. 
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command 
        SqlCommand cmd = conn.CreateCommand(); 

        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 

        Console.WriteLine("Update completed"); 
    } 
}); 
```

Pakiety, które należy wykonać, Obsługa błędu przejściowego są pobierane automatycznie podczas tworzenia aplikacji przykładowej elastycznej bazy danych. 

## <a name="transactional-consistency"></a>Spójności transakcyjnej
Transakcyjne właściwości dotrą do wszystkich operacji lokalne niezależnego fragmentu. Na przykład transakcji przesłane za pośrednictwem routingu zależne od danych wykonywania w zakresie niezależnych docelowy dla połączenia. W tej chwili jest nie możliwości przewidzianych rejestrowanie wiele połączeń w transakcji, a w związku z tym nie bez gwarancji transakcyjne dla operacji wykonywanych przez odłamków.

## <a name="next-steps"></a>Kolejne kroki
Aby odłączyć niezależnych lub podłącz niezależnego fragmentu, zobacz [za pomocą klasy RecoveryManager rozwiązywania problemów mapy niezależnego fragmentu](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

