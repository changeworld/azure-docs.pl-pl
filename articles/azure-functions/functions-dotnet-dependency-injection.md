---
title: Użyj wstrzykiwanie zależności w .NET usługi Azure Functions
description: Dowiedz się, jak wstrzykiwanie zależności na użytek rejestrowanie i używanie usługi w funkcje platformy .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Usługa Azure functions, functions, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408447"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Użyj wstrzykiwanie zależności w .NET usługi Azure Functions

Usługa Azure Functions obsługuje zależności wzorzec projektowy oprogramowania iniekcji (DI), czyli technikę do osiągnięcia [Inwersja kontroli (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) między klasami i ich zależności.

Usługa Azure Functions opartą na funkcji wstrzykiwanie zależności programu ASP.NET Core.  Należy zrozumieć usług, okresy istnienia i wzorców projektowych [wstrzykiwanie zależności platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) przed ich użyciem w funkcji.

## <a name="installing-dependency-injection-packages"></a>Instalowanie pakietów wstrzykiwania zależności

Aby można było używać funkcji iniekcji zależności, należy uwzględnić pakiet NuGet, który udostępnia tych interfejsów API.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Rejestrowanie usługi

Do rejestrowania usług, Utwórz metodę, konfigurowanie i dodawanie składników do `IFunctionsHostBuilder` wystąpienia.  Host usługi Azure Functions tworzy `IFunctionsHostBuilder` i przekazuje je bezpośrednio do skonfigurowanego metodę.

Można zarejestrować usługi skonfiguruj metodę, należy dodać atrybut zestawu, który określa typ dla Twojego konfigurowanie przy użyciu metody `FunctionsStartup` atrybutu.

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

## <a name="service-lifetimes"></a>Okresy istnienia usługi

Aplikacje funkcji platformy Azure zapewnia ten sam okres istnienia klucza usługi jako [wstrzykiwanie zależności w programie ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)przejściowy, o określonym zakresie, a pojedyncze.

W aplikacji funkcji okres istnienia usługi o określonym zakresie dopasowuje okres istnienia wykonywania funkcji. Usługi o określonym zakresie są tworzone raz na wykonanie.  Dalsze żądania dla tej usługi podczas wykonywania ponownie użyć tego wystąpienia.  Okres istnienia usługi singleton dopasowuje okres istnienia hosta i zostanie ponownie użyty w wykonań funkcji w tym wystąpieniu.

Pojedyncze okres istnienia usługi są zalecane w przypadku połączeń i klientów, na przykład `SqlConnection`, `CloudBlobClient`, lub `HttpClient`.

Wyświetlanie lub pobieranie [próbki okresy istnienia innej usługi](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Rejestrowanie usługi

Dostawcy logowania, należy zalecaną metodą jest zarejestrować `ILoggerProvider`.  Dla usługi Application Insights funkcji usługi Application Insights automatycznie dodaje dla Ciebie.  

> [!WARNING]
> Nie należy dodawać `AddApplicationInsightsTelemetry()` usług kolekcji jako zarejestruje usług, które nie są zgodne z tym, co jest świadczona przez środowiska. 
 
## <a name="function-app-provided-services"></a>Usługi aplikacji udostępnianą — funkcja

Host funkcji zostanie zarejestrowany wielu usług.  Poniżej przedstawiono usług, które są bezpieczne zależni.  Inne usługi hosta nie są obsługiwane do zarejestrowania lub są zależne od.  W przypadku innych usług chcesz skorzystać z zależności, należy [utworzyć problem i dyskusji w witrynie GitHub](https://github.com/azure/azure-functions-host).

|Typ usługi|Okres istnienia|Opis|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|pojedyncze|Konfiguracja środowiska uruchomieniowego|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|pojedyncze|Odpowiedzialne za świadczenie identyfikator wystąpienia hosta|

### <a name="overriding-host-services"></a>Zastępowanie usług hosta

Zastępowanie usługi świadczone przez hosta nie jest obecnie obsługiwane.  W przypadku usług, które mają być zastępowanie, zapoznaj się z [utworzyć problem i dyskusji w witrynie GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Monitorowanie aplikacji funkcji](functions-monitoring.md)
* [Najlepsze rozwiązania dotyczące funkcji](functions-best-practices.md)