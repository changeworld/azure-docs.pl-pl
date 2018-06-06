---
title: Azure Application Insights dla platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web dla dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 459fbbe734577af474fae8de3acf9be732780d6c
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736538"
---
# <a name="application-insights-for-aspnet-core"></a>Usługa Application Insights dla aplikacji ASP.NET Core

Azure Application Insights udostępnia szczegółowe monitorowanie aplikacji sieci web do poziomu kodu. Ułatwia monitorowanie aplikacji sieci web, dostępności, wydajności i użycia. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika.

W tym artykule przedstawiono tworzenie przykładowej platformy ASP.NET Core [stron Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikacji w Visual Studio i jak rozpocząć monitorowanie za pomocą usługi Azure Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- NET Core 2.0.0 SDK lub nowszym.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) wersji 15.7.3 lub nowszym z obciążeniem programowanie ASP.NET i sieć web. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Tworzenie projektu platformy ASP.NET Core w programie Visual Studio

1. Kliknij prawym przyciskiem myszy, a następnie uruchom **programu Visual Studio 2017** jako administrator.
2. Wybierz **pliku** > **nowe** > **projektu** (Ctrl-Shift-N).

   ![Zrzut ekranu przedstawiający Menu programu Visual Studio pliku nowego projektu](./media/app-insights-asp-net-core/001-new-project.png)

3. Rozwiń węzeł **Visual C#** > Wybierz **.NET Core** > **aplikacji sieci Web platformy ASP.NET Core**. Wprowadź **nazwa** > **Nazwa rozwiązania** > Sprawdź **Tworzenie nowego repozytorium Git**.

   ![Zrzut ekranu przedstawiający Kreator nowego projektu plików programu Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Wybierz **.Net Core** > **platformy ASP.NET Core 2.0** **aplikacji sieci Web** > **OK**.

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Wyszukiwanie usługi Application Insights

Domyślnie w programie Visual Studio w wersji 2015 Update 2 albo nowszy z platformy ASP.NET Core 2 + na podstawie projektu, należy skorzystać z [wyszukiwania usługi Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio) nawet zanim należy jawnie dodać usługi Application Insights do projektu.

Aby przetestować tę funkcję:

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Wybierz **widoku** > **inne okna** > **wyszukiwania usługi Application Insights**.

   ![Zrzut ekranu przedstawiający narzędzi diagnostycznych programu Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Dane telemetryczne sesji debugowania jest obecnie dostępny tylko lokalne analizy. Aby w pełni włączyć usługi Application Insights, wybierz **gotowości Telemetrii** w prawym górnym prawym przyciskiem myszy, lub wykonaj kroki opisane w następnej sekcji.

   ![Zrzut ekranu przedstawiający Visual Studio Application Insights wyszukiwania](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Aby dowiedzieć się więcej na temat sposobu świateł Visual Studio funkcji, takich jak [wyszukiwania usługi Application Insights](app-insights-visual-studio.md) i [CodeLens](app-insights-visual-studio-codelens.md) lokalnie przed dodano usługę Application Insights do projektu platformy ASP.NET Core wyewidencjonować wyjaśnienie w [koniec tego artykułu.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Dodawanie telemetrii usługi Application Insights

1. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights...** . (Lub kliknąć prawym przyciskiem myszy **usług połączonych** i wybierz polecenie Dodaj połączonych usług.)

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Wybierz **wprowadzenie**. (W zależności od używanej wersji programu Visual Studio tekst mogą się nieco różnić. Zamiast tego należy mieć niektóre starsze wersje **Start wolnego** przycisku.)

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/008-get-started.png)

3. Wybierz odpowiednie **subskrypcji** > **zasobów** > **zarejestrować**.

## <a name="changes-made-to-your-project"></a>Zmienia Made do projektu

Usługa Application Insights jest niskiego obciążenia. Aby przejrzeć zmiany do projektu, dodając telemetrii usługi Application Insights:

Wybierz **widoku** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **zmiany**

- Całkowita liczba czterech zmiany:

  ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Tworzony jest jeden nowy plik:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Trzy pliki zostały zmodyfikowane: (dodatkowe komentarze dodane wyróżnienia zmian)

  _appsettings.json_

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

  _ContosoDotNetCore.csproj_

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

   _Program.cs_

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

## <a name="synthetic-transactions-with-powershell"></a>Transakcje syntetyczne przy użyciu programu PowerShell

Aby zautomatyzować żądania kierowane do aplikacji za pomocą transakcji syntetycznych.

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Skopiuj adres url z pola adresu przeglądarki. Jest w formacie http://localhost:{random numer portu}

   ![Zrzut ekranu przedstawiający adres url na pasku adresu przeglądarki](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Uruchom następującą pętlę programu PowerShell do utworzenia 100 transakcji syntetycznych względem aplikacji testu. Zmodyfikuj numer portu po **localhost:** odpowiadające adresu url skopiowanego w poprzednim kroku.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Portal usługi Otwórz Application Insights

Po uruchomieniu programu PowerShell z poprzedniej sekcji, uruchom program Application Insights, aby wyświetlić transakcje i upewnij się, że dane są zbierane. 

Wybierz z menu programu Visual Studio **projektu** > **usługi Application Insights** > **Otwórz Portal usługi Application Insights**

   ![Zrzut ekranu przedstawiający aplikacji Insights — omówienie](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> Na przykład zrzucie ekranu powyżej **strumień na żywo**, **czas ładowania strony widoku**, i **nieudanych żądań** nie są obecnie zbierane. Następna sekcja przeprowadzi każdego dodawania. Jeśli są zbierane **strumień na żywo**, i **czas ładowania strony widoku**, tylko wykonaj kroki **nieudanych żądań**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Zbieranie żądań zakończonych niepowodzeniem, strumień na żywo i czas ładowania strony widoku

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Technicznie **nieudanych żądań** są zbierane, ale brak jeszcze wystąpił. Aby przyspieszyć proces wzdłuż wyjątek niestandardowych można dodać do istniejącego projektu, aby symulować wyjątek rzeczywistych. Jeśli aplikacja nadal działa w programie Visual Studio przed kontynuowaniem **Zatrzymaj debugowanie** (Shift + F5)

1. W **Eksploratora rozwiązań** > Rozwiń **stron** > **About.cshtml** > Otwórz **About.cshtml.cs**.

   ![Zrzut ekranu przedstawiający Eksploratora rozwiązań programu Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Dodaj wyjątek w obszarze ``Message=`` i Zapisz zmiany w pliku.

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

Dostęp do funkcji strumień na żywo z usługi Application Insights o aktualizacji platformy ASP.NET Core **Microsoft.ApplicationInsights.AspNetCore 2.2.0** pakietów NuGet.

W programie Visual Studio, wybierz **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > wersji **2.2.0** > **aktualizacji**.

  ![Zrzut ekranu Menedżera pakietów NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Nastąpi wielu o potwierdzenie. Przeczytaj i zaakceptuj, jeśli zgadzasz się z zmiany.

### <a name="page-view-load-time"></a>Czas ładowania strony widoku

1. W programie Visual Studio przejdź do **Eksploratora rozwiązań** > **stron** > będzie konieczna modyfikacja dwa pliki: _Layout.cshtml_, i  _ViewImports.cshtml_

2. W __ViewImports.cshtml_, dodać:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. W **_Layout.cshtml** Dodaj wiersz poniżej przed ``</head>`` tag, ale także przed innymi skryptami.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test nie powiódł się żądania, czas ładowania strony widoku, strumień na żywo

Aby przetestować i upewnij się, że wszystko działa:

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Przejdź do **o** stronę, aby wyzwolić wyjątek testu. (Jeśli system jest uruchomiony w trybie debugowania, należy kliknąć **Kontynuuj** w programie Visual Studio dla wyjątku wyświetlani w usłudze Application Insights.)

3. Uruchom ponownie symulowane skryptu transakcji programu PowerShell z wcześniej (może być konieczne dostosowanie numer portu w skrypcie).

4. Jeśli Przegląd Insights aplikacji nie jest nadal otwarty, z menu programu Visual Studio, wybierz **projektu** > **usługi Application Insights** > **Otwórz aplikacji Insights Portal**. 

   > [!TIP]
   > Jeśli nie widzisz jeszcze nowego ruchu, sprawdź **zakres czasu** i kliknij przycisk **Odśwież**.

   ![Okno zrzut ekranu — omówienie](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Wybierz strumień na żywo

   ![Zrzut ekranu przedstawiający strumień na żywo metryk](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Jeśli programu PowerShell skryptu można nadal działa powinna zostać wyświetlona metryki na żywo, jeśli została zatrzymana, uruchom skrypt ponownie z strumień na żywo Otwórz.)

## <a name="app-insights-sdk-comparison"></a>Porównanie SDK Insights aplikacji

Twarde Praca grupy produktów usługi Application Insights do osiągnięcia parzystość funkcji między [pełnego zestawu .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) i .net Core SDK. 2.2.0 wydanie [platformy ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) dla usługi Application Insights przede wszystkim zamknął luki funkcji.

Aby dowiedzieć się więcej na temat różnic oraz wady i zalety między [.NET i .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Porównanie zestawu SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metryki na żywo**      | **+** |**-** | **+** |
   | **Kanał danych Telemetrycznych serwera** | **+** |**-** | **+**|
   |**Adaptacyjną pobierania próbek**| **+** | **-** | **+**|
   | **Wywołania zależności SQL**     | **+** |**-** | **+**|
   | **Liczniki wydajności*** | **+** | **-**| **-**|

_Liczniki wydajności_ w tym kontekście odnosi się do [liczniki wydajności po stronie serwera](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , takich jak procesor, pamięć i użycie dysku.

## <a name="open-source-sdk"></a>Zestaw SDK open source
[Przeczytaj i brać udział w kodzie](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Nadal wyszukiwania usługi Application Insights

Aby lepiej zrozumieć, jak wyszukiwania usługi Application Insights działa w programie Visual Studio dla projektu platformy ASP.NET Core 2, nawet w przypadku jawnego instalowanie pakietów NuGet usługi Application Insights nie zostało jeszcze przeprowadzone. Może być przydatne do sprawdzenia danych wyjściowych debugowania.

W przypadku wyszukiwania w danych wyjściowych wyraz _szczegółowe informacje o_ będzie Podświetl wyniki podobne do następujących:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

Środowisko CoreCLR jest ładowania dwóch zestawów: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

I _stanie nieskonfigurowanym_ w każdym wystąpieniu usługi Application Insights telemetrii wskazuje, że ta aplikacja nie jest skojarzona z ikey, dzięki czemu danych, który jest generowany, gdy aplikacja jest uruchomiona, nie są wysyłane do usługi Azure i jest tylko dostępne dla lokalnego wyszukiwania i analizy.

Część jak jest to możliwe jest to, że pakiet NuGet _Microsoft.AspNetCore.All_ przyjmuje jako zależność [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Zrzut ekranu NuGet zależności wykresu dla Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

Poza Visual Studio Jeśli edytowania projektu platformy ASP.NET Core w VSCode lub w innym edytorze te zestawy nie ładuje automatycznie podczas debugowania, jeśli nie zostało to jeszcze jawnie dodać usługi Application Insights do projektu.

Jednak w programie Visual Studio to oświetlenia zapasowej lokalnej usługi Application Insights funkcje z zewnętrznych zestawów odbywa się za pośrednictwem użycie [interfejsu IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) dynamicznie dodaje usługi Application Insights podczas debugowania.

Aby dowiedzieć się więcej na temat zwiększenia aplikacji z [zewnętrznych zestawu w ASP.NET Core z IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Wyłączanie usługi Application Insights w projektach Visual Studio .NET Core

Automatyczne światła się funkcji wyszukiwania usługi Application Insights mogą być użyteczne do pewnych, oglądanie danych telemetrycznych debugowania generowane, gdy go nie oczekujesz może być trudne.

Jeśli właśnie wyłączanie generowania telemetrii wystarczy do metody konfiguracji można dodać tego bloku kodu z _Startup.cs_ pliku:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Środowisko CoreCLR nadal ładuje _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ i _Microsoft.ApplicationInsights.AspNetCore.dll_, ale nie będzie wykonywać żadnych czynności.

Jeśli chcesz całkowicie wyłączyć usługę Application Insights do projektu programu Visual Studio .NET Core preferowaną metodą jest wybranie **narzędzia** > **opcje**  >   **Projekty i rozwiązania** > **projekty sieci Web** > i pole wyboru, aby wyłączyć lokalne usługi Application Insights dla projektów sieci web platformy ASP.NET Core. Ta funkcja została dodana w Visual Studio 15,6.

![Zrzut ekranu programu Visual Studio opcje okna Projekty sieci Web ekranu](./media/app-insights-asp-net-core/014-disable.png)

Jeśli używasz starszej wersji programu Visual Studio, i chcesz całkowicie usunąć wszystkie zestawy ładowane za pośrednictwem IHostingStartup można dodać:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

Aby _Program.cs_:

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

Lub też może dodać ``"ASPNETCORE_preventHostingStartup": "True"`` do _launchSettings.json_ zmiennych środowiskowych.

Problem przy użyciu jednej z tych metod jest on nie powoduje, że wyłączy wszystko w programie Visual Studio, który został wykorzystaniu światła IHostingStartup się funkcji usługi Application Insights.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Kolejne kroki
* [Eksploruj przepływa użytkowników](app-insights-usage-flows.md) zrozumieć, jak użytkownicy nawigują między aplikacji.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) aby zobaczyć stan kodu źródłowego i zmiennych w tej chwili jest zgłaszany wyjątek.
* [Za pomocą interfejsu API](app-insights-api-custom-events-metrics.md) wysyłanie własnych zdarzenia i metryki dla bardziej szczegółowy widok wydajności i użycia aplikacji.
* [Badania dostępności](app-insights-monitor-web-app-availability.md) Sprawdź aplikację stale z całego świata.
