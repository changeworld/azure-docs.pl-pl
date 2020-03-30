---
title: Zarządzanie połączeniami w usłudze Azure Functions
description: Dowiedz się, jak uniknąć problemów z wydajnością w usłudze Azure Functions przy użyciu klientów połączeń statycznych.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276454"
---
# <a name="manage-connections-in-azure-functions"></a>Zarządzanie połączeniami w usłudze Azure Functions

Funkcje w aplikacji funkcji współużytkują zasoby. Wśród tych zasobów udostępnionych są połączenia: połączenia HTTP, połączenia z bazą danych i połączenia z usługami, takimi jak usługa Azure Storage. Gdy wiele funkcji są uruchomione jednocześnie, jest możliwe, aby zabraknie dostępnych połączeń. W tym artykule wyjaśniono, jak kodować funkcje, aby uniknąć używania większej liczby połączeń niż potrzebują.

## <a name="connection-limit"></a>Limit połączenia

Liczba dostępnych połączeń jest ograniczona częściowo dlatego, że aplikacja funkcyjna działa w [środowisku piaskownicy](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jednym z ograniczeń, które piaskownica nakłada na kod jest limit liczby połączeń wychodzących, który jest obecnie 600 aktywnych (łącznie 1200) połączeń na wystąpienie. Po osiągnięciu tego limitu środowisko wykonawcze funkcji zapisuje następujący `Host thresholds exceeded: Connections`komunikat do dzienników: . Aby uzyskać więcej informacji, zobacz [limity usługi Functions](functions-scale.md#service-limits).

Ten limit jest na wystąpienie. Gdy [kontroler skalowania dodaje wystąpienia aplikacji funkcji](functions-scale.md#how-the-consumption-and-premium-plans-work) do obsługi większej liczby żądań, każde wystąpienie ma limit połączenia niezależnego. Oznacza to, że nie ma limitu połączenia globalnego i można mieć znacznie więcej niż 600 aktywnych połączeń we wszystkich aktywnych wystąpień.

Podczas rozwiązywania problemów upewnij się, że usługa Application Insights została włączona dla aplikacji funkcji. Usługa Application Insights umożliwia wyświetlanie metryk dla aplikacji funkcji, takich jak wykonania. Aby uzyskać więcej informacji, zobacz [Wyświetlanie danych telemetrycznych w usłudze Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Klienci statyczni

Aby uniknąć przytrzymywania większej liczby połączeń niż jest to konieczne, należy ponownie użyć wystąpień klienta zamiast tworzyć nowe z każdym wywołaniem funkcji. Zaleca się ponowne stosowanie połączeń klienta dla dowolnego języka, w jakim można napisać funkcję. Na przykład klienci platformy .NET, tacy jak [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)i klienci usługi Azure Storage, mogą zarządzać połączeniami, jeśli używasz pojedynczego klienta statycznego.

Oto kilka wskazówek, których należy przestrzegać podczas korzystania z klienta specyficznego dla usługi w aplikacji usługi Azure Functions:

- *Nie* należy tworzyć nowego klienta przy każdym wywołaniu funkcji.
- *Należy* utworzyć pojedynczy, statyczny klient, który może używać każdego wywołania funkcji.
- *Należy rozważyć* utworzenie pojedynczego, statycznego klienta w klasie pomocnika udostępnionego, jeśli różne funkcje używają tej samej usługi.

## <a name="client-code-examples"></a>Przykłady kodu klienta

W tej sekcji przedstawiono najlepsze rozwiązania dotyczące tworzenia i używania klientów z kodu funkcji.

### <a name="httpclient-example-c"></a>Przykład httpclient (C#)

Oto przykład kodu funkcji języka C#, który tworzy statyczne [wystąpienie HttpClient:](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Typowe pytanie dotyczące [protokołu HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) w witrynie .NET brzmi :"Czy powinienem pozbyć się mojego klienta?" Ogólnie rzecz biorąc, można usunąć `IDisposable` obiekty, które implementują po zakończeniu korzystania z nich. Ale nie usuwać klienta statycznego, ponieważ nie są wykonywane przy użyciu go po zakończeniu funkcji. Chcesz, aby klient statyczny był na żywo na czas trwania aplikacji.

### <a name="http-agent-examples-javascript"></a>Przykłady agentów HTTP (JavaScript)

Ponieważ zapewnia lepsze opcje zarządzania połączeniami, należy użyć klasy macierzystej [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) zamiast `node-fetch` metod nienatywnych, takich jak moduł. Parametry połączenia są konfigurowane `http.agent` za pomocą opcji w klasie. Aby uzyskać szczegółowe opcje dostępne z agentem HTTP, zobacz [nowy agent(\[opcje\]).](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)

Klasa `http.globalAgent` globalna `http.request()` używana przez ma wszystkie te wartości ustawione na ich odpowiednie wartości domyślne. Zalecanym sposobem konfigurowania limitów połączeń w funkcji jest ustawienie maksymalnej liczby globalnie. W poniższym przykładzie ustawia się maksymalna liczba gniazd dla aplikacji funkcji:

```js
http.globalAgent.maxSockets = 200;
```

 Poniższy przykład tworzy nowe żądanie HTTP z niestandardowym agentem HTTP tylko dla tego żądania:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Przykład kodu DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) łączy się z wystąpieniem usługi Azure Cosmos DB. Dokumentacja usługi Azure Cosmos DB zaleca [użycie klienta usługi Azure Cosmos DB przez cały okres istnienia aplikacji.](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage) W poniższym przykładzie pokazano jeden wzorzec do wykonywania tego w funkcji:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Przykład kodu CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) łączy się z wystąpieniem usługi Azure Cosmos DB. Dokumentacja usługi Azure Cosmos DB zaleca [użycie klienta usługi Azure Cosmos DB przez cały okres istnienia aplikacji.](../cosmos-db/performance-tips.md#sdk-usage) W poniższym przykładzie pokazano jeden wzorzec do wykonywania tego w funkcji:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Połączenia SqlClient

Kod funkcji może używać dostawcy danych programu .NET Framework dla programu SQL Server[(SqlClient)](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)do nawiązywać połączenia z relacyjnej bazy danych SQL. Jest to również podstawowy dostawca dla platform danych, które opierają się na ADO.NET, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). W przeciwieństwie do połączeń [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) i [DocumentClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET domyślnie implementuje buforowanie połączeń. Ale ponieważ nadal można zabraknąć połączeń, należy zoptymalizować połączenia z bazą danych. Aby uzyskać więcej informacji, zobacz [Sql Server Connection Pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Niektóre struktury danych, takie jak Entity Framework, zazwyczaj uzyskać parametry połączenia z **ConnectionStrings** sekcji pliku konfiguracji. W takim przypadku należy jawnie dodać parametry połączenia bazy danych SQL do **kolekcji Parametry połączenia** ustawień aplikacji funkcji i pliku [local.settings.json](functions-run-local.md#local-settings-file) w projekcie lokalnym. Jeśli tworzysz wystąpienie [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji, należy przechowywać wartość ciągu połączenia w **ustawieniach aplikacji** z innymi połączeniami.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, dlaczego zalecamy klientów statycznych, zobacz [Nieprawidłowy wzorzec wystąpienia](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Aby uzyskać więcej wskazówek dotyczących wydajności usługi Azure Functions, zobacz [Optymalizowanie wydajności i niezawodności funkcji platformy Azure.](functions-best-practices.md)
