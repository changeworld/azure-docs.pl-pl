---
title: Usługa Application Insights dla usług Azure Cloud Services | Microsoft Docs
description: Skutecznie monitoruj role sieci Web i procesu roboczego za pomocą usługi Application Insights
services: application-insights
documentationcenter: ''
keywords: WAD2AI, diagnostyka platformy Azure
author: mrbullwinkle
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: f36a9e21478d2629d705d90179a6db5175c78299
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="application-insights-for-azure-cloud-services"></a>Usługa Application Insights dla usług Azure Cloud Services
[Aplikacje usługi Microsoft Azure Cloud](https://azure.microsoft.com/services/cloud-services/) mogą być monitorowane przez usługę [Application Insights][start] w celu sprawdzania ich dostępności, wydajności, błędów i użycia. W tym celu dane z zestawów SDK usługi Application Insights są łączone z danymi z usługi [Azure Diagnotics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) pochodzącymi z usług w chmurze. Dzięki uzyskiwanym opiniom dotyczącym wydajności i skuteczności aplikacji możesz dokonać opartych na informacjach wyborów dotyczących kierunku projektu w każdym cyklu życia.

![Przykład](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Przed rozpoczęciem
Będą potrzebne:

* Subskrypcja platformy [Microsoft Azure](http://azure.com). Zaloguj się przy użyciu konta Microsoft, które możesz mieć dla systemu Windows, usługi XBox Live lub innych usług w chmurze firmy Microsoft. 
* Narzędzia Microsoft Azure Tools 2.9 lub nowsze.
* Narzędzia Developer Analytics Tools 7.10 lub nowsze.

## <a name="quick-start"></a>Szybki start
Najszybszym i najłatwiejszym sposobem monitorowania usługi w chmurze za pomocą usługi Application Insights jest wybranie tej opcji podczas publikowania usługi na platformie Azure.

![Przykład](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Ta opcja umożliwia instrumentację aplikacji w czasie wykonywania dzięki udostępnieniu całej telemetrii potrzebnej do monitorowania żądań, wyjątków i zależności w roli sieci Web oraz liczników wydajności z ról procesu roboczego. Wszelkie ślady diagnostyczne wygenerowane przez aplikację również są wysyłane do usługi Application Insights.

Jeśli to wszystko, czego potrzebujesz, to koniec! Następne kroki to [wyświetlanie metryk z poziomu aplikacji](app-insights-metrics-explorer.md), [wykonywanie zapytań do danych za pomocą funkcji analizy](app-insights-analytics.md) i, być może, konfigurowanie [pulpitu nawigacyjnego](app-insights-dashboards.md). Możesz chcieć skonfigurować [testy dostępności](app-insights-monitor-web-app-availability.md) i [dodać kod do stron sieci Web](app-insights-javascript.md) w celu monitorowania wydajności w przeglądarce.

Ale możesz również korzystać z dodatkowych opcji:

* Wysyłanie danych z różnych składników i kompilowanie konfiguracji w celu oddzielenia zasobów.
* Dodawanie niestandardowej telemetrii z aplikacji.

Jeśli te opcje Cię zainteresowały, czytaj dalej.

## <a name="sample-application-instrumented-with-application-insights"></a>Przykładowa aplikacja z instrumentacją zapewnianą przez usługę Application Insights
Przyjrzyj się tej [przykładowej aplikacji](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), w której usługa Application Insights została dodana do usługi w chmurze z dwoma rolami procesów roboczych hostowanych na platformie Azure. 

Poniżej znajdują się informacje, jak dostosować własny projekt usługi w chmurze w taki sam sposób.

## <a name="plan-resources-and-resource-groups"></a>Planowanie zasobów i grup zasobów
Dane telemetryczne z aplikacji są przechowywane, analizowane i wyświetlane w zasobie platformy Azure typu Application Insights. 

Każdy zasób należy do grupy zasobów. Grupy zasobów służą do zarządzania kosztami, do przyznawania dostępu członkom zespołu i wdrażania aktualizacji w jednej skoordynowanej transakcji. Na przykład możesz [napisać skrypt w celu wdrożenia](../azure-resource-manager/resource-group-template-deploy.md) usługi Azure Cloud Service i jej zasobów monitorowania usługi Application Insights łącznie w jednej operacji.

### <a name="resources-for-components"></a>Zasoby dla składników
Zalecany schemat obejmuje utworzenie osobnego zasobu dla każdego składnika aplikacji — czyli każdej roli sieci Web i roli procesu roboczego. Możesz analizować każdy składnik oddzielnie, ale możesz też utworzyć [pulpit nawigacyjny](app-insights-dashboards.md), który zgromadzi kluczowe wykresy ze wszystkich składników, dzięki czemu można będzie je porównać i monitorować razem. 

Alternatywny schemat obejmuje wysłanie danych telemetrycznych z więcej niż jednej roli do tego samego zasobu, ale [dodanie właściwości wymiaru do każdego elementu telemetrii](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer), która określa jego rolę źródłową. W tym schemacie wykresy metryk, takich jak wyjątki, zwykle przedstawiają agregację liczby elementów z różnych ról, ale w razie potrzeby możesz posegmentować wykres zgodnie z identyfikatorem roli. Wyszukiwanie można również filtrować według tego samego wymiaru. Ta alternatywa nieco ułatwia jednoczesne wyświetlanie wszystkich elementów, ale może też prowadzić do wystąpienia pewnych niejasności między rolami.

Telemetria przeglądarki zwykle jest zawarta w tym samym zasobie, co jej rola sieci Web po stronie serwera.

Umieść zasoby usługi Application Insights dla różnych składników w jednej grupie zasobów. Ułatwia to wspólne zarządzanie nimi. 

### <a name="separating-development-test-and-production"></a>Oddzielanie tworzenia, testowania i produkcji
Jeśli opracowujesz zdarzenia niestandardowe dla następnej funkcji, gdy poprzednia wersja działa, wyślij telemetrię tworzenia do oddzielnych zasobów usługi Application Insights. W przeciwnym razie będzie Ci trudno znaleźć telemetrie testów wśród całego ruchu z działającej witryny.

Aby uniknąć tej sytuacji, należy utworzyć osobne zasoby dla każdej konfiguracji kompilacji lub „oznakowania” (tworzenie, testy, produkcja,...) systemu. Umieść zasoby dla każdej konfiguracji kompilacji w oddzielnej grupie zasobów. 

Aby wysłać dane telemetryczne do odpowiednich zasobów, możesz skonfigurować zestaw SDK usługi Application Insights tak, aby pobierał inny klucz instrumentacji w zależności od konfiguracji kompilacji. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Tworzenie zasobu usługi Application Insights dla każdej roli
Jeśli postanowisz utworzyć osobny zasób dla każdej roli — oraz, być może, oddzielny zestaw dla każdej konfiguracji kompilacji — wówczas najłatwiej utworzyć je wszystkie w portalu usługi Application Insights. (Jeśli tworzysz wiele zasobów, możesz [zautomatyzować ten proces](app-insights-powershell.md)).

1. W witrynie [Azure Portal][portal] utwórz nowy zasób usługi Application Insights. Jako typ aplikacji wybierz ASP.NET. 

    ![Kliknij kolejno polecenia Nowy, Application Insights](./media/app-insights-cloudservices/01-new.png)
2. Należy pamiętać, że każdy zasób jest identyfikowany przez klucz instrumentacji. Może on być potrzebny później, jeśli chcesz ręcznie skonfigurować zestaw SDK lub sprawdzić jego konfigurację.

    ![Kliknij opcję Właściwości, zaznacz klucz i naciśnij klawisze Ctrl+C](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Konfigurowanie diagnostyki platformy Azure dla każdej roli
Ustaw tę opcję, aby monitorować aplikację za pomocą usługi Application Insights. W przypadku ról sieci Web zapewnia to monitorowanie wydajności, alerty i diagnostykę oraz analizę użycia. W przypadku innych ról możesz wyszukiwać i monitorować diagnostykę platformy Azure, taką jak ponowne uruchomienie, liczniki wydajności i wywołania interfejsu System.Diagnostics.Trace. 

1. W Eksploratorze rozwiązań programu Visual Studio w obszarze &lt;YourCloudService&gt;, Role, otwórz właściwości każdej roli.
2. W pozycji **Konfiguracja** ustaw pozycję **Wysyłaj dane diagnostyczne do usługi Application Insights** i wybierz odpowiedni, utworzony wcześniej zasób usługi Application Insights.

Jeśli zamierzasz używać osobnego zasobu usługi Application Insights dla każdej konfiguracji kompilacji, najpierw wybierz konfigurację.

![We właściwościach poszczególnych ról platformy Azure skonfiguruj usługę Application Insights](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

To powoduje wstawianie kluczy instrumentacji usługi Application Insights do plików o nazwie `ServiceConfiguration.*.cscfg`. ([Przykładowy kod](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Jeśli chcesz zróżnicować poziom informacji diagnostycznych wysyłanych do usługi Application Insights, możesz to zrobić, [bezpośrednio edytując pliki `.cscfg`](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Zainstaluj zestaw SDK w każdym projekcie
Ta opcja dodaje możliwość dodawania niestandardowych telemetrii biznesowych do każdej roli w celu dokładniejszego przeanalizowania sposobu używania i działania aplikacji.

W programie Visual Studio skonfiguruj zestaw SDK usługi Application Insights dla każdego projektu aplikacji w chmurze.

1. **Role sieci Web**: kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Konfiguruj usługę Application Insights** lub **Dodaj > Telemetria usługi Application Insights**.

2. **Role procesów roboczych**: 
 * Kliknij projekt prawym przyciskiem myszy i wybierz pozycję **Zarządzaj pakietami NuGet**.
 * Dodaj pozycję [Usługa Application Insights dla serwerów systemu Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Wyszukaj „Application Insights”](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Skonfiguruj zestaw SDK do przesyłania danych do zasobu usługi Application Insights.

    W odpowiedniej funkcji uruchamiania ustaw klucz instrumentacji z ustawień konfiguracji w pliku cscfg:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Zrób to dla każdej roli w aplikacji. Zobacz przykłady:
   
   * [Rola sieci Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Rola procesu roboczego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [W przypadku stron sieci Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Ustaw plik ApplicationInsights.config tak, aby zawsze był kopiowany do katalogu wyjściowego. 
   
    (W pliku config zostaną wyświetlone komunikaty z prośbą o umieszczenie tam klucza instrumentacji. Jednak dla aplikacji w chmurze lepiej ustawić to z pliku cscfg. Zapewni to, że rola jest prawidłowo identyfikowana w portalu).

#### <a name="run-and-publish-the-app"></a>Uruchom i opublikuj aplikację
Uruchom aplikację, a następnie zaloguj się na platformie Azure. Otwórz utworzone zasoby usługi Application Insights, a zobaczysz poszczególne punkty danych znajdujące się w obszarze [wyszukiwania](app-insights-diagnostic-search.md) i zagregowane dane w [Eksploratorze metryk](app-insights-metrics-explorer.md). 

Dodaj więcej funkcji telemetrii — zobacz sekcje poniżej — a następnie opublikuj aplikację, aby uzyskać rzeczywiste opinie o diagnostyce i użyciu. 

#### <a name="no-data"></a>Brak danych?
* Otwórz kafelek [Wyszukaj][diagnostic], aby wyświetlić poszczególne zdarzenia.
* Otwieraj różne strony w aplikacji, aby wygenerować dane telemetryczne.
* Odczekaj kilka sekund, a następnie kliknij przycisk Odśwież.
* Zobacz [Rozwiązywanie problemów][qna].

## <a name="view-azure-diagnostic-events"></a>Wyświetlanie zdarzeń diagnostycznych platformy Azure
Informacje o usłudze [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) dostępne w usłudze Application Insights:

* Liczniki wydajności są wyświetlane jako metryki niestandardowe. 
* Dzienniki zdarzeń systemu Windows są wyświetlane jako ślady i zdarzenia niestandardowe.
* Dzienniki aplikacji, dzienniki śledzenia zdarzeń systemu Windows i wszelkie dzienniki infrastruktury diagnostycznej są wyświetlane jako ślady.

Aby wyświetlić liczniki wydajności i liczby zdarzeń, otwórz pozycję [Eksplorator metryk](app-insights-metrics-explorer.md) i dodaj nowy wykres:

![Dane diagnostyczne platformy Azure](./media/app-insights-cloudservices/23-wad.png)

Użyj [wyszukiwania](app-insights-diagnostic-search.md) lub [zapytania analitycznego](app-insights-analytics-tour.md), aby wyszukiwać w różnych dziennikach śledzenia wysyłanych przez diagnostykę platformy Azure. Załóżmy na przykład, że masz nieobsłużony wyjątek, który spowodował awarię i odtworzenie roli. Te informacje będą wyświetlane w kanale aplikacji dziennika zdarzeń systemu Windows. Możesz użyć wyszukiwania, aby wyszukać błąd w dzienniku zdarzeń systemu Windows i uzyskać pełen ślad stosu dla wyjątku. Pomoże Ci to znaleźć główną przyczynę problemu.

![Wyszukiwanie diagnostyki platformy Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Dalsze funkcje telemetrii
Poniższe sekcje pokazują, jak uzyskać dodatkowe dane telemetryczne z różnych aspektów aplikacji.

## <a name="track-requests-from-worker-roles"></a>Śledzenie żądań z ról procesów roboczych
W rolach sieci Web moduł żądań automatycznie zbiera dane o żądaniach HTTP. Zobacz [przykładową rolę MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole), aby zapoznać się z przykładami sposobu zastąpienia domyślnego zachowania kolekcji. 

Możesz przechwycić wydajności wywołań do ról procesów roboczych, śledząc je w taki sam sposób, jak żądania HTTP. W usłudze Application Insights żądanie typu telemetrii mierzy jednostkę po stronie nazwanego serwera, które może być synchronizowane i może niezależnie zakończyć się sukcesem lub niepowodzeniem. Gdy żądania HTTP są przechwytywane automatycznie przez zestaw SDK, możesz wstawić własny kod do śledzenia żądań dla ról procesów roboczych.

Zobacz dwie role procesu roboczego mające instrumentację do zgłaszania żądania: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) i [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Wyjątki
Zobacz [Monitoring Exceptions in Application Insights](app-insights-asp-net-exceptions.md) (Monitorowanie wyjątków w usłudze Application Insights), aby uzyskać informacje, w jaki sposób możesz gromadzić nieobsługiwane wyjątki z różnych typów aplikacji sieci Web.

Przykładowa rola sieci Web ma kontrolery MVC5 i Web API 2. Nieobsługiwane wyjątki od tych dwóch są przechwytywane za pomocą następujących programów obsługi:

* [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) konfigurowanego [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) dla kontrolerów MVC5
* [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) konfigurowanego [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) dla kontrolerów Web API 2

Dla ról procesów roboczych istnieją dwa sposoby śledzenia wyjątków:

* TrackException(ex)
* Jeśli został dodany pakiet NuGet odbiornika śledzenia usługi Application Insights, możesz użyć interfejsu **System.Diagnostics.Trace** do rejestrowania wyjątków. [Przykładowy kod.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

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

Możesz określić dodatkowe niestandardowe lub inne liczniki wydajności systemu Windows, edytując plik ApplicationInsights.config [jak w poniższym przykładzie](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![Liczniki wydajności](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Skorelowana telemetria dla ról procesów roboczych
Gdy możesz zobaczyć, co doprowadziło do nieudanego lub znacznie opóźnionego żądania, gromadzisz znaczne doświadczenie diagnostyczne. Za pomocą ról sieci Web zestaw SDK automatycznie konfiguruje korelację między powiązaną telemetrią. Dla ról procesów roboczych możesz użyć inicjatora niestandardowej telemetrii, aby ustawić wspólny atrybut kontekstu Operation.Id dla wszystkich telemetrii, aby to osiągnąć. Dzięki temu możesz szybko sprawdzić, czy problem opóźnienia/błędu został spowodowany przez zależność, czy też przez kod. 

Oto kroki tej procedury:

* Ustaw identyfikator korelacji na parametr CallContext, jak pokazano [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). W takim przypadku użyjemy Identyfikatora żądania jako identyfikatora korelacji
* Dodaj niestandardową implementację parametru TelemetryInitializer, aby ustawić parametr Operation.Id na ustawiony powyżej correlationId. Tutaj znajduje się przykład: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Dodaj inicjator niestandardowej telemetrii. Możesz to zrobić w pliku ApplicationInsights.config lub w kodzie, co zostało pokazane [tutaj](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Gotowe. Środowisko pracy w portalu jest już gotowe i możesz łatwo zobaczyć całą skojarzoną telemetrię:

![Skorelowana telemetria](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Telemetria klienta
[Dodaj zestaw SDK JavaScript do stron sieci Web][client], aby uzyskiwać dane telemetryczne oparte na przeglądarce, takie jak liczba wyświetleń strony, czasy ładowania strony i wyjątki skryptów, oraz mieć możliwość napisania niestandardowej telemetrii w skryptach strony.

## <a name="availability-tests"></a>Testy dostępności
[Konfigurowanie testów sieci Web][availability], aby upewnić się, że aplikacja stale działa i odpowiada.

## <a name="display-everything-together"></a>Wyświetlanie wszystkich elementów razem
Aby uzyskać ogólny obraz systemu, można przenieść kluczowe wykresy monitorowania razem na jeden [pulpit nawigacyjny](app-insights-dashboards.md). Na przykład możesz przypiąć liczbę żądań i awarii poszczególnych ról. 

Jeśli system używa innych usług platformy Azure, takich jak Stream Analytics, należy również uwzględnić wykresy ich monitorowania. 

Jeśli masz klienta aplikacji mobilnej, użyj pakietu [App Center](app-insights-mobile-center-quickstart.md). Utwórz zapytania w pozycji [Analiza](app-insights-analytics.md), aby wyświetlić liczby zdarzeń i przypiąć je do pulpitu nawigacyjnego.

## <a name="example"></a>Przykład
[Przykład](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) monitoruje usługę mającą rolę sieci Web i dwie role procesów roboczych.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Wyjątek „nie można odnaleźć metody” podczas uruchamiania w usługach Azure Cloud Services
Czy to kompilacja dla .NET 4.6? Wersja 4.6 nie jest automatycznie obsługiwana w rolach usług Azure Cloud Services. [Zainstaluj wersję 4.6 w każdej roli](../cloud-services/cloud-services-dotnet-install-dotnet.md) przed uruchomieniem aplikacji.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Następne kroki
* [Konfigurowanie wysyłania usługi Diagnostyka Azure do usługi Application Insights](app-insights-azure-diagnostics.md)
* [Automatyczne tworzenie zasobów usługi Application Insights](app-insights-powershell.md)
* [Automatyzacja usługi Diagnostyka Azure](app-insights-powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 
