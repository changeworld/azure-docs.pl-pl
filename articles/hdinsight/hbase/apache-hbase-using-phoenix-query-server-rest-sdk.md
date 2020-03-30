---
title: SDK REST serwera zapytań phoenix — usługa Azure HDInsight
description: Zainstaluj i użyj zestawu REST SDK dla serwera zapytań phoenix w usłudze Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612261"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/) jest open source, masowo równolegle relacyjnej warstwy bazy danych na górze [Apache HBase](apache-hbase-overview.md). Phoenix umożliwia używanie zapytań podobnych do SQL z HBase za pośrednictwem narzędzi SSH, takich jak [SQLLine.](apache-hbase-query-with-phoenix.md) Phoenix udostępnia również serwer HTTP o nazwie Phoenix Query Server (PQS), cienki klient, który obsługuje dwa mechanizmy transportu komunikacji z klientem: JSON i bufory protokołów. Bufory protokołu jest mechanizmem domyślnym i oferuje bardziej wydajną komunikację niż JSON.

W tym artykule opisano sposób używania SDK REST PQS do tworzenia tabel, wierszy upsert indywidualnie i zbiorczo oraz wybierania danych przy użyciu instrukcji SQL. W przykładach użyto [sterownika Microsoft .NET dla serwera zapytań Apache Phoenix](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Ten SDK jest zbudowany na [api API Avatica Apache Calcite,](https://calcite.apache.org/avatica/) które używają wyłącznie buforów protokołu dla formatu serializacji.

Aby uzyskać więcej informacji, zobacz [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Sterownik Microsoft .NET dla serwera zapytań Apache Phoenix jest dostarczany jako pakiet NuGet, który można zainstalować z **konsoli Menedżera pakietów NuGet** programu Visual Studio za pomocą następującego polecenia:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Tworzenie wystąpienia nowego obiektu PhoenixClient

Aby rozpocząć korzystanie z biblioteki, `PhoenixClient` wystąpienia nowego `ClusterCredentials` obiektu, `Uri` przekazując zawierające do klastra i klastra Apache Hadoop nazwę użytkownika i hasło.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Zastąp clustername nazwą klastra HDInsight HBase, a nazwę użytkownika i hasło poświadczeniami Hadoop określonymi podczas tworzenia klastra. Domyślną nazwą użytkownika Hadoop jest **admin**.

## <a name="generate-unique-connection-identifier"></a>Generowanie unikatowego identyfikatora połączenia

Aby wysłać jedno lub więcej żądań do PQS, należy dołączyć unikatowy identyfikator połączenia, aby skojarzyć żądania z połączeniem.

```csharp
string connId = Guid.NewGuid().ToString();
```

Każdy przykład najpierw wywołuje `OpenConnectionRequestAsync` metodę, przekazując w identyfikatorze połączenia unikatowe. Następnie zdefiniuj `ConnectionProperties` i `RequestOptions`, przekazując te `ConnectionSyncRequestAsync` obiekty i wygenerowany identyfikator połączenia do metody. `ConnectionSyncRequest` Obiekt PQS pomaga zapewnić, że zarówno klient, jak i serwer mają spójny widok właściwości bazy danych.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest i jego właściwości ConnectionProperties

Aby `ConnectionSyncRequestAsync`wywołać , `ConnectionProperties` przekazać w obiekcie.

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

Oto kilka interesujących właściwości:

| Właściwość | Opis |
| -- | -- |
| Autozatwierdzania | Wartość logiczna oznaczająca, czy `autoCommit` jest włączona dla transakcji phoenix. |
| ReadOnly | Wartość logiczna oznaczająca, czy połączenie jest tylko do odczytu. |
| Transakcyjnazolacja | Liczba całkowita oznaczająca poziom izolacji transakcji zgodnie ze specyfikacją JDBC — zobacz poniższą tabelę.|
| Wykaz | Nazwa katalogu do użycia podczas pobierania właściwości połączenia. |
| Schemat | Nazwa schematu, który ma być używany podczas pobierania właściwości połączenia. |
| Isdirty | Wartość logiczna oznaczająca, czy właściwości zostały zmienione. |

Oto `TransactionIsolation` wartości:

| Wartość izolacji | Opis |
| -- | -- |
| 0 | Transakcje nie są obsługiwane. |
| 1 | Brudne odczyty, odczyty nie powtarzalne i odczyty fantomowe mogą wystąpić. |
| 2 | Zapobiegnienie odczytom brudnym, ale mogą wystąpić odczyty i odczyty fantomowe. |
| 4 | Zapobiega się odczytom brudnym i odczytom nie powtarzalnym, ale mogą wystąpić odczyty fantomowe. |
| 8 | Zapobiega się odczytom brudnym, odczytom nie powtarzalnym i odczytom fantomowym. |

## <a name="create-a-new-table"></a>Tworzenie nowej tabeli

HBase, podobnie jak każdy inny RDBMS, przechowuje dane w tabelach. Phoenix używa standardowych zapytań SQL do tworzenia nowych tabel, podczas definiowania typów klucza podstawowego i kolumny.

W tym przykładzie i wszystkich późniejszych `PhoenixClient` przykładach należy użyć wystąpienia obiektu zgodnie z definicją w [obszarze Wystąpieniiate nowego obiektu PhoenixClient](#instantiate-new-phoenixclient-object).

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

W poprzednim przykładzie tworzy `Customers` nową `IF NOT EXISTS` tabelę o nazwie przy użyciu opcji. Wywołanie `CreateStatementRequestAsync` tworzy nową instrukcję na serwerze Avitica (PQS). Blok `finally` zamyka zwrócone `CreateStatementResponse` i `OpenConnectionResponse` obiekty.

## <a name="insert-data-individually"></a>Wstawianie danych indywidualnie

W tym przykładzie pokazano poszczególne `List<string>` wstawianie danych, odwołujące się do kolekcji skrótów stanów amerykańskich i terytoriów:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

Wartość `StateProvince` kolumny tabeli będzie używana w operacji późniejszego wyboru.

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

Struktura wykonywania instrukcji wstawiania jest podobna do tworzenia nowej tabeli. Na końcu `try` bloku transakcja jest jawnie zatwierdzona. W tym przykładzie powtarza wstaw transakcję 300 razy. W poniższym przykładzie przedstawiono bardziej wydajny proces wstawiania partii.

## <a name="batch-insert-data"></a>Dane wstawiania partii

Poniższy kod jest prawie identyczny z kodem do wstawiania danych indywidualnie. W tym przykładzie używa `UpdateBatch` obiektu `ExecuteBatchRequestAsync`w wywołaniu `ExecuteRequestAsync` do , a nie wielokrotnie wywołując z przygotowaną instrukcją.

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

W jednym środowisku testowym indywidualne wstawienie 300 nowych rekordów zajęło prawie 2 minuty. Natomiast wstawienie 300 rekordów jako partii wymagało tylko 6 sekund.

## <a name="select-data"></a>Wybieranie danych

W tym przykładzie pokazano, jak ponownie użyć jednego połączenia do wykonania wielu zapytań:

1. Zaznacz wszystkie rekordy, a następnie pobierz pozostałe rekordy po przywróceniu domyślnej maksymalnej liczby 100.
2. Użyj instrukcji wyboru całkowitej liczby wierszy, aby pobrać pojedynczy wynik skalarny.
3. Wykonaj instrukcję select, która zwraca całkowitą liczbę odbiorców na stan lub terytorium.

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

Dane wyjściowe `select` instrukcji powinny być następujące wyniki:

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

## <a name="next-steps"></a>Następne kroki

* [Oprogramowanie Apache Phoenix w usłudze HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Korzystanie z sdk Apache HBase REST](apache-hbase-rest-sdk.md)
