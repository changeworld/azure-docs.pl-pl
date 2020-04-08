---
title: Usuwanie usługi Application Insights w programie Visual Studio — Monitor platformy Azure
description: Jak usunąć SDK usługi Application Insights dla ASP.NET i ASP.NET Core w programie Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805107"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Jak usunąć aplikację Application Insights w programie Visual Studio

W tym artykule pokazano, jak usunąć ASP.NET i ASP.NET Core Application Insights SDK w programie Visual Studio.

Aby usunąć usługa Application Insights, należy usunąć pakiety NuGet i odwołania z interfejsu API w aplikacji. Pakiety NuGet można odinstalować przy użyciu konsoli zarządzania pakietami lub zarządzania rozwiązaniem NuGet w programie Visual Studio. W poniższych sekcjach zostaną wyświetlane dwa sposoby usuwania pakietów NuGet i co zostało automatycznie dodane w projekcie. Pamiętaj, aby potwierdzić dodane pliki i obszary z własnym kodem, w którym zostały wykonane wywołania interfejsu API są usuwane.

## <a name="uninstall-using-the-package-management-console"></a>Odinstalowywanie przy użyciu Konsoli zarządzania pakietami

# <a name="net"></a>[.NET](#tab/net)

1. Aby otworzyć Konsolę zarządzania pakietami, w górnym menu wybierz polecenie Narzędzia > Menedżer pakietów NuGet > konsoli Menedżera pakietów.
     
    ![W górnym menu kliknij pozycję Narzędzia > Menedżer pakietów NuGet > Konsoli Menedżera pakietów](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Jeśli kolekcja śledzenia jest włączona, należy najpierw odinstalować microsoft.ApplicationInsights.TraceListener. Wprowadź `Uninstall-package Microsoft.ApplicationInsights.TraceListener` następnie wykonaj poniższy krok, aby usunąć microsoft.ApplicationInsights.Web.

1. Wprowadź następujące polecenie: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Po wprowadzeniu polecenia pakiet usługi Application Insights i wszystkie jego zależności zostaną odinstalowane z projektu.
    
    ![Polecenie Wprowadź w konsoli](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Aby otworzyć Konsolę zarządzania pakietami, w górnym menu wybierz polecenie Narzędzia > Menedżer pakietów NuGet > konsoli Menedżera pakietów.

    ![W górnym menu kliknij pozycję Narzędzia > Menedżer pakietów NuGet > Konsoli Menedżera pakietów](./media/remove-application-insights/package-manager.png)

1. Wprowadź następujące polecenie: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Po wprowadzeniu polecenia pakiet usługi Application Insights i wszystkie jego zależności zostaną odinstalowane z projektu.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Odinstalowywanie przy użyciu interfejsu użytkownika programu Visual Studio NuGet

# <a name="net"></a>[.NET](#tab/net)

1. W *Eksploratorze* rozwiązań po prawej stronie kliknij prawym przyciskiem myszy **rozwiązanie** i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    Następnie zostanie wyświetlony ekran, który umożliwia edycję wszystkich pakietów NuGet, które są częścią projektu.
    
     ![Kliknij prawym przyciskiem myszy pozycję Rozwiązanie w Eksploratorze rozwiązań, a następnie wybierz pozycję Zarządzaj pakietami NuGet dla rozwiązania](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Jeśli kolekcja śledzenia jest włączona, należy najpierw odinstalować microsoft.ApplicationInsights.TraceListener bez usunięcia zależności zaznaczone, a następnie wykonaj poniższe kroki, aby odinstalować Microsoft.ApplicationInsights.Web z usuń zależności zaznaczone.
    
1. Kliknij pakiet "Microsoft.ApplicationInsights.Web".Po prawej stronie zaznacz pole wyboru obok *pozycji Projekt,* aby wybrać wszystkie projekty.
    
1. Aby usunąć wszystkie zależności podczas odinstalowywania, wybierz przycisk rozwijany **Opcje** poniżej sekcji, w której zaznaczono projekt.

    W obszarze *Opcje odinstalowywania*zaznacz pole wyboru obok pozycji *Usuń zależności*.

1. Wybierz **pozycję Odinstaluj**.
    
    ![Sprawdź usuwanie zależności, a następnie odinstaluj](./media/remove-application-insights/uninstall-framework.png)

    Zostanie wyświetlone okno dialogowe, w tym wszystkie zależności, które mają zostać usunięte z aplikacji.Wybierz **ok,** aby odinstalować.
    
    ![Sprawdź usuwanie zależności, a następnie odinstaluj](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Po odinstalowaniu wszystkiego w *Eksploratorze rozwiązań*nadal możesz zobaczyć "ApplicationInsights.config" i "AiHandleErrorAttribute.cs".Oba pliki można usunąć ręcznie.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. W *Eksploratorze* rozwiązań po prawej stronie kliknij prawym przyciskiem myszy **rozwiązanie** i wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania**.

    Następnie zostanie wyświetlony ekran, który umożliwia edycję wszystkich pakietów NuGet, które są częścią projektu.

    ![Kliknij prawym przyciskiem myszy pozycję Rozwiązanie w Eksploratorze rozwiązań, a następnie wybierz pozycję Zarządzaj pakietami NuGet dla rozwiązania](./media/remove-application-insights/manage-nuget-core.png)

1. Kliknij pakiet "Microsoft.ApplicationInsights.AspNetCore". Po prawej stronie zaznacz pole wyboru obok *pozycji Projekt,* aby zaznaczyć wszystkie projekty, a następnie wybierz pozycję **Odinstaluj**.

    ![Sprawdź usuwanie zależności, a następnie odinstaluj](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Co jest tworzone podczas dodawania usługi Application Insights

Po dodaniu usługi Application Insights do projektu, tworzy pliki i dodaje kod do niektórych plików. Tylko odinstalowanie pakietów NuGet nie zawsze odrzuci pliki i kod. Aby w pełni usunąć usługa Application Insights, należy sprawdzić i ręcznie usunąć dodany kod lub pliki wraz z wszelkimi wywołaniami interfejsu API dodanymi w projekcie.

# <a name="net"></a>[.NET](#tab/net)

Po dodaniu danych telemetrycznych usługi Application Insights do projektu ASP.NET programu Visual Studio dodaje następujące pliki:

- Plik ApplicationInsights.config
- AiHandleErrorAttribute.cs

Dodaje się następujące fragmenty kodu:

- [Nazwa projektu].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Jeśli projekt ma plik Layout.cshtml, kod poniżej jest dodawany.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Po dodaniu danych telemetrycznych usługi Application Insights do projektu szablonu programu Visual Studio ASP.NET Core dodaje on następujący kod:

- [Nazwa projektu].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Następne kroki

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)