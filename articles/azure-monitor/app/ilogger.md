---
title: Eksploruj dzienniki śledzenia platformy .NET za pomocą aplikacji ILogger — usługa Azure Application Insights
description: Przykłady korzystania z dostawcy usługi Azure Application Insights ILogger z aplikacjami ASP.NET Core i konsoli.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0f40c1c1a8ee7f20c769a62e9746da43face4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276380"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider dla dzienników iloggera .NET Core

ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi rodzajami wbudowanych i zewnętrznych dostawców rejestrowania. Rejestrowanie odbywa się przez **wywołanie Log()** lub wariant go w wystąpieniach *ILogger.* W tym artykule pokazano, jak używać *ApplicationInsightsLoggerProvider* do przechwytywania dzienników ILogger w konsoli i ASP.NET aplikacji Core. W tym artykule opisano również, jak ApplicationInsightsLoggerProvider integruje się z innymi dane telemetryczne usługi Application Insights.
Aby dowiedzieć się więcej, zobacz [Logowanie w ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Podstawowe aplikacje

ApplicationInsightsLoggerProvider jest domyślnie włączony w [programie Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.1 (i nowszej) po włączeniu regularnego monitorowania usługi Application Insights za pomocą jednej z metod:

- Wywołując **UseApplicationInsights** metody rozszerzenia na IWebHostBuilder (Teraz przestarzałe)
- Wywołując **AddApplicationInsightsTelemetry** metody na IServiceCollection

Dzienniki ILogger, że AplikacjaInsightsLoggerDograł apvider podlegają tej samej konfiguracji, jak inne dane telemetryczne, które są zbierane. Mają ten sam zestaw TelemetryInilizatory i TelemetryProcessors, używają tego samego TelemetryChannel i są skorelowane i próbkowane w taki sam sposób jak inne dane telemetryczne. Jeśli używasz wersji 2.7.1 lub nowszej, nie jest wymagana żadna akcja do przechwytywania dzienników ILogger.

Tylko *dzienniki ILogger ostrzeżenie* lub wyższe (ze wszystkich [kategorii)](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-3.1#log-category)są wysyłane do usługi Application Insights domyślnie. Ale można [zastosować filtry, aby zmodyfikować to zachowanie](#control-logging-level). Dodatkowe kroki są wymagane do przechwytywania dzienników ILogger z **Program.cs** lub **Startup.cs**. (Zobacz [Przechwytywanie dzienników ILogger z Startup.cs i Program.cs w aplikacjach ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Jeśli używasz starszej wersji microsoft.ApplicationInsights.AspNet SDK lub chcesz po prostu użyć ApplicationInsightsLoggerProvider bez innego monitorowania usługi Application Insights, należy użyć następującej procedury:

1. Zainstaluj pakiet NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Zmodyfikuj **Program.cs,** jak pokazano poniżej:

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

Kod w kroku 2 `ApplicationInsightsLoggerProvider`konfiguruje . Poniższy kod przedstawia przykład controller klasy, który używa `ILogger` do wysyłania dzienników. Dzienniki są przechwytywane przez usługa Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Przechwytywanie dzienników ILogger z Startup.cs i Program.cs w aplikacjach ASP.NET Core

> [!NOTE]
> W ASP.NET Core 3.0 i nowszych nie `ILogger` jest już możliwe wstrzykiwanie Startup.cs i Program.cs. Zobacz https://github.com/aspnet/Announcements/issues/353 więcej szczegółów.

Nowy ApplicationInsightsLoggerProvider można przechwytywać dzienniki z początku potoku uruchamiania aplikacji. Mimo że ApplicationInsightsLoggerProvider jest automatycznie włączony w usłudze Application Insights (począwszy od wersji 2.7.1), nie ma klucza instrumentacji skonfigurowane dopiero później w potoku. Tak więc tylko dzienniki z **kontrolera**/inne klasy zostaną przechwycone. Aby przechwycić każdy dziennik, począwszy od **Program.cs** i **Startup.cs** się, należy jawnie włączyć klucz instrumentacji dla ApplicationInsightsLoggerProvider. Ponadto *telemetryConfiguration* nie jest w pełni skonfigurowany podczas logowania z **Program.cs** lub **Startup.cs** siebie. Więc te dzienniki będą miały minimalną konfigurację, która używa InMemoryChannel, bez próbkowania i bez standardowych inicjatorów telemetrii lub procesorów.

Poniższe przykłady pokazują tę możliwość za pomocą **Program.cs** i **Startup.cs**.

#### <a name="example-programcs"></a>Przykład Program.cs

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

#### <a name="example-startupcs"></a>Przykład Startup.cs

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrowanie ze starego aplikacjiInsightsLoggerProvider

Microsoft.ApplicationInsights.AspNet SDK wersje przed 2.7.1 obsługiwane dostawcy rejestrowania, który jest teraz przestarzały. Ten dostawca został włączony za pomocą metody rozszerzenia **AddApplicationInsights()** iLoggerFactory. Zaleca się migrację do nowego dostawcy, która obejmuje dwa kroki:

1. Usuń wywołanie *ILoggerFactory.AddApplicationInsights()* z metody **Startup.Configure(),** aby uniknąć podwójnego rejestrowania.
2. Ponownie zastosować wszystkie reguły filtrowania w kodzie, ponieważ nie będą przestrzegane przez nowego dostawcę. Przeciążenia *iLoggerFactory.AddApplicationInsights()* miały minimalne funkcje LogLevel lub filtrowania. Z nowym dostawcą filtrowanie jest częścią samej struktury rejestrowania. Nie jest wykonywana przez dostawcę usługi Application Insights. Tak więc wszelkie filtry, które są dostarczane za pośrednictwem *iLoggerFactory.AddApplicationInsights()* przeciążenia powinny zostać usunięte. I reguły filtrowania powinny być dostarczane przez następujące [instrukcje poziomu rejestrowania kontroli.](#control-logging-level) Jeśli używasz *appsettings.json* do filtrowania rejestrowania, będzie nadal współpracować z nowym dostawcą, ponieważ oba używają tego samego aliasu dostawcy, *ApplicationInsights*.

Nadal można użyć starego dostawcy. (Zostanie on usunięty tylko w głównej zmianie wersji na 3. *xx*.) Zalecamy jednak migrację do nowego dostawcy z następujących powodów:

- Poprzedni dostawca nie obsługuje [zakresów dziennika](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). W nowym dostawcy właściwości z zakresu są automatycznie dodawane jako właściwości niestandardowe do zebranych danych telemetrycznych.
- Dzienniki mogą być teraz przechwytywane znacznie wcześniej w potoku uruchamiania aplikacji. Dzienniki z **programów** i **uruchamiania** klas można teraz przechwytywane.
- Z nowym dostawcą filtrowanie odbywa się na poziomie struktury. Dzienniki usługi Application Insights można filtrować w taki sam sposób, jak w przypadku innych dostawców, w tym wbudowanych dostawców, takich jak Console, Debug i tak dalej. Te same filtry można również zastosować do wielu dostawców.
- W ASP.NET Core (2.0 i nowsze) zalecanym sposobem [włączenia dostawców rejestrowania](https://github.com/aspnet/Announcements/issues/255) jest użycie metod rozszerzenia na ILoggingBuilder w **Program.cs.**

> [!Note]
> Nowy dostawca jest dostępny dla aplikacji docelowych NETSTANDARD2.0 lub nowszych. Jeśli aplikacja jest przeznaczona dla starszych wersji .NET Core, takich jak .NET Core 1.1 lub jeśli jest przeznaczona dla programu .NET Framework, nadal używaj starego dostawcy.

## <a name="console-application"></a>Aplikacja konsoli

> [!NOTE]
> Istnieje nowy SDK usługi Application Insights o nazwie [Microsoft.ApplicationInsights.WorkerService,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) który może służyć do włączania usługi Application Insights (ILogger i inne dane telemetryczne usługi Application Insights) dla dowolnych aplikacji konsoli. Zaleca się użycie tego pakietu i powiązanych instrukcji [stąd](../../azure-monitor/app/worker-service.md).

Poniższy kod przedstawia przykładową aplikację konsoli, która jest skonfigurowana do wysyłania śladów ILogger do usługi Application Insights.

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

W tym przykładzie użyto `Microsoft.Extensions.Logging.ApplicationInsights`pakietu autonomicznego . Domyślnie ta konfiguracja używa "minimum minimum" TelemetryConfiguration do wysyłania danych do usługi Application Insights. Minimum oznacza, że InMemoryChannel jest kanałem, który jest używany. Nie ma próbkowania i nie ma standardowych TelemetryInilizatory. To zachowanie można zastąpić dla aplikacji konsoli, jak pokazano w poniższym przykładzie.

Zainstaluj ten dodatkowy pakiet:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

W poniższej sekcji pokazano, jak zastąpić domyślną konfigurację telemetryczną przy użyciu **usług. Konfigurowanie\<metody>() konfiguracji telemetryconfiguration.** W tym `ServerTelemetryChannel` przykładzie konfiguruje i próbkowania. Dodaje niestandardowy ITelemetryInitializer do TelemetryConfiguration.

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

## <a name="control-logging-level"></a>Poziom rejestrowania sterowania

ASP.NET Core *ILogger* infra ma wbudowany mechanizm do stosowania [filtrowania dziennika.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) Dzięki temu można kontrolować dzienniki, które są wysyłane do każdego zarejestrowanego dostawcy, w tym dostawcy usługi Application Insights. Filtrowanie można wykonać w konfiguracji (zazwyczaj przy użyciu pliku *appsettings.json)* lub w kodzie. Instrument ten jest zapewniony przez same ramy. Nie jest specyficzne dla dostawcy usługi Application Insights.

Poniższe przykłady zastosuj reguły filtru do ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Tworzenie reguł filtrowania w konfiguracji za pomocą pliku appsettings.json

W przypadku aplikacjiInsightsLoggerProvider alias `ApplicationInsights`dostawcy jest . W poniższej sekcji *appsettings.json* nakazuje dostawców rejestrowania zazwyczaj do logowania na poziomie *Ostrzeżenie* i powyżej. Następnie zastępuje `ApplicationInsightsLoggerProvider` do dziennika kategorii, które zaczynają się od "Microsoft" na poziomie *Błąd* i powyżej.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Tworzenie reguł filtrowania w kodzie

Poniższy fragment kodu konfiguruje dzienniki *dla ostrzeżenia* i powyżej ze wszystkich kategorii i *błędów* i powyżej `ApplicationInsightsLoggerProvider`z kategorii, które zaczynają się od "Microsoft", które mają być wysyłane do . Ta konfiguracja jest taka sama jak w poprzedniej sekcji w *pliku appsettings.json*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Jakie są stare i nowe wersje ApplicationInsightsLoggerProvider?

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) zawiera wbudowany ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), który został włączony za pomocą metod rozszerzenia **ILoggerFactory.** Ten dostawca jest oznaczony jako przestarzały od wersji 2.7.1. Zostanie on całkowicie usunięty w następnej istotnej zmianie wersji. Sam pakiet [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) nie jest przestarzały. Jest to wymagane, aby włączyć monitorowanie żądań, zależności i tak dalej.

Sugerowaną alternatywą jest nowy pakiet [autonomiczny Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), który zawiera ulepszony ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) i metody rozszerzenia w usłudze ILoggerBuilder w celu włączenia go.

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.1 przyjmuje zależność od nowego pakietu i automatycznie włącza przechwytywanie ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Dlaczego niektóre dzienniki ILogger są wyświetlane dwa razy w usłudze Application Insights?

Duplikacja może wystąpić, jeśli masz starszą (obecnie przestarzałą) wersję ApplicationInsightsLoggerProvider włączone przez wywołanie `AddApplicationInsights` `ILoggerFactory`. Sprawdź, czy metoda **Konfigurowanie** ma następujące właściwości, i usuń ją:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Jeśli wystąpi podwójne rejestrowanie podczas debugowania `EnableDebugLogger` z programu Visual Studio, ustaw *na false* w kodzie, który umożliwia usługi Application Insights, w następujący sposób. Ta duplikacja i poprawka jest istotne tylko podczas debugowania aplikacji.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Zaktualizowałem do [witryny Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) w wersji 2.7.1, a dzienniki z iLogger są przechwytywane automatycznie. Jak całkowicie wyłączyć tę funkcję?

Zobacz [sekcję Poziom rejestrowania sterowania,](../../azure-monitor/app/ilogger.md#control-logging-level) aby zobaczyć, jak filtrować dzienniki w ogóle. Aby wyłączyć applicationinsightsLoggerProvider, `LogLevel.None`należy użyć:

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Dlaczego niektóre dzienniki ILogger nie mają takich samych właściwości jak inne?

Usługa Application Insights przechwytuje i wysyła dzienniki ILogger przy użyciu tej samej telemetryConfiguration, która jest używana dla każdej innej telemetrii. Ale jest wyjątek. Domyślnie konfiguracja telemetryconfiguration nie jest w pełni skonfigurowana podczas logowania z **Program.cs** lub **Startup.cs**. Dzienniki z tych miejsc nie będą miały domyślnej konfiguracji, więc nie będą uruchamiane wszystkie telemetryinilizatory i telemetryprocesorów.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Używam autonomicznego pakietu Microsoft.Extensions.Logging.ApplicationInsights i chcę ręcznie zarejestrować kilka dodatkowych niestandardowych danych telemetrycznych. Jak to zrobić?

Podczas korzystania z pakietu autonomicznego, `TelemetryClient` nie jest wstrzykiwany do kontenera DI, `TelemetryClient` więc należy utworzyć nowe wystąpienie i używać tej samej konfiguracji, jak logger provider używa, jak pokazano w poniższym kodzie. Gwarantuje to, że ta sama konfiguracja jest używana dla wszystkich niestandardowych danych telemetrycznych, a także danych telemetrycznych z ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Jeśli używasz pakietu Microsoft.ApplicationInsights.AspNetCore, aby włączyć usługę `TelemetryClient` Application Insights, zmodyfikuj ten kod, aby uzyskać bezpośrednio w konstruktorze. Na przykład zobacz [to często zadawane pytania](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Jaki typ telemetrii usługi Application Insights jest produkowany z dzienników ILogger? Lub gdzie mogę zobaczyć dzienniki ILogger w usłudze Application Insights?

ApplicationInsightsLoggerProvider przechwytuje dzienniki ILogger i tworzy z nich tracetelemetry. Jeśli exception obiekt jest przekazywany do **Log()** metoda na ILogger, *ExceptionTelemetry* jest tworzony zamiast TraceTelemetry. Te elementy telemetryczne można znaleźć w tych samych miejscach, co inne tracetelemetry lub exceptiontelemetry dla usługi Application Insights, w tym portalu, analizy lub debugera lokalnego programu Visual Studio.

Jeśli wolisz zawsze wysyłać TraceTelemetry, użyj tego fragmentu:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Nie mam zainstalowanego narzędzia SDK i używam rozszerzenia Usługi Azure Web Apps, aby włączyć usługę Application Insights dla aplikacji ASP.NET Core. Jak korzystać z nowego dostawcy? 

Rozszerzenie usługi Application Insights w usłudze Azure Web Apps używa nowego dostawcy. Reguły filtrowania można zmodyfikować w pliku *appsettings.json* dla aplikacji.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Używam autonomicznego pakietu Microsoft.Extensions.Logging.ApplicationInsights i włączam dostawcę usługi Application Insights, wywołując **konstruktora. AddApplicationInsights("ikey")**. Czy istnieje opcja, aby uzyskać klucz oprzyrządowania z konfiguracji?


Modify Program.cs i appsettings.json w następujący sposób:

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

   Odpowiednia `appsettings.json`sekcja z:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Ten kod jest wymagany tylko wtedy, gdy używasz autonomicznego dostawcy rejestrowania. W przypadku regularnego monitorowania usługi Application Insights klucz instrumentacji jest ładowany automatycznie ze ścieżki konfiguracji *ApplicationInsights: Instrumentationkey*. Appsettings.json powinien wyglądać następująco:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

* [Logowanie do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Dzienniki śledzenia platformy .NET w usłudze Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
