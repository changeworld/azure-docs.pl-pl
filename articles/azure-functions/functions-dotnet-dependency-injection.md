---
title: Użyj wstrzykiwanie zależności w .NET usługi Azure Functions
description: Dowiedz się, jak wstrzykiwanie zależności na użytek rejestrowanie i używanie usługi w funkcje platformy .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Usługa Azure functions, functions, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: b1a6751f0d788c26af60b28eee994dc9b3877f00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693249"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Użyj wstrzykiwanie zależności w .NET usługi Azure Functions

Usługa Azure Functions obsługuje zależności wzorzec projektowy oprogramowania iniekcji (DI), czyli technikę do osiągnięcia [Inwersja kontroli (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależności.

Usługa Azure Functions opartą na funkcji wstrzykiwanie zależności programu ASP.NET Core. Wiedziały, usług, okresy istnienia i wzorców projektowych [wstrzykiwanie zależności platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) przed użyciem funkcji DI w usłudze Azure Functions aplikacji jest zalecane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem iniekcji zależności, należy zainstalować następujące pakiety NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pakiet Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) wersji 1.0.28 lub nowszej

## <a name="register-services"></a>Rejestrowanie usługi

Aby zarejestrować usługi, należy utworzyć metodę, aby skonfigurować i dodawanie składników do `IFunctionsHostBuilder` wystąpienia.  Host usługi Azure Functions tworzy wystąpienie `IFunctionsHostBuilder` i przekazuje je bezpośrednio do wybranej metody.

Aby zarejestrować metody, należy dodać `FunctionsStartup` atrybutu zestawu, który określa nazwę typu, używany podczas uruchamiania.

```csharp
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

## <a name="use-injected-dependencies"></a>Użyj zależności wprowadzonego kodu

Platformy ASP.NET Core używa iniekcji konstruktora, aby udostępnić zależności do funkcji. W poniższym przykładzie pokazano sposób, w jaki `IMyService` i `HttpClient` zależności są wstrzykiwane do funkcji wyzwalanej przez HTTP.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

Użycie iniekcji konstruktora oznacza, że nie należy używać statycznych funkcji, jeśli chcesz korzystać z zalet iniekcji zależności.

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje funkcji platformy Azure zapewnia ten sam okres istnienia klucza usługi jako [wstrzykiwanie zależności w programie ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): przejściowych, o określonym zakresie i pojedyncze.

W aplikacji funkcji okres istnienia usługi o określonym zakresie dopasowuje okres istnienia wykonywania funkcji. Usługi o określonym zakresie są tworzone raz na wykonanie. Dalsze żądania dla tej usługi podczas wykonywania ponownie używać istniejącego wystąpienia usługi. Okres istnienia usługi singleton dopasowuje okres istnienia hosta i zostanie ponownie użyty w wykonań funkcji w tym wystąpieniu.

Pojedyncze okres istnienia usługi są zalecane w przypadku połączeń i klientów, na przykład `SqlConnection`, `CloudBlobClient`, lub `HttpClient` wystąpień.

Wyświetlanie lub pobieranie [próbki okresy istnienia innej usługi](https://aka.ms/functions/di-sample) w witrynie GitHub.

## <a name="logging-services"></a>Rejestrowanie usługi

Dostawcy logowania, należy zalecaną metodą jest zarejestrować `ILoggerProvider` wystąpienia. Usługa Application Insights jest dodawany automatycznie przez usługę Azure Functions.

> [!WARNING]
> Nie należy dodawać `AddApplicationInsightsTelemetry()` do kolekcji usługi, ponieważ rejestrów usługi powodujące konflikt z usług udostępnianych przez środowisko.

## <a name="function-app-provided-services"></a>Usługi aplikacji udostępnianą — funkcja

Host funkcji rejestruje wielu usług. Bezpiecznie wykonać jako zależności w aplikacji są następujące usługi:

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|pojedyncze|Konfiguracja środowiska uruchomieniowego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|pojedyncze|Odpowiedzialne za świadczenie identyfikator wystąpienia hosta|

W przypadku innych usług, aby skorzystać z zależności, [Utwórz problem i zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usługi świadczone przez hosta nie jest obecnie obsługiwane.  W przypadku usług, które chcesz zastąpić, [Utwórz problem i zaproponuj je w serwisie GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

- [Monitorowanie aplikacji funkcji](functions-monitoring.md)
- [Najlepsze rozwiązania dotyczące funkcji](functions-best-practices.md)
