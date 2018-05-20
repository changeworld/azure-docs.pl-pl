---
title: Phoenix kwerendy serwera REST SDK — usługa Azure HDInsight | Dokumentacja firmy Microsoft
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: ef89bcea3eab92c3137a6f532398764462ae204c
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix kwerendy serwera REST SDK

[Apache Phoenix](http://phoenix.apache.org/) jest typu open source, warstwa równoległemu relacyjnej bazy danych, nad [HBase](apache-hbase-overview.md). Phoenix umożliwia przy użyciu kwerend przypominającego SQL z bazą danych HBase za pośrednictwem SSH narzędzi takich jak [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix udostępnia również serwera HTTP o nazwie Phoenix kwerendy serwera (PQS), cienki klient, który obsługuje dwa mechanizmy transportu do komunikacji z klientem: JSON i bufory protokołu. Protokół buforów domyślnego mechanizmu i oferuje komunikacji bardziej efektywne niż JSON.

W tym artykule opisano sposób użycia PQS REST SDK do tworzenia tabel, wiersze upsert indywidualnie i zbiorczo, a następnie wybierz dane za pomocą instrukcji SQL. W przykładach użyto [sterownik programu Microsoft .NET dla Apache Phoenix kwerendy serwera](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Zestaw SDK jest oparty na [Avatica Apache Calcite](https://calcite.apache.org/avatica/) interfejsów API, które używają wyłącznie protokołu buforów dla formatu serializacji.

Aby uzyskać więcej informacji, zobacz [Apache Calcite Avatica protokołu buforów odwołanie](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Sterownik programu Microsoft .NET dla Apache Phoenix kwerendy serwera jest dostępna jako pakietu NuGet, które można zainstalować z programu Visual Studio **Konsola Menedżera pakietów NuGet** przy użyciu następującego polecenia:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Uruchamianie nowego obiektu PhoenixClient

Aby rozpocząć korzystanie z biblioteki, Utwórz wystąpienie nowego `PhoenixClient` obiektu, przekazując `ClusterCredentials` zawierający `Uri` do klastra i nazwę użytkownika Hadoop i hasło klastra.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Zamień NAZWAKLASTRA z nazwą klastra usługi HDInsight, HBase oraz nazwę użytkownika i hasło podane poświadczenia usługi Hadoop na utworzenie klastra. Domyślna nazwa użytkownika Hadoop jest **admin**.

## <a name="generate-unique-connection-identifier"></a>Generuj Unikatowy identyfikator połączenia

Aby wysłać PQS co najmniej jedno żądanie, musisz obejmują unikatowy identyfikator połączenia do skojarzenia z tym połączeniem żądań.

```csharp
string connId = Guid.NewGuid().ToString();
```

Każdy przykład najpierw nawiązuje połączenie `OpenConnectionRequestAsync` metody, przekazując Unikatowy identyfikator połączenia. Następnie określ `ConnectionProperties` i `RequestOptions`, przekazywanie tych obiektów oraz identyfikatora połączenia wygenerowane do `ConnectionSyncRequestAsync` metody. W PQS `ConnectionSyncRequest` obiektu gwarantuje, że klient i serwer ma spójny widok właściwości bazy danych.

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

Poniżej przedstawiono niektóre właściwości odsetek:

| Właściwość | Opis |
| -- | -- |
| Autozatwierdzania | Wartość logiczna wskazująca czy `autoCommit` jest włączona dla Phoenix transakcji. |
| Tylko do odczytu | Wartość logiczna wskazująca, czy połączenie jest tylko do odczytu. |
| TransactionIsolation | Liczba całkowita wskazująca poziom izolacji transakcji na specyfikacji JDBC — zobacz poniższą tabelę.|
| Wykaz | Nazwa katalogu do użycia podczas pobierania właściwości połączenia. |
| Schemat | Nazwa schematu do użycia podczas pobierania właściwości połączenia. |
| Właściwości IsDirty | Wartość logiczna wskazująca, czy właściwości zostały zmienione. |

Poniżej przedstawiono `TransactionIsolation` wartości:

| Wartość izolacji | Opis |
| -- | -- |
| 0 | Transakcje nie są obsługiwane. |
| 1 | Odczytów, -powtarzalne odczyty i fantomu odczyty mogą wystąpić. |
| 2 | Nie będą mogły odczytów, ale -powtarzalne odczyty i fantomu odczyty mogą wystąpić. |
| 4 | Odczytów i odczytuje powtarzalne nie będą mogły, ale fantomu odczyty mogą wystąpić. |
| 8 | Nie są wszystkie odczytów, -powtarzalne odczyty i fantomu odczytów. |

## <a name="create-a-new-table"></a>Utwórz nową tabelę

Baza danych HBase, podobnie jak inne RDBMS przechowuje dane w tabelach. Phoenix używa standardowego zapytania SQL w celu utworzenia nowe tabele podczas określania podstawowych typów kluczy i kolumn.

W tym przykładzie i wszystkie kolejne przykłady, użyj wystąpień `PhoenixClient` obiekt zgodnie z definicją w [wystąpienia nowy obiekt PhoenixClient](#instantiate-new-phoenixclient-object).

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

Powyższy przykład tworzy nową tabelę o nazwie `Customers` przy użyciu `IF NOT EXISTS` opcji. `CreateStatementRequestAsync` Wywołanie tworzy nowy raport na serwerze Avitica (PQS). `finally` Bloku zamyka zwróconego `CreateStatementResponse` i `OpenConnectionResponse` obiektów.

## <a name="insert-data-individually"></a>Wstawianie danych indywidualnie

Ten przykład przedstawia dane dotyczące poszczególnych Wstaw odwołujące się do `List<string>` kolekcji American skróty stanu i terytorium:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Tabela `StateProvince` w kolejnych operacji select zostanie użyta wartość kolumny.

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

Struktura wykonania instrukcji insert jest podobny do tworzenia nowej tabeli. Należy pamiętać, że na końcu `try` blok, transakcja jest jawnie zatwierdzone. W tym przykładzie powtarza transakcji insert 300 razy. W poniższym przykładzie przedstawiono większą wydajność procesu wstawiania wsadowego.

## <a name="batch-insert-data"></a>Wstaw dane

Następujący kod jest niemal identyczny z kodem indywidualnie wstawiania danych. W tym przykładzie użyto `UpdateBatch` obiektu w wywołaniu `ExecuteBatchRequestAsync`, zamiast wywoływania wielokrotnie `ExecuteRequestAsync` o przygotowanej instrukcji.

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

W środowisku testowym jeden indywidualnie wstawianie nowych rekordów 300 trwało prawie 2 minuty. Z kolei Wstawianie rekordów 300 jako partii wymagane tylko 6 sekund.

## <a name="select-data"></a>Wybieranie danych

W tym przykładzie pokazano, jak ponowne użycie jednego połączenia do wykonania wielu zapytań:

1. Wybierz wszystkie rekordy, a następnie pobrać pozostałych rekordów po zwróceniu maksymalna domyślne 100.
2. Użyć instrukcji select liczba całkowita liczba wierszy do pobrania pojedynczego wyniku skalarne.
3. Wykonanie instrukcji select, która zwraca łączna liczba klientów na stanu lub terytorium.

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

Dane wyjściowe `select` instrukcje powinny być następujące:

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

* [Phoenix w usłudze HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Przy użyciu zestawu SDK REST HBase](apache-hbase-rest-sdk.md)
