---
title: Routing zależny od danych
description: Jak używać klasy ShardMapManager w aplikacjach .NET do routingu zależnego od danych, funkcji podzielonej bazy danych w bazie danych SQL azure
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
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069459"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Kierowanie kwerendy do odpowiedniej bazy danych za pomocą routingu zależnego od danych

**Routing zależny od danych** to możliwość użycia danych w kwerendzie do kierowania żądania do odpowiedniej bazy danych. Routing zależny od danych jest podstawowym wzorcem podczas pracy z podzielonymi bazami danych. Kontekst żądania może również służyć do kierowania żądania, zwłaszcza jeśli klucz dzielenia na fragmenty nie jest częścią kwerendy. Każda określona kwerenda lub transakcja w aplikacji przy użyciu routingu zależnego od danych jest ograniczona do uzyskiwania dostępu do jednej bazy danych na żądanie. W przypadku narzędzi elastycznych bazy danych SQL platformy Azure ten routing jest realizowany za pomocą klasy **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

Aplikacja nie musi śledzić różnych ciągów połączeń lub lokalizacji bazy danych skojarzonych z różnymi wycinkami danych w środowisku podzielonym na fragmenty. Zamiast tego [Menedżer map niezależnego fragmentu](sql-database-elastic-scale-shard-map-management.md) otwiera połączenia z poprawnymi bazami danych w razie potrzeby, na podstawie danych na mapie niezależnego fragmentu i wartości klucza dzielenia na fragmenty, który jest celem żądania aplikacji. Kluczem jest zazwyczaj *customer_id* *, tenant_id*, *date_key*lub inny określony identyfikator, który jest podstawowym parametrem żądania bazy danych.

Aby uzyskać więcej informacji, zobacz [Skalowanie programu SQL Server za pomocą routingu zależnego od danych](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Pobierz bibliotekę klienta

Aby pobrać:

* Wersja Java biblioteki, zobacz [Maven Central Repozytorium](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* W wersji .NET biblioteki zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Korzystanie z funkcji ShardMapManager w aplikacji routingu zależnej od danych

Aplikacje powinny utworzyć wystąpienie **programu ShardMapManager** podczas inicjowania przy użyciu wywołania fabrycznego **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). W tym przykładzie zainicjowane są zarówno **ShardMapManager,** jak i określony **map shardmap,** który zawiera. W tym przykładzie przedstawiono metody GetSqlShardMapManager i GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Używanie najniższych poświadczeń uprawnień umożliwia uzyskanie mapy niezależnego fragmentu

Jeśli aplikacja nie manipuluje samą mapą niezależnego fragmentu, poświadczenia używane w metodzie fabrycznej powinny mieć uprawnienia tylko do odczytu w bazie danych **mapy niezależnego fragmentu.** Te poświadczenia zazwyczaj różnią się od poświadczeń używanych do otwierania połączeń z menedżerem mapy niezależnego fragmentu. Zobacz też [Poświadczenia używane do uzyskiwania dostępu do biblioteki klienta elastycznej bazy danych](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Wywołanie metody OpenConnectionForKey

**Metoda ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) zwraca połączenie gotowe do wydawania poleceń do odpowiedniej bazy danych na podstawie wartości parametru **klucza.** Informacje o niezależnej macierzy są buforowane w aplikacji przez **ShardMapManager**, więc te żądania zazwyczaj nie obejmują wyszukiwania bazy danych w bazie danych **global shard map.**

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* Parametr **klucza** jest używany jako klucz odnośnika do mapy niezależnego fragmentu w celu określenia odpowiedniej bazy danych dla żądania.
* **ConnectionString** służy do przekazywania tylko poświadczeń użytkownika dla żądanego połączenia. Nazwa bazy danych lub nazwa serwera nie jest uwzględniona w tym *połączeniuString,* ponieważ metoda określa bazę danych i serwer przy użyciu **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) powinny być ustawione na **ConnectionOptions.Validate** jeśli środowisko, w którym mapy niezależnego fragmentu może ulec zmianie i wiersze mogą przenosić się do innych baz danych w wyniku operacji podziału lub scalania. To sprawdzanie poprawności obejmuje krótkie zapytanie do mapy niezależnego fragmentu lokalnego w docelowej bazie danych (nie do mapy niezależnego fragmentu globalnego) przed dostarczeniem połączenia do aplikacji.

Jeśli sprawdzanie poprawności mapy lokalnego niezależnego fragmentu nie powiedzie się (co wskazuje, że pamięć podręczna jest niepoprawna), Menedżer map niezależnego fragmentu wysyła zapytanie do mapy globalnego niezależnego fragmentu w celu uzyskania nowej poprawnej wartości wyszukiwania, zaktualizowania pamięci podręcznej oraz uzyskania i zwrócenia odpowiedniego połączenia z bazą danych. .

Użyj **ConnectionOptions.None** tylko wtedy, gdy zmiany mapowania niezależnego fragmentu nie są oczekiwane, gdy aplikacja jest w trybie online. W takim przypadku można założyć, że wartości buforowane są zawsze poprawne, a dodatkowe wywołanie sprawdzania poprawności w obie strony do docelowej bazy danych można bezpiecznie pominąć. Zmniejsza to ruch w bazie danych. **ConnectionOptions** można również ustawić za pomocą wartości w pliku konfiguracyjnym, aby wskazać, czy zmiany dzielenia na fragmenty są oczekiwane, czy nie w okresie czasu.  

W tym przykładzie użyto wartości **identyfikatora klienta**klucza całkowitej, przy użyciu obiektu **ShardMap** o nazwie **customerShardMap**.  

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

**Metoda OpenConnectionForKey** zwraca nowe już otwarte połączenie z poprawną bazą danych. Połączenia wykorzystywane w ten sposób nadal w pełni korzystać z buforowania połączeń.

**Metoda OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) jest również dostępna, jeśli aplikacja korzysta z programowania asynchronicznego.

## <a name="integrating-with-transient-fault-handling"></a>Integracja z obsługą usterek przejściowych

Najlepszym rozwiązaniem w tworzeniu aplikacji dostępu do danych w chmurze jest zapewnienie, że błędy przejściowe są przechwytywał przez aplikację i że operacje są ponowione kilka razy przed wykasuniem błędu. Przejściowa obsługa błędów dla aplikacji w chmurze jest omawiana w artykule Obsługa błędów przejściowych[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET).](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))

Obsługa błędów przejściowych może współistnieć naturalnie ze wzorcem routingu zależnego od danych. Kluczowym wymaganiem jest ponowienie próby całego żądania dostępu do danych, w tym **using** bloku, który uzyskał połączenie routingu zależne od danych. W poprzednim przykładzie można przepisać w następujący sposób.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Przykład — routing zależny od danych z obsługą błędów przejściowych

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

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

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

Pakiety niezbędne do zaimplementowania obsługi błędów przejściowych są pobierane automatycznie podczas tworzenia przykładowej aplikacji elastycznej bazy danych.

## <a name="transactional-consistency"></a>Spójność transakcyjna

Właściwości transakcyjne są gwarantowane dla wszystkich operacji lokalnych do niezależnego fragmentu. Na przykład transakcje przesyłane za pośrednictwem routingu zależnego od danych są wykonywane w zakresie niezależnego fragmentu docelowego dla połączenia. W tej chwili nie ma żadnych możliwości przewidzianych do rejestrowania wielu połączeń do transakcji i dlatego nie istnieją żadne gwarancje transakcyjne dla operacji wykonywanych przez fragmenty.

## <a name="next-steps"></a>Następne kroki

Aby odłączyć fragment lub ponownie dołączyć fragment, zobacz [Używanie klasy RecoveryManager w celu rozwiązania problemów z mapą niezależnego fragmentu](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
