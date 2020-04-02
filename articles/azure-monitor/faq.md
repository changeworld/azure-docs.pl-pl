---
title: Często zadawane pytania dotyczące usługi Azure Monitor | Dokumenty firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: 306d847c2bc5af72d37dbf8bf472a5bae63e9fd5
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528502"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure Monitor

To często zadawane pytania firmy Microsoft to lista często zadawanych pytań dotyczących usługi Azure Monitor.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
[Usługa Azure Monitor](overview.md) to usługa na platformie Azure, która zapewnia monitorowanie wydajności i dostępności aplikacji i usług na platformie Azure, innych środowiskach w chmurze lub lokalnie. Usługa Azure Monitor zbiera dane z wielu źródeł do wspólnej platformy danych, gdzie można je analizować pod kątem trendów i anomalii. Zaawansowane funkcje w usłudze Azure Monitor pomagają szybko identyfikować i reagować na krytyczne sytuacje, które mogą mieć wpływ na aplikację.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Jaka jest różnica między usługą Azure Monitor, usługa Log Analytics i usługa Application Insights?
We wrześniu 2018 r. firma Microsoft połączyła usługę Azure Monitor, usługa Log Analytics i usługa Application Insights w jedną usługę, aby zapewnić zaawansowane kompleksowe monitorowanie aplikacji i składników, na których polegają. Funkcje usługi Log Analytics i usługi Application Insights nie uległy zmianie, chociaż niektóre funkcje zostały przemianowane na usługę Azure Monitor, aby lepiej odzwierciedlać ich nowy zakres. Aparat danych dziennika i język zapytań usługi Log Analytics jest teraz określany jako dzienniki usługi Azure Monitor. Zobacz [aktualizacje terminologii usługi Azure Monitor](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Ile kosztuje usługa Azure Monitor?
Funkcje usługi Azure Monitor, które są automatycznie włączone, takie jak zbieranie metryk i dzienników aktywności są dostarczane bezpłatnie. Istnieje koszt skojarzony z innymi funkcjami, takimi jak zapytania dziennika i alerty. Szczegółowe informacje o cenach można znaleźć na [stronie cennika usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="how-do-i-enable-azure-monitor"></a>Jak włączyć usługę Azure Monitor?
Usługa Azure Monitor jest włączona w momencie tworzenia nowej subskrypcji platformy Azure, a [metryki](platform/data-platform-metrics.md) [dziennika](platform/activity-logs-overview.md) aktywności i platformy są zbierane automatycznie. Tworzenie [ustawień diagnostycznych](platform/diagnostic-settings.md) w celu zbierania bardziej szczegółowych informacji na temat działania zasobów platformy Azure i dodawanie [rozwiązań monitorowania](insights/solutions.md) i szczegółowych [informacji](insights/insights-overview.md) w celu zapewnienia dodatkowej analizy zebranych danych dla określonych usług. 

### <a name="how-do-i-access-azure-monitor"></a>Jak uzyskać dostęp do usługi Azure Monitor?
Dostęp do wszystkich funkcji i danych usługi Azure Monitor z menu **Monitor** w witrynie Azure portal. **Sekcja Monitorowanie** menu dla różnych usług platformy Azure zapewnia dostęp do tych samych narzędzi z danymi filtrowanymi do określonego zasobu. Dane usługi Azure Monitor są również dostępne dla różnych scenariuszy przy użyciu interfejsu wiersza polecenia, programu PowerShell i interfejsu API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Czy istnieje lokalna wersja usługi Azure Monitor?
Nie. Usługa Azure Monitor to skalowalna usługa w chmurze, która przetwarza i przechowuje duże ilości danych, chociaż usługa Azure Monitor może monitorować zasoby lokalne i w innych chmurach.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Czy usługa Azure Monitor może monitorować zasoby lokalne?
Tak, oprócz zbierania danych monitorowania z zasobów platformy Azure usługa Azure Monitor może zbierać dane z maszyn wirtualnych i aplikacji w innych chmurach i lokalnie. Zobacz [Źródła monitorowania danych dla usługi Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Czy usługa Azure Monitor integruje się z programem Operations Manager w usłudze System Center?
Istniejącą grupę zarządzania programu System Center Operations Manager można połączyć z usługą Azure Monitor w celu zbierania danych od agentów do dzienników monitora platformy Azure. Dzięki temu można używać zapytań dziennika i rozwiązania do analizowania danych zebranych od agentów. Można również skonfigurować istniejących agentów programu Operations Manager programu System Center do wysyłania danych bezpośrednio do usługi Azure Monitor. Zobacz [Łączenie programu Operations Manager z usługą Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Jakich adresów IP używa usługa Azure Monitor?
Zobacz [adresy IP używane przez usługi Application Insights i log analytics,](app/ip-addresses.md) aby uzyskać listę adresów IP i portów wymaganych dla agentów i innych zasobów zewnętrznych w celu uzyskania dostępu do usługi Azure Monitor. 

## <a name="monitoring-data"></a>Dane monitorowania

### <a name="where-does-azure-monitor-get-its-data"></a>Gdzie usługa Azure Monitor otrzymuje swoje dane?
Usługa Azure Monitor zbiera dane z różnych źródeł, w tym dzienników i metryk z platformy Azure i zasobów, aplikacji niestandardowych i agentów działających na maszynach wirtualnych. Inne usługi, takie jak Usługa Azure Security Center i Obserwator sieci, zbierają dane do obszaru roboczego usługi Log Analytics, dzięki czemu można je analizować za pomocą danych usługi Azure Monitor. Można również wysłać dane niestandardowe do usługi Azure Monitor przy użyciu interfejsu API REST dla dzienników lub metryk. Zobacz [Źródła monitorowania danych dla usługi Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Jakie dane są zbierane przez usługę Azure Monitor? 
Usługa Azure Monitor zbiera dane z różnych źródeł do [dzienników](platform/data-platform-logs.md) lub [metryk.](platform/data-platform-metrics.md) Każdy typ danych ma swoje względne zalety, a każdy obsługuje określony zestaw funkcji w usłudze Azure Monitor. Istnieje pojedyncza baza danych metryk dla każdej subskrypcji platformy Azure, podczas gdy można utworzyć wiele obszarów roboczych usługi Log Analytics do zbierania dzienników w zależności od wymagań. Zobacz [platformę danych usługi Azure Monitor](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Czy istnieje maksymalna ilość danych, które można zbierać w usłudze Azure Monitor?
Nie ma ograniczeń co do ilości danych metrycznych, które można zbierać, ale te dane są przechowywane przez maksymalnie 93 dni. Zobacz [Przechowywanie metryk](platform/data-platform-metrics.md#retention-of-metrics). Nie ma limitu ilości danych dziennika, które można zbierać, ale może mieć wpływ warstwy cenowej, którą wybierzesz dla obszaru roboczego usługi Log Analytics. Zobacz [szczegóły cen .](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Jak uzyskać dostęp do danych zebranych przez usługę Azure Monitor?
Szczegółowe informacje i rozwiązania zapewniają niestandardowe środowisko pracy z danymi przechowywanymi w usłudze Azure Monitor. Można pracować bezpośrednio z danymi dziennika przy użyciu kwerendy dziennika napisane w Języku Kusto Query (KQL). W witrynie Azure portal można pisać i uruchamiać kwerendy i interaktywnie analizować dane przy użyciu usługi Log Analytics. Analizuj metryki w witrynie Azure portal za pomocą Eksploratora metryk. Zobacz [Analizowanie danych dziennika w usłudze Azure Monitor](log-query/log-query-overview.md) i wprowadzenie do [Eksploratora metryk platformy Azure](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Rozwiązania i spostrzeżenia

### <a name="what-is-an-insight-in-azure-monitor"></a>Co to jest wgląd w usługę Azure Monitor?
Szczegółowe informacje zapewniają dostosowane środowisko monitorowania dla określonych usług platformy Azure. Używają tych samych metryk i dzienników, co inne funkcje w usłudze Azure Monitor, ale mogą zbierać dodatkowe dane i zapewniać unikatowe środowisko w witrynie Azure portal. Zobacz [szczegółowe informacje w usłudze Azure Monitor](insights/insights-overview.md).

Aby wyświetlić szczegółowe informacje w witrynie Azure portal, zobacz **sekcję Insights** w menu **Monitor** lub **monitorowanie** w menu usługi.

### <a name="what-is-a-solution-in-azure-monitor"></a>Co to jest rozwiązanie w usłudze Azure Monitor?
Rozwiązania do monitorowania są spakowane zestawy logiki do monitorowania określonej aplikacji lub usługi na podstawie funkcji usługi Azure Monitor. Zbierają dane dziennika w usłudze Azure Monitor i udostępniają zapytania dziennika i widoki do ich analizy przy użyciu wspólnego środowiska w witrynie Azure portal. Zobacz [Rozwiązania do monitorowania w usłudze Azure Monitor](insights/solutions.md).

Aby wyświetlić rozwiązania w witrynie Azure portal, kliknij przycisk **Więcej** w sekcji **Insights** w menu **Monitor.** Kliknij **przycisk Dodaj,** aby dodać dodatkowe rozwiązania do obszaru roboczego.

## <a name="logs"></a>Dzienniki

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Jaka jest różnica między dziennikami usługi Azure Monitor a Eksploratorem danych platformy Azure?
Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Dzienniki usługi Azure Monitor są tworzone na podstawie usługi Azure Data Explorer i używają tego samego języka KQL (Kusto Query Language) z niewielkimi różnicami. Zobacz [różnice językowe zapytań dziennika usługi Azure Monitor](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Jak pobrać dane dziennika?
Wszystkie dane są pobierane z obszaru roboczego usługi Log Analytics przy użyciu kwerendy dziennika napisanej przy użyciu języka KQL (KQL). Można napisać własne zapytania lub użyć rozwiązań i szczegółowych informacji, które zawierają zapytania dziennika dla określonej aplikacji lub usługi. Zobacz [Omówienie zapytań dziennika w usłudze Azure Monitor](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Co to jest obszar roboczy usługi Log Analytics?
Wszystkie dane dziennika zebrane przez usługę Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics. Obszar roboczy jest zasadniczo kontenerem, w którym dane dziennika są zbierane z różnych źródeł. Może mieć jeden obszar roboczy usługi Log Analytics dla wszystkich danych monitorowania lub może mieć wymagania dla wielu obszarów roboczych. Zobacz [Projektowanie wdrożenia dzienników usługi Azure Monitor](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Czy można przenieść istniejący obszar roboczy usługi Log Analytics do innej subskrypcji platformy Azure?
Obszar roboczy można przenieść między grupami zasobów lub subskrypcjami, ale nie do innego regionu. Zobacz [Przenoszenie obszaru roboczego usługi Log Analytics do innej grupy subskrypcji lub zasobów](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Dlaczego nie widzę przycisków Eksploratora zapytań i Zapisywanie w usłudze Log Analytics?

**Przyciski Eksploratora kwerend**, **Zapisz** i **Nowa reguła alertu** nie są dostępne, gdy [zakres kwerendy](log-query/scope.md) jest ustawiony na określony zasób. Aby utworzyć alerty, zapisać lub załadować kwerendę, usługa Log Analytics musi być objęta zakresem obszaru roboczego. Aby otworzyć usługę Log Analytics w kontekście obszaru roboczego, wybierz **polecenie Dzienniki** z menu **Usługi Azure Monitor.** Wybrany jest ostatnio używany obszar roboczy, ale można wybrać dowolny inny obszar roboczy. Zobacz [Zakres i zakres czasu kwerendy dziennika w usłudze Azure Monitor Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Dlaczego pojawia się błąd: "Zarejestruj dostawcę zasobów 'Microsoft.Insights' dla tej subskrypcji, aby włączyć tę kwerendę" podczas otwierania usługi Log Analytics z maszyny Wirtualnej? 
Wielu dostawców zasobów są automatycznie rejestrowane, ale może być konieczne ręczne zarejestrowanie niektórych dostawców zasobów. Zakres rejestracji jest zawsze subskrypcja. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Dlaczego podczas otwierania usługi Log Analytics z maszyny Wirtualnej jest wyświetlany komunikat o błędzie bez dostępu? 
Aby wyświetlić dzienniki maszyn wirtualnych, należy udzielić uprawnienia do odczytu do obszarów roboczych, które przechowuje dzienniki maszyn wirtualnych. W takich przypadkach administrator musi udzielić ci uprawnień na platformie Azure.

## <a name="alerts"></a>Alerty

### <a name="what-is-an-alert-in-azure-monitor"></a>Co to jest alert w usłudze Azure Monitor?
Alerty proaktywnie powiadamiają o znalezieniu ważnych warunków w danych monitorowania. Umożliwiają one identyfikowanie i rozwiązywanie problemów, zanim użytkownicy systemu zauważą je. Istnieje wiele rodzajów alertów:

- Metryka — wartość metryki przekracza próg.
- Kwerenda dziennika — wyniki kwerendy dziennika odpowiadają zdefiniowanym kryteriom.
- Dziennik aktywności — zdarzenie dziennika aktywności spełnia zdefiniowane kryteria.
- Test sieci Web — wyniki testu dostępności są zgodne ze zdefiniowanymi kryteriami.


Zobacz [Omówienie alertów na platformie Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Co to jest grupa działania?
Grupa akcji to zbiór powiadomień i akcji, które mogą być wyzwalane przez alert. Wiele alertów może używać jednej grupy akcji, co pozwala na wykorzystanie wspólnych zestawów powiadomień i akcji. Zobacz [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Co to jest reguła akcji?
Reguła akcji umożliwia modyfikowanie zachowania zestawu alertów spełniających określone kryteria. Dzięki temu można wykonać takie wymagania, jak wyłączyć akcje alertów podczas okna konserwacji. Grupę akcji można również zastosować do zestawu alertów, zamiast stosować je bezpośrednio do reguł alertów. Zobacz [Reguły akcji](platform/alerts-action-rules.md).

## <a name="agents"></a>Agenci

### <a name="does-azure-monitor-require-an-agent"></a>Czy usługa Azure Monitor wymaga agenta?
Agent jest wymagany tylko do zbierania danych z systemu operacyjnego i obciążeń na maszynach wirtualnych. Maszyny wirtualne mogą znajdować się na platformie Azure, innym środowisku chmury lub lokalnie. Zobacz [Omówienie agentów usługi Azure Monitor](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Jaka jest różnica między agentami usługi Azure Monitor?
Rozszerzenie diagnostyki platformy Azure jest przeznaczone dla maszyn wirtualnych platformy Azure i zbiera dane do metryk usługi Azure Monitor, usługi Azure Storage i usługi Azure Event Hubs. Agent usługi Log Analytics jest przeznaczony dla maszyn wirtualnych na platformie Azure, innym środowisku chmury lub lokalnie i zbiera dane do dzienników usługi Azure Monitor. Agent zależności wymaga agenta usługi Log Analytics i zebrane szczegóły procesu i zależności. Zobacz [Omówienie agentów usługi Azure Monitor](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Czy ruch agenta korzysta z połączenia Usługi ExpressRoute?
Ruch na platformie Azure Monitor używa obwodu usługi Azure Dla komunikacji równorzędnej firmy Microsoft. Opis różnych typów ruchu usługi ExpressRoute można znaleźć w [dokumentacji usługi ExpressRoute.](../expressroute/expressroute-faqs.md#supported-services) 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Jak mogę potwierdzić, że agent usługi Log Analytics jest w stanie komunikować się z usługą Azure Monitor?
W Panelu sterowania na komputerze agenta wybierz pozycję **Ustawienia & zabezpieczeń**, Microsoft Monitoring **Agent** . Na karcie **Usługi Azure Log Analytics (OMS)** ikona zielonego znacznika wyboru potwierdza, że agent może komunikować się z usługą Azure Monitor. Żółta ikona ostrzeżenia oznacza, że agent ma problemy. Jednym z typowych powodów jest usługa **Microsoft Monitoring Agent** została zatrzymana. Użyj menedżera sterowania usługą, aby ponownie uruchomić usługę.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Jak zatrzymać agenta usługi Log Analytics przed komunikowaniem się z usługą Azure Monitor?
W przypadku agentów połączonych bezpośrednio z usługą Log Analytics otwórz Panel sterowania i wybierz pozycję **Ustawienia & zabezpieczeń**, Microsoft Monitoring **Agent**. Na karcie **Usługi Azure Log Analytics (OMS)** usuń wszystkie obszary robocze na liście. W programie System Center Operations Manager usuń komputer z listy zarządzanych komputerów usługi Log Analytics. Program Operations Manager aktualizuje konfigurację agenta, aby nie zgłaszać już usługi Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Ile danych jest wysyłanych na agenta?
Ilość danych wysyłanych na agenta zależy od:

* Rozwiązania, które zostały włączone
* Liczba zbieranych dzienników i liczników wydajności
* Ilość danych w dziennikach

Aby uzyskać szczegółowe [informacje, zobacz Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor.](platform/manage-cost-storage.md)

W przypadku komputerów, na których można uruchomić agenta WireData, użyj następującej kwerendy, aby zobaczyć, ile danych jest wysyłanych:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Ile przepustowości sieci jest używane przez microsoft management agent (MMA) podczas wysyłania danych do usługi Azure Monitor?
Przepustowość jest funkcją na ilość wysyłanych danych. Dane są kompresowane, gdy są wysyłane przez sieć.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Jak mogę otrzymywać powiadomienia o zatrzymaniu zbierania danych z agenta usługi Log Analytics?

Użyj kroki opisane w [tworzenie nowego alertu dziennika,](platform/alerts-metric.md) aby otrzymywać powiadomienia po zatrzymaniu zbierania danych. Użyj następujących ustawień dla reguły alertu:

- **Zdefiniuj warunek alertu:** Określ obszar roboczy usługi Log Analytics jako miejsce docelowe zasobu.
- **Kryteria alertu** 
   - **Nazwa sygnału**: *Niestandardowe wyszukiwanie dziennika*
   - **Zapytanie wyszukiwania**:`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logika alertu:** **Na podstawie** *liczby wyników,* **Warunek** *większy niż*, Wartość **progowa** *0*
   - **Oceniono na podstawie**: **Okres (w minutach)** *30*, **Częstotliwość (w minutach)** *10*
- **Definiowanie szczegółów alertu** 
   - **Nazwa**: *Pobieranie danych zostało zatrzymane*
   - **Ważność**: *Ostrzeżenie*

Określ istniejącą lub nową [grupę akcji,](platform/action-groups.md) aby gdy alert dziennika spełniał kryteria, użytkownik jest powiadamiany, jeśli brak jest pulsu przez ponad 15 minut.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Jakie są wymagania zapory dla agentów usługi Azure Monitor?
Aby uzyskać szczegółowe informacje na temat wymagań zapory, zobacz [Wymagania zapory](platform/log-analytics-agent.md#firewall-requirements)sieciowej.


## <a name="visualizations"></a>Wizualizacje

### <a name="why-cant-i-see-view-designer"></a>Dlaczego nie widzę widoku projektanta?

Projektant widoku jest dostępny tylko dla użytkowników przypisanych z uprawnieniami współautora lub wyższymi w obszarze roboczym usługi Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemy z konfiguracją
*Mam problem z konfiguracją:*

* [Aplikacja platformy .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitorowanie już uruchomionej aplikacji](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostyka platformy Azure](platform/diagnostics-extension-to-application-insights.md)
* [Aplikacje internetowe w języku Java](app/java-troubleshoot.md)

*Nie otrzymuję żadnych danych z serwera*

* [Ustawianie wyjątków zapory](app/ip-addresses.md)
* [Konfigurowanie serwera ASP.NET](app/monitor-performance-live-website-now.md)
* [Konfigurowanie serwera Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Czy mogę korzystać ze usługi Application Insights z ...?

* [Aplikacje sieci Web na serwerze usług IIS w zestawie skalowania maszyny wirtualnej platformy Azure lub platformy Azure](app/azure-vm-vmss-apps.md)
* [Aplikacje sieci Web na serwerze usług IIS — lokalnie lub na maszynie Wirtualnej](app/asp-net.md)
* [Aplikacje internetowe w języku Java](app/java-get-started.md)
* [Aplikacje Node.js](app/nodejs.md)
* [Aplikacje sieci Web na platformie Azure](app/azure-web-apps.md)
* [Usługi w chmurze na platformie Azure](app/cloudservices.md)
* [Serwery aplikacji uruchomione w cker](app/docker.md)
* [Jednostronicowe aplikacje internetowe](app/javascript.md)
* [Program SharePoint](app/sharepoint.md)
* [Aplikacja klasyczna systemu Windows](app/windows-desktop.md)
* [Inne platformy](app/platforms.md)

### <a name="is-it-free"></a>Czy to za darmo?

Tak, do użytku eksperymentalnego. W podstawowym planie cenowym wniosek może bezpłatnie wysyłać określony limit danych każdego miesiąca. Bezpłatny dodatek jest wystarczająco duży, aby objąć rozwój i publikowanie aplikacji dla niewielkiej liczby użytkowników. Można ustawić limit, aby zapobiec przetwarzaniu więcej niż określonej ilości danych.

Większe ilości danych telemetrycznych są ładowane przez Gb. Podpowiadamy, jak [ograniczyć opłaty.](app/pricing.md)

Plan Enterprise pobiera opłatę za każdy dzień, w którym każdy węzeł serwera sieci web wysyła dane telemetryczne. Jest odpowiedni, jeśli chcesz używać ciągłego eksportu na dużą skalę.

[Przeczytaj plan cenowy](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Ile to kosztuje?

* Otwórz **stronę Użycie i szacowane koszty** w zasobie usługi Application Insights. Istnieje wykres ostatnich użycia. W razie potrzeby można ustawić limit woluminu danych.
* Otwórz [blok rozliczeń platformy Azure,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) aby wyświetlić rachunki we wszystkich zasobach.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Co usługa Application Insights modyfikuje w moim projekcie?
Szczegóły zależą od typu projektu. Dla aplikacji sieci web:

* Dodaje te pliki do projektu:
  * Plik ApplicationInsights.config
  * ai.js
* Instaluje te pakiety NuGet:
  * *Interfejs API usługi Application Insights* — podstawowy interfejs API
  * *Interfejs API usługi Application Insights dla aplikacji sieci Web* — służy do wysyłania danych telemetrycznych z serwera
  * *Interfejs API usługi Application Insights dla aplikacji JavaScript* — służy do wysyłania danych telemetrycznych z klienta
* Pakiety obejmują następujące zestawy:
  * Statystyki aplikacji firmy Microsoft.Application
  * Microsoft.ApplicationInsights.Platform
* Wstawia elementy do:
  * Web.config
  * packages.config
* (Tylko nowe projekty — jeśli [dodasz aplikację Application Insights do istniejącego projektu,][start]musisz to zrobić ręcznie). Wstawia fragmenty kodu klienta i serwera, aby zainicjować je przy identyfikatorze zasobu usługi Application Insights. Na przykład w aplikacji MVC kod jest wstawiany do\_strony wzorcowej Views/Shared/ Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Jak uaktualnić starsze wersje SDK?
Zobacz [informacje o wersji](app/release-notes.md) dla SDK odpowiednie dla danego typu aplikacji.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Jak mogę zmienić zasób platformy Azure, do którego mój projekt wysyła dane?
W Eksploratorze `ApplicationInsights.config` rozwiązań kliknij prawym przyciskiem myszy i wybierz polecenie **Aktualizuj wgląd w aplikacje**. Można wysłać dane do istniejącego lub nowego zasobu na platformie Azure. Kreator aktualizacji zmienia klucz instrumentacji w aplikacjiInsights.config, który określa, gdzie zestaw SDK serwera wysyła dane. Jeśli nie zaznaczysz "Aktualizuj wszystko", zmieni to również klucz, który pojawia się na stronach internetowych.

### <a name="what-is-status-monitor"></a>Co to jest monitor stanu?

Aplikacja klasyczna, której można używać na serwerze sieci Web usług IIS w celu skonfigurowania usługi Application Insights w aplikacjach sieci Web. Nie zbiera danych telemetrycznych: można ją zatrzymać, gdy nie konfigurujesz aplikacji. 

[Dowiedz się więcej](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Jakie dane telemetryczne są zbierane przez usługa Application Insights?

Z aplikacji sieci web serwera:

* Żądania HTTP
* [Zależności](app/asp-net-dependencies.md). Wywoławnie: bazy danych SQL; Wywołania HTTP do usług zewnętrznych; Usługa Azure Cosmos DB, tabela, magazyn obiektów blob i kolejka. 
* [Wyjątki](app/asp-net-exceptions.md) i ślady stosu.
* [Liczniki wydajności](app/performance-counters.md) — jeśli używasz [Monitora stanu,](app/monitor-performance-live-website-now.md) [monitorowania platformy Azure dla usług app services,](app/azure-web-apps.md) [monitorowania platformy Azure dla zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej](app/azure-vm-vmss-apps.md)lub [modułu zapisującego zbierane usługi Application Insights.](app/java-collectd.md)
* [Niestandardowe zdarzenia i metryki,](app/api-custom-events-metrics.md) które można kodować.
* [Dzienniki śledzenia,](app/asp-net-trace-logs.md) jeśli skonfigurujesz odpowiedni moduł zbierający.

Ze [stron internetowych klienta:](app/javascript.md)

* [Liczba wyświetleń strony](app/usage-overview.md)
* [Połączenia AJAX](app/asp-net-dependencies.md) Żądania wykonane z uruchomionego skryptu.
* Dane ładowania widoku strony
* Liczba użytkowników i sesji
* [Uwierzytelnione identyfikatory użytkowników](app/api-custom-events-metrics.md#authenticated-users)

Z innych źródeł, jeśli je skonfigurujesz:

* [Diagnostyka platformy Azure](platform/diagnostics-extension-to-application-insights.md)
* [Importowanie do analizy](platform/data-collector-api.md)
* [Analiza dzienników](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Czy mogę odfiltrować lub zmodyfikować niektóre dane telemetryczne?

Tak, na serwerze można napisać:

* Procesor telemetrii do filtrowania lub dodawania właściwości do wybranych elementów telemetrycznych przed ich wysłaniem z aplikacji.
* Inicjatora telemetrii, aby dodać właściwości do wszystkich elementów telemetrii.

Dowiedz się więcej o [ASP.NET](app/api-filtering-sampling.md) lub [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Jak obliczane są dane o mieście, kraju/regionie i innych lokalizacjach geograficznych?

Wyszukujemy adres IP (IPv4 lub IPv6) klienta internetowego za pomocą [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria przeglądarki: Zbieramy adres IP nadawcy.
* Telemetria serwera: Moduł usługi Application Insights zbiera adres IP klienta. Nie jest zbierany, jeśli `X-Forwarded-For` jest ustawiony.
* Aby dowiedzieć się więcej o sposobie gromadzenia adresów IP i danych geolokalizacyjnych w usłudze Application Insights, zapoznaj się z tym [artykułem](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


Można `ClientIpHeaderTelemetryInitializer` skonfigurować, aby wziąć adres IP z innego nagłówka. W niektórych systemach, na przykład, jest przenoszony przez `X-Originating-IP`serwer proxy, moduł równoważenia obciążenia lub cdn do . [Dowiedz się więcej](https://apmtips.com/blog/2016/07/05/client-ip-address/).

Za [pomocą usługi Power BI](app/export-power-bi.md ) można wyświetlać dane telemetryczne żądania na mapie.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Jak długo dane są przechowywane w portalu? Czy jest to bezpieczne?
Zapoznaj się z przechowywaniem [danych i prywatnością][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Co się stanie z telemetrią usługi Application Insight, gdy serwer lub urządzenie utraci połączenie z platformą Azure?

Wszystkie nasze zestawy SDK, w tym zestaw SDK sieci web, zawierają "niezawodny transport" lub "niezawodny transport". Gdy serwer lub urządzenie utraci połączenie z platformą Azure, dane telemetryczne są [przechowywane lokalnie w systemie plików](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (SDK serwera) lub w html5 Session Storage (Web SDK). SDK będzie okresowo ponawiać próbę wysłania tej danych telemetrycznych, dopóki nasza usługa pozyskiwania uzna ją za "przestarzałą" (48 godzin dla dzienników, 30 minut dla metryk). Przestarzała telemetria zostanie porzucona. W niektórych przypadkach, takich jak gdy magazyn lokalny jest pełny, ponowić próbę nie nastąpi.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Czy dane osobowe mogą być wysyłane w telemetrii?

Jest to możliwe, jeśli kod wysyła takie dane. Może się również zdarzyć, jeśli zmienne w ślady stosu obejmują dane osobowe. Zespół programistów powinien przeprowadzać oceny ryzyka, aby upewnić się, że dane osobowe są właściwie przetwarzane. [Dowiedz się więcej o zatrzymywaniu i prywatności danych](app/data-retention-privacy.md).

**Wszystkie** oktety adresu internetowego klienta są zawsze ustawiane na 0 po wyszukaniu atrybutów lokalizacji geograficznej.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mój klucz instrumentacji jest widoczny w źródle mojej strony internetowej. 

* Jest to powszechna praktyka w zakresie monitorowania rozwiązań.
* Nie można go wykorzystać do kradzieży danych.
* Może służyć do wypaczenia danych lub wyzwalania alertów.
* Nie słyszeliśmy, że którykolwiek klient miał takie problemy.

Można wykonać następujące czynności:

* Użyj dwóch oddzielnych kluczy instrumentacji (oddzielnych zasobów usługi Application Insights) dla danych klienta i serwera. Lub
* Napisz serwer proxy, który działa na serwerze i mieć klienta sieci web wysyłać dane za pośrednictwem tego serwera proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Jak wyświetlić dane POST w wyszukiwaniu diagnostycznym?
Nie rejestrujemy danych POST automatycznie, ale można użyć tracktrace wywołania: umieścić dane w parametrze message. Ma dłuższy limit rozmiaru niż limity właściwości ciągu, chociaż nie można filtrować na nim.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Czy należy używać pojedynczych lub wielu zasobów usługi Application Insights?

Użyj jednego zasobu dla wszystkich składników lub ról w jednym systemie biznesowym. Użyj oddzielnych zasobów do tworzenia, testowania i wydawania wersji wersji oraz dla niezależnych aplikacji.

* [Zobacz dyskusję tutaj](app/separate-resources.md)
* [Przykład — usługa w chmurze z rolami roboczymi i sieci web](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Jak dynamicznie zmieniać klucz oprzyrządowania?

* [Dyskusja tutaj](app/separate-resources.md)
* [Przykład — usługa w chmurze z rolami roboczymi i sieci web](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Jakie są liczby użytkowników i sesji?

* Zestaw JavaScript SDK ustawia plik cookie użytkownika na kliencie sieci web, aby zidentyfikować powracających użytkowników i plik cookie sesji do grupowania działań.
* Jeśli nie ma skryptu po stronie klienta, można [ustawić pliki cookie na serwerze](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Jeśli jeden prawdziwy użytkownik korzysta z witryny w różnych przeglądarkach lub za pomocą przeglądania prywatnego /incognito lub różnych maszyn, będą one liczone więcej niż raz.
* Aby zidentyfikować zalogowanego użytkownika na komputerach i w przeglądarkach, dodaj wywołanie [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Czy wszystko zostało włączone w usłudze Application Insights?
| Co powinno zostać wyświetlone | Jak go zdobyć | Dlaczego chcesz to |
| --- | --- | --- |
| Wykresy dostępności |[Testy internetowe](app/monitor-web-app-availability.md) |Dowiedz się, że Twoja aplikacja internetowa jest w górę |
| Serwer aplikacji perf: czasy odpowiedzi, ... |[Dodawanie usługi Application Insights do projektu](app/asp-net.md) lub [instalowanie Monitora stanu AI na serwerze](app/monitor-performance-live-website-now.md) (lub pisanie własnego kodu w celu [śledzenia zależności)](app/api-custom-events-metrics.md#trackdependency) |Wykrywanie problemów z perf |
| Telemetria zależności |[Instalowanie Monitora stanu AI na serwerze](app/monitor-performance-live-website-now.md) |Diagnozowanie problemów z bazami danych lub innymi składnikami zewnętrznymi |
| Pobierz ślady stosu z wyjątków |[Wstaw wywołania TrackException w kodzie](app/asp-net-exceptions.md) (ale niektóre z nich są zgłaszane automatycznie) |Wykrywanie i diagnozowanie wyjątków |
| Szukaj śladów dziennika |[Dodawanie karty rejestrowania](app/asp-net-trace-logs.md) |Diagnozowanie wyjątków, problemów z perf |
| Podstawy użycia klienta: wyświetlenia strony, sesje, ... |[Inicjalizator JavaScript na stronach internetowych](app/javascript.md) |Analiza użycia |
| Niestandardowe dane klienta |[Śledzenie połączeń na stronach internetowych](app/api-custom-events-metrics.md) |Zwiększ komfort użytkowania |
| Niestandardowe metryki serwera |[Śledzenie połączeń na serwerze](app/api-custom-events-metrics.md) |Analiza biznesowa |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Dlaczego liczby na wykresach wyszukiwania i metryki są nierówne?

[Próbkowanie](app/sampling.md) zmniejsza liczbę elementów telemetrycznych (żądania, zdarzenia niestandardowe i tak dalej), które są faktycznie wysyłane z aplikacji do portalu. W polu Wyszukaj zobaczysz liczbę faktycznie odebranych elementów. Na wykresach metryk, które wyświetlają liczbę zdarzeń, zobaczysz liczbę oryginalnych zdarzeń, które wystąpiły. 

Każdy element, który jest `itemCount` przesyłany zawiera właściwość, która pokazuje, ile oryginalnych zdarzeń, które reprezentuje element. Aby obserwować próbkowanie w działaniu, można uruchomić tę kwerendę w Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automatyzacja

#### <a name="configuring-application-insights"></a>Konfigurowanie statystyk aplikacji

Skrypty [programu PowerShell](app/powershell.md) można zapisać przy użyciu usługi Azure Resource Monitor w celu:

* Tworzenie i aktualizowanie zasobów usługi Application Insights.
* Ustaw plan cenowy.
* Pobierz klucz oprzyrządowania.
* Dodaj alert metryki.
* Dodaj test dostępności.

Nie można skonfigurować raportu Eksploratora metryk ani konfigurowania ciągłego eksportowania.

#### <a name="querying-the-telemetry"></a>Wykonywanie zapytań o dane telemetryczne

Użyj [interfejsu API REST](https://dev.applicationinsights.io/) do uruchamiania zapytań [Analytics.](app/analytics.md)

### <a name="how-can-i-set-an-alert-on-an-event"></a>Jak ustawić alert o zdarzeniu?

Alerty platformy Azure są tylko na metryki. Utwórz metrykę niestandardową, która przekracza próg wartości w każdym przypadku wystąpienia zdarzenia. Następnie ustaw alert na metryki. Otrzymasz powiadomienie za każdym razem, gdy metryka przekroczy próg w dowolnym kierunku; nie otrzymasz powiadomienia do pierwszego przejazdu, bez względu na to, czy wartość początkowa jest wysoka, czy niska; zawsze jest opóźnienie kilku minut.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Czy istnieją opłaty za transfer danych między aplikacją sieci Web platformy Azure a usługą Application Insights?

* Jeśli aplikacja sieci Web platformy Azure jest hostowana w centrum danych, w którym znajduje się punkt końcowy kolekcji usługi Application Insights, nie ma żadnych opłat. 
* Jeśli w centrum danych hosta nie ma punktu końcowego zbierania danych, dane telemetryczne aplikacji poniosą [opłaty wychodzące platformy Azure.](https://azure.microsoft.com/pricing/details/bandwidth/)

Nie zależy to od miejsca, w którym znajduje się zasób usługi Application Insights. To zależy tylko od rozkładu naszych punktów końcowych.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Czy mogę wysłać dane telemetryczne do portalu usługi Application Insights?

Zalecamy korzystanie z naszych sdk i korzystać z [interfejsu API SDK.](app/api-custom-events-metrics.md) Istnieją warianty SDK dla różnych [platform.](app/platforms.md) Te zestawy SDK obsługują buforowanie, kompresję, ograniczanie przepustowości, ponownych prób i tak dalej. Jednak [schemat pozyskiwania](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) i protokół [punktu końcowego](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) są publiczne.

### <a name="can-i-monitor-an-intranet-web-server"></a>Czy mogę monitorować intranetowy serwer www?

Tak, ale musisz zezwolić na ruch do naszych usług przez wyjątki zapory lub przekierowania proxy.
- QuickPulse (właso)`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider`https://dc.services.visualstudio.com:443` 
- TelemetryChannel`https://dc.services.visualstudio.com:443` 


Przejrzyj naszą pełną listę usług i adresów IP [tutaj](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Wyjątek zapory

Zezwalaj serwerowi sieci web na wysyłanie danych telemetrycznych do naszych punktów końcowych. 

#### <a name="gateway-redirect"></a>Przekierowanie bramy

Rozsyłanie ruchu z serwera do bramy w intranecie przez zastąpienie punktów końcowych w konfiguracji. Jeśli te właściwości "Punktu końcowego" nie są obecne w konfiguracji, te klasy użyją wartości domyślnych pokazanych poniżej w przykładzie ApplicationInsights.config. 

Brama powinna kierować ruch do adresu podstawowego naszego punktu końcowego. W konfiguracji zastąp `http://<your.gateway.address>/<relative path>`wartości domyślne programem .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Przykład applicationinsights.config z domyślnymi punktami końcowymi:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider jest dostępny od wersji 2.6.0.



#### <a name="proxy-passthrough"></a>Przekazywanie serwera proxy

Przekazywanie serwera proxy można osiągnąć, konfigurując serwer proxy na poziomie komputera lub aplikacji.
Aby uzyskać więcej informacji, zobacz artykuł dotnet na [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Przykład web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Czy mogę uruchomić testy sieci Web dostępności na serwerze intranetowym?

Nasze [testy internetowe](app/monitor-web-app-availability.md) są przeprowadzane w punktach obecności rozmieszczonych na całym świecie. Istnieją dwa rozwiązania:

* Drzwi zapory — zezwalaj na żądania do serwera z [długiej i zmiennej listy agentów testów internetowych](app/ip-addresses.md).
* Napisz własny kod, aby wysyłać okresowe żądania do serwera z wewnątrz intranetu. W tym celu można uruchomić testy sieci Web programu Visual Studio. Tester może wysyłać wyniki do usługi Application Insights przy użyciu interfejsu API TrackAvailability().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Jak długo trwa gromadzenie danych telemetrycznych?

Większość danych usługi Application Insights ma opóźnienie poniżej 5 minut. Niektóre dane mogą trwać dłużej; zazwyczaj większe pliki dziennika. Aby uzyskać więcej informacji, zobacz [umowy SLA usługi Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów

To często zadawane pytania firmy Microsoft to lista często zadawanych pytań dotyczących usługi Azure Monitor dla kontenerów. Jeśli masz jakieś dodatkowe pytania dotyczące rozwiązania, wejdź na [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Co reprezentują *inne procesy* w widoku węzeł?

**Inne procesy** mają na celu pomóc w jasnym zrozumieniu głównej przyczyny wysokiego użycia zasobów w węźle. Dzięki temu można odróżnić użycie między procesami konteneryzowanymi a procesami niekontralizowanymi.

Jakie są te **inne procesy?** 

Są to niekontralizowane procesy, które są uruchamiane w węźle.  

Jak to obliczyć?

**Inne procesy** = *Całkowite użycie z CAdvisor* - Użycie z*konteneryzowany proces*

**Inne procesy** obejmują:

- Samodzielnie zarządzane lub zarządzane procesy kubernetes nie konteneryzowane 

- Procesy wykonywania kontenera  

- Kubelet  

- Procesy systemowe uruchomione w węźle 

- Inne obciążenia inne niż Kubernetes uruchomione na sprzęcie węzła lub maszynie wirtualnej 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nie widzę wartości właściwości Image i Name wypełnione podczas kwerendy ContainerLog tabeli.

W przypadku wersji agenta ciprod12042019 i nowszych te dwie właściwości nie są wypełniane dla każdego wiersza dziennika, aby zminimalizować koszty poniesione na zebranych danych dziennika. Istnieją dwie opcje, aby zbadać tabelę, które zawierają te właściwości z ich wartościami:

#### <a name="option-1"></a>Opcja 1 

Dołącz do innych tabel, aby uwzględnić te wartości właściwości w wynikach.

Zmodyfikuj zapytania, aby uwzględnić ```ContainerInventory``` właściwości Image i ImageTag z tabeli, dołączając do właściwości ContainerID. Właściwość Name (jak wcześniej pojawiła się ```ContainerLog``` w tabeli) z pola ContaineName tabeli KubepodInventory, dołączając do właściwości ContainerID. Jest to zalecana opcja.

Poniższy przykład przedstawia przykładową kwerendę szczegółową, która wyjaśnia, jak uzyskać te wartości pól za pomocą sprzężeń.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opcja 2

Ponownie włączyć kolekcję dla tych właściwości dla każdego wiersza dziennika kontenera.

Jeśli pierwsza opcja nie jest wygodna ze względu na zmiany zapytania, można ponownie ```log_collection_settings.enrich_container_logs``` włączyć zbieranie tych pól, włączając ustawienie na mapie konfiguracji agenta, zgodnie z opisem w [ustawieniach konfiguracji zbierania danych](insights/container-insights-agent-config.md).

> [!NOTE]
> Druga opcja nie jest zalecane w dużych klastrów, które mają więcej niż 50 węzłów, ponieważ generuje wywołania serwera interfejsu API z każdego węzła w klastrze do wykonania tego wzbogacenia. Ta opcja zwiększa również rozmiar danych dla każdego zebranego wiersza dziennika.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Czy mogę wyświetlać dane zebrane w grafanie?

Usługa Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym usługi Log Analytics na pulpitach nawigacyjnych usługi Grafana. Udostępniliśmy szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego grafany,](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) aby rozpocząć pracę, i odwołać się do informacji, jak wysyłać zapytania o dodatkowe dane z monitorowanych klastrów w celu wizualizacji w niestandardowych pulpitach nawigacyjnych Grafana. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Czy mogę monitorować klaster z aparatem AKS za pomocą usługi Azure Monitor dla kontenerów?

Usługa Azure Monitor dla kontenerów obsługuje monitorowanie obciążeń kontenerów wdrożonych w klastrze z aparatem AKS (wcześniej znanym jako aparat ACS) hostowanym na platformie Azure. Aby uzyskać więcej informacji i omówienie kroków wymaganych do włączenia monitorowania w tym scenariuszu, zobacz [Korzystanie z usługi Azure Monitor dla kontenerów dla aparatu AKS.](https://github.com/microsoft/OMS-docker/tree/aks-engine)

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Dlaczego nie widzę danych w moim obszarze roboczym usługi Log Analytics?

Jeśli codziennie nie widzisz żadnych danych w obszarze roboczym usługi Log Analytics w określonym czasie, być może osiągnąłeś domyślny limit 500 MB lub dzienny limit określony w celu kontrolowania ilości danych do zbierania dziennie. Po dotrzymaniu limitu dla dnia zbieranie danych zatrzymuje się i wznawia tylko następnego dnia. Aby przejrzeć użycie danych i zaktualizować ją do innej warstwy cenowej na podstawie przewidywanych wzorców użycia, zobacz [Rejestrowanie użycia danych i kosztów](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jakie są stany kontenera określone w ContainerInventory tabeli?

Tabela ContainerInventory zawiera informacje o kontenerach zatrzymanych i uruchomionych. Tabela jest wypełniana przez przepływ pracy wewnątrz agenta, który wysyła zapytanie doklin dla wszystkich kontenerów (uruchomione i zatrzymane) i przekazuje te dane do obszaru roboczego usługi Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak rozwiązać błąd *braku subskrypcji?*

Jeśli zostanie wyświetlony błąd **Brak rejestracji subskrypcji dla microsoft.OperationsManagement**, można go rozwiązać, rejestrując dostawcę zasobów **Microsoft.OperationsManagement** w subskrypcji, w której zdefiniowano obszar roboczy. Dokumentację, jak to zrobić, można znaleźć [tutaj](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Czy istnieje obsługa klastrów AKS z włączoną funkcją RBAC?

Rozwiązanie do monitorowania kontenerów nie obsługuje narzędzia RBAC, ale jest obsługiwane za pomocą usługi Azure Monitor for Containers. Strona szczegółów rozwiązania może nie wyświetlać odpowiednich informacji w blokach, które pokazują dane dla tych klastrów.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak włączyć zbieranie dzienników dla kontenerów w obszarze nazw systemu kube za pośrednictwem helma?

Kolekcja dziennika z kontenerów w obszarze nazw systemu kube jest domyślnie wyłączona. Zbieranie dzienników można włączyć, ustawiając zmienną środowiskową na omsagent. Aby uzyskać więcej informacji, zobacz [stronę Usługi Azure Monitor dla kontenerów](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) Usługi GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak zaktualizować omsagent do najnowszej wersji?

Aby dowiedzieć się, jak uaktualnić agenta, zobacz [Zarządzanie agentami](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Jak włączyć rejestrowanie wielowierszowe?

Obecnie usługa Azure Monitor dla kontenerów nie obsługuje rejestrowania wielowierszowego, ale dostępne są obejścia. Można skonfigurować wszystkie usługi do pisania w formacie JSON, a następnie Docker/Moby zapisze je jako pojedynczy wiersz.

Na przykład można zawinąć dziennik jako obiekt JSON, jak pokazano w poniższym przykładzie dla przykładowej aplikacji node.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Te dane będą wyglądać następująco w usłudze Azure Monitor dla dzienników podczas kwerendy dla niego:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Aby uzyskać szczegółowe informacje na temat problemu, zapoznaj się z poniższym [linkiem GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak rozwiązać błędy usługi Azure AD po włączeniu dzienników na żywo? 

Może zostać wyświetlony następujący błąd: **Adres URL odpowiedzi określony w żądaniu nie jest zgodny z adresami\>URL odpowiedzi skonfigurowanym dla aplikacji: '<application ID '**. Rozwiązanie do jego rozwiązania można znaleźć w artykule [Jak wyświetlać dane kontenerów w czasie rzeczywistym za pomocą usługi Azure Monitor dla kontenerów.](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Dlaczego nie mogę uaktualnić klastra po wydzieleniu?

Jeśli po włączeniu usługi Azure Monitor dla kontenerów dla klastra AKS, usunąć obszar roboczy usługi Log Analytics, do którego klaster wysyłał swoje dane, podczas próby uaktualnienia klastra zakończy się niepowodzeniem. Aby obejść ten problem, należy wyłączyć monitorowanie, a następnie ponownie włączyć go odwołując się do innego prawidłowego obszaru roboczego w ramach subskrypcji. Podczas próby ponownego wykonania uaktualnienia klastra, należy przetworzyć i zakończyć pomyślnie.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Które porty i domeny muszę otworzyć/otworzyć na białej liście agenta?

Zobacz [wymagania zapory sieciowej](insights/container-insights-onboard.md#network-firewall-requirements) dla informacji o konfiguracji serwera proxy i zapory wymaganych dla konteneryzowanego agenta z chmurami Azure, Azure US GOVERNMENT i Azure China 21Vianet.

## <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
To często zadawane pytania firmy Microsoft to lista często zadawanych pytań dotyczących usługi Azure Monitor dla maszyn wirtualnych. Jeśli masz jakieś dodatkowe pytania dotyczące rozwiązania, wejdź na [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i opublikuj swoje pytania. Gdy pytanie jest często zadawane, dodajemy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Czy mogę być na pokładzie istniejącego obszaru roboczego?
Jeśli maszyny wirtualne są już połączone z obszarem roboczym usługi Log Analytics, możesz nadal używać tego obszaru roboczego podczas dołączania do usługi Azure Monitor dla maszyn wirtualnych, pod warunkiem, że znajduje się on w jednym z obsługiwanych regionów wymienionych [w tym miejscu.](insights/vminsights-enable-overview.md#prerequisites)


### <a name="can-i-onboard-to-a-new-workspace"></a>Czy mogę ować się na pokładzie do nowego obszaru roboczego? 
Jeśli maszyny wirtualne nie są obecnie połączone z istniejącym obszarem roboczym usługi Log Analytics, należy utworzyć nowy obszar roboczy do przechowywania danych. Tworzenie nowego domyślnego obszaru roboczego odbywa się automatycznie, jeśli skonfigurujesz jedną maszynę wirtualną platformy Azure dla usługi Azure Monitor dla maszyn wirtualnych za pośrednictwem witryny Azure portal.

Jeśli zdecydujesz się użyć metody opartej na skryptach, te kroki są omówione w [włącz usługę Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure PowerShell lub Resource Manager szablonu](insights/vminsights-enable-at-scale-powershell.md) artykułu. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Co zrobić, jeśli moja maszyna wirtualna jest już raportowania do istniejącego obszaru roboczego?
Jeśli zbierasz już dane z maszyn wirtualnych, być może zostały już skonfigurowane do raportowania danych do istniejącego obszaru roboczego usługi Log Analytics.  Tak długo, jak ten obszar roboczy znajduje się w jednym z naszych obsługiwanych regionów, można włączyć usługę Azure Monitor dla maszyn wirtualnych do tego istniejącego obszaru roboczego.  Jeśli obszar roboczy, którego już używasz, nie znajduje się w jednym z naszych obsługiwanych regionów, nie będzie można w tej chwili korzystać z usługi Azure Monitor dla maszyn wirtualnych.  Aktywnie pracujemy nad wspieraniem kolejnych regionów.


### <a name="why-did-my-vm-fail-to-onboard"></a>Dlaczego moja maszyna wirtualna nie została wbudowana?
Podczas dołączania maszyny Wirtualnej platformy Azure z witryny Azure portal, występują następujące kroki:

* Jeśli ta opcja została zaznaczona, zostanie utworzony domyślny obszar roboczy usługi Log Analytics.
* Agent usługi Log Analytics jest zainstalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej, jeśli zostanie to określone, że jest wymagane.  
* Agent zależności mapy map usługi Azure Monitor dla maszyn wirtualnych jest instalowany na maszynach wirtualnych platformy Azure przy użyciu rozszerzenia, jeśli zostanie to określone. 

Podczas procesu pokładowego sprawdzamy stan na każdym z powyższych, aby zwrócić stan powiadomienia w portalu. Konfiguracja obszaru roboczego i instalacji agenta zwykle trwa od 5 do 10 minut. Wyświetlanie danych monitorowania w portalu trwa dodatkowe 5 do 10 minut.  

Jeśli zainicjowano dołączanie i zobacz komunikaty wskazujące, że maszyna wirtualna musi być wbudowana, poczekaj do 30 minut, aby maszyna wirtualna mogła ukończyć proces. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nie widzę niektórych ani żadnych danych na wykresach wydajności mojej maszyny Wirtualnej
Nasze wykresy wydajności zostały zaktualizowane w celu użycia danych przechowywanych w tabeli *InsightsMetrics.*  Aby wyświetlić dane na tych wykresach, należy uaktualnić, aby korzystać z nowego rozwiązania usługi VM Insights.  Więcej informacji można znaleźć w naszym [FAQ ga.](insights/vminsights-ga-release-faq.md)

Jeśli nie widzisz danych o wydajności w tabeli dysków lub na niektórych wykresach wydajności, liczniki wydajności mogą nie być skonfigurowane w obszarze roboczym. Aby rozwiązać problem, uruchom następujący [skrypt programu PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Czym różni się funkcja mapy usługi Azure Monitor dla maszyn wirtualnych od mapy usługi?
Funkcja mapa usługi Azure Monitor dla maszyn wirtualnych jest oparta na mapie usługi, ale ma następujące różnice:

* Widok mapy można uzyskać z bloku maszyny Wirtualnej i z usługi Azure Monitor dla maszyn wirtualnych w usłudze Azure Monitor.
* Połączenia na mapie można teraz kliknąć i wyświetlić widok danych metryki połączenia w panelu bocznym dla wybranego połączenia.
* Istnieje nowy interfejs API, który jest używany do tworzenia map, aby lepiej obsługiwać bardziej złożone mapy.
* Monitorowane maszyny wirtualne są teraz uwzględnione w węźle grupy klienta, a wykres pierścieniowy pokazuje proporcję monitorowanych i niemonitorowanych maszyn wirtualnych w grupie.  Może również służyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Monitorowane maszyny wirtualne są teraz uwzględniane w węzłach grupy portów serwera, a wykres pierścieniowy pokazuje proporcję monitorowanych i niemonitorowanych maszyn w grupie.  Może również służyć do filtrowania listy maszyn, gdy grupa jest rozwinięta.
* Styl mapy został zaktualizowany, aby był bardziej spójny z mapą aplikacji w szczegółowych informacjach o aplikacji.
* Panele boczne zostały zaktualizowane i nie mają pełnego zestawu integracji, które były obsługiwane w mapie usług — zarządzanie aktualizacjami, śledzenie zmian, zabezpieczenia i service desk. 
* Opcja wybierania grup i maszyn do mapowania została zaktualizowana i obsługuje teraz subskrypcje, grupy zasobów, zestawy skalowania maszyn wirtualnych platformy Azure i usługi w chmurze.
* Nie można utworzyć nowych grup komputerów mapy usług w usłudze Azure Monitor dla maszyn wirtualnych.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Dlaczego na wykresach skuteczności są wyświetlane linie przerywane?
Może to nastąpić z kilku powodów.  W przypadkach, gdy istnieje luka w zbieraniu danych, przedstawiamy linie jako kropkowane.  Jeśli zmodyfikowano częstotliwość próbkowania danych dla włączonych liczników wydajności (domyślnym ustawieniem jest zbieranie danych co 60 sekund), na wykresie można zobaczyć kropkowane linie, jeśli wybierzesz wąski zakres czasu dla wykresu, a częstotliwość próbkowania jest mniejsza niż rozmiar zasobnika używany na wykresie (na przykład częstotliwość próbkowania wynosi co 10 minut, a każde zasobnik na wykresie wynosi 5 minut).  Wybranie szerszego zakresu czasu do wyświetlenia powinno spowodować, że linie wykresu będą wyświetlane jako linie ciągłe, a nie kropki w tym przypadku.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Czy grupy są obsługiwane za pomocą usługi Azure Monitor dla maszyn wirtualnych?
Tak, po zainstalowaniu agenta zależności zbieramy informacje z maszyn wirtualnych, aby wyświetlać grupy na podstawie subskrypcji, grupy zasobów, zestawów skalowania maszyny wirtualnej i usług w chmurze.  Jeśli używasz mapy usług i utworzono grupy maszyn, są one również wyświetlane.  Grupy komputerów pojawią się również w filtrze grup, jeśli utworzono je dla przeglądanych obszarów roboczych. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Jak wyświetlić szczegółowe informacje o tym, co napędza linię 95 percentyla na wykresach wydajności zagregowanej?
Domyślnie lista jest sortowana w celu wyświetlenia maszyn wirtualnych, które mają najwyższą wartość dla 95 percentyla dla wybranej metryki, z wyjątkiem wykresu Dostępnej pamięci, który pokazuje maszyny o najniższej wartości 5 percentyla.  Kliknięcie wykresu spowoduje otwarcie widoku **Lista N z** wybraną odpowiednią metryką.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>W jaki sposób funkcja Mapa obsługuje duplikaty usług IP w różnych sieciach wirtualnych i podsieciach?
Jeśli duplikujesz zakresy adresów IP za pomocą zestawów skalowania maszyn wirtualnych platformy Vm lub platformy Azure w podsieciach i sieciach wirtualnych, może to spowodować, że usługa Azure Monitor for VMs Map wyświetli nieprawidłowe informacje. Jest to znany problem i badamy możliwości poprawy tego doświadczenia.

### <a name="does-map-feature-support-ipv6"></a>Czy funkcja Mapa obsługuje IPv6?
Funkcja mapy obsługuje obecnie tylko protokół IPv4 i badamy obsługę IPv6. Obsługujemy również IPv4, który jest tunelowany wewnątrz IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Podczas ładowania mapy dla grupy zasobów lub innej dużej grupy mapa jest trudna do wyświetlenia
Chociaż poczyniliśmy ulepszenia mapy do obsługi dużych i złożonych konfiguracji, zdajemy sobie sprawę, że mapa może mieć wiele węzłów, połączeń i węzłów pracujących jako klaster.  Jesteśmy zobowiązani do dalszego zwiększania wsparcia w celu zwiększenia skalowalności.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Dlaczego wykres sieciowy na karcie Wydajność wygląda inaczej niż wykres sieciowy na stronie Omówienie maszyny Wirtualnej platformy Azure?

Na stronie przeglądu maszyny Wirtualnej platformy Azure są wyświetlane wykresy oparte na pomiarze aktywności hosta na maszynie wirtualnej gościa.  Dla wykresu sieciowego w ariacie maszyny Wirtualnej platformy Azure omówienie, wyświetla tylko ruch sieciowy, który będzie rozliczany.  Nie obejmuje to ruchu sieci między wirtualnym.  Dane i wykresy wyświetlane dla usługi Azure Monitor dla maszyn wirtualnych są oparte na danych z maszyny wirtualnej gościa, a na wykresie sieciowym są wyświetlane cały ruch TCP/IP, który jest przychodzący i wychodzący do tej maszyny wirtualnej, w tym sieć międzywirtualna.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Jak jest mierzony czas reakcji dla danych przechowywanych w VMConnection i wyświetlanych w panelu połączenia i skoroszytach?

Czas reakcji jest przybliżeniem. Ponieważ nie instrumentujemy kodu aplikacji, tak naprawdę nie wiemy, kiedy rozpoczyna się żądanie i kiedy nadejdzie odpowiedź. Zamiast tego obserwujemy dane wysyłane na połączenie, a następnie dane powracają na tym połączeniu. Nasz agent śledzi te wiadomości i odbiera i próbuje je sparować: sekwencja wysyła, po której następuje sekwencja odbiera jest interpretowana jako para żądania/odpowiedzi. Czas między tymi operacjami jest czas odpowiedzi. Będzie ona obejmować opóźnienie sieci i czas przetwarzania serwera.

To przybliżenie działa dobrze dla protokołów, które są oparte na żądaniu/odpowiedzi: jedno żądanie wychodzi na połączenie i pojedyncza odpowiedź nadejdzie. Dotyczy to protokołu HTTP(S) (bez potoków), ale nie jest spełniony dla innych protokołów.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Czy ich ograniczenia, jeśli jestem na Log Analytics Bezpłatny plan cenowy?
Jeśli skonfigurowano usługę Azure Monitor z obszarem roboczym usługi Log Analytics przy użyciu *bezpłatnej* warstwy cenowej, funkcja Map usługi Azure Monitor dla maszyn wirtualnych będzie obsługiwać tylko pięć połączonych komputerów podłączonych do obszaru roboczego. Jeśli masz pięć maszyn wirtualnych podłączonych do wolnego obszaru roboczego, należy odłączyć jedną z maszyn wirtualnych, a następnie połączyć nową maszynę wirtualną, nowa maszyna wirtualna nie jest monitorowana i odzwierciedlana na stronie Mapa.  

W tym warunku zostanie wyświetlony monit z opcją **Wypróbuj teraz** po otwarciu maszyny Wirtualnej i **wybraniu aplikacji Insights** z lewego okienka, nawet po zainstalowaniu go już na maszynie wirtualnej.  Jednak nie są monitowane z opcjami, jak zwykle występuje, jeśli ta maszyna wirtualna nie były dołączane do usługi Azure Monitor dla maszyn wirtualnych. 


## <a name="next-steps"></a>Następne kroki
Jeśli na twoje pytanie nie ma odpowiedzi, możesz zapoznać się z poniższymi forami, aby uzyskać dodatkowe pytania i odpowiedzi.

- [Analiza dzienników](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Aby uzyskać ogólne opinie na temat usługi Azure Monitor, odwiedź [forum opinii](https://feedback.azure.com/forums/34192--general-feedback).
