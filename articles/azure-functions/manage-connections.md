---
title: Jak zarządzać połączenia w usługi Azure Functions
description: Dowiedz się, jak uniknąć problemów z wydajnością w funkcji platformy Azure przy użyciu statycznych połączeń klientów.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655083"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Jak zarządzać połączenia w usługi Azure Functions

Funkcje w aplikacji funkcji udostępniania zasobów, a wśród tych zasobów udostępnionych są połączenia &mdash; połączenia HTTP, połączenia z bazą danych i połączeń do usług platformy Azure, takie jak magazyn. Równocześnie uruchomiono wiele funkcji prawdopodobnie brak dostępnych połączeń. W tym artykule wyjaśniono, jak kod funkcji w celu uniknięcia przy użyciu połączenia więcej niż rzeczywiście potrzebne.

## <a name="connections-limit"></a>Limit połączeń

Liczba dostępnych połączeń jest ograniczona, częściowo, ponieważ aplikacja funkcja jest uruchamiana w [piaskownicy usługi Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Jednym z ograniczeń, które piaskownicy nakłada się na kodzie jest [limit na liczbę połączeń aktualnie 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Po osiągnięciu tego limitu czasu wykonywania funkcji tworzy dziennik z następującym komunikatem: `Host thresholds exceeded: Connections`.

Ryzyko przekracza limit, Zwiększ podczas [kontrolera skali dodaje wystąpień aplikacji funkcji](functions-scale.md#how-the-consumption-plan-works). Każde wystąpienie aplikacji funkcja może być wywoływanie funkcji wielokrotnie jednocześnie, a wszystkie te funkcje korzystają z połączeń, które są wliczane do 300 limit.

## <a name="use-static-clients"></a>Przy użyciu statycznych klientów

Aby uniknąć, zawierający więcej połączeń niż to konieczne, ponownie użyć wystąpienia klienta, zamiast tworzyć nowe przy każdym wywołaniu funkcji. Klientów platformy .NET, takich jak `HttpClient`, `DocumentClient`, i klientów usługi Magazyn Azure mogą zarządzać połączeniami użycie pojedynczego klienta statycznych.

Oto wskazówki, które należy wykonać podczas za pomocą klienta specyficznych dla usług w aplikacji usługi Azure Functions:

- **NIE** tworzenia nowego klienta z każdego wywołania funkcji.
- **CZY** utworzyć pojedynczą, statyczne klienta, który mogą być używane przez każdego wywołania funkcji.
- **Należy wziąć pod uwagę** tworzenia pojedynczego klienta statycznego w klasie pomocnika udostępnionego, jeśli różne funkcje korzystają z tej samej usługi.

## <a name="httpclient-code-example"></a>Przykładowy kod HttpClient

Oto przykład kodu funkcji, która tworzy statycznego `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Często zadawane pytania dotyczące programu .NET `HttpClient` jest "Powinien I być disposing Moje klienta?" Ogólnie rzecz biorąc, usuwania obiektów implementujących `IDisposable` po zakończeniu korzystania z nich. Ale nie dysponować statycznych klienta, ponieważ nie są realizowane przy użyciu go podczas kończenia funkcji. Kliencki statyczne na żywo na czas trwania aplikacji.

## <a name="documentclient-code-example"></a>Przykładowy kod DocumentClient

`DocumentClient` nawiązuje połączenie z wystąpieniem bazy danych rozwiązania Cosmos. Dokumentacja rozwiązania Cosmos bazy danych zaleca, aby użytkownik [przez czas ich istnienia aplikacji za pomocą klienta pojedyncze bazy danych Azure rozwiązania Cosmos](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). W poniższym przykładzie przedstawiono jeden wzorzec operacją w funkcji.

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

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat przyczyn zaleca się statycznych klientów, zobacz [antipattern niewłaściwy wystąpienia](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Aby uzyskać więcej porad dotyczących wydajności usługi Azure Functions, zobacz [optymalizację wydajności i niezawodności usługi Azure Functions](functions-best-practices.md).