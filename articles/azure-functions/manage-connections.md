---
title: Sposób zarządzania połączeniami w usłudze Azure Functions
description: Dowiedz się, jak uniknąć problemów z wydajnością w usłudze Azure Functions przy użyciu statycznych połączenia klientów.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: tdykstra
ms.openlocfilehash: 9e5c56dc3679e9ffbd67d906ca7d971439319ee5
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125380"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Sposób zarządzania połączeniami w usłudze Azure Functions

Funkcji w aplikacji funkcji udostępniania zasobów, a wśród tych zasoby udostępnione są połączenia &mdash; połączenia HTTP, połączenia z bazą danych i połączeń z usługami platformy Azure, takie jak magazyn. Wiele funkcji są uruchomione równocześnie, prawdopodobnie brak dostępnych połączeń. W tym artykule wyjaśniono, jak kod funkcji uniknięcia więcej połączeń niż rzeczywiście konieczne.

## <a name="connections-limit"></a>Limit połączeń

Liczba dostępnych połączeń jest ograniczona, częściowo, ponieważ aplikacja funkcji jest uruchamiana w [piaskownicy usługi Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). On ograniczenia, które piaskownicy nakłada się na kodzie [dzienny limit liczby połączeń obecnie 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Po osiągnięciu tego limitu, środowisko uruchomieniowe usługi functions tworzy dziennik z następującym komunikatem: `Host thresholds exceeded: Connections`.

Prawdopodobieństwo przekracza limit rośnie, gdy [kontrolera skalowania dodaje wystąpień aplikacji funkcji](functions-scale.md#how-the-consumption-plan-works) do obsługi liczby żądań. Każde wystąpienie aplikacji funkcji, mogą działać wiele funkcji, które korzystają z połączeń, które są wliczane do limitu 300.

## <a name="use-static-clients"></a>Przy użyciu statycznych klientów

Aby uniknąć, zawierający więcej połączeń niż to konieczne, ponownie użyć wystąpienia klienta, zamiast tworzyć nowe przy każdym wywołaniu funkcji. Klienci platformy .NET, takich jak [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), i zarządzać połączeń klientów usługi Azure Storage, jeśli za pomocą pojedynczej, statycznej klienta.

Poniżej przedstawiono kilka wskazówek, które należy wykonać podczas przy użyciu klienta specyficzne dla usługi w aplikacji usługi Azure Functions:

- **NIE** tworzenia nowego klienta przy użyciu każdego wywołania funkcji.
- **CZY** Tworzenie statycznej, pojedynczego klienta, który może być używany przez każdego wywołania funkcji.
- **Należy wziąć pod uwagę** tworzenia klienta pojedynczego, statycznej klasy pomocnika udostępnionych, jeśli różne funkcje korzystają z tej samej usługi.

## <a name="httpclient-code-example"></a>Przykład kodu klasy HttpClient

Poniżej przedstawiono przykładowy kod funkcji, który tworzy statycznego [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Często zadawane pytania dotyczące programu .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) jest "Powinien mogę być disposing mojego klienta?" Ogólnie rzecz biorąc, Usuń obiekty, które implementują `IDisposable` po zakończeniu korzystania z nich. Ale nie dispose statyczne klienta, ponieważ nie są wykonywane przy użyciu jej, gdy funkcja skończy działanie. Kliencki statyczny czas trwania aplikacji na żywo.

## <a name="documentclient-code-example"></a>Przykładowy kod DocumentClient

[Klient DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) łączy się z wystąpieniem usługi Azure Cosmos DB. Dokumentacja usługi Azure Cosmos DB zaleca, aby użytkownik [okres istnienia aplikacji za pomocą klienta usługi Azure Cosmos DB pojedyncze](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Jednym ze wzorców tą operacją w funkcji można znaleźć w poniższym przykładzie:

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

## <a name="sqlclient-connections"></a>Połączeń klient SQL

Kod funkcji może używać .NET Framework Data Provider for SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) aby nawiązywać połączenia z relacyjnej bazy danych SQL. Dotyczy to również źródłowy dostawca struktur danych, które zależą od ADO.NET, takim jak Entity Framework. W odróżnieniu od [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) i [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) połączeń ADO.NET implementuje buforowanie połączeń, domyślnie. Jednakże ponieważ nadal można uruchomić z połączeń, należy zoptymalizować połączeń z bazą danych. Aby uzyskać więcej informacji, zobacz [programu SQL Server połączenia puli (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Niektóre struktur danych, takich jak [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), zazwyczaj pobrać parametry połączenia z **ConnectionStrings** sekcję pliku konfiguracji. W takim przypadku należy jawnie dodać parametry połączenia bazy danych SQL do **parametry połączenia** kolekcję ustawień aplikacji funkcji, a w [pliku local.settings.json](functions-run-local.md#local-settings-file) w projekcie lokalnym. Jeśli tworzysz [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) w kodzie funkcji należy przechowywać wartość parametrów połączenia w **ustawienia aplikacji** przy użyciu innych połączeń.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat przyczyn zaleca się klientów statycznych, zobacz [antywzorzec niewłaściwy sposób tworzenia wystąpień](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Aby uzyskać więcej porad dotyczących wydajności usługi Azure Functions, zobacz [zoptymalizować wydajność i niezawodność usługi Azure Functions](functions-best-practices.md).