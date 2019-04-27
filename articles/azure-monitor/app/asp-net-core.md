---
title: Usługi Azure Application Insights dla platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web platformy ASP.NET Core dla dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: ae0d3658d9ae8534b1596fa7363495926cd0dfe7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693918"
---
# <a name="application-insights-for-aspnet-core"></a>Usługa Application Insights dla aplikacji ASP.NET Core

Usługa Azure Application Insights zapewnia szczegółowe monitorowanie aplikacji sieci web do poziomu kodu. Możesz łatwo monitorować dostępność, wydajność i użycie aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika.

Ten artykuł przeprowadzi Cię przez kroki tworzenia przykładu platformy ASP.NET Core [stron Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikacji w programie Visual Studio. On również dowiesz się, jak rozpocząć monitorowanie za pomocą usługi Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- .NET core 2.0.0 SDK lub nowszy
- [Program Visual Studio 2017](https://www.visualstudio.com/downloads/) wersji 15.7.3 lub nowszej, za pomocą obciążenia programowania platformy ASP.NET i sieci web

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Tworzenie projektu platformy ASP.NET Core w programie Visual Studio

1. Kliknij prawym przyciskiem myszy **programu Visual Studio 2017**, a następnie wybierz pozycję **Uruchom jako administrator**.
2. Wybierz **pliku** > **nowe** > **projektu** (Ctrl + Shift + N).

   ![Menu Nowy projekt zrzut ekranu programu Visual Studio](./media/asp-net-core/001-new-project.png)

3. Rozwiń węzeł **Visual C#**. Wybierz **platformy .NET Core** > **aplikacji sieci Web platformy ASP.NET Core**. Wprowadź nazwę projektu i nazwę rozwiązania, a następnie wybierz **Utwórz nowe repozytorium Git**.

   ![Kreator nowego projektu zrzut ekranu programu Visual Studio](./media/asp-net-core/002-asp-net-core-web-application.png)

4. Wybierz **platformy .NET Core** > **platformy ASP.NET Core 2.0** **aplikacji sieci Web** > **OK**.

    ![Zrzut ekranu programu Visual Studio nowy wybór szablonu projektu](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Wyszukiwania usługi Application Insights

Program Visual Studio 2015 Update 2 lub nowszym z projektem platformy ASP.NET Core 2 + na podstawie możesz korzystać z zalet [wyszukiwania usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)nawet w przypadku, aby jawnie dodać usługę Application Insights do projektu.

Aby przetestować tę funkcję:

1. Uruchom aplikację. Aby uruchomić aplikację, zaznacz **usług IIS Express** ikonę (![zrzut ekranu programu Visual Studio usług IIS Express ikonę](./media/asp-net-core/004-iis-express.png)).

2. Wybierz **widoku** > **innych Windows** > **wyszukiwania usługi Application Insights**.

   ![Wybór narzędzia diagnostyczne zrzut ekranu programu Visual Studio](./media/asp-net-core/005-view-other-windows-search.png)

3. Obecnie dane telemetryczne sesji debugowania jest dostępna tylko dla lokalnych analizy. Aby w pełni włączyć usługi Application Insights, wybierz **gotowości Telemetrii** w prawym górnym rogu lub ukończone wszystkie czynności opisane w następnej sekcji.

   ![Zrzut ekranu programu Visual Studio Application Insights, wyszukiwanie](./media/asp-net-core/006-search.png)

> [!NOTE]
> Aby dowiedzieć się więcej na temat sposobu programu Visual Studio udostępnia funkcje, takie jak [wyszukiwania usługi Application Insights](../../azure-monitor/app/visual-studio.md) i [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) lokalnie przed dodaniem usługi Application Insights do projektu programu ASP.NET Core, zobacz [ Kontynuowanie wyszukiwania usługi Application Insights](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Dodawanie telemetrii usługi Application Insights

1. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights**. (Lub kliknąć prawym przyciskiem myszy **podłączone usługi**, a następnie wybierz pozycję **Dodaj podłączoną usługę**.)

    ![Zrzut ekranu programu Visual Studio nowe menu wyboru projektu](./media/asp-net-core/007-project-add-telemetry.png)

2. Wybierz **wprowadzenie**. (W zależności od używanej wersji programu Visual Studio, tekst może się nieco różnić. Niektóre starsze wersje mają **Rozpocznij za darmo** zamiast tego przycisku.)

    ![Zrzut ekranu z Application Insights przycisk Rozpocznij](./media/asp-net-core/008-get-started.png)

3. Wybierz swoją subskrypcję, a następnie wybierz **zasobów** > **zarejestrować**.

## <a name="changes-made-to-your-project"></a>Zmiany wprowadzone do projektu

Usługa Application Insights jest niskie obciążenie. Aby przejrzeć modyfikacje do projektu, dodając telemetria usługi Application Insights:

Wybierz **widoku** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **zmiany**

- Są wyświetlane cztery łączna liczba zmian:

  ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/asp-net-core/009-changes.png)

- Jeden nowy plik zostanie utworzony:

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- Trzy pliki są modyfikowane (dodatkowe komentarze dodane do Wyróżniaj zmiany):

  - _appsettings.json_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="send-ilogger-logs-to-application-insights"></a>Wysyłanie dzienników ILogger do usługi Application Insights

Usługa Application Insights obsługuje przechwytywania dzienniki wysłane za pośrednictwem ILogger. Aby skonfigurować rejestrowanie wyewidencjonowania przykłady kodu [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="synthetic-transactions-with-powershell"></a>Transakcje syntetyczne przy użyciu programu PowerShell

Aby zautomatyzować żądania do aplikacji za pomocą transakcji syntetycznych:

1. Aby uruchomić aplikację, wybierz pozycję ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/asp-net-core/004-iis-express.png) ikona.

2. Skopiuj adres URL z paska adresu przeglądarki. Adres URL jest w formacie `http://localhost:<port number>`.

   ![Zrzut ekranu przedstawiający na pasku adresu URL w przeglądarce](./media/asp-net-core/0013-copy-url.png)

3. Uruchom następującą pętlę programu PowerShell, aby utworzyć 100 transakcji syntetycznych przy użyciu aplikacji testu. Zmodyfikuj numer portu po `localhost:` dopasować adres URl, który został skopiowany w poprzednim kroku. Na przykład:

   ```powershell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Otwórz portal usługi Application Insights

Po uruchomieniu polecenia programu PowerShell w poprzedniej sekcji, otwórz usługę Application Insights, aby wyświetlić transakcji i upewnij się, że dane są zbierane. 

W menu programu Visual Studio wybierz **projektu** > **usługi Application Insights** > **Otwórz Portal usługi Application Insights**.

   ![Zrzut ekranu przedstawiający omówienie usługi Application Insights](./media/asp-net-core/010-portal.png)

> [!NOTE]
> Na poprzednim zrzucie ekranu pokazano przykład **Live Stream**, **czas ładowania wyświetlenia strony**, i **nieudanych żądań** nie są zbierane. Następna sekcja przeprowadzi Cię przez kroki, aby dodać każdy z nich. Jeśli zbierasz już **Live Stream** i **czas ładowania wyświetlenia strony**, wykonaj kroki odpowiednie tylko dla **nieudanych żądań**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Zbieraj żądań zakończonych niepowodzeniem, Stream na żywo i wyświetlenie strony — czas ładowania

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Technicznie rzecz biorąc żądań zakończonych niepowodzeniem są zbierane, ale jeszcze wystąpił brak żądań zakończonych niepowodzeniem. Aby przyspieszyć ten proces, można dodać niestandardowy wyjątek do istniejącego projektu, aby symulować rzeczywiste wyjątek. Jeśli aplikacja jest nadal uruchomione w programie Visual Studio, przed kontynuowaniem, wybierz opcję **Zatrzymaj debugowanie** (Shift + F5).

1. W **Eksploratora rozwiązań**, rozwiń węzeł **stron** > **About.cshtml**, a następnie otwórz *About.cshtml.cs*.

   ![Zrzut ekranu przedstawiający Eksploratora rozwiązań programu Visual Studio](./media/asp-net-core/011-about.png)

2. Dodaj wyjątek w obszarze ``Message=``, a następnie zapisz zmiany w pliku.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Transmisja strumieniowa na żywo

Aby uzyskać dostęp do funkcji Live Stream Application Insights za pomocą platformy ASP.NET Core, należy zaktualizować Microsoft.ApplicationInsights.AspNetCore 2.2.0 pakietów NuGet.

W programie Visual Studio, wybierz **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > (wersja) **2.2.0** > **aktualizacji**.

  ![Zrzut ekranu Menedżera pakietów NuGet](./media/asp-net-core/012-nuget-update.png)

Są wyświetlane monity o potwierdzenie wielu. Przeczytaj i zaakceptuj, jeśli zgadzasz się ze zmianami.

### <a name="page-view-load-time"></a>Wyświetlenie strony — czas ładowania

1. W programie Visual Studio, przejdź do **Eksploratora rozwiązań** > **stron**. Należy zmodyfikować dwa pliki: *Layout.cshtml* i *ViewImports.cshtml*.

2. W *ViewImports.cshtml*, Dodaj następujący kod:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. W *Layout.cshtml*, Dodaj następujący kod przed ``</head>`` tagu i przed wszystkie inne skrypty:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Test żądania zakończone niepowodzeniem oraz czas ładowania widoku strony, a Live Stream

Aby przetestować i upewnij się, że wszystko działa:

1. Uruchom aplikację. Aby uruchomić aplikację, wybierz pozycję ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/asp-net-core/004-iis-express.png) ikona.

2. Przejdź do **o** strony, aby wyzwolić wyjątek testu. (Jeśli jesteś w trybie debugowania w programie Visual Studio, wybierz **Kontynuuj** dla wyjątkiem pojawienie się w usłudze Application Insights.)

3. Ponowne uruchomienie symulowanego skrypt programu PowerShell transakcji, które było wcześniej używane. (Może być konieczne dostosowanie numer portu w skrypcie).

4. Jeśli **Przegląd** strony w aplikacji Insights nadal nie jest otwarty, w menu programu Visual Studio, wybierz opcję **projektu** > **usługi Application Insights**  >  **Otwórz Portal usługi Application Insights**. 

   > [!TIP]
   > Jeśli nie widzisz swojej natężenia ruchu, sprawdź wartość **zakres czasu**, a następnie wybierz pozycję **Odśwież**.

   ![Zrzut ekranu przedstawiający okno — omówienie](./media/asp-net-core/0019-overview-updated.png)

5. Wybierz **Live Stream**.

   ![Zrzut ekranu przedstawiający Stream metryki na żywo](./media/asp-net-core/0020-live-metrics-stream.png)

   (Jeśli skrypt programu PowerShell jest nadal uruchomione, powinieneś zobaczyć metryki na żywo. Jeśli skrypt programu PowerShell zostało przerwane, następnie ponownie uruchom skrypt po otwarciu Live Stream.)

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK porównania

Grupy produktów usługi Application Insights ma zostały wszelkich starań, aby osiągnąć równoważność funkcji między [pełny program .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) i .NET Core SDK. 2.2.0 wersji [zestawu SDK dla platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) dla usługi Application Insights w dużej mierze zamyka różnica między funkcjami.

Poniższa tabela opisuje kilka różnic oraz kompromis między [.NET i .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Porównanie zestawu SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metryki na żywo**      | **+** |**-** | **+** |
   | **Kanał danych telemetrycznych serwera** | **+** |**-** | **+**|
   |**Próbkowanie adaptacyjne**| **+** | **-** | **+**|
   | **Wywołania zależności SQL**     | **+** |**-** | **+**|
   | **Liczniki wydajności*** | **+** | **-**| **-**|

Zobacz liczniki wydajności, w tym kontekście [liczniki wydajności po stronie serwera](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , takich jak procesor, pamięć i użycie dysku.

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
[Odczytywanie i Współtworzenie kodu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Kontynuowanie wyszukiwania usługi Application Insights

W tej sekcji mogą pomóc lepiej zrozumieć, jak działa wyszukiwania usługi Application Insights w programie Visual Studio dla projektów ASP.NET Core 2. Działa w ten sposób, nawet wtedy, gdy pakiety NuGet usługi Application Insights jawnie nie zainstalowano jeszcze. Również może być przydatne do sprawdzenia danych wyjściowych debugowania.

W przypadku wyszukiwania danych wyjściowych dla słowa _insight_, zostały wyróżnione wyniki podobne do następujących:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

W danych wyjściowych CoreCLR ładuje dwa zestawy: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

_Nieskonfigurowana_ odwołania w każdym wystąpieniu telemetria usługi Application Insights wskazuje, czy ta aplikacja nie jest skojarzona z klucza ikey. Dane, który jest generowany, gdy uruchomiona jest aplikacja nie jest wysyłana na platformie Azure. Dane są dostępne tylko w przypadku lokalnego wyszukiwania i analizy.

Funkcje jest możliwe, w części ponieważ pakiet NuGet _pakiet_ przyjmuje [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_ ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) jako zależność.

![Zrzut ekranu programu NuGet wykres zależności dla pakiet](./media/asp-net-core/013-dependency.png)

Poza programem Visual Studio Jeśli edytowania projektu ASP.NET Core w VSCode lub innym edytorze, te zestawy nie automatycznie obciążenia podczas debugowania, jeśli nie zostały jawnie dodane usługi Application Insights do projektu.

Jednak w programie Visual Studio, to oświetlenia zapasowej lokalnych funkcji usługi Application Insights z zewnętrznych zestawów odbywa się za pomocą [interfejsu interfejsu IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). Interfejsu są dodawane dynamicznie usługi Application Insights podczas debugowania.

Dowiedz się więcej na temat zwiększenia aplikację na podstawie [zestawu zewnętrznego w programie ASP.NET Core za pomocą interfejsu IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Wyłącz usługę Application Insights w projektach programu Visual Studio .NET Core

Mimo, że wyszukiwanie automatyczne światła pracy usługi Application Insights funkcje mogą być przydatne, widzisz dane telemetryczne debugowania generowany, gdy nie oczekujesz, że może być mylące.

Jeżeli wystarczy tylko wyłączenie generowania danych telemetrycznych można dodać tego bloku kodu, aby **skonfigurować** metody usługi _Startup.cs_ pliku:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR nadal ładuje _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ i _Microsoft.ApplicationInsights.AspNetCore.dll_, ale pliki nie robią niczego.

Jeśli chcesz całkowicie wyłączyć usługi Application Insights w projekcie programu Visual Studio .NET Core preferowaną metodą jest wybranie **narzędzia** > **opcje**  >   **Projekty i rozwiązania** > **projektów sieci Web**. Wybierz **Wyłącz lokalną usługę Application Insights dla projektów sieci web platformy ASP.NET Core** pole wyboru. Ta funkcja została dodana w Visual Studio 15.6.

![Zrzut ekranu z opcjami okna Web projektów programu Visual Studio ekranu](./media/asp-net-core/014-disable.png)

Jeśli używasz starszej wersji programu Visual Studio, a chcesz całkowicie usunąć wszystkie zestawy, które zostały załadowane za pośrednictwem *interfejsu IHostingStartup*, masz dwie opcje:

* Dodaj `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` do _Program.cs_:

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* Dodaj ``"ASPNETCORE_preventHostingStartup": "True"`` do _launchSettings.json_ zmiennych środowiskowych.

Problem przy użyciu jednej z tych metod polega na tym, że nie wyłączają się tylko usługa Application Insights. Te metody także wyłączyć, wszystko w programie Visual Studio, który był używany *interfejsu IHostingStartup* funkcji światła w górę.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Kolejne kroki
* [Zapoznaj się z przepływy użytkownika](../../azure-monitor/app/usage-flows.md) Aby zrozumieć, jak użytkownicy nawigują w aplikacjach.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) Aby wyświetlić stan zmiennych i kod źródłowy w tej chwili, zgłaszany jest wyjątek.
* [Za pomocą interfejsu API](../../azure-monitor/app/api-custom-events-metrics.md) do wysłania własnych zdarzeń i metryk, aby uzyskać bardziej szczegółowy widok wydajności i użycia Twojej aplikacji.
* Użyj [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) do sprawdzenia aplikacji stale z całego świata.
