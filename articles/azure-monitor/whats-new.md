---
title: Co nowego w dokumentacji usługi Azure Monitor
description: Znaczące aktualizacje dokumentacji usługi Azure Monitor aktualizowane co miesiąc.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: c29790035ec4e971957784e826a1e8bd8e0c9329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79500497"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co nowego w dokumentacji usługi Azure Monitor?
Ten artykuł zawiera listę artykułów usługi Azure Monitor, które są nowe lub zostały znacznie zaktualizowane. Zostanie odświeżony w pierwszym tygodniu każdego miesiąca, aby uwzględnić aktualizacje artykułów z poprzedniego miesiąca.

## <a name="february-2020"></a>Luty 2020 r.

### <a name="agents"></a>Agenci
Wiele aktualizacji w ramach przepisywania zawartości rozszerzenia diagnostyki.

- [Omówienie agentów monitorowania platformy Azure](platform/agents-overview.md) — zrestrukturyzowane tabele, aby lepiej wyjaśnić unikatowe funkcje każdego agenta.
- [Omówienie rozszerzenia diagnostyki platformy Azure](platform/diagnostics-extension-overview.md) — pełne przepisywanie.
- [Użyj magazynu obiektów blob dla usług IIS i magazynu tabel dla zdarzeń w usłudze Azure Monitor](platform/diagnostics-extension-logs.md) — ogólne przepisywanie aktualizacji i przejrzystości.
- [Zainstaluj i skonfiguruj rozszerzenie diagnostyki systemu Windows Azure (WAD)](platform/diagnostics-extension-windows-install.md) — nowy artykuł. 
- [Schemat rozszerzenia diagnostyki systemu Windows](platform/diagnostics-extension-schema-windows.md) — reorganizowany.
- [Wysyłaj dane z rozszerzenia diagnostyki systemu Windows Azure do usługi Azure Event Hubs](platform/diagnostics-extension-stream-event-hubs.md) — całkowicie przepisane i zaktualizowane.
- [Przechowywanie i wyświetlanie danych diagnostycznych w usłudze Azure Storage](/azure/cloud-services/diagnostics-extension-to-storage) — całkowicie przepisane i zaktualizowane.
- [Rozszerzenie maszyny wirtualnej usługi Log Analytics dla systemu Windows](../virtual-machines/extensions/oms-windows.md) — lepiej wyjaśnia relacje z agentem usługi Log Analytics.
- [Rozszerzenie maszyny wirtualnej usługi Azure Monitor dla systemu Linux](../virtual-machines/extensions/oms-linux.md) — lepiej wyjaśnia relacje z agentem usługi Log Analytics.




### <a name="application-insights"></a>Application Insights
- [Parametry połączenia w usłudze Azure Application Insights](app/sdk-connection-string.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Spostrzeżenia i rozwiązania

#### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
- [Zintegruj usługę Azure Active Directory z usługą Azure Kubernetes Service](../aks/azure-ad-integration.md) — dodano notatkę do tworzenia aplikacji klienckiej do obsługi klastra obsługującego usługę RBAC do obsługi usługi Azure Monitor dla kontenerów.

#### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
- [Usługa Azure Monitor dla maszyn wirtualnych (GA) często zadawane pytania](insights/vminsights-ga-release-faq.md) — zmień sposób przechowywania danych wydajności.

#### <a name="office-365"></a>Office 365
- [Rozwiązanie do zarządzania usługą Office 365 na platformie Azure](insights/solution-office-365.md) — zaktualizowana data wycofania.


### <a name="logs"></a>Dzienniki
- [Optymalizuj zapytania dziennika w usłudze Azure Monitor](log-query/query-optimization.md) — nowy artykuł.
- [Zarządzanie użyciem i kosztami dzienników usługi Azure Monitor](platform/manage-cost-storage.md) — ulepszone przykładowe zapytania ułatwiające zrozumienie użycia.

### <a name="metrics"></a>Metryki
- [Metryki platformy Azure Monitor można eksportować za pomocą ustawień diagnostycznych](platform/metrics-supported-export-diagnostic-settings.md) — dodano sekcję dotyczącą zmiany zachowania wartości null i zero.


### <a name="visualizations"></a>Wizualizacje
Wiele nowych artykułów dla przewodnika konwersji widoku projektanta do skoroszytów.

- [Przewodnik przejścia widoku usługi Azure Monitor do skoroszytów](platform/view-designer-conversion-overview.md) — nowy artykuł.
- [Projektant widoku usługi Azure Monitor do opcji konwersji skoroszytów](platform/view-designer-conversion-options.md) — nowy artykuł.
- [Projektant widoku usługi Azure Monitor do konwersji kafelków skoroszytów](platform/view-designer-conversion-tiles.md) — nowy artykuł.
- [Projektant widoku usługi Azure Monitor do skoroszytów podsumowanie konwersji i dostęp](platform/view-designer-conversion-access.md) — nowy artykuł.
- [Projektant widoku usługi Azure Monitor do skoroszytów — typowe zadania konwersji](platform/view-designer-conversion-tasks.md) — nowy artykuł.
- [Projektant widoku usługi Azure Monitor do przykładów konwersji skoroszytów](platform/view-designer-conversion-examples.md) — nowy artykuł.




## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="general"></a>Ogólne
- [Co jest monitorowane przez usługę Azure Monitor?](monitor-reference.md) - Nowy artykuł.

### <a name="agents"></a>Agenci
- [Zbieranie danych dziennika za pomocą agenta usługi Azure Log Analytics](platform/log-analytics-agent.md) — zaktualizowana tabela wymagań zapory sieciowej.


### <a name="alerts"></a>Alerty
- [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure portal](platform/action-groups.md) — ustawienie usunięte dla funkcji w wersji 2, które nie są już wymagane.
- [Utwórz alert metryki z szablonem Menedżera zasobów](platform/alerts-metric-create-templates.md) — dodano przykład parametru *ignoreDataBefore.*  Dodano ograniczenia dotyczące reguł z wieloma kryteriami.
- [Za pomocą interfejsu API REST alert usługi Log Analytics](platform/api-alerts.md) — przykład JSON poprawione.


### <a name="application-insights"></a>Application Insights
- [Adresy IP używane przez usługi Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowano sekcję testu dostępności, aby dodać regułę portu przychodzącego, aby zezwolić na ruch przy użyciu grup zabezpieczeń sieci azure.
- [Rozwiązywanie problemów z profilera usługi Azure Application Insights](app/profiler-troubleshooting.md) — zaktualizowane ogólne rozwiązywanie problemów.
- [Próbkowanie telemetrii w usłudze Azure Application Insights](app/sampling.md) — zaktualizowane i zrestrukturyzowane w celu poprawy czytelności na podstawie opinii klientów.


### <a name="data-security"></a>Bezpieczeństwo danych
- [Konfiguracja klucza zarządzanego przez klienta usługi Azure Monitor](platform/customer-managed-keys.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Spostrzeżenia i rozwiązania

#### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
- [Konfigurowanie usługi Azure Monitor dla zbierania danych agenta kontenerów](insights/container-insights-agent-config.md) — dodano szczegóły dotyczące uaktualniania agenta w usłudze Azure Red Hat OpenShift i dodano dodatkowe informacje, aby odróżnić metody uaktualniania agenta.
- [Tworzenie alertów wydajności dla usługi Azure Monitor dla kontenerów](insights/container-insights-alerts.md) — poprawione informacje i zaktualizowane kroki tworzenia alertu o danych wydajności przechowywanych w obszarze roboczym przy użyciu alertów kontekstu obszaru roboczego.
- [Monitorowanie usługi Kubernetes za pomocą usługi Azure Monitor dla kontenerów](insights/container-insights-analyze.md) — zaktualizowano zarówno artykuł omówienia, jak i artykuł analizy dotyczący obsługi klastrów kubernetes systemu Windows.
- [Konfigurowanie klastrów Azure Red Hat OpenShift za pomocą usługi Azure Monitor dla kontenerów](insights/container-insights-azure-redhat-setup.md) — dodano szczegóły dotyczące uaktualniania agenta w usłudze Azure Red Hat OpenShift i dodano dodatkowe informacje, aby odróżnić metody uaktualniania agenta.
- [Konfigurowanie klastrów kubernetes hybrydowych za pomocą usługi Azure Monitor dla kontenerów](insights/container-insights-hybrid-setup.md) — zaktualizowano w celu odzwierciedlenia dodatkowej obsługi bezpiecznego portu:10250 za pomocą usługi cAdvisor firmy Kubelet.
- [Jak zarządzać agentem usługi Azure Monitor dla kontenerów](insights/container-insights-manage-agent.md) — zaktualizowano szczegóły związane z zachowaniem i konfigurem skrobania metryk za pomocą usługi Azure Red Hat OpenShift w porównaniu z innymi typami klastrów kubernetes.
- [Konfigurowanie usługi Azure Monitor dla kontenerów Prometheus Integration](insights/container-insights-prometheus-integration.md) — zaktualizowane szczegóły związane z zachowaniem i konfiguracją skrobania metryk za pomocą usługi Azure Red Hat OpenShift w porównaniu z innymi typami klastrów kubernetes.
- [Jak zaktualizować usługę Azure Monitor dla kontenerów dla metryk](insights/container-insights-update-metrics.md) — zaktualizowane szczegóły związane z zachowaniem i config metryk skrobanie z azure red hat OpenShift w porównaniu do innych typów klastrów Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
- Często zadawane pytania dotyczące [usługi Azure Monitor for VMs (GA)](insights/vminsights-ga-release-faq.md) — dodano informacje dotyczące uaktualniania obszaru roboczego i agentów do nowej wersji.

#### <a name="office-365"></a>Office 365
- [Rozwiązanie do zarządzania usługą Office 365](insights/solution-office-365.md) na platformie Azure — dodano szczegóły i często zadawane pytania dotyczące migracji do rozwiązania usługi Office 365 w usłudze Azure Sentinel. Usunięto sekcję dołączania.



### <a name="logs"></a>Dzienniki
- [Zarządzanie obszarami roboczymi usługi Log Analytics w usłudze Azure Monitor](platform/manage-access.md) — aktualizacje akcji Nie.
- [Zarządzanie użyciem i kosztami dzienników usługi Azure Monitor](platform/manage-cost-storage.md) — dodano wyjaśnienie dotyczące obliczania ilości danych w sekcji Model cenowy.
- [Użyj szablonów usługi Azure Resource Manager, aby utworzyć i skonfigurować obszar roboczy usługi Log Analytics](platform/template-workspace-configuration.md) — zaktualizowany szablon z nowymi warstwami cenowymi.


### <a name="platform-logs"></a>Dzienniki platformy
- [Zbieranie dziennika aktywności platformy Azure z ustawieniami diagnostycznymi — Usługa Azure Monitor](platform/diagnostic-settings-legacy.md) — dodatkowe informacje o zmienionych właściwościach.
- [Eksportuj dziennik aktywności platformy Azure](platform/activity-log-export.md) — aktualizowane w celu zmiany interfejsu użytkownika. 





## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="agents"></a>Agenci
- [Łączenie komputerów z systemem Linux z usługą Azure Monitor](platform/agent-linux.md) — nowy artykuł.

### <a name="alerts"></a>Alerty
- [Utwórz alert metryki z szablonem Menedżera zasobów](platform/alerts-metric-create-templates.md) — dodano przykład metryki niestandardowej.
- [Tworzenie alertów z progami dynamicznymi w usłudze Azure Monitor](platform/alerts-dynamic-thresholds.md) — dodano sekcję dotyczącą interpretacji dynamicznych wykresów progowych.
- [Omówienie alertów i monitorowania powiadomień na platformie Azure](platform/alerts-overview.md) — zaktualizowane zapytanie wykresu zasobów.
- [Obsługiwane zasoby dla alertów metryk w usłudze Azure Monitor](platform/alerts-metric-near-real-time.md) — aktualizacja do obsługiwanych metryk i wymiarów.
- [Przełącz się ze starszego interfejsu API alertów usługi Log Analytics do nowego interfejsu API alertów platformy Azure](platform/alerts-log-api-switch.md) — dodano notatkę o zmodyfikowanej nazwie alertu.
- [Dowiedz się, jak działają alerty metryk w usłudze Azure Monitor.](platform/alerts-metric-overview.md) - Dodano obsługiwane typy zasobów do monitorowania na dużą skalę.

### <a name="application-insights"></a>Application Insights
- [Usługa Application Insights for Worker Service apps (aplikacje inne niż HTTP)](app/worker-service.md) — dodano domyślny poziom rejestrowania do kodu języka C#. Zaktualizowana wersja referencyjna pakietu.
- [Odwołanie do aplikacji ApplicationInsights.config — Azure](app/configuration-with-applicationinsights-config.md) — zaktualizowany przykładowy kod.
- [Automatyzacja usługi Azure Application Insights za pomocą](app/powershell.md) szablonu programu PowerShell — aktualizacja do menedżera zasobów.
- [Pakiety NuGet usługi Azure Monitor aplikacji —](app/nuget.md) zaktualizowane wersje pakietów.
- [Utwórz nowy zasób usługi Azure Application Insights](app/create-new-resource.md) — uwaga dodana do unikatowej nazwy globalnie.
- [Diagnozuj za pomocą strumienia metryk na żywo — usługa Azure Application Insights](app/live-stream.md) — zaktualizowane ASP.NET wymagania dotyczące wersji core SDK.
- [Liczniki zdarzeń w usłudze Application Insights](app/eventcounters.md) — zaktualizowano kategorię i tabelę do metryk niestandardowych.
- [Poznaj dzienniki śledzenia java w usłudze Azure Application Insights](app/java-trace-logs.md) — dodano konfigurację dla progu rejestrowania agenta Java.
- [Adresy IP używane przez usługi Application Insights i Log Analytics](app/ip-addresses.md) — zaktualizowane adresy IP dla strumienia metryk na żywo.
- [Monitorowanie wydajności usług aplikacji platformy Azure](app/azure-web-apps.md) — dodano obsługę ASP.NET Core 3.0. 
- [Monitoruj aplikacje Języka Python za pomocą usługi Azure Monitor (wersja zapoznawcza)](app/opencensus-python.md) — dodano wyjaśnienie dotyczące mapowania schematu Języka Python w języku OpenCensus na platformie Azure . Schemat monitora
- [Informacje o wersji usługi Azure Application Insights](app/release-notes.md) — dodano uwagi dotyczące starszych wersji.
- [Kanały telemetrii w usłudze Azure Application Insights](app/telemetry-channels.md) — zaktualizowany czas trwania odrzuconych danych w dłuższym okresie utraconego połączenia.
- [Próbkowanie danych telemetrycznych w usłudze Azure Application Insights](app/sampling.md) — poprawiony fragment kodu dla niestandardowego telemetryinitializer.
- [Rozwiązywanie problemów z aplikacjami w projekcie sieci Web Java](app/java-troubleshoot.md) — usunięto instrukcję o nie obsługującej kolekcji zależności w zestawie JDK 9.

### <a name="insights-and-solutions"></a>Spostrzeżenia i rozwiązania
- [Usługa Azure Monitor dla kontenerów Często zadawane pytania](insights/container-insights-faq.md) — dodano pytanie w polach Obraz i Nazwa.
- [Rozwiązanie usługi Azure SQL Analytics w usłudze Azure Monitor](insights/azure-sql.md) — zaktualizowana baza danych czeka na obsługę wystąpienia zarządzanego.
- [Konfigurowanie usługi Azure Monitor dla zbierania danych agenta kontenerów](insights/container-insights-agent-config.md) — dodano ustawienie dla enrich_container_logs.
- [Konfigurowanie klastrów kubernetes hybrydowych za pomocą usługi Azure Monitor dla kontenerów](insights/container-insights-hybrid-setup.md) — dodano sekcję rozwiązywania problemów.
- [Monitoruj stan replikacji usługi Active Directory za pomocą](insights/ad-replication-status.md) usługi Azure Monitor — zaktualizowano warunek wstępny programu .NET Framework.
- [Rozwiązanie Monitor wydajności sieci na platformie Azure](insights/network-performance-monitor.md) — dodano obsługiwane regiony.
- [Zoptymalizuj środowisko usługi Active Directory za pomocą](insights/ad-assessment.md) zaktualizowanego wymogu wstępnego usługi Azure Monitor — .NET Framework.
- [Zoptymalizuj środowisko programu SQL Server za pomocą](insights/sql-assessment.md) usługi Azure Monitor — zaktualizowano warunek wstępny programu .NET Framework.
- [Zoptymalizuj środowisko programu Operations Manager centrum systemu za pomocą](insights/scom-assessment.md) usługi Azure Log Analytics — warunek wstępny platformy .NET Framework zaktualizowany.
- [Obsługiwane połączenia z łącznikiem zarządzania usługami IT w usłudze Azure Log Analytics](platform/itsmc-connections.md) — dodano nowy Jork, aby wstępnie wymagania identyfikator klienta i klucz tajny klienta.

### <a name="logs"></a>Dzienniki
- [Usuń i odzyskaj obszar roboczy usługi Azure Log Analytics](platform/delete-workspace.md) — dodano metodę programu PowerShell.
- [Projektowanie wdrożenia dzienników usługi Azure Monitor](platform/design-logs-deployment.md) — zwiększono szybkość pozyskiwania dla obszaru roboczego.

### <a name="metrics"></a>Metryki
- [Metryki platformy Azure Monitor można eksportować za pomocą ustawień diagnostycznych](platform/metrics-supported-export-diagnostic-settings.md) — nowy artykuł.

### <a name="platform-logs"></a>Dzienniki platformy
Wiele artykułów zaktualizowanych w ramach restrukturyzacji zawartości dla dzienników platformy na podstawie nowej funkcji konfigurowania dziennika aktywności przy użyciu ustawień diagnostycznych.

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](platform/resource-logs-collect-storage.md)
- [Schemat zdarzenia dziennika aktywności platformy Azure](platform/activity-log-schema.md)
- [Limity usług usługi Azure Monitor](service-limits.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics](platform/activity-log-collect.md)
- [Zbieranie dziennika aktywności platformy Azure z ustawieniami diagnostycznymi (wersja zapoznawcza) — Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Zbieranie dzienników aktywności platformy Azure do obszaru roboczego usługi Log Analytics w dzierżawach platformy Azure](platform/activity-log-collect-tenants.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym usługi Log Analytics](platform/resource-logs-collect-workspace.md)
- [Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Usługi Resource Manager](platform/diagnostic-settings-template.md)
- [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](platform/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](platform/activity-log-export.md)
- [Omówienie dzienników platformy Azure](platform/platform-logs-overview.md)
- [Przesyłanie strumieniowe danych monitorowania platformy Azure do Centrum zdarzeń](platform/stream-monitoring-data-event-hubs.md)
- [Strumieniowanie dzienników platformy Azure do centrum zdarzeń](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Przewodniki Szybki start i samouczki

- [Tworzenie wykresu metryk w usłudze Azure Monitor](learn/tutorial-metrics-explorer.md) — nowy artykuł.
- [Zbieraj dzienniki zasobów z zasobu platformy Azure i analizuj za pomocą usługi Azure Monitor](learn/tutorial-resource-logs.md) — nowy artykuł.
- [Monitorowanie zasobu platformy Azure za pomocą usługi Azure Monitor](learn/quick-monitor-azure-resource.md) — nowy artykuł.
   
## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz udostępnić dokumentację usługi Azure Monitor, zapoznaj się z [Przewodnikiem dla współautorów dokumentów](https://docs.microsoft.com/contribute/).