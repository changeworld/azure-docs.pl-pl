---
title: Używanie iniekcji zależności w programie .NET Azure Functions
description: Dowiedz się, jak używać iniekcji zależności do rejestrowania i używania usług w usłudze .NET Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions, architektura bezserwerowa
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e31f3dc166177ce36289b97d85d90a9582c9cae5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375997"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Używanie iniekcji zależności w programie .NET Azure Functions

Azure Functions obsługuje wzorzec projektowania oprogramowania dla iniekcji zależności (DI), który jest techniką do osiągnięcia [niewersji kontroli (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależnościami.

Azure Functions kompilacje na ASP.NET Core funkcji iniekcji zależności. Znane są informacje o usługach, okresach istnienia i wzorcach projektowania [ASP.NET Core iniekcja zależności](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) przed użyciem funkcji di w aplikacji Azure Functions.

Obsługa iniekcji zależności rozpoczyna się od Azure Functions 2. x.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było użyć iniekcji zależności, należy zainstalować następujące pakiety NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pakiet Microsoft. NET. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) w wersji 1.0.28 lub nowszej

- Opcjonalnie: [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) wymagane tylko do rejestrowania HttpClient podczas uruchamiania

## <a name="register-services"></a>Zarejestruj usługi

Aby zarejestrować usługi, można utworzyć metodę konfigurowania i dodawania składników do `IFunctionsHostBuilder` wystąpienia.  Azure Functions host tworzy wystąpienie `IFunctionsHostBuilder` i przekazuje je bezpośrednio do metody.

Aby zarejestrować metodę, Dodaj `FunctionsStartup` atrybut zestawu, który określa nazwę typu używaną podczas uruchamiania. Kod odwołuje się również do wersji wstępnej [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) na NuGet.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Użyj wstrzykiwanych zależności

ASP.NET Core używa iniekcji konstruktora w celu udostępnienia zależności funkcji. Poniższy przykład demonstruje, jak `IMyService` i `HttpClient` zależności są wstrzykiwane do funkcji wyzwalanej przez protokół http. 

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

Użycie iniekcji konstruktorów oznacza, że nie należy używać funkcji statycznych, jeśli chcesz skorzystać z iniekcji zależności. W przypadku klienta Cosmos odwołaj się do [tego](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/CodeSamples/AzureFunctions/AzureFunctionsCosmosClient.cs).

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje Azure Functions zapewniają te same okresy istnienia usługi jak [iniekcja zależności ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): przejściowy, zakresowy i pojedynczy.

W aplikacji Functions okres istnienia usługi w zakresie jest zgodny z okresem istnienia funkcji. Usługi w zakresie są tworzone raz na wykonanie. Późniejsze żądania dla tej usługi podczas wykonywania ponownie użyją istniejącego wystąpienia usługi. Okres istnienia usługi pojedynczej jest zgodny z okresem istnienia hosta i jest ponownie używany w ramach wykonywania funkcji w tym wystąpieniu.

Pojedyncze usługi okresu istnienia są zalecane w przypadku połączeń i klientów `SqlConnection`, `CloudBlobClient`na przykład `HttpClient` , lub wystąpień.

Wyświetl lub Pobierz [przykład różnych okresów istnienia usługi](https://aka.ms/functions/di-sample) w serwisie GitHub.

## <a name="logging-services"></a>Usługi rejestrowania

Jeśli potrzebujesz własnego dostawcy rejestrowania, zalecanym sposobem jest zarejestrowanie `ILoggerProvider` wystąpienia. Application Insights jest automatycznie dodawane przez Azure Functions.

> [!WARNING]
> Nie należy dodawać `AddApplicationInsightsTelemetry()` do kolekcji usług, ponieważ rejestruje ona usługi, które powodują konflikt z usługami udostępnianymi przez środowisko.

## <a name="function-app-provided-services"></a>Usługi funkcji dostarczone przez aplikację

Host funkcji rejestruje wiele usług. Następujące usługi są bezpieczne do podjęcia jako zależność w aplikacji:

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Pojedynczego|Konfiguracja środowiska uruchomieniowego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Pojedynczego|Osoba odpowiedzialna za podanie identyfikatora wystąpienia hosta|

Jeśli istnieją inne usługi, dla których chcesz wziąć zależność, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usług dostarczonych przez hosta nie jest obecnie obsługiwane.  Jeśli istnieją usługi, które chcesz przesłonić, [Utwórz problem i Zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące zasoby:

- [Jak monitorować aplikację funkcji](functions-monitoring.md)
- [Najlepsze rozwiązania dotyczące funkcji](functions-best-practices.md)
