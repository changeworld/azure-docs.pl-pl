---
title: Eksplorowanie dzienników śledzenia .NET w usłudze Azure Application Insights za pomocą ILogger
description: Przykłady korzystania z wdrożenia usługi Azure Application Insights ILogger za pomocą aplikacji konsoli i ASP.NET Core.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: deaddfbd27c4ffe6738988c6368ce4f9c3a7fa78
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359147"
---
# <a name="ilogger"></a>ILogger

Platforma ASP.NET Core obsługuje interfejs API rejestrowania, która współdziała z różnych dostawców rejestrowania wbudowanych oraz innych firm. W tym artykule pokazano, jak obsługiwać rejestrowanie za pomocą usługi Application Insights ILogger implementację zarówno w konsoli i aplikacje platformy ASP.NET Core. Aby dowiedzieć się więcej na temat rejestrowania ILogger na podstawie, zobacz [w tym artykule](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Aplikacja konsolowa

Poniżej przedstawiono przykładową aplikację konsoli skonfigurowany tak, aby wysyłał informacje śledzenia ILogger do usługi Application Insights.

Zainstalowane pakiety:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Aplikacja platformy ASP.NET Core

Poniżej pokazano przykładowy aplikacji programu ASP.NET Core skonfigurowane do wysyłania danych śledzenia ILogger do usługi application insights. W tym przykładzie można wykonać wysyłać ślady ILogger z pliku Program.cs, Startup.cs lub inną logikę sterownika/aplikacji.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up by Application Insights.  
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

W obu powyższych przykładach, pakietu autonomicznego Microsoft.Extensions.Logging.ApplicationInsights jest używany. Domyślnie ta konfiguracja korzysta z absolutnego minimum `TelemetryConfiguration` do wysyłania danych do usługi Application Insights. Absolutnego minimum oznacza, że kanał używany będzie `InMemoryChannel`, nie pobierania próbek i nie TelemetryInitializers standardowych. To zachowanie można przesłonić dla aplikacji konsoli, jak pokazano w poniższym przykładzie.

Zainstalować ten pakiet dodatkowe:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślny `TelemetryConfiguration`. Ten przykład umożliwia skonfigurowanie `ServerTelemetryChannel`, próbkowanie oraz niestandardowe `ITelemetryInitializer`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Powyższe podejście można zastosować w aplikacji ASP.NET Core, bardziej typowym podejściem jest łączenie regularnych application monitoring (żądania, zależności itd.) z przechwytywania ILogger jak pokazano poniżej.

Zainstalować ten pakiet dodatkowe:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Dodaj następujące polecenie, aby `ConfigureServices` metody. Ten kod spowoduje włączenie aplikacji regularnego monitorowania za pomocą domyślnej konfiguracji (ServerTelemetryChannel LiveMetrics, żądanie/zależności, korelacji itp.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

W tym przykładzie konfiguracja używana przez `ApplicationInsightsLoggerProvider` jest taka sama jak wartość używana przez monitorowanie aplikacji w regularnych. W związku z tym oba `ILogger` ślady i innych danych telemetrycznych (żądań, zależności itd.) będą uruchomione tego samego zestawu `TelemetryInitializers`, `TelemetryProcessors`, i `TelemetryChannel`. Będą one skorelowane i próbkowane tak/nie próbkowane w taki sam sposób.

Istnieje jednak wyjątek dotyczący tego zachowania. Wartość domyślna `TelemetryConfiguration` nie pełni skonfiguruj, kiedy rejestruje czegoś ze `Program.cs` lub `Startup.cs` siebie, więc tych dzienników nie będzie domyślna konfiguracja. Jednak co inne dziennika (na przykład dzienniki z kontrolerów, modele itp.), czy mają taką konfigurację.

## <a name="control-logging-level"></a>Poziom rejestrowania formantu

Oprócz filtrowania dzienniki na kodzie, jak w powyższych przykładach, istnieje również możliwość sterowania przechwytuje poziom rejestrowania usługi Application Insights, modyfikując `appsettings.json`. [Rejestrowanie podstawowe informacje dotyczące dokumentacji platformy ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) pokazuje, jak można to osiągnąć. Specjalnie dla usługi Application Insights jest nazwę aliasu dostawcy `ApplicationInsights`, jak pokazano w poniższym przykładzie, który konfiguruje `ApplicationInsights` do przechwycenia tylko dzienniki `Warning` i nowsze wersje z wszystkich kategorii.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

- [Rejestrowanie na podstawie ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Dzienniki śledzenia platformy .NET](../../azure-monitor/app/asp-net-trace-logs.md)
