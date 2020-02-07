---
title: Co nowego w dokumentacji Azure Monitor
description: Ważne aktualizacje dokumentacji Azure Monitor aktualizowane co miesiąc.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/05/2020
ms.openlocfilehash: f390de40504d4d6065705ca23555ac2164c9847b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065565"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Co nowego w dokumentacji Azure Monitor?
Ten artykuł zawiera listę artykułów Azure Monitor, które są nowe lub zostały znacząco zaktualizowane. Zostanie odświeżony pierwszy tydzień każdego miesiąca, aby uwzględnić aktualizacje artykułów z poprzedniego miesiąca.

## <a name="january-2020"></a>Styczeń 2020 r.

### <a name="general"></a>Ogólne
- [Co jest monitorowane przez Azure Monitor?](monitor-reference.md) — Nowy artykuł.

### <a name="agents"></a>Agenci
- [Zbierz dane dziennika za pomocą usługi Azure log Analytics Agent](platform/log-analytics-agent.md) — Zaktualizowano tabelę wymagań zapory sieciowej.


### <a name="alerts"></a>Alerty
- [Tworzenie grup akcji i zarządzanie nimi w ustawieniach Azure Portal](platform/action-groups.md) , które zostały usunięte dla funkcji w wersji 2, które nie są już wymagane.
- [Utwórz alert metryki z przykładem Menedżer zasobów szablonu](platform/alerts-metric-create-templates.md) dodanym jako parametr *ignoreDataBefore* .  Dodano ograniczenia dotyczące reguł wielokryteriów.
- [Za pomocą log Analytics alert API REST](platform/api-alerts.md) — Poprawiono przykład JSON.


### <a name="application-insights"></a>Application Insights
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — Zaktualizowano sekcję Test dostępności wraz z dodawaniem reguły portów przychodzących w celu zezwalania na ruch przy użyciu sieciowych grup zabezpieczeń platformy Azure.
- [Rozwiązywanie problemów z usługą Azure Application Insights Profiler](app/profiler-troubleshooting.md) — zaktualizowane ogólne Rozwiązywanie problemów.
- [Próbkowanie danych telemetrycznych na platformie Azure Application Insights](app/sampling.md) — zaktualizowane i rozbudowane w celu zwiększenia czytelności na podstawie opinii klientów.


### <a name="data-security"></a>Bezpieczeństwo danych
- [Azure monitor konfiguracja klucza zarządzanego przez klienta](platform/customer-managed-keys.md) — nowy artykuł.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania

#### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
- [Konfigurowanie Azure monitor do zbierania danych przez agentów kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift oraz dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Utwórz alerty wydajności dla Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-alerts) informacje o zaktualizowanych kontenerach i zaktualizowanych krokach w celu utworzenia alertu dotyczącego danych wydajności przechowywanych w obszarze roboczym przy użyciu alertów kontekstu obszaru roboczego.
- [Kubernetes monitorowanie za pomocą Azure monitor dla kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-analyze) — Zaktualizowano artykuł z omówieniem i artykuł dotyczący analizy w celu obsługi klastrów Windows Kubernetes.
- [Skonfiguruj klastry usługi Azure Red Hat OpenShift za pomocą Azure monitor dla kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-azure-redhat-setup) — dodano szczegóły dotyczące uaktualniania agenta na platformie Azure Red Hat OpenShift i dodano dodatkowe informacje w celu odróżnienia metod uaktualnienia agenta.
- [Skonfiguruj hybrydowe klastry Kubernetes z Azure monitor dla kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-hybrid-setup) — zaktualizowano w celu odzwierciedlenia dodanej obsługi bezpiecznego portu: 10250 z cAdvisor Kubelet.
- [Jak zarządzać Azure Monitorą dla agenta kontenerów](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-manage-agent) — Zaktualizowano szczegółowe informacje związane z zachowaniem i konfiguracją wycinków metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Konfigurowanie Azure monitor na potrzeby integracji z integracją Prometheus](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration) — Zaktualizowano szczegółowe informacje związane z zachowaniem i konfiguracją wycinków metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.
- [Jak zaktualizować Azure monitor kontenerów dla metryk](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-update-metrics) — Zaktualizowano szczegóły dotyczące zachowania i konfiguracji odporności metryk z usługą Azure Red Hat OpenShift w porównaniu z innymi typami klastrów Kubernetes.


#### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
- [Azure monitor dla maszyn wirtualnych (ga) — często zadawane pytania](insights/vminsights-ga-release-faq.md) — dodano informacje dotyczące uaktualniania obszaru roboczego i agentów do nowej wersji.

#### <a name="office-365"></a>Office 365
- [Rozwiązanie do zarządzania pakietem office 365 na platformie Azure](insights/solution-office-365.md) — dodano szczegóły i często zadawane pytania dotyczące migracji do rozwiązania pakietu Office 365 na platformie Azure. Usunięto sekcję dołączania.



### <a name="logs"></a>Dzienniki
- [Zarządzanie obszarami roboczymi log Analytics w programie Azure monitor](platform/manage-access.md) — aktualizacje nie są wykonywane.
- [Zarządzanie użyciem i kosztami Azure monitor dzienników](platform/manage-cost-storage.md) — dodano wyjaśnienie dotyczące obliczania ilości danych w sekcji model cen.
- [Użyj szablonów Azure Resource Manager, aby utworzyć i skonfigurować szablon log Analytics obszaru roboczego](platform/template-workspace-configuration.md) zaktualizowany przy użyciu nowych warstw cenowych.


### <a name="platform-logs"></a>Dzienniki platformy
- [Zbierz dziennik aktywności platformy Azure z ustawieniami diagnostycznymi — Azure monitor](platform/diagnostic-settings-legacy.md) — dodatkowe informacje na temat zmienionych właściwości.
- [Eksportowanie dziennika aktywności platformy Azure](platform/activity-log-export.md) — zaktualizowano w celu wprowadzenia zmian w interfejsie użytkownika. 





## <a name="december-2019"></a>Grudzień 2019 r.

### <a name="agents"></a>Agenci
- [Połącz komputery z systemem Linux, aby Azure monitor](platform/agent-linux.md) — nowy artykuł.

### <a name="alerts"></a>Alerty
- [Utwórz alert metryki za pomocą szablonu Menedżer zasobów](platform/alerts-metric-create-templates.md) dodanego przykładu dla metryki niestandardowej.
- [Tworzenie alertów z progami dynamicznymi w sekcji Azure monitor](platform/alerts-dynamic-thresholds.md) dodano w celu interpretacji wykresów progów dynamicznych.
- [Przegląd alertów i monitorowania powiadomień na platformie Azure](platform/alerts-overview.md) — zaktualizowane zapytanie dotyczące grafu zasobów.
- [Obsługiwane zasoby dla alertów metryk w Azure monitor](platform/alerts-metric-near-real-time.md) -Update do obsługiwanych metryk i wymiarów.
- [Przełączenie się ze starszej wersji interfejsu API alertów usługi log Analytics na nową funkcję API alertów platformy Azure](platform/alerts-log-api-switch.md) — dodano uwagę na zmodyfikowaną nazwę
- [Informacje o tym, jak alerty metryk działają w Azure Monitor.](platform/alerts-metric-overview.md) -Dodano obsługiwane typy zasobów do monitorowania w odpowiedniej skali.

### <a name="application-insights"></a>Application Insights
- [Application Insights dla aplikacji usługi Worker (aplikacje inne niż http)](app/worker-service.md) — dodano domyślny poziom rejestrowania do C# kodu. Zaktualizowana wersja odwołania do pakietu.
- [ApplicationInsights. config — informacje o platformie Azure](app/configuration-with-applicationinsights-config.md) — zaktualizowany przykładowy kod.
- [Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell](app/powershell.md) — aktualizacja do Menedżer zasobów szablonu.
- [Azure Monitor Application Insights pakietów NuGet](app/nuget.md) — Zaktualizowano wersje pakietów.
- [Utwórz nową usługę Azure Application Insights Resource](app/create-new-resource.md) — Uwaga dodana do globalnie unikatowej nazwy.
- [Diagnozuj przy użyciu Live Metrics Stream platformy Azure Application Insights](app/live-stream.md) — Zaktualizowano wymagania dotyczące wersji zestawu SDK ASP.NET Core.
- [Liczniki zdarzeń w Application Insights](app/eventcounters.md) — Zaktualizowano kategorię i tabelę do customMetrics.
- [Poznaj dzienniki śledzenia języka Java na platformie Azure Application Insights](app/java-trace-logs.md) — dodano konfigurację dla progu rejestrowania agenta Java.
- [Adresy IP używane przez Application Insights i log Analytics](app/ip-addresses.md) — zaktualizowane adresy ip dla Live Metrics Stream.
- [Monitorowanie wydajności usług Azure App Services](app/azure-web-apps.md) — dodano obsługę ASP.NET Core 3,0. 
- [Monitoruj aplikacje języka Python za pomocą Azure monitor (wersja zapoznawcza)](app/opencensus-python.md) — dodaliśmy wyjaśnienie dla mapowania schematu OpenCensus języka Python na platformę Azure. Monitoruj schemat
- [Informacje o wersji dla platformy Azure Application Insights](app/release-notes.md) — notatki dodane dla starszych wersji.
- [Kanały telemetrii na platformie Azure Application Insights](app/telemetry-channels.md) — zaktualizowany czas trwania odrzuconych danych podczas dłuższego okresu utraconych połączeń.
- [Próbkowanie danych telemetrycznych w Application Insights platformy Azure](app/sampling.md) — poprawione fragmenty kodu dla niestandardowej TelemetryInitializer.
- [Rozwiązywanie problemów Application Insights w projekcie sieci Web w języku Java](app/java-troubleshoot.md) — usunięto instrukcję dotyczącą braku obsługi kolekcji zależności w programie JDK 9.

### <a name="insights-and-solutions"></a>Szczegółowe informacje i rozwiązania
- [Azure monitor for Containers — często zadawane pytania](insights/container-insights-faq.md) — pytanie dotyczące pól obrazu i nazwy.
- [Azure SQL Analytics rozwiązanie w Azure monitor](insights/azure-sql.md) — zaktualizowana baza danych czeka na obsługę wystąpienia zarządzanego.
- [Skonfiguruj Azure monitor dla ustawienia Dodawanie kolekcji danych agenta kontenerów](insights/container-insights-agent-config.md) dla enrich_container_logs.
- [Skonfiguruj hybrydowe klastry Kubernetes za pomocą Azure monitor dla kontenerów](insights/container-insights-hybrid-setup.md) — Dodano sekcję rozwiązywania problemów.
- [Monitoruj Active Directory stan replikacji z](insights/ad-replication-status.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Network Performance Monitor rozwiązanie na platformie Azure](insights/network-performance-monitor.md) — dodano Obsługiwane regiony.
- [Zoptymalizuj środowisko Active Directory z](insights/ad-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko SQL Server z](insights/sql-assessment.md) aktualizacją wymagań wstępnych Azure monitor .NET Framework.
- [Zoptymalizuj środowisko System Center Operations Manager za pomocą usługi Azure log Analytics](insights/scom-assessment.md) -.NET Framework aktualizacji wymagań wstępnych.
- [Obsługiwane połączenia z łącznik zarządzania usługami IT na platformie Azure log Analytics](platform/itsmc-connections.md) — dodano Nowy Jork do wstępnie wymaganego identyfikatora klienta i klucza tajnego klienta.

### <a name="logs"></a>Dzienniki
- [Usuń i Odzyskaj metodę programu PowerShell dla usługi Azure log Analytics Workspace](platform/delete-workspace.md) .
- [Projektowanie Azure Monitorych dzienników](platform/design-logs-deployment.md) — szybkość pozyskiwania dla obszaru roboczego zwiększyła się.

### <a name="metrics"></a>Metryki
- [Metryki platformy Azure monitor eksportowane za pośrednictwem ustawień diagnostycznych](platform/metrics-supported-export-diagnostic-settings.md) — nowy artykuł.

### <a name="platform-logs"></a>Dzienniki platformy
Zaktualizowano wiele artykułów w ramach restrukturyzacji zawartości dla dzienników platformy w oparciu o nową funkcję konfigurowania dziennika aktywności przy użyciu ustawień diagnostycznych.

- [Archiwizuj dzienniki zasobów platformy Azure na koncie magazynu](platform/resource-logs-collect-storage.md)
- [Schemat zdarzeń dziennika aktywności platformy Azure](platform/activity-log-schema.md)
- [Limity usługi Azure Monitor](service-limits.md)
- [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics](platform/activity-log-collect.md)
- [Zbierz dziennik aktywności platformy Azure przy użyciu ustawień diagnostycznych (wersja zapoznawcza) — Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Zbieranie dzienników aktywności platformy Azure w obszarze roboczym Log Analytics w ramach dzierżawców platformy Azure](platform/activity-log-collect-tenants.md)
- [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym Log Analytics](platform/resource-logs-collect-workspace.md)
- [Tworzenie ustawień diagnostycznych na platformie Azure przy użyciu szablonu Menedżer zasobów](platform/diagnostic-settings-template.md)
- [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure](platform/diagnostic-settings.md)
- [Eksportowanie dziennika aktywności platformy Azure](platform/activity-log-export.md)
- [Omówienie dzienników platformy Azure](platform/platform-logs-overview.md)
- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń](platform/stream-monitoring-data-event-hubs.md)
- [Przesyłanie strumieniowe dzienników platformy Azure do centrum zdarzeń](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Przewodniki Szybki start i samouczki

- [Utwórz wykres metryk w Azure monitor](learn/tutorial-metrics-explorer.md) — nowy artykuł.
- [Zbierz dzienniki zasobów z zasobów platformy Azure i Analizuj je za pomocą Azure monitor](learn/tutorial-resource-logs.md) — nowy artykuł.
- [Monitoruj zasób platformy Azure za pomocą Azure monitor](learn/quick-monitor-azure-resource.md) — nowy artykuł.
   
## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz współtworzyć dokumentację Azure Monitor, zobacz [Przewodnik współautora dokumentów](https://docs.microsoft.com/contribute/).