---
title: Eksplorowanie dzienników śledzenia .NET w usłudze Azure Application Insights za pomocą ILogger
description: Przykłady korzystania z dostawcy usługi Azure Application Insights ILogger za pomocą aplikacji konsoli i ASP.NET Core.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: b8c0d84f6989e60c7abef0d423230300896663de
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877911"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider dla platformy .NET Core ILogger dzienników

Platforma ASP.NET Core obsługuje API rejestrowania, który współpracuje z różnymi rodzajami rejestrowania wbudowanych i innych dostawców. Rejestrowanie odbywa się za pomocą wywołania Log() lub w wariancie na `ILogger` wystąpień. W tym artykule pokazano, jak `ApplicationInsightsLoggerProvider` do przechwytywania `ILogger` dzienniki zarówno w konsoli i aplikacje platformy ASP.NET Core. W tym artykule opisano również sposób `ApplicationInsightsLoggerProvider` jest zintegrowana z innymi telemetria usługi Application Insights.
Aby dowiedzieć się więcej rejestrowania w programie Asp.Net Core, zobacz [w tym artykule](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplikacje platformy ASP.NET Core

Począwszy od [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 wersji lub nowszy, `ApplicationInsightsLoggerProvider` jest domyślnie włączona, podczas włączania przy użyciu standardowych metod - przez regularne monitorowanie usługi Application Insights wywoływanie `UseApplicationInsights` metody rozszerzenia na IWebHostBuilder lub `AddApplicationInsightsTelemetry` IServiceCollection metody rozszerzenia. `ILogger` Dzienniki są przechwytywane przez `ApplicationInsightsLoggerProvider` podlegają samą konfigurację jak żadnych innych danych telemetrycznych zebranych. i.e mają one ten sam zestaw `TelemetryInitializer`s, `TelemetryProcessor`s, używa tych samych `TelemetryChannel`, zostaną skorelowane i próbkowane w taki sam sposób co dane telemetryczne.  Jeśli korzystasz z tej wersji zestawu SDK lub nowszej, a następnie są wymagane żadne działania w celu przechwycenia `ILogger` dzienniki.

Domyślnie tylko `ILogger` dzienniki z `Warning` lub powyżej (z wszystkich kategorii) są wysyłane do usługi Application Insights. To zachowanie można zmienić, stosując filtry, jak pokazano [tutaj](#control-logging-level). Również dodatkowe kroki są wymagane, jeśli `ILogger` loguje się z `Program.cs` lub `Startup.cs` mają być przechwytywane, jak pokazano [tutaj](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Jeśli używasz wcześniejszej wersji zestawu Microsoft.ApplicationInsights.AspNet SDK lub chcesz po prostu użyj ApplicationInsightsLoggerProvider, bez żadnych innych monitorowanie usługi Application Insights, wykonaj następujące czynności.

1. Zainstaluj pakiet nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. modyfikowanie `Program.cs` poniżej

```csharp
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Extensions.Logging;

    public class Program
    {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
                {
                    // Providing an instrumentation key here is required if you are using standalone package Microsoft.Extensions.Logging.ApplicationInsights
                    // or if you want to capture logs from early in the application startup pipeline from Startup.cs or Program.cs itself.
                    builder.AddApplicationInsights("ikey");

                    // Optional: Apply filters to control what logs are sent to Application Insights.
                    // The following configures LogLevel Information or above to be sent to Application Insights for all categories.
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.Information);
                }
            );
    }
```

Powyższy kod skonfiguruje `ApplicationInsightsLoggerProvider`. Poniżej przedstawiono przykład klasy kontrolera, który używa `ILogger` do wysyłania dzienników, które są przechwytywane przez dotycząca usługi Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Przechwytywanie ILogger dzienniki w pliku Startup.cs, Program.cs w aplikacji programu Asp.Net Core

Za pomocą nowego ApplicationInsightsLoggerProvider istnieje możliwość przechwytywania dzienników z wczesnym etapie procesu uruchamiania aplikacji. Nawet wtedy, gdy ApplicationInsightsLoggerProvider jest automatycznie włączone usługi Application Insights (z 2.7.0-beta3 lub nowszy), nie mają ustawienia klucza Instrumentacji do momentu później w potoku, dlatego tylko dzienniki z kontrolera / będzie można przechwycić innych klas. Aby przechwycić zaloguje, począwszy od `Program.cs` i `Startup.cs` , jeden należy jawnie włączyć ApplicationInsightsLoggerProvider przy użyciu klucza instrumentacji. Jest również pamiętać, że `TelemetryConfiguration` nie pełni skonfiguruj, kiedy rejestruje czegoś ze `Program.cs` lub `Startup.cs` siebie, więc tych dzienników użyje bez konfiguracji minimalne, która używa InMemoryChannel, nie pobierania próbek i brak standardowych telemetrii Inicjatory lub procesorów.

Poniżej przedstawiono przykłady `Program.cs` i `Startup.cs` przy użyciu tej możliwości.

#### <a name="example-programcs"></a>Przykład pliku Program.cs

```csharp
    using Microsoft.AspNetCore;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Extensions.Logging;

    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateWebHostBuilder(args).Build();
            var logger = host.Services.GetRequiredService<ILogger<Program>>();
            // This will be picked up by AI
            logger.LogInformation("From Program. Running the host now..");
            host.Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging(
              builder =>
                  {
                    // providing an instrumentation key here is required if you are using standalone package Microsoft.Extensions.Logging.ApplicationInsights
                    // or if you want to capture logs from early in the application startup pipeline from Startup.cs or Program.cs itself.
                    builder.AddApplicationInsights("ikey");

                    // Adding the filter below to ensure logs of all severity from Program.cs is sent to ApplicationInsights.
                    // Replace YourAppName with the namespace of your application's Program.cs
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("YourAppName.Program", LogLevel.Trace);
                    // Adding the filter below to ensure logs of all severity from Startup.cs is sent to ApplicationInsights.
                    // Replace YourAppName with the namespace of your application's Startup.cs
                    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("YourAppName.Startup", LogLevel.Trace);
                  }
                );
    }
```

#### <a name="example-startupcs"></a>Przykład pliku Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migrowanie ze starych ApplicationInsightsLoggerProvider

Wersje zestawu SDK Microsoft.ApplicationInsights.AspNet przed 2.7.0-beta2, obsługiwane dostawcy logowania, która jest obecnie przestarzała. Ten dostawca został włączony `AddApplicationInsights()` metody rozszerzenia `ILoggerFactory`. Ten dostawca jest obecnie przestarzała i przeprowadzić migrację do nowego dostawcę sugerowane są użytkownicy. Migracja obejmuje dwa kroki.

1. Usuń wywołanie funkcji ILoggerFactory.AddApplicationInsights() z `Startup.Configure()` metodę, aby uniknąć podwójnego rejestrowania.
2. Ponieważ nie będą dotyczyły przez nowego dostawcę, należy ponownie zastosować żadnych reguł filtrowania w kodzie. Przeciążenia ILoggerFactory.AddApplicationInsights() miał minimalne funkcje LogLevel lub filtru. Za pomocą nowego dostawcę filtrowanie jest częścią struktury rejestrowania i nie jest wykonywane przez dostawcę usługi Application Insights. Filtry, więc wszelkie udostępniane za pośrednictwem `ILoggerFactory.AddApplicationInsights()` przeciążenia, które powinny zostać usunięte, a reguły filtrowania należy podać następujące [te](#control-logging-level) instrukcje. Jeśli używasz `appsettings.json` filtrującą dane rejestrowania, jego będą nadal działać przy użyciu nowego dostawcę zarówno do używania tego samego Alias dostawcy - **ApplicationInsights**.

Gdy starego dostawcy usług mogą być nadal używane (go jest teraz przestarzały i zostanie usunięta tylko w wersji głównej zmiany 3.xx), migracja do dostawcy nowszej zdecydowanie zaleca się z następujących powodów.

1. Poprzedni dostawca bez obsługi [zakresy](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). W przypadku nowego dostawcę właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowych do zebranych danych telemetrycznych.
2. Dzienniki mogą być przechwytywane teraz znacznie wcześniej w potoku do uruchamiania aplikacji. i.e Teraz można przechwycić dzienników z klas programu i uruchamiania.
3. Za pomocą nowego dostawcę odbywa się na poziomie framework sam. Filtrowanie dzienników do dostawcy usługi Application Insights może odbywać się w dokładnie taki sam sposób jak w przypadku innych dostawców rozwiązań, w tym wbudowane dostawców, takich jak konsoli debugowania i tak dalej. Istnieje również możliwość zastosowania tych samych filtrów do wielu dostawców.
4. [Zalecane](https://github.com/aspnet/Announcements/issues/255) sposobem w programie Asp.Net Core (2.0 lub nowszy) Włącz rejestrowanie dostawców jest przy użyciu metody rozszerzenia na ILoggingBuilder w `Program.cs` sam.

> [!Note]
Nowego dostawcy jest dostępna dla aplikacji przeznaczonych na `NETSTANDARD2.0` lub nowszej. Jeśli aplikacja jest przeznaczone dla starszej wersji platformy .NET Core, takich jak .NET Core 1.1 lub przeznaczonych dla platformy .NET Framework, można nadal używać starego dostawcy usług.

## <a name="console-application"></a>Aplikacja konsolowa

Poniższy kod pokazuje Przykładowa aplikacja Konsolowa skonfigurowane do wysyłania `ILogger` ślady do usługi Application Insights.

Zainstalowane pakiety:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
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

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to Application Insights for all
            // categories.
            loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                                                                    ("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

W powyższym przykładzie pakietu autonomicznego `Microsoft.Extensions.Logging.ApplicationInsights` jest używany. Domyślnie ta konfiguracja korzysta z absolutnego minimum `TelemetryConfiguration` do wysyłania danych do usługi Application Insights. Absolutnego minimum oznacza, że kanał używany będzie `InMemoryChannel`, nie pobierania próbek i nie TelemetryInitializers standardowych. To zachowanie można przesłonić dla aplikacji konsoli, jak pokazano w poniższym przykładzie.

Zainstalować ten pakiet dodatkowe:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślny `TelemetryConfiguration` przy użyciu `services.Configure<TelemetryConfiguration>()` metody. Ten przykład konfiguruje `ServerTelemetryChannel`, pobieranie próbek i dodaje niestandardową `ITelemetryInitializer` do `TelemetryConfiguration`.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Poziom rejestrowania formantu

Asp.Net Core `ILogger` infra ma wbudowany mechanizm do zastosowania [filtrowanie](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) dzienników, co umożliwia użytkownikom określanie dzienniki wysyłane do zarejestrowanych dostawców, w tym dostawcy usługi Application Insights. Filtrowanie może odbywać się w konfiguracji (zazwyczaj przy użyciu `appsettings.json` plików) lub w kodzie. Tej funkcji znajduje się w samej strukturze, a nie jest specyficzne dla dostawcy usługi Application Insights.

Poniżej podano przykłady stosowania reguły filtra do ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Tworzenie reguły filtrów w konfiguracji z pliku appsettings.json

ApplicationInsightsLoggerProvider, alias dostawcy jest `ApplicationInsights`. Poniżej pokazano sekcji `appsettings.json` Konfiguruje dzienniki `Warning` i nowsze wersje z wszystkich kategorii `Error` i nowsze wersje z kategorii, rozpoczynając od "Microsoft", który ma zostać wysłany do `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Tworzenie reguły filtrowania w kodzie

Poniższego kodu, fragment kodu Konfiguruje dzienniki `Warning` i nowsze wersje z wszystkich kategorii `Error` i nowsze wersje z kategorii, począwszy od Microsoft, które zostanie wysłane do `ApplicationInsightsLoggerProvider`. Ta konfiguracja jest taka sama jak powyżej w pliku config `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.Warning)
               .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*1. Co to jest ApplicationInsightsLoggerProvider stare i nowe?*

* [Zestaw SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) są dostarczane z wbudowanych ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), który został włączony, za pomocą element ILoggerFactory metody rozszerzenia. Ten dostawca jest oznaczony jako przestarzały z 2.7.0-beta2 lub nowszy i zostaną całkowicie usunięte w następnej wersji głównej zmiany. [To](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pakietu sam nie jest przestarzały i jest wymagana, aby włączyć monitorowanie żądań, zależności itd.

* Sugerowane alternatywne jest nowy pakiet autonomiczny [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), zawierający ulepszone (ApplicationInsightsLoggerProvider Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) i metod rozszerzeń na ILoggerBuilder Włączanie go.

* [Zestaw SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 wersji i nowszych wersjach będą zależni powyższych pakietów i umożliwia `ILogger` przechwytywania automatycznie.

*2. Widzę niektórych `ILogger` dzienniki są wyświetlane dwa razy w usłudze Application Insights?*

* Ta duplikacja jest możliwe, jeśli masz starszą wersję (teraz przestarzały) `ApplicationInsightsLoggerProvider` włączone przez wywołanie metody `AddApplicationInsights` na `ILoggerFactory`. Sprawdź, czy Twoje `Configure` metoda ma i usuń go.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* W przypadku rejestrowania podwójne podczas debugowania w programie Visual Studio, zmodyfikuj kod używany do następująco, włącz usługę Application Insights, ustawiając `EnableDebugLogger` wartość false. Tego problemu dublowania i poprawka dotyczy tylko podczas debugowania aplikacji.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Zaktualizowano w celu [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) teraz zwróciła 2.7.0-beta3 wersji i loguje się z `ILogger` są przechwytywane automatycznie. Jak można wyłączyć tę funkcję całkowicie?*

* Zobacz [to](../../azure-monitor/app/ilogger.md#control-logging-level) sekcji, aby wiedzieć, jak filtrować rejestruje ogólnie rzecz biorąc. Aby ApplicationInsightsLoggerProvider wyłączysz użyj `LogLevel.None` dla niego.

  W kodzie

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("", LogLevel.None);
    ```

  W konfiguracji

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Widzę niektórych `ILogger` dzienniki nie są mających te same właściwości co inne osoby?*

* Application Insights przechwytywania i wysyła `ILogger` dzienniki, korzystając z tych samych `TelemetryConfiguration` używane dla każdego dane telemetryczne. Występuje wyjątek od tej reguły. Wartość domyślna `TelemetryConfiguration` nie pełni skonfiguruj, kiedy rejestruje czegoś ze `Program.cs` lub `Startup.cs` siebie, więc dzienników z tych miejsc bez domyślnej konfiguracji i dlatego nie będzie uruchomiony wszystkie `TelemetryInitializer`s i `TelemetryProcessor`s.

*5. Jakiego typu danych telemetrycznych usługi Application Insights jest generowany z `ILogger` dzienniki? lub gdzie mogę znaleźć `ILogger` dzienników w usłudze Application Insights?*

* Przechwytuje ApplicationInsightsLoggerProvider `ILogger` dzienniki i tworzy `TraceTelemetry` z niego. Jeśli obiekt wyjątku jest przekazywany do metody Log() na ILogger, następnie zamiast `TraceTelemetry`, `ExceptionTelemetry` zostanie utworzony. Te elementy danych telemetrycznych można znaleźć w tych samych miejsc, jak każdy inny `TraceTelemetry` lub `ExceptionTelemetry` dla usługi Application Insights, w tym portalu, analizy i lokalny debuger programu Visual Studio.
Jeśli wolisz zawsze wysyłaj `TraceTelemetry`, następnie za pomocą fragmentu kodu ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*5. Nie mam zainstalowany zestaw SDK, a za pomocą rozszerzenia aplikacji sieci Web platformy Azure można włączyć usługi Application Insights dla aplikacji platformy Asp.Net Core. Jak używać nowego dostawcę?*

* Rozszerzenie usługi Application Insights w aplikacji sieci Web platformy Azure używa starego dostawcy usług. Reguły filtrowania można modyfikować w `appsettings.json` dla aplikacji. Jeśli chcesz korzystać z zalet nowego dostawcę, należy użyć Instrumentacja w czasie kompilacji, biorąc pod uwagę zależności nuget zestawu SDK. Ten dokument zostanie zaktualizowany, gdy rozszerzenie jest przełączany na używanie nowego dostawcę.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

* [Rejestrowanie w programie Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia .NET w usłudze Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
