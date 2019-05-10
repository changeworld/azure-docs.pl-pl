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
ms.openlocfilehash: ca842ce46a58dafa87581b77bcbd802191f7fcd1
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511026"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider dla platformy .NET Core ILogger dzienników

Platforma ASP.NET Core obsługuje API rejestrowania, który współpracuje z różnymi rodzajami rejestrowania wbudowanych i innych dostawców. Rejestrowanie odbywa się przez wywołanie metody **Log()** lub w wariancie je na *ILogger* wystąpień. W tym artykule przedstawiono sposób użycia *ApplicationInsightsLoggerProvider* do przechwytywania ILogger dzienniki konsoli i aplikacje platformy ASP.NET Core. W tym artykule opisano również, jak ApplicationInsightsLoggerProvider integruje się z innymi telemetria usługi Application Insights.
Aby dowiedzieć się więcej, zobacz [rejestrowania w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplikacje platformy ASP.NET Core

ApplicationInsightsLoggerProvider jest domyślnie włączone w [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 wersji (i nowszych) po ponownym włączeniu regularnego monitorowania usługi Application Insights przy użyciu jednej ze standardowych metody:
- Przez wywołanie metody **UseApplicationInsights** IWebHostBuilder metody rozszerzenia 
- Przez wywołanie metody **AddApplicationInsightsTelemetry** IServiceCollection metody rozszerzenia

Dzienniki ILogger, które przechwytuje ApplicationInsightsLoggerProvider podlegają taką samą konfigurację jak inne dane telemetryczne, które są zbierane. Mają tego samego zestawu TelemetryInitializers i TelemetryProcessors, użyj tego samego TelemetryChannel i są powiązane i próbkowania w taki sam sposób jak inne dane telemetryczne. Jeśli używasz wersji 2.7.0-beta3 lub nowszy, jest wymagana do przechwytywania dzienników ILogger żadna akcja.

Tylko *ostrzeżenie* lub wyższej dzienniki ILogger (z wszystkich kategorii) są domyślnie wysyłane do usługi Application Insights. Ale możesz [Zastosuj filtry, aby zmodyfikować to zachowanie](#control-logging-level). Dodatkowe kroki są wymagane do przechwytywania dzienników ILogger z **Program.cs** lub **Startup.cs**. (Zobacz [przechwytywania ILogger loguje się w pliku Startup.cs i Program.cs aplikacje platformy ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Jeśli używasz starszej wersji zestawu Microsoft.ApplicationInsights.AspNet SDK lub po prostu użyć ApplicationInsightsLoggerProvider bez żadnych innych monitorowanie usługi Application Insights, użyj następującej procedury:

1. Zainstaluj pakiet NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modyfikowanie **Program.cs** jak pokazano poniżej:

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
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Kod w kroku 2 służy do konfigurowania `ApplicationInsightsLoggerProvider`. Poniższy kod przedstawia przykład klasy kontrolera, który używa `ILogger` Wyślij dzienniki. Dzienniki są przechwytywane przez usługę Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Przechwytywanie dzienniki ILogger z pliku Startup.cs i Program.cs w aplikacji platformy ASP.NET Core

Nowe ApplicationInsightsLoggerProvider można przechwycić dzienników z wczesnym etapie procesu uruchamiania aplikacji. Mimo że ApplicationInsightsLoggerProvider jest automatycznie włączone w usłudze Application Insights (począwszy od wersji 2.7.0-beta3), nie ma klucza instrumentacji, aż później skonfigurować potok. Dlatego tylko dzienniki z **kontrolera**/ będzie można przechwycić innych klas. Do przechwytywania każdego dziennika, począwszy od **Program.cs** i **Startup.cs** , musisz jawnie włączyć klucz instrumentacji dla ApplicationInsightsLoggerProvider. Ponadto *TelemetryConfiguration* nie zostało w pełni skonfigurowane podczas logowania z **Program.cs** lub **Startup.cs** sam. Dlatego te dzienniki mają minimalnej konfiguracji, korzystającą z InMemoryChannel, nie próbkowanie i nie inicjatory standardowa telemetria lub procesorów.

W poniższych przykładach pokazano tej funkcji za pomocą **Program.cs** i **Startup.cs**.

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
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrowanie ze starych ApplicationInsightsLoggerProvider

Wersje zestawu SDK Microsoft.ApplicationInsights.AspNet przed 2.7.0-beta2 obsługiwane dostawcy logowania, która jest teraz przestarzały. Ten dostawca został włączony przez **AddApplicationInsights()** metody rozszerzenia element ILoggerFactory. Zaleca się, które można migrować do nowego dostawcę, który obejmuje dwa kroki:

1. Usuń *ILoggerFactory.AddApplicationInsights()* wywołać z **Startup.Configure()** metodę, aby uniknąć podwójnego rejestrowania.
2. Ponieważ nie będą dotyczyły przez nowego dostawcę, należy ponownie zastosować żadnych reguł filtrowania w kodzie. Przeciążenia *ILoggerFactory.AddApplicationInsights()* miał minimalne funkcje LogLevel lub filtru. Za pomocą nowego dostawcę filtrowanie jest częścią struktury rejestrowania się. Nie jest wykonywane przez dostawcę usługi Application Insights. Dlatego wszystkie filtry, które są udostępniane za pośrednictwem *ILoggerFactory.AddApplicationInsights()* przeciążenia, które powinny zostać usunięte. I reguły filtrowania, należy podać wykonując [kontrolować poziom rejestrowania](#control-logging-level) instrukcje. Jeśli używasz *appsettings.json* filtrującą dane rejestrowania, go będą nadal działać przy użyciu nowego dostawcę, ponieważ obaj użytkownicy za pomocą takiego samego aliasu dostawcy *ApplicationInsights*.

Można nadal używać starego dostawcy usług. (Zostanie on usunięty tylko w przypadku zmiany wersji głównej 3. *xx*.) Jednak zaleca się przeprowadzenie migracji do nowego dostawcę z następujących powodów:

- Poprzedni dostawca brakuje obsługi [dziennika zakresy](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). W przypadku nowego dostawcę właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowych do zebranych danych telemetrycznych.
- Dzienniki mogą być przechwytywane teraz znacznie wcześniej w potoku do uruchamiania aplikacji. Dzienniki z **Program** i **uruchamiania** klasy teraz mogą być przechwytywane.
- Za pomocą nowego dostawcę filtrowanie odbywa się na poziomie framework sam. Można filtrować dzienniki dostawcy usługi Application Insights w taki sam sposób jak w przypadku innych dostawców rozwiązań, w tym wbudowane dostawców, takich jak konsoli debugowania i tak dalej. Można również zastosować tych samych filtrów do wielu dostawców.
- W programie ASP.NET Core (2.0 lub nowszy), zalecanym sposobem [włączyć rejestrowanie dostawców](https://github.com/aspnet/Announcements/issues/255) przy użyciu metody rozszerzenia na ILoggingBuilder w **Program.cs** sam.

> [!Note]
> Nowego dostawcy jest dostępna dla aplikacji, których platformą docelową NETSTANDARD2.0 lub nowszej. Jeśli aplikacja jest przeznaczony dla starszych wersji platformy .NET Core, takich jak .NET Core 1.1, lub jest ono przeznaczone dla .NET Framework, można nadal używać starego dostawcy usług.

## <a name="console-application"></a>Aplikacja konsolowa

Poniższy kod pokazuje Przykładowa aplikacja konsolowa, która jest skonfigurowana do wysyłania danych śledzenia ILogger do usługi Application Insights.

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
        // Create the DI container.
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
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
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

W tym przykładzie użyto pakietu autonomicznego `Microsoft.Extensions.Logging.ApplicationInsights`. Domyślnie ta konfiguracja korzysta z "absolutnego minimum" TelemetryConfiguration do wysyłania danych do usługi Application Insights. Absolutnego minimum oznacza, że InMemoryChannel kanał, który jest używany. Nie ma żadnych TelemetryInitializers pobierania próbek i standard nie. To zachowanie można przesłonić dla aplikacji konsoli, co ilustruje poniższy przykład.

Zainstalować ten pakiet dodatkowe:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślny TelemetryConfiguration za pomocą **usług. Konfigurowanie<TelemetryConfiguration>()** metody. Ten przykład konfiguruje `ServerTelemetryChannel` i próbkowania. Dodaje niestandardowy ITelemetryInitializer do TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Poziom rejestrowania formantu

ASP.NET Core *ILogger* infra ma wbudowany mechanizm do zastosowania [filtrowanie dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Dzięki temu można kontrolować dzienników, które są wysyłane do każdego zarejestrowanego dostawcę, w tym dostawcy usługi Application Insights. Filtrowanie może odbywać się w konfiguracji (zazwyczaj przy użyciu *appsettings.json* plików) lub w kodzie. Ten obiekt jest zapewniana przez framework sam w sobie. Nie jest specyficzne dla dostawcy usługi Application Insights.

Poniższe przykłady dotyczą ApplicationInsightsLoggerProvider reguły filtrowania.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Tworzenie reguły filtrów w konfiguracji z pliku appsettings.json

ApplicationInsightsLoggerProvider, alias dostawcy jest `ApplicationInsights`. Poniższa sekcja *appsettings.json* Konfiguruje dzienniki *ostrzeżenie* i nowsze wersje z wszystkich kategorii i *błąd* i nowsze wersje z kategorii, które zaczyna się " Microsoft"do wysłania do `ApplicationInsightsLoggerProvider`.

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

Poniższy fragment kodu Konfiguruje dzienniki *ostrzeżenie* i nowsze wersje z wszystkich kategorii i dla *błąd* i nowsze wersje z kategorii rozpoczynających się od "Microsoft", który ma zostać wysłany do `ApplicationInsightsLoggerProvider`. Ta konfiguracja jest taki sam jak w poprzedniej sekcji w *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Co to są stare i nowe wersje ApplicationInsightsLoggerProvider?

[Zestaw SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) uwzględnione ApplicationInsightsLoggerProvider wbudowane (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), który został włączony przez  **Element ILoggerFactory** metody rozszerzenia. Ten dostawca jest oznaczony jako przestarzały z 2.7.0-beta2 wersji. Zostanie on usunięty całkowicie w następnej wersji głównej zmiany. [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pakietu nie jest przestarzały. Jest to wymagane, aby włączyć monitorowanie żądań, zależności i tak dalej.

Sugerowane alternatywne jest nowy pakiet autonomiczny [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), który zawiera ulepszone (ApplicationInsightsLoggerProvider Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) i metody rozszerzenia na ILoggerBuilder Włączanie go.

[Zestaw SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 wersji ma zależność od nowego pakietu i automatycznie włącza Przechwytywanie ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Dlaczego niektóre dzienniki ILogger są wyświetlane dwa razy w usłudze Application Insights?

Duplikowanie może wystąpić, jeśli masz starszą wersję (teraz przestarzały) ApplicationInsightsLoggerProvider włączone przez wywołanie metody `AddApplicationInsights` na `ILoggerFactory`. Sprawdź, czy Twoje **Konfiguruj** metoda ma i usuń go:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Występują podwójnego rejestrowania podczas debugowania w programie Visual Studio, należy ustawić `EnableDebugLogger` do *false* w kodzie, który umożliwia usługi Application Insights w następujący sposób. Ten dublowania i poprawka dotyczy tylko podczas debugowania aplikacji.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Zaktualizowano w celu [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 wersji i dzienniki z ILogger są przechwytywane automatycznie. Jak wyłączyć tę funkcję całkowicie?

Zobacz [kontrolować poziom rejestrowania](../../azure-monitor/app/ilogger.md#control-logging-level) sekcji, aby dowiedzieć się, jak filtrować rejestruje ogólnie rzecz biorąc. Aby ApplicationInsightsLoggerProvider wyłączysz użyj `LogLevel.None`:

**W kodzie:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**W konfiguracji:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Dlaczego niektóre dzienniki ILogger nie mają te same właściwości co inne osoby?

Usługa Application Insights powoduje przechwycenie i wysyła dzienniki ILogger przy użyciu tego samego TelemetryConfiguration, używany do każdego dane telemetryczne. Występuje wyjątek. Domyślnie TelemetryConfiguration nie jest w pełni skonfigurowany podczas logowania z **Program.cs** lub **Startup.cs**. Dzienniki z tych miejsc nie mają domyślnej konfiguracji, dlatego nie być uruchomiona, wszystkie TelemetryInitializers i TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Używam pakietu autonomicznego Microsoft.Extensions.Logging.ApplicationInsights i chcę ręcznie rejestrować pewne dodatkowe niestandardowych danych telemetrycznych. Jak należy zrobić?

Gdy używasz pakietu autonomicznego `TelemetryClient` nie są wstrzykiwane do kontenera DI, więc musisz utworzyć nowe wystąpienie klasy `TelemetryClient` i użyj takiej samej konfiguracji jako używa dostawcy rejestratora, co ilustruje poniższy kod. Gwarantuje to, że taką samą konfiguracją jest używany dla wszystkich niestandardowych danych telemetrycznych, a także dane telemetryczne z ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Jeśli używasz pakietu Microsoft.ApplicationInsights.AspNetCore, aby włączyć usługę Application Insights, zmodyfikuj ten kod, aby uzyskać `TelemetryClient` bezpośrednio w konstruktorze. Aby uzyskać przykład, zobacz [tych często zadawanych Pytaniach](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jakiego typu danych telemetrycznych usługi Application Insights są generowane z dzienników ILogger? Lub gdzie mogę znaleźć dzienników ILogger w usłudze Application Insights?

ApplicationInsightsLoggerProvider przechwytuje ILogger dzienniki i tworzy TraceTelemetry z nich. Jeśli obiekt wyjątku jest przekazywany do **Log()** metody ILogger, *ExceptionTelemetry* jest tworzony zamiast TraceTelemetry. Te elementy danych telemetrycznych można znaleźć w tych samych miejsc co inne TraceTelemetry lub ExceptionTelemetry dla usługi Application Insights, w tym portalu, analizy i lokalny debuger programu Visual Studio.

Jeśli wolisz zawsze wysyłaj TraceTelemetry, za pomocą tego fragmentu kodu: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Zainstalowany zestaw SDK nie jest dostępny i czy za pomocą usługi Azure Web Apps rozszerzenia Włącz usługę Application Insights dla aplikacji platformy ASP.NET Core. Jak używać nowego dostawcę? 

Rozszerzenie usługi Application Insights w usłudze Azure Web Apps używa starego dostawcy usług. Można zmodyfikować reguły filtrowania w *appsettings.json* pliku dla aplikacji. Aby skorzystać z nowego dostawcę, należy użyć Instrumentacja w czasie kompilacji biorąc pod uwagę zależności NuGet zestawu SDK. Ten artykuł zostanie zaktualizowany, gdy rozszerzenie jest przełączany na używanie nowego dostawcę.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Za pomocą pakietu autonomicznego Microsoft.Extensions.Logging.ApplicationInsights i włączanie dostawcy usługi Application Insights, wywołując **konstruktora. AddApplicationInsights("ikey")**. Czy istnieje możliwość uzyskania klucza Instrumentacji z konfiguracji?


Zmodyfikuj plik Program.cs i appsettings.json:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Odpowiedniej sekcji z `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ten kod jest wymagany tylko wtedy, gdy używasz dostawcy rejestrowania autonomicznego. Regularnego monitorowania usługi Application Insights, aby uzyskać klucz Instrumentacji zostanie załadowany ze ścieżki konfiguracji *dotycząca usługi Application Insights: Instrumentationkey*. AppSettings.JSON powinien wyglądać następująco:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usługach:

* [Rejestrowanie w programie ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia .NET w usłudze Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
