---
title: Oddzielanie danych telemetrycznych od etapu programowania, testowania i wersji w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Bezpośrednie dane telemetryczne do różnych zasobów dla sygnatury rozwoju, testowania i produkcji.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713470"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Oddzielanie danych telemetrycznych z programowania, testowania i produkcji

Podczas tworzenia następnej wersji aplikacji sieci web, użytkownik nie chce mieszały się [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) dane telemetryczne z nowej wersji i już pełnej wersji. Aby uniknąć nieporozumień, Wyślij dane telemetryczne z różnych etapach rozwoju do oddzielnych zasobów usługi Application Insights przy użyciu kluczy Instrumentacji oddzielne (kluczy ikey). Aby ułatwić przemieszcza się nieco od jednego etapu do innego należy zmienić wartość klucza instrumentacji, może być warto ustawić klucz Instrumentacji w kodzie, a w pliku konfiguracji. 

(Jeśli system jest usługi Azure Cloud Service, ma [innej metody ustalania osobne kluczy ikey](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>O zasobach i kluczy Instrumentacji

Po skonfigurowaniu monitorowanie usługi Application Insights dla aplikacji sieci web, tworzenia usługi Application Insights *zasobów* w systemie Microsoft Azure. Możesz otworzyć tego zasobu w witrynie Azure portal, aby można było zobaczyć i analizować dane telemetryczne zebrane z aplikacji. Zasób jest identyfikowany przez *klucz Instrumentacji* (klucz Instrumentacji). Po zainstalowaniu pakietu usługi Application Insights do monitorowania aplikacji, skonfigurujesz go z kluczem instrumentacji, tak aby wie, gdzie wysyłać dane telemetryczne.

Zazwyczaj zdecydujesz się używać oddzielne zasoby lub pojedynczy zasób udostępniony w różnych scenariuszach:

* Różne, niezależne aplikacje — użyć osobnego zasobu i klucz instrumentacji dla każdej aplikacji.
* Wiele składników lub ról aplikacji biznesowej jeden — użyj [pojedynczy zasób udostępniony](../../azure-monitor/app/app-map.md) dla wszystkich składników aplikacji. Można filtrować i posegmentowana według właściwości cloud_RoleName telemetrii.
* Rozwoju, testowania i wydanie — użyć osobnego zasobu i ikey wersji systemu w "sygnatury" lub etapu produkcji.
* A | Testowanie B — użyć pojedynczego zasobu. Tworzenie parametru TelemetryInitializer, aby dodać właściwość do telemetrii, który identyfikuje wariantów.


## <a name="dynamic-ikey"></a> Klucz Instrumentacji dynamiczne

Aby ułatwić zmienić klucz Instrumentacji przemieszcza się w kodzie między etapami produkcji, ustaw go w kodzie, a w pliku konfiguracji.

Ustaw klucz w metodzie inicjalizacji, takich jak pliku global.aspx.cs usługi sieci Web platformy ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

W tym przykładzie kluczy ikey dla różnych zasobów są umieszczane w różnych wersjach pliku konfiguracji sieci web. Zamiana pliku konfiguracji sieci web — które można zrobić w skrypcie release - będą zamieniać zasobu docelowego.

### <a name="web-pages"></a>Strony sieci Web
Klucz Instrumentacji jest również używane na stronach sieci web aplikacji [skryptu, która jest wyświetlana w bloku szybki start](../../azure-monitor/app/javascript.md). Zamiast kodowania go dosłownie w skrypcie, wygenerowany na podstawie stanu serwera. Na przykład w aplikacji platformy ASP.NET:

*Język JavaScript w aparacie Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Utwórz dodatkowe zasoby usługi Application Insights
Do oddzielania danych telemetrycznych dla składników innej aplikacji lub dla różnych sygnaturach (dev/testowania/produkcji) tego samego składnika, następnie musisz utworzyć nowy zasób usługi Application Insights.

W [portal.azure.com](https://portal.azure.com), Dodaj zasób usługi Application Insights:

![Kliknij kolejno polecenia Nowy, Application Insights](./media/separate-resources/01-new.png)

* **Typ aplikacji** dotyczy, zostanie wyświetlony w bloku przeglądu i właściwości, które są dostępne w [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md). Jeśli nie widać danego typu aplikacji, wybierz jeden z typów sieci web dla stron sieci web.
* **Grupa zasobów** jest wygoda dla właściwości, takie jak zarządzanie [kontroli dostępu](../../azure-monitor/app/resources-roles-access-control.md). Można użyć oddzielnych grupach zasobów dla rozwoju, testowania i produkcji.
* **Subskrypcja** jest Twoje konto płatności na platformie Azure.
* **Lokalizacja** jest, w którym przechowujemy Twoje dane. Obecnie nie można zmienić. 
* **Dodaj do pulpitu nawigacyjnego** umieszcza kafelka szybkiego dostępu do zasobu na stronie głównej usługi Azure. 

Tworzenie zasobu zajmuje kilka sekund. Gdy wszystko będzie gotowe, zostanie wyświetlony alert.

(Możesz napisać [skrypt programu PowerShell](../../azure-monitor/app/powershell-script-create-resource.md) automatycznego tworzenia zasobu.)

### <a name="getting-the-instrumentation-key"></a>Uzyskiwanie klucza Instrumentacji
Klucz Instrumentacji identyfikuje zasób, który został utworzony. 

![Kliknij Essentials, kliknij przycisk klucz instrumentacji, CTRL + C](./media/separate-resources/02-props.png)

Wymagane są klucze instrumentacji, zasobów, do którego Twoja aplikacja będzie wysyłać dane.

## <a name="filter-on-build-number"></a>Filtrowanie według numeru kompilacji
Po opublikowaniu nowej wersji aplikacji, chcesz mieć możliwość oddzielenia dane telemetryczne z różnych kompilacji.

Można ustawić właściwości wersja aplikacji, dzięki czemu można filtrować [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) i [Eksplorator metryk](../../azure-monitor/app/metrics-explorer.md) wyników.

![Filtrowanie według właściwości](./media/separate-resources/050-filter.png)

Istnieje kilka różnych metod ustawienie dla właściwości wersja aplikacji.

* Ustaw bezpośrednio:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* OPAKOWYWANIE tę linię w [inicjatora telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#defaults) do upewnij się, że wszystkie wystąpienia TelemetryClient są spójne.
* [ASP.NET] Ustaw wersję `BuildInfo.config`. Wersja z węzła BuildLabel przejmą moduł sieci web. Uwzględnij ten plik w projekcie i pamiętaj, aby ustawić właściwość zawsze Kopiuj w Eksploratorze rozwiązań.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generowanie BuildInfo.config automatycznie w programie MSBuild. Aby to zrobić, dodaj kilka wierszy do Twojej `.csproj` pliku:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Spowoduje to wygenerowanie pliku o nazwie *yourProjectName*. BuildInfo.config. Proces publikowania zmieni jego nazwę na BuildInfo.config.

    Etykieta kompilacji zawiera symbolu zastępczego (AutoGen_...), gdy kompilujesz za pomocą programu Visual Studio. Jednak podczas kompilowania za pomocą narzędzia MSBuild, jest ono wypełnione numer poprawnej wersji.

    Aby zezwolić na program MSBuild, aby wygenerować numerów wersji, należy ustawić wersję takich jak `1.0.*` w AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Śledzenie wersji i wydania
Aby śledzić wersje aplikacji, upewnij się, że plik `buildinfo.config` jest generowany przez proces aparatu Microsoft Build Engine. W pliku .csproj dodaj ten kod:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

Jeśli plik zawiera informację o kompilacji, moduł sieci Web usługi Application Insights automatycznie dodaje **wersję aplikacji** jako właściwość do każdego elementu telemetrii. Pozwala to na filtrowanie według wersji podczas przeprowadzania [wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md) lub [eksplorowania metryk](../../azure-monitor/app/metrics-explorer.md).

Numer wersji kompilacji jest jednak generowany tylko przez aparat Microsoft Build Engine, a nie podczas kompilowania przez deweloperów w programie Visual Studio.

### <a name="release-annotations"></a>Adnotacje dotyczące wersji
Jeśli używasz DevOps platformy Azure, możesz to zrobić [uzyskać znacznik adnotacji](../../azure-monitor/app/annotations.md) dodawany do Twoich wykresów, zawsze wtedy, gdy wydasz nową wersję. Na następującej ilustracji pokazano sposób wyświetlania tego znacznika.

![Zrzut ekranu przedstawiający przykładową adnotację dotyczącą wersji widoczną na wykresie](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Kolejne kroki

* [Zasoby udostępnione dla wielu ról](../../azure-monitor/app/app-map.md)
* [Utwórz inicjatora Telemetrii w celu odróżnienia A | Warianty B](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
