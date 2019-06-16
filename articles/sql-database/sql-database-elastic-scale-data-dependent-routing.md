---
title: Routing zależny od danych z usługą Azure SQL Database | Dokumentacja firmy Microsoft
description: Sposób użycia klasy ShardMapManager w aplikacjach .NET na dane zależne od routingu funkcji podzielonej na fragmenty baz danych w usłudze Azure SQL Database
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
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584968"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Użyj danych zależne od routingu, aby skierować zapytanie do odpowiedniej bazy danych

**Routing zależny od danych** jest możliwość użycia danych w zapytaniu Kieruj żądania do odpowiedniej bazy danych. Routing zależne od danych jest podstawowy wzorzec, podczas pracy z bazami danych podzielonych na fragmenty. Kontekst żądania może również kierować żądania, zwłaszcza, jeśli klucz fragmentowania nie jest częścią zapytania. Każdej określonej kwerendy lub transakcji w aplikacji przy użyciu routingu zależnego od danych jest ograniczony do uzyskiwania dostępu do jednej bazy danych na żądanie. Narzędzi elastycznej bazy danych SQL Azure, tym routing odbywa się za pomocą **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) klasy.

Aplikacja nie musi się do śledzenia różnych parametrów połączenia lub lokalizacje bazy danych skojarzone z inną wycinki danych w środowisku podzielonej na fragmenty. Zamiast tego [Menedżera mapowań fragmentów](sql-database-elastic-scale-shard-map-management.md) połączenia zostanie otwarta do właściwych baz danych, w razie potrzeby, na podstawie danych mapy fragmentów i wartość klucza fragmentowania, który jest elementem docelowym żądania aplikacji. Klucz jest zazwyczaj *customer_id*, *tenant_id*, *date_key*, lub niektóre inne określone identyfikator, który jest podstawowe parametru żądania bazy danych.

Aby uzyskać więcej informacji, zobacz [skalowanie w poziomie SQL Server z routingu zależnego od danych](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Pobierz biblioteki klienckiej

Aby pobrać:

* Wersja języka Java, biblioteki, zobacz [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* .NET w wersji biblioteki, zobacz [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Za pomocą ShardMapManager w aplikacji routingu zależnego od danych

Utworzyć wystąpienie aplikacji **ShardMapManager** podczas inicjowania przy użyciu wywołania fabryki **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [platformy .NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). W tym przykładzie zarówno **ShardMapManager** i określony **ShardMap** niej są inicjowane. Ten przykład przedstawia GetSqlShardMapManager i GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) metody.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Użyj poświadczeń o najniższej uprawnień możliwe w celu uzyskania mapowania fragmentów

Jeśli aplikacja nie jest manipulowanie mapy fragmentów, poświadczenia używane w metodzie fabryki powinny mieć uprawnienia tylko do odczytu **globalne mapy fragmentów** bazy danych. Te poświadczenia zazwyczaj różnią się od poświadczenia użyte do otwarcia połączenia z Menedżera map fragmentów. Zobacz też [poświadczenia umożliwiają dostęp do biblioteki klienckiej Elastic Database](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Wywołaj metodę OpenConnectionForKey

**Metoda ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) zwraca gotowy do wystawiania poleceń do odpowiedniej bazy danych na podstawie wartości połączenia **klucz** parametru. Informacje o fragmencie są buforowane w aplikacji przez **ShardMapManager**, więc te żądania nie obejmują na ogół wyszukiwania w bazie danych względem **mapowania fragmentów w globalnej** bazy danych.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **Klucz** parametr jest używany jako klucz wyszukiwania do mapy fragmentów, aby określić odpowiednią bazę danych dla żądania.
* **ConnectionString** używany do przekazywania tylko poświadczenia użytkownika dla żądanego połączenia. Nie nazwy bazy danych lub nazwy serwera znajduje się w tym *connectionString* ponieważ metoda określa bazę danych i serwera przy użyciu **ShardMap**.
* **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) powinna być równa **ConnectionOptions.Validate** Jeśli środowisko, w którym dzielenie map na fragmenty maja zmiany i wiersze mogą przenosić do innych baz danych w wyniku operacji dzielenia ani scalania. To sprawdzanie poprawności obejmuje krótki zapytanie w celu lokalnego podzielonej na fragmenty mapy w docelowej bazy danych (nie do mapy fragmentów globalne), aby połączenie został dostarczony do aplikacji.

W przypadku niepowodzenia weryfikacji względem lokalnego podzielonej na fragmenty mapy (co oznacza, że pamięć podręczna jest nieprawidłowy) Menedżera mapowań fragmentów zapytania globalnego podzielonej na fragmenty mapy uzyskać nowe poprawnej wartości do wyszukiwania, zaktualizuj pamięć podręczną i uzyskać i zwraca połączenie z odpowiednią bazą danych .

Użyj **ConnectionOptions.None** tylko podczas zmiany mapowania fragmentów nie powinny podczas, gdy aplikacja jest w trybie online. W takiej sytuacji wartości z pamięci podręcznej można przyjąć, że zawsze jest poprawny, a następnie bezpiecznie pominięte wywołanie bardzo obustronne sprawdzania poprawności docelowej bazy danych. Które zmniejszają ruchu bazy danych. **ConnectionOptions** może być również ustawiana za pomocą wartości w pliku konfiguracji do wskazania oczekuje fragmentowania zmiany lub nie w przedziale czasu.  

W tym przykładzie użyto wartości klucza liczby całkowitej **CustomerID**przy użyciu **ShardMap** obiektu o nazwie **customerShardMap**.  

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

**OpenConnectionForKey** metoda zwraca nowe połączenie już open prawidłową bazę danych. Połączenia używane w ten sposób nadal umożliwiają pełne wykorzystywanie zalet buforowania połączeń.

**Metoda OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) jest również dostępna, jeśli aplikacja podejmuje programowania asynchronicznego użycia.

## <a name="integrating-with-transient-fault-handling"></a>Integrowanie z obsługi błędów przejściowych

Najlepszym rozwiązaniem w opracowywaniu aplikacji dostęp do danych w chmurze jest upewnij się, że błędy przejściowe są przechwytywane przez aplikację, a operacje zostaną ponowione kilka razy przed zgłoszenie błędu. Obsługa dla aplikacji w chmurze błędu przejściowego jest omówiona w obsługi błędów przejściowych ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Obsługa błędu przejściowego mogą współistnieć w naturalny sposób za pomocą wzorca routingu zależnego od danych. Najważniejszym wymaganiem jest, aby ponowić próbę, w tym żądania dostępu do danych całej **przy użyciu** blok, który uzyskać połączenie routingu zależnego od danych. Poprzedni przykład można dopasować w następujący sposób.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Przykład — dane zależne od routing za pomocą obsługi błędów przejściowych

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

Niezbędne do zaimplementowania obsługi błędów przejściowych pakiety są pobierane automatycznie podczas tworzenia aplikacji przykładowych elastycznej bazy danych.

## <a name="transactional-consistency"></a>Poziom spójności transakcyjnej

Właściwości transakcji gwarantują dla wszystkich działań lokalnych do fragmentu. Na przykład transakcje przesłane za pomocą routingu zależnego od danych wykonaj w zakresie fragmentu docelowego dla połączenia. W tej chwili to nie możliwości przewidziane rejestrowanie wiele połączeń w transakcji, a w związku z tym nie żadnej gwarancji transakcyjnej dla operacji między fragmentami.

## <a name="next-steps"></a>Kolejne kroki

Aby odłączyć fragmentu lub ponownie dołączyć fragmentu, zobacz [używanie klasy RecoveryManager do Rozwiązywanie problemów z mapami fragmentów](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]