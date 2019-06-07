---
title: Usługi w chmurze usługi Application Insights dla platformy Azure | Dokumentacja firmy Microsoft
description: Skutecznie monitoruj role sieci Web i procesu roboczego za pomocą usługi Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, diagnostyka platformy Azure
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: eb7cbb80be12498242363eb8141a468e08cba73a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478330"
---
# <a name="application-insights-for-azure-cloud-services"></a>Usługi w chmurze usługi Application Insights dla platformy Azure
[Usługa Application Insights] [ start] można monitorować [aplikacje usługi Azure cloud](https://azure.microsoft.com/services/cloud-services/) dostępność, wydajność, błędy i użycia przez połączenie danych z zestawów SDK Application Insights z [Diagnostyki azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) danych z usług w chmurze. Dzięki uzyskiwanym opiniom dotyczącym wydajności i skuteczności aplikacji możesz dokonać opartych na informacjach wyborów dotyczących kierunku projektu w każdym cyklu życia.

![Pulpit nawigacyjny przeglądu](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy:

* [Azure](https://azure.com) subskrypcji. Zaloguj się przy użyciu swojego konta Microsoft, Windows, usługi Xbox Live lub innych usług w chmurze firmy Microsoft. 
* Microsoft Azure tools 2.9 lub nowsze.
* Developer Analytics Tools 7.10 lub nowsze.

## <a name="get-started-quickly"></a>Szybkie rozpoczęcie efektywnej pracy
Najszybszym i najłatwiejszym sposobem monitorowania usługi w chmurze za pomocą usługi Application Insights jest wybranie tej opcji podczas publikowania usługi na platformie Azure.

![Przykładowa strona ustawień diagnostycznych](./media/cloudservices/azure-cloud-application-insights.png)

Ta opcja umożliwia Instrumentację aplikacji w czasie wykonywania, dzięki czemu wszystkie dane telemetryczne, który chcesz monitorować, żądań, wyjątków i zależności w roli sieci web. Monitoruje ona liczników wydajności z ról procesu roboczego. Wszelkie ślady diagnostyczne generowanych przez aplikację również są wysyłane do usługi Application Insights.

Jeśli ta opcja jest wszystko, czego potrzebujesz, wszystko będzie gotowe. 

Kolejnymi krokami [wyświetlanie metryk z poziomu aplikacji](../../azure-monitor/app/metrics-explorer.md), [wykonywanie zapytań do danych z analizą](../../azure-monitor/app/analytics.md). 

Do monitorowania wydajności w przeglądarce, możesz również chcieć skonfigurować [testy dostępności](../../azure-monitor/app/monitor-web-app-availability.md) i [Dodaj kod do strony sieci Web](../../azure-monitor/app/javascript.md).

W kolejnych sekcjach omówiono następujące dodatkowe opcje:

* Wysyłanie danych z różnych składników i kompilowanie konfiguracji w celu rozdzielenia zasobów.
* Dodawanie niestandardowej telemetrii z aplikacji.

## <a name="sample-app-instrumented-with-application-insights"></a>Przykładowa aplikacja z Instrumentacją zapewnianą przez usługę Application Insights
W tym [przykładową aplikację](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Application Insights jest dodawany do usługi w chmurze przy użyciu dwóch ról procesów roboczych hostowanych na platformie Azure. 

W następnej sekcji dowiesz się, jak dostosować własny projekt usługi w chmurze w taki sam sposób.

## <a name="plan-resources-and-resource-groups"></a>Planowanie zasobów i grup zasobów
Dane telemetryczne z Twojej aplikacji jest przechowywane, analizowane i wyświetlane w zasobie platformy Azure typu Application Insights. 

Każdy zasób należy do grupy zasobów. Grupy zasobów są używane do zarządzania kosztami, aby udzielić dostępu do członków zespołu i wdrażania aktualizacji w ramach jednej skoordynowanej transakcji. Na przykład, można wykonać następujące akcje [napisać skrypt, aby wdrożyć](../../azure-resource-manager/resource-group-template-deploy.md) usługi w chmurze platformy Azure i jej usługi Application Insights, monitorowanie zasobów łącznie w jednej operacji.

### <a name="resources-for-components"></a>Zasoby dla składników
Firma Microsoft zaleca, aby utworzyć osobny zasób dla każdego składnika aplikacji. Oznacza to możesz utworzyć zasób dla każdej roli sieci web i roli procesu roboczego. Możesz analizować każdy składnik oddzielnie, ale możesz tworzyć [pulpit nawigacyjny](../../azure-monitor/app/overview-dashboard.md) który zgromadzi kluczowe wykresy ze wszystkich składników, tak aby można porównać i monitorować je w jednym widoku. 

Alternatywnym podejściem jest do wysyłania danych telemetrycznych z więcej niż jednej roli do tego samego zasobu, ale [dodanie właściwości wymiaru do każdego elementu telemetrii](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer) która określa jego rolę źródłową. W tym podejściu wykresy metryk, takich jak wyjątki, zwykle przedstawiają agregację liczby elementów z różnych ról, ale możesz posegmentować wykres zgodnie z identyfikatorem roli, zgodnie z potrzebami. Można także filtrować wyszukiwania przez ten sam wymiar. Ta alternatywa sprawia, że nieco prostsze wyświetlić wszystkie elementy w tym samym czasie, ale również może prowadzić do wystąpienia pewnych niejasności między rolami.

Telemetria przeglądarki zwykle jest zawarta w tym samym zasobie, co jej rola sieci Web po stronie serwera.

Umieść zasoby usługi Application Insights dla różnych składników w jednej grupie zasobów. Takie podejście ułatwia zarządzanie nimi jednocześnie. 

### <a name="separate-development-test-and-production"></a>Oddzielanie programowania, testowania i produkcji
Jeśli opracowujesz zdarzenia niestandardowe dla następnej funkcji, gdy poprzednia wersja działa, wyślij telemetrię tworzenia do oddzielnych zasobów usługi Application Insights. W przeciwnym razie może być trudno znaleźć telemetrie testów wśród całego ruchu z działającej witryny.

Aby uniknąć tej sytuacji, należy utworzyć oddzielne zasoby dla każdej konfiguracji kompilacji lub "sygnatury" (rozwój, test, produkcji i tak dalej) systemu. Umieść zasoby dla każdej konfiguracji kompilacji w oddzielnej grupie zasobów. 

Aby wysyłać dane telemetryczne do odpowiednich zasobów, możesz skonfigurować zestaw SDK usługi Application Insights tak, aby pobierał inny klucz instrumentacji, w zależności od konfiguracji kompilacji. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Tworzenie zasobu usługi Application Insights dla każdej roli

Jeśli zdecydujesz się utworzyć osobny zasób dla każdej roli, a być może, oddzielny zestaw dla każdej konfiguracji kompilacji, najłatwiej utworzyć je wszystkie w portalu usługi Application Insights. Jeśli tworzysz wiele zasobów, możesz to zrobić [zautomatyzować proces](../../azure-monitor/app/powershell.md).

1. W [witryny Azure portal][portal], wybierz opcję **New** > **usług dla deweloperów**  >   **Usługa Application Insights**.  

    ![Application Insights okienko](./media/cloudservices/01-new.png)

1. W **typ aplikacji** listy rozwijanej wybierz **aplikacji sieci web ASP.NET**.  
    Każdy zasób jest identyfikowany przez klucz instrumentacji. Ten klucz może być potrzebny później, jeśli chcesz ręcznie skonfigurować lub zweryfikować konfiguracji zestawu SDK.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurowanie diagnostyki platformy Azure dla każdej roli
Ustaw tę opcję, aby monitorować aplikację za pomocą usługi Application Insights. Dla ról sieci web ta opcja zapewnia monitorowanie wydajności, alerty, diagnostyki i analizy użycia. W przypadku innych ról możesz wyszukiwać i monitorować diagnostyki platformy Azure, takie jak ponowne uruchomienie, liczniki wydajności i wywołania interfejsu System.Diagnostics.Trace. 

1. W Eksploratorze rozwiązań w usłudze Visual Studio w obszarze  **\<YourCloudService >**  > **role**, otwórz właściwości każdej roli.

1. W **konfiguracji**, wybierz opcję **Wysyłaj dane diagnostyczne do usługi Application Insights** pole wyboru, a następnie wybierz zasób usługi Application Insights, który został utworzony wcześniej.

Jeśli zamierzasz używać osobnego zasobu usługi Application Insights dla każdej konfiguracji kompilacji, najpierw wybierz konfigurację.

![Skonfiguruj usługę Application Insights](./media/cloudservices/configure-azure-diagnostics.png)

To powoduje wstawianie kluczy Instrumentacji usługi Application Insights do plików o nazwie *ServiceConfiguration.\*. cscfg*. Oto [przykładowego kodu](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg).

Jeśli chcesz zróżnicować poziom informacji diagnostycznych, które są wysyłane do usługi Application Insights, możesz to zrobić [, edytując *.cscfg* pliki bezpośrednio](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

## <a name="sdk"></a>Zainstaluj zestaw SDK w każdym projekcie
Po wybraniu tej opcji można dodać niestandardowych telemetrii biznesowych do każdej roli. Opcja udostępnia dokładniejszego przeanalizowania sposobu aplikacji jest używany, i wykonuje.

W programie Visual Studio skonfiguruj zestaw SDK usługi Application Insights dla każdego projektu aplikacji w chmurze.

1. Aby skonfigurować **role sieci web**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Konfiguruj usługę Application Insights** lub **Dodaj > telemetria usługi Application Insights**.

1. Aby skonfigurować **ról procesów roboczych**: 

    a. Kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Zarządzaj pakietami NuGet**.

    b. Dodaj pozycję [Usługa Application Insights dla serwerów systemu Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Wyszukaj „Application Insights”](./media/cloudservices/04-ai-nuget.png)

1. Aby skonfigurować zestaw SDK, aby wysyłać dane do zasobu usługi Application Insights:

    a. W odpowiedniej funkcji uruchamiania Ustaw klucz Instrumentacji z ustawień konfiguracji w *.cscfg* pliku:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. Powtórz "krok" dla każdej roli w swojej aplikacji. Zobacz przykłady:
   
    * [Rola sieci Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Rola procesu roboczego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Dla stron sieci Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. Ustaw *plik ApplicationInsights.config* plik, aby zawsze był kopiowany do katalogu wyjściowego.  
    Wiadomości w *.config* pliku prosi o umieścić tam klucza instrumentacji. Jednak dla aplikacji w chmurze jest lepiej ustawić to z *.cscfg* pliku. Takie podejście zapewnia, że rola jest prawidłowo identyfikowana w portalu.

#### <a name="run-and-publish-the-app"></a>Uruchom i opublikuj aplikację

1. Uruchom aplikację i zaloguj się do platformy Azure. 

1. Otwórz zasoby usługi Application Insights, które zostały utworzone.  
    Poszczególne punkty danych są wyświetlane w [wyszukiwania](../../azure-monitor/app/diagnostic-search.md), i zagregowane dane są wyświetlane w [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md). 

1. Dodawanie większej ilości danych telemetrycznych (zobacz w kolejnych sekcjach), a następnie opublikować aplikację, aby uzyskać na żywo dane diagnostyczne i użycia opinii. 

Jeśli nie ma żadnych danych, wykonaj następujące czynności:
1. Aby wyświetlić poszczególne zdarzenia, otwórz [wyszukiwania] [ diagnostic] kafelka.
1. Otwórz różne strony w aplikacji, tak, aby go wygenerować dane telemetryczne.
1. Odczekaj kilka sekund, a następnie kliknij przycisk **Odśwież**.  
    Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów][qna].

## <a name="view-azure-diagnostics-events"></a>Wyświetlanie zdarzeń diagnostycznych platformy Azure
Możesz znaleźć [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) informacji w usłudze Application Insights w następujących lokalizacjach:

* Liczniki wydajności są wyświetlane jako metryki niestandardowe. 
* Dzienniki zdarzeń systemu Windows są wyświetlane jako ślady i zdarzenia niestandardowe.
* Dzienniki aplikacji, dzienniki śledzenia zdarzeń systemu Windows i wszelkie dzienniki infrastruktury diagnostycznej są wyświetlane jako ślady.

Aby wyświetlić liczniki wydajności i liczby zdarzeń, otwórz [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) i dodać w poniższej tabeli:

![Dane diagnostyczne platformy Azure](./media/cloudservices/23-wad.png)

Aby wyszukiwać w różnych dziennikach śledzenia wysyłanych przez diagnostykę platformy Azure, użyj [wyszukiwania](../../azure-monitor/app/diagnostic-search.md) lub [zapytania usługi Analytics](../../azure-monitor/log-query/get-started-portal.md). Na przykład załóżmy, że masz nieobsłużony wyjątek, który spowodował awarię i odtworzenie roli. Te informacje będą wyświetlane w kanale aplikacji dziennika zdarzeń systemu Windows. Wyszukiwanie umożliwia wyświetlanie błędów w dzienniku zdarzeń Windows i uzyskać pełen ślad stosu dla wyjątku. To ułatwia znalezienie głównej przyczyny problemu.

![Wyszukiwanie diagnostyki platformy Azure](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii
W kolejnych sekcjach omówiono, jak uzyskać dodatkową telemetrię z różnych aspektów aplikacji.

## <a name="track-requests-from-worker-roles"></a>Śledzenie żądań z ról procesów roboczych
W rolach sieci Web moduł żądań automatycznie zbiera dane o żądaniach HTTP. Aby uzyskać przykłady sposobu zastąpienia domyślnego zachowania kolekcji, zobacz [przykładową rolę MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Możesz przechwycić wydajności wywołań do ról procesów roboczych, śledząc je w taki sam sposób, jak żądania HTTP. W usłudze Application Insights żądanie typu telemetrii mierzy jednostkę po stronie nazwanego serwera, które może być synchronizowane i może niezależnie zakończyć się sukcesem lub niepowodzeniem. Mimo że żądania HTTP są przechwytywane automatycznie przez zestaw SDK, możesz wstawić własny kod do śledzenia żądań dla ról procesów roboczych.

Zobacz dwie role proces roboczy przykład Instrumentację do zgłaszania żądania: 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Wyjątki
Aby dowiedzieć się, jak gromadzić nieobsługiwane wyjątki z różnych typów aplikacji sieci web, zobacz [monitorowanie wyjątków w usłudze Application Insights](../../azure-monitor/app/asp-net-exceptions.md).

Przykładowa rola sieci Web ma kontrolery MVC5 i Web API 2. Nieobsługiwane wyjątki od tych dwóch są przechwytywane za pomocą następujących programów obsługi:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) dla kontrolerów MVC5 [jak pokazano w poniższym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) 
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) dla kontrolerów Web API 2 [jak pokazano w poniższym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) 

Dla ról procesów roboczych można śledzić wyjątki na dwa sposoby:

* Użyj TrackException(ex).
* Jeśli dodano pakiet NuGet odbiornika śledzenia usługi Application Insights, można użyć System.Diagnostics.Trace rejestrowania wyjątków [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107).

## <a name="performance-counters"></a>Liczniki wydajności
Domyślnie są zbierane następujące liczniki:

* \Process(??. APP_WIN32_PROC??)\% Czas procesora
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Następujące liczniki są również zbierane dla ról sieci Web:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Można określić dodatkowe niestandardowe lub inne liczniki wydajności Windows, edytując *plik ApplicationInsights.config* [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Liczniki wydajności](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Skorelowana telemetria dla ról procesów roboczych
Zaawansowaną diagnostykę obejmującą uwzględnianie środowisko pracy można wyświetlić, co doprowadziło do żądania zakończone niepowodzeniem lub wysokie opóźnienie. W przypadku ról sieci web zestaw SDK automatycznie konfiguruje korelację między powiązaną telemetrią. 

Aby osiągnąć ten widok dla ról procesów roboczych, można użyć inicjatora niestandardowej telemetrii, można ustawić wspólny atrybut kontekstu Operation.Id dla wszystkich telemetrii. Umożliwia to wyświetlanie rzut oka, czy problem opóźnienia lub błąd został spowodowany przez zależność lub w kodzie. 

Oto kroki tej procedury:

* Ustaw identyfikator korelacji na parametr CallContext [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). W tym przypadku używamy Identyfikatora żądania jako wartość correlationId.
* Dodaj niestandardową implementację parametru TelemetryInitializer, aby ustawić parametr Operation.Id na identyfikator korelacji, która została wcześniej ustawiona. Aby uzyskać przykład, zobacz [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Dodaj inicjator niestandardowej telemetrii. Możesz to zrobić to w *plik ApplicationInsights.config* pliku lub w kodzie [jak pokazano w tym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233).

## <a name="client-telemetry"></a>Telemetria klienta
Można pobrać telemetrii oparte na przeglądarce, takie jak wyświetleń strony, czasy ładowania stron lub wyjątki skryptów i zapisywanie niestandardowych danych telemetrycznych w skryptach strony, zobacz [Dodaj zestaw SDK JavaScript do strony sieci Web][client].

## <a name="availability-tests"></a>Testy dostępności
Aby upewnić się, aplikacja pozostaje na żywo i dynamiczne [Konfigurowanie testów sieci web][availability].

## <a name="display-everything-together"></a>Wyświetlanie wszystkich elementów razem
Aby uzyskać ogólny obraz systemu, można wyświetlić kluczowe wykresy monitorowania razem na jednej [pulpit nawigacyjny](../../azure-monitor/app/overview-dashboard.md). Na przykład możesz przypiąć liczbę żądań i awarii poszczególnych ról. 

Jeśli system używa innych usług platformy Azure, takich jak Stream Analytics, uwzględnić wykresy ich monitorowania oraz. 

Jeśli masz klienta aplikacji mobilnej, użyj pakietu [App Center](../../azure-monitor/learn/mobile-center-quickstart.md). Utwórz zapytania w pozycji [Analiza](../../azure-monitor/app/analytics.md), aby wyświetlić liczby zdarzeń i przypiąć je do pulpitu nawigacyjnego.

## <a name="example"></a>Przykład
[Przykład](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoruje usługę mającą rolę sieci Web i dwie role procesów roboczych.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek "nie można odnaleźć metody" na temat uruchamiania w usługach Azure cloud services
Czy to kompilacja dla .NET 4.6? .NET 4.6 nie jest automatycznie obsługiwana w rolach usług platformy Azure w chmurze. [Zainstalować program .NET 4.6 w każdej roli](../../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie wysyłania usługi Diagnostyka Azure do usługi Application Insights](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Automatyczne tworzenie zasobów usługi Application Insights](../../azure-monitor/app/powershell.md)
* [Automatyzacja usługi Diagnostyka Azure](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
