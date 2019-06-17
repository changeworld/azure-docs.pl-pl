---
title: Serwera Phoenix Query Server SDK REST — usługa Azure HDInsight
description: Zainstaluj i użytek zestawu SDK REST serwera Phoenix Query Server w usłudze Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: 1f468cac29579d8748f61a47b548a67d36ff8279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695948"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server zestawu SDK REST

[Apache Phoenix](https://phoenix.apache.org/) typu open source, warstwy równoległe na wielką skalę relacyjnej bazy danych, w górnej części [bazy danych Apache HBase](apache-hbase-overview.md). Phoenix pozwala na używanie zapytań przypominający SQL z bazą danych HBase za pomocą protokołu SSH narzędzi takich jak [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix zapewnia również serwera HTTP o nazwie Phoenix Query Server (PQS), klienta zubożonego, który obsługuje dwa mechanizmy transportu do komunikacji z klientem: JSON i Protocol Buffers. Protocol Buffers jest domyślnego mechanizmu i oferuje bardziej wydajne komunikacji i JSON.

W tym artykule opisano, jak używać zestawu SDK REST PQS tabele, wiersze upsert indywidualnie i zbiorcze, oraz wybieranie danych przy użyciu instrukcji języka SQL. W przykładach użyto [sterownik programu Microsoft .NET dla serwera Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Ten zestaw SDK jest oparta na [Avatica Apache Calcite](https://calcite.apache.org/avatica/) interfejsów API, które używają wyłącznie Protocol Buffers format serializacji.

Aby uzyskać więcej informacji, zobacz [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Sterownik programu Microsoft .NET dla serwera Apache Phoenix Query Server jest dostępna jako pakiet NuGet, które mogą być instalowane z Visual Studio **Konsola Menedżera pakietów NuGet** za pomocą następującego polecenia:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Utwórz wystąpienie nowego obiektu PhoenixClient

Aby rozpocząć korzystanie z biblioteki, utworzyć `PhoenixClient` obiektu, przekazując `ClusterCredentials` zawierający `Uri` do klastra i Apache Hadoop, nazwę użytkownika i hasło klastra.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Zamień CLUSTERNAME nazwy klastra HDInsight HBase i nazwy użytkownika i HASŁA przy użyciu poświadczeń usługi Hadoop, określone w procesie tworzenia klastra. Domyślna nazwa użytkownika usługi Hadoop jest **administratora**.

## <a name="generate-unique-connection-identifier"></a>Generuj Unikatowy identyfikator połączenia

Aby wysyłać co najmniej jednego żądania do PQS, musisz obejmują unikatowy identyfikator połączenia do kojarzenia żądań z połączeniem.

```csharp
string connId = Guid.NewGuid().ToString();
```

Każdy przykład najpierw wywołuje `OpenConnectionRequestAsync` metody, przekazując Unikatowy identyfikator połączenia. Następnie zdefiniuj `ConnectionProperties` i `RequestOptions`, przekazywanie tych obiektów i identyfikator wygenerowany połączenia `ConnectionSyncRequestAsync` metody. Firmy PQS `ConnectionSyncRequest` obiektu pomaga, upewnij się, że klient i serwer spójny widok właściwości bazy danych.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest i jego ConnectionProperties

Aby wywołać `ConnectionSyncRequestAsync`, Przekaż `ConnectionProperties` obiektu.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Poniżej przedstawiono niektóre właściwości zainteresowania:

| Właściwość | Opis |
| -- | -- |
| AutoCommit | Wartość logiczna wskazująca czy `autoCommit` jest włączona dla rozwiązania Phoenix transakcji. |
| ReadOnly | Wartość logiczna wskazująca, czy połączenie jest tylko do odczytu. |
| TransactionIsolation | Liczba całkowita wskazująca poziom izolacji transakcji na specyfikację JDBC — zobacz poniższą tabelę.|
| Wykaz | Nazwa katalogu do użycia podczas pobierania właściwości połączenia. |
| Schemat | Nazwa schematu do użycia podczas pobierania właściwości połączenia. |
| IsDirty | Wartość logiczna wskazująca, czy właściwości została zmieniona. |

Poniżej przedstawiono `TransactionIsolation` wartości:

| Wartość izolacji | Opis |
| -- | -- |
| 0 | Transakcje nie są obsługiwane. |
| 1 | Odczytów, -powtarzalnego odczytu i urojone odczyty mogą wystąpić. |
| 2 | Nie będą mogły odczytów, ale odczyty powtarzalne i urojone odczyty mogą wystąpić. |
| 4 | Nie będą mogły odczytów i -powtarzalnego odczytu, ale fantomu odczyty mogą wystąpić. |
| 8 | Nie są wszystkie odczytów, -powtarzalnego odczytu i urojone odczytów. |

## <a name="create-a-new-table"></a>Utwórz nową tabelę

Baza danych HBase, podobnie jak inne RDBMS przechowuje dane w tabelach. Phoenix używa standardowych zapytań SQL w celu utworzenia nowych tabel podczas definiowania podstawowe typy klucza i kolumn.

W tym przykładzie i wszystkich kolejnych przykładów, użyj skonkretyzowanej `PhoenixClient` obiektu zgodnie z definicją w [utworzenia wystąpienia nowego obiektu PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Poprzedni przykład tworzy nową tabelę o nazwie `Customers` przy użyciu `IF NOT EXISTS` opcji. `CreateStatementRequestAsync` Wywołanie tworzy nowy raport na serwerze Avitica (PQS). `finally` Bloku zamyka zwracanego `CreateStatementResponse` i `OpenConnectionResponse` obiektów.

## <a name="insert-data-individually"></a>Wstawianie danych indywidualnie

Ten przykład przedstawia dane osoby wstawić, odwołuje się do `List<string>` zbiór American skróty stanów i terytoriów:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tabela `StateProvince` wartości kolumny, które będą używane w kolejnych operacji wyboru.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Struktura do wykonywania instrukcji insert jest podobne do tworzenia nowej tabeli. Należy pamiętać, że na końcu `try` bloku, transakcja jest jawnie zatwierdzone. W tym przykładzie powtarza transakcja insert 300 godzin. Poniższy przykład pokazuje większą wydajność procesu wstawiania wsadowego.

## <a name="batch-insert-data"></a>Wstaw dane

Poniższy kod jest niemal identyczny jak kod do wstawiania danych indywidualnie. W tym przykładzie użyto `UpdateBatch` obiektów w wywołaniu `ExecuteBatchRequestAsync`, zamiast wielokrotnie podczas wywoływania `ExecuteRequestAsync` przygotowanej instrukcji.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

W środowisku testowym jeden indywidualnie Wstawianie 300 nowych rekordów trwało prawie 2 minuty. Z kolei wstawiania rekordów 300 jako zadania wsadowego wymagane tylko 6 kilka sekund.

## <a name="select-data"></a>Wybieranie danych

W tym przykładzie pokazano, jak to ponowne użycie jednego połączenia do wykonania wielu zapytań:

1. Wybierz wszystkie rekordy, a następnie pobrać pozostałych rekordów po zwróceniu domyślnej maksymalnej liczby 100.
2. Użyj instrukcji select wiersz sumy liczby do pobrania pojedynczego wyniku skalarne.
3. Wykonywanie instrukcji select, która zwraca łączna liczba klientów na stan lub terytorium.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Dane wyjściowe `select` instrukcje powinny być następujące wyniki:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Kolejne kroki 

* [Oprogramowanie Apache Phoenix w HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Korzystanie z zestawu SDK REST HBase Apache](apache-hbase-rest-sdk.md)
