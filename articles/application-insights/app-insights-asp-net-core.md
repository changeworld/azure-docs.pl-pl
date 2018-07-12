---
title: Usługi Azure Application Insights dla platformy ASP.NET Core | Dokumentacja firmy Microsoft
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
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: f9ab9b9af81bf1827c2da646908e204bd051706b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970938"
---
# <a name="application-insights-for-aspnet-core"></a>Usługa Application Insights dla aplikacji ASP.NET Core

Usługa Azure Application Insights zapewnia szczegółowe monitorowanie aplikacji sieci web do poziomu kodu. Możesz łatwo monitorować dostępność, wydajność i użycie aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika.

W tym artykule opisano proces tworzenia przykładu platformy ASP.NET Core [stron Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikacji w programie Visual Studio i Rozpocznij monitorowanie za pomocą usługi Azure Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Zestaw .NET Core 2.0.0 SDK lub nowszej.
- [Program Visual Studio 2017](https://www.visualstudio.com/downloads/) wersji 15.7.3 lub nowszej z obciążeniami rozwoju platformy ASP.NET i sieci web. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Tworzenie projektu platformy ASP.NET Core w programie Visual Studio

1. Kliknij prawym przyciskiem myszy, a następnie uruchom **programu Visual Studio 2017** jako administrator.
2. Wybierz **pliku** > **nowe** > **projektu** (Ctrl-Shift-N).

   ![Zrzut ekranu przedstawiający Menu nowy projekt plików programu Visual Studio](./media/app-insights-asp-net-core/001-new-project.png)

3. Rozwiń **Visual C#** > Wybierz **platformy .NET Core** > **aplikacji sieci Web platformy ASP.NET Core**. Wprowadź **nazwa** > **Nazwa rozwiązania** > Sprawdź **Utwórz nowe repozytorium Git**.

   ![Zrzut ekranu przedstawiający Kreatora nowego projektu plik programu Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Wybierz **.Net Core** > **platformy ASP.NET Core 2.0** **aplikacji sieci Web** > **OK**.

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Wyszukiwanie usługi Application Insights

Domyślnie w programie Visual Studio w wersji 2015 Update 2 lub nowszym za pomocą platformy ASP.NET Core 2 + na podstawie projektu, możesz korzystać z zalet [wyszukiwania usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) nawet zanim dodasz usługę Application Insights do projektu.

W celu przetestowania tej funkcji:

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Wybierz **widoku** > **Windows inne** > **wyszukiwania usługi Application Insights**.

   ![Zrzut ekranu przedstawiający narzędzia diagnostyczne Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Dane telemetryczne sesji debugowania dla jest obecnie dostępna tylko lokalne analizy. Aby w pełni włączyć usługi Application Insights, wybierz **gotowości Telemetrii** w prawym górnym prawym przyciskiem myszy lub postępuj zgodnie z instrukcjami w następnej sekcji.

   ![Zrzut ekranu programu Visual Studio Application Insights, wyszukiwanie](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Aby dowiedzieć się więcej na temat sposobu programu Visual Studio udostępnia funkcje, takie jak [wyszukiwania usługi Application Insights](app-insights-visual-studio.md) i [CodeLens](app-insights-visual-studio-codelens.md) lokalnie przed dodaniu usługi Application Insights do projektu programu ASP.NET Core Wyewidencjonuj wyjaśnienie w [końcowej części tego artykułu.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Dodawanie telemetrii usługi Application Insights

1. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights...** . (Lub kliknąć prawym przyciskiem myszy **podłączonych usług** i wybierz polecenie Dodaj podłączoną usługę.)

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Wybierz **wprowadzenie**. (W zależności od używanej wersji programu Visual Studio tekst może się nieco różnić. Niektóre starsze wersje należy mieć **Rozpocznij za darmo** przycisku.)

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/008-get-started.png)

3. Wybierz odpowiednią **subskrypcji** > **zasobów** > **zarejestrować**.

## <a name="changes-made-to-your-project"></a>Zmienia Made do projektu

Usługa Application Insights jest niskie obciążenie. Aby przejrzeć modyfikacje do projektu, dodając telemetria usługi Application Insights:

Wybierz **widoku** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **zmiany**

- Cztery łączna liczba zmian:

  ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Jeden nowy plik zostanie utworzony:

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

- Trzy pliki są modyfikowane: (dodatkowe komentarze dodane wyróżniania zmian)

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

Aby zautomatyzować żądania do aplikacji za pomocą transakcji syntetycznych.

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/004-iis-express.png)

2. Skopiuj adres url z paska adresu przeglądarki. Jest w formacie http://localhost:{random numer portu}

   ![Zrzut ekranu przedstawiający paska adresu url w przeglądarce](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Uruchom następującą pętlę programu PowerShell do utworzenia 100 transakcji syntetycznych względem aplikacji testowych. Zmodyfikuj numer portu po **localhost:** dopasować adres url skopiowany w poprzednim kroku.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Portal usługi Open Application Insights

Po uruchomieniu programu PowerShell z poprzedniej sekcji, uruchom usługę Application Insights do wyświetlania transakcji i upewnij się, że dane są zbierane. 

Wybierz z menu programu Visual Studio **projektu** > **usługi Application Insights** > **Otwórz Portal usługi Application Insights**

   ![Zrzut ekranu przedstawiający omówienie usługi Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> W powyższym zrzucie ekranu przykładu **Live Stream**, **czas ładowania wyświetlenia strony**, i **nieudanych żądań** nie są obecnie zbierane. Następna sekcja przeprowadzi każdego dodawania. Jeśli zbierasz już **Live Stream**, i **czas ładowania wyświetlenia strony**, tylko wykonaj kroki **nieudanych żądań**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Zbieraj żądań zakończonych niepowodzeniem, Stream na żywo i wyświetlenie strony — czas ładowania

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Z technicznego punktu widzenia **nieudanych żądań** są zbierane, ale żaden jeszcze wystąpił. Aby przyspieszyć proces wzdłuż niestandardowego wyjątku można dodać do istniejącego projektu, aby symulować rzeczywiste wyjątek. Jeśli Twoja aplikacja nadal działa w programie Visual Studio przed kontynuacją **Zatrzymaj debugowanie** (Shift + F5)

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

Dostęp do funkcji Live Stream usługi Application Insights za pomocą platformy ASP.NET Core aktualizacji **Microsoft.ApplicationInsights.AspNetCore 2.2.0** pakietów NuGet.

W programie Visual Studio, wybierz **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > wersja **2.2.0** > **aktualizacji**.

  ![Zrzut ekranu Menedżera pakietów NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Występuje wiele monity o potwierdzenie. Przeczytaj i zaakceptuj, jeśli zgadzasz się ze zmianami.

### <a name="page-view-load-time"></a>Wyświetlenie strony — czas ładowania

1. W programie Visual Studio przejdź do **Eksploratora rozwiązań** > **stron** > będzie trzeba zmodyfikować dwa pliki: _Layout.cshtml_, i  _ViewImports.cshtml_

2. W __ViewImports.cshtml_, Dodaj:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. W **_Layout.cshtml** Dodaj wiersz poniżej przed ``</head>`` tagu, ale także przed innymi skryptami.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test zakończył się niepowodzeniem, żądania, wyświetlenie strony — czas ładowania, Live Stream.

Do przetestowania i upewnij się, że wszystko działa:

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Przejdź do **o** strony, aby wyzwolić wyjątek testu. (Jeśli używasz w trybie debugowania, należy kliknąć przycisk **Kontynuuj** w programie Visual Studio dla wyjątkiem pojawienie się w usłudze Application Insights.)

3. Ponowne uruchomienie symulowanego skryptu transakcji programu PowerShell z wcześniejszego (może być konieczne dostosowanie numer portu w skrypcie).

4. Jeśli aplikacje — Przegląd Insights nie jest wciąż otwarty, z menu programu Visual Studio, wybierz **projektu** > **usługi Application Insights** > **otwierania aplikacji Portal usługi insights**. 

   > [!TIP]
   > Jeśli nie widzisz jeszcze nowego ruchu, sprawdź **zakres czasu** i kliknij przycisk **Odśwież**.

   ![Zrzut ekranu Przegląd okna](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Wybierz pozycję Live Stream.

   ![Zrzut ekranu przedstawiający Stream metryki na żywo](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Jeśli PowerShell skryptu możesz nadal działa powinien zostać wyświetlony metryki na żywo, jeżeli została zatrzymana, uruchom skrypt ponownie przy użyciu Live Stream otwarty).

## <a name="app-insights-sdk-comparison"></a>App Insights SDK porównania

Grupy produktów usługi Application Insights ma zostały wszelkich starań, aby osiągnąć równoważność funkcji między [pełny program .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) i.Net Core SDK. 2.2.0 wersji [zestawu SDK dla platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) dla usługi Application Insights stopniu zamknął różnica między funkcjami.

Aby dowiedzieć się więcej na temat różnic i kompromis między [.NET i .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | Porównanie zestawu SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metryki na żywo**      | **+** |**-** | **+** |
   | **Kanał danych Telemetrycznych serwera** | **+** |**-** | **+**|
   |**Próbkowanie adaptacyjne**| **+** | **-** | **+**|
   | **Wywołania zależności SQL**     | **+** |**-** | **+**|
   | **Liczniki wydajności*** | **+** | **-**| **-**|

_Liczniki wydajności_ w tym kontekście oznacza [liczniki wydajności po stronie serwera](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , takich jak procesor, pamięć i użycie dysku.

## <a name="open-source-sdk"></a>Zestaw SDK typu open-source
[Odczytywać opinie i dodawać do kodu](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Kontynuowanie wyszukiwania usługi Application Insights

Aby lepiej zrozumieć, jak wyszukiwania usługi Application Insights działa w programie Visual Studio dla projektów ASP.NET Core 2, nawet wtedy, gdy jest to jawne instalację pakietów NuGet usługi Application Insights nie zostało jeszcze przeprowadzone. Przydatne może być do sprawdzenia danych wyjściowych debugowania.

W przypadku wyszukiwania danych wyjściowych dla wyrazu _insight_ zostanie wyróżniona, wyniki podobne do następującego:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

CoreCLR to dwa zestawy ładowania: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

I _nieskonfigurowana_ każde wystąpienie usługi Application Insights w danych telemetrycznych wskazuje, czy ta aplikacja nie jest skojarzony z klucza ikey, więc danych, który jest generowany, gdy aplikacja jest uruchomiona, nie są wysyłane do platformy Azure i jest tylko dostępna w przypadku lokalnego wyszukiwania i analizy.

Część jak jest to możliwe, jest to, że pakiet NuGet _pakiet_ przyjmuje jako zależność [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Zrzut ekranu programu NuGet zależności wykresu dla pakiet](./media/app-insights-asp-net-core/013-dependency.png)

Poza programem Visual Studio Jeśli edytowania projektem platformy ASP.NET Core w VSCode lub innym edytorze te zestawy w takich sytuacjach przydałaby automatycznie załadować podczas debugowania, jeśli nie zostały jawnie dodane usługi Application Insights do projektu.

Jednak w programie Visual Studio to oświetlenia zapasowej lokalnych funkcji usługi Application Insights z zewnętrznych zestawów odbywa się za pośrednictwem użytkowania [interfejsu interfejsu IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) której są dodawane dynamicznie usługi Application Insights podczas debugowania.

Aby dowiedzieć się więcej na temat zwiększenia aplikację na podstawie [zestawu zewnętrznego w programie ASP.NET Core za pomocą interfejsu IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Jak wyłączyć usługę Application Insights w projektach programu Visual Studio .NET Core

Automatyczne światła się funkcji wyszukiwania usługi Application Insights mogą być przydatne do niektórych, martwiły debugowania dane telemetryczne generowane, gdy nie otwieraj go może być mylące.

Jeżeli wystarczy tylko wyłączenie generowania danych telemetrycznych można dodać ten blok kodu do metody konfiguracji usługi _Startup.cs_ pliku:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Nadal można załadować oprogramowania CoreCLR _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ i _Microsoft.ApplicationInsights.AspNetCore.dll_, ale ich nie spowoduje żadnego efektu.

Jeśli chcesz całkowicie wyłączyć usługi Application Insights w projekcie programu Visual Studio .NET Core preferowaną metodą jest wybranie **narzędzia** > **opcje**  >   **Projekty i rozwiązania** > **projektów sieci Web** > i pole wyboru, aby wyłączyć lokalną usługę Application Insights dla projektów sieci web platformy ASP.NET Core. Ta funkcja została dodana w Visual Studio 15.6.

![Zrzut ekranu z opcjami okna Web projektów programu Visual Studio ekranu](./media/app-insights-asp-net-core/014-disable.png)

Jeśli używasz starszej wersji programu Visual Studio, a chcesz całkowicie usunąć wszystkich zestawów załadowanych za pomocą interfejsu IHostingStartup można dodać:

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

Alternatywnie możesz też dodać ``"ASPNETCORE_preventHostingStartup": "True"`` do _launchSettings.json_ zmiennych środowiskowych.

Ten problem przy użyciu jednej z tych metod jest nie nie tylko wyłączenie go spowoduje wyłączenie niczego w programie Visual Studio, który został wykorzystaniu światła interfejsu IHostingStartup funkcje w usłudze Application Insights.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Kolejne kroki
* [Zapoznaj się z użytkownikami przepływy](app-insights-usage-flows.md) Aby zrozumieć, jak użytkownicy nawigują w aplikacjach.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) Aby wyświetlić stan zmiennych i kod źródłowy w tej chwili, zgłaszany jest wyjątek.
* [Za pomocą interfejsu API](app-insights-api-custom-events-metrics.md) do wysłania własnych zdarzeń i metryk, aby uzyskać bardziej szczegółowy widok wydajności i użycia Twojej aplikacji.
* [Testy dostępności](app-insights-monitor-web-app-availability.md) Sprawdź aplikację stale z całego świata.
