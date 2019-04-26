---
title: Monitorowania ciągłego za pomocą usługi Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano kolejne kroki umożliwiające włączanie ciągłego monitorowania w całym przepływów pracy za pomocą usługi Azure Monitor.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: bwren
ms.openlocfilehash: 1b86bc015b187fe75e79ba04df60a6bc5257a9b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60497424"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Monitorowanie ciągłe za pomocą usługi Azure Monitor

Ciągłe monitorowanie odwołuje się do procesu i technologii, które wymagają, aby obejmował monitorowanie w każdej fazie usługi DevOps i cykle życia operacji IT. Ułatwia zapewnienie stale kondycji, wydajności i niezawodności aplikacji i infrastruktury, kiedy przesuwa się on od projektowania do produkcji. Ciągłego monitorowania kompilacji na koncepcji ciągłej integracji i ciągłego wdrażania (CI/CD), które ułatwiają tworzenie i dostarczanie oprogramowania szybciej i bardziej niezawodnie zapewnienia trwałych korzyści użytkownikom.

[Usługa Azure Monitor](overview.md) jest jednolite rozwiązanie monitorowania na platformie Azure, udostępniająca observability pełnych w aplikacjach i infrastruktury w chmurze i lokalnych. Bezproblemowo współpracuje z [programu Visual Studio i Visual Studio Code](https://visualstudio.microsoft.com/) podczas tworzenia i testowania oraz integruje się z [DevOps platformy Azure](/azure/devops/user-guide/index) dla release management i zarządzania elementami roboczymi podczas wdrażania i operacje. Nawet integrują się one narzędzia ITSM i rozwiązania SIEM w wybranym ułatwia śledzenie problemów i zdarzeń w istniejących procesów IT.

W tym artykule opisano kolejne kroki umożliwiające włączanie ciągłego monitorowania w całym przepływów pracy za pomocą usługi Azure Monitor. Zawiera łącza do dokumentacji, która zawiera szczegółowe informacje dotyczące implementacji różnych funkcji.


## <a name="enable-monitoring-for-all-your-applications"></a>Włącz monitorowanie dla wszystkich aplikacji
Aby uzyskać observability w całym środowisku, musisz włączyć monitorowania dla wszystkich aplikacji sieci web i usług. To pozwala łatwo przedstawić wizualnie połączenia i transakcji end-to-end dotyczące wszystkich składników.

- [Funkcja Azure DevOps Projects](../devops-project/overview.md) umożliwiają uproszczone środowisko z istniejącego kodu i repozytorium Git lub wybrać jeden z przykładowych aplikacji, aby utworzyć potok ciągłej integracji (CI) i ciągłe dostarczanie (CD) na platformie Azure.
- [Ciągłe monitorowanie w potoku metodyki DevOps w wersji](../azure-monitor/app/continuous-monitoring.md) umożliwia bramy ani wycofać wdrożenie oparte na danych monitorowania.
- [Monitor stanu](../azure-monitor/app/monitor-performance-live-website-now.md) umożliwia instrumentowanie działającej aplikacji .NET na Windows za pomocą usługi Azure Application Insights, bez konieczności modyfikowania ani jego ponownego wdrażania kodu.
- Jeśli masz dostęp do kodu aplikacji, Włącz pełne monitorowanie za pomocą [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) przez zainstalowanie usługi Azure Monitor zestaw Application Insights SDK dla [.NET](../azure-monitor/learn/quick-monitor-portal.md), [języka Java ](../azure-monitor/learn/java-quick-start.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md), lub [innych języków programowania](../azure-monitor/app/platforms.md). Dzięki temu można określić niestandardowe zdarzenia, metryki lub wyświetleń stron, które są istotne dla Twojej firmy i aplikacji.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Włącz monitorowanie dla całej infrastruktury
Aplikacje są tylko tak niezawodna jak ich podstawowej infrastruktury. Posiadanie monitorowanie jest włączone w całej infrastrukturze pomoże Ci osiągnąć pełną observability i ułatwić odnaleźć potencjalną główną przyczynę, gdy coś się nie powiedzie. Usługa Azure Monitor pomaga śledzić, kondycji i wydajności infrastruktury całej hybrydowej, łącznie z zasobami, takimi jak maszyny wirtualne, kontenery, magazynowania i sieci.

- Automatycznie Pobierz [platformy metryk, dzienników aktywności i dzienników diagnostyki](platform/data-sources.md) przez większość zasobów platformy Azure bez konfiguracji.
- Włącz dokładniejsze monitorowanie maszyn wirtualnych z [usługi Azure Monitor dla maszyn wirtualnych](insights/vminsights-overview.md).
-  Włącz monitorowanie bardziej AKS klastrów za pomocą [usługi Azure Monitor dla kontenerów](insights/container-insights-overview.md).
- Dodaj [rozwiązania do monitorowania](insights/solutions-inventory.md) dla różnych aplikacji i usług w danym środowisku.


[Infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code) to zarządzanie infrastrukturą w modelu opisowym przy użyciu tej samej wersji, jak zespoły DevOps na użytek kodu źródłowego. Ona dodaje niezawodność i skalowalność do środowiska i umożliwia wykorzystanie podobne procesy, które umożliwiają zarządzanie aplikacjami.

-  Użyj [szablonów usługi Resource Manager](platform/template-workspace-configuration.md) aby umożliwić monitorowanie i Konfigurowanie alertów za pośrednictwem duży zestaw zasobów.
- Użyj [usługi Azure Policy](../governance/policy/overview.md) do wymuszają różne reguły dotyczące zasobów. Daje to gwarancję, że te zasoby pozostają zgodne ze standardami firmy i umów dotyczących poziomu usług. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Łączenie zasobów w grupach zasobów platformy Azure
Typowa aplikacja na platformie Azure już dziś obejmuje wiele zasobów, takich jak maszyny wirtualne i usługi App Service lub mikrousług w serwisie Cloud Services, usługi AKS klastrów lub usługi Service Fabric. Aplikacje te często wykorzystywać zależności, takich jak Event Hubs, Storage, SQL i usługi Service Bus.

- Łączenie zasobów inAzure grup zasobów można pobrać pełny wgląd we wszystkie zasoby, które tworzą różnych aplikacji. [Usługa Azure Monitor dla grup zasobów](../azure-monitor/insights/resource-group-insights.md) to prosty sposób, aby śledzić kondycję i wydajność całej aplikacji pełnych i umożliwia przechodzenie odpowiednie składniki dla wszelkich badań lub debugowania.

## <a name="ensure-quality-through-continuous-deployment"></a>Zapewnić jakość dzięki ciągłemu wdrażaniu
Ciągła integracja / ciągłe wdrażanie pozwala na integrowanie i wdrażanie zmiany kodu do aplikacji na podstawie wyników testów automatycznych automatycznie. On usprawnia proces wdrażania i zapewnia jakości o wszelkich zmianach przed ich przenieść do środowiska produkcyjnego.


- Użyj [potoki Azure](/azure/devops/pipelines) Implementowanie ciągłego wdrażania i automatyzowanie całego procesu od zatwierdzania kodu do środowiska produkcyjnego, oparte na testach ciągłej integracji/ciągłego wdrażania.
- Użyj [bramki jakości](/azure/devops/pipelines/release/approvals/gates) można zintegrować monitorowanie w przed wdrożeniem lub po wdrożeniu. Dzięki temu są spełniane metryki klucza kondycji i wydajności (KPI) jako aplikacji przenieść z deweloperów do produkcji i różnice w środowisku infrastruktury lub skalowania jest do niekorzystnego wpływu na kluczowych wskaźników wydajności.
- [Obsługa osobne monitorowania instancje](../azure-monitor/app/separate-resources.md) między środowiska innego wdrożenia, takie jak deweloperów, testerów, Canary i produkcyjne. Dzięki temu zebranych danych odpowiednich dla skojarzonych aplikacji i infrastruktury. Jeśli zachodzi potrzeba skorelowania danych między środowiskami, możesz użyć [wykresy wielu zasobów w Eksploratorze metryk](../azure-monitor/platform/metrics-charts.md) lub Utwórz [zapytania obejmujące wiele zasobów w usłudze Azure Monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Tworzenie alertów informacje z możliwością działania za pomocą akcji
Krytyczne aspekty monitorowania jest aktywnie powiadamiającą administratorów o wszystkie bieżące oraz dostęp do przewidywanych problemy. 

- Tworzenie [alertów w usłudze Azure Monitor](../azure-monitor/platform/alerts-overview.md) na podstawie dzienników i metryk do identyfikowania stanów niepowodzenia przewidywalne. Powinny być celem wprowadzania wszystkie alerty są wiarygodne, czyli reprezentują rzeczywistych warunków krytycznych i starać się redukować liczbę fałszywych alarmów. Użyj [dynamicznymi progami](platform/alerts-dynamic-thresholds.md) można automatycznie obliczyć odniesienia na dane metryk niż definiowanie własnych progi statyczne. 
- Definiuj akcje dotyczące alertów, aby użyć jest najbardziej skutecznym sposobem powiadamiania administratorów. Dostępne [akcje w przypadku powiadomień](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) są wiadomości SMS, wiadomości e-mail, powiadomienia wypychane lub połączeń głosowych.
- Bardziej zaawansowane akcje [nawiązać połączenie z narzędziem ITSM](platform/itsmc-overview.md) lub inne systemy zarządzania alertami, za pośrednictwem [elementów webhook](platform/activity-log-alerts-webhook.md).
- Korygowanie sytuacjach identyfikowane w alertach, jak również za pomocą [elementów runbook usługi Azure Automation](../automation/automation-webhooks.md) lub [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , można uruchamiać z poziomu alertu przy użyciu elementów webhook. 
- Użyj [skalowania automatycznego](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) można dynamicznie zwiększać i zmniejszać zasoby obliczeniowe na podstawie zebranych metryk.

## <a name="prepare-dashboards-and-workbooks"></a>Przygotowywanie pulpitów nawigacyjnych i skoroszyty
Zapewnienia, że usługi deweloperów i operacyjne mają dostęp do narzędzi i te same dane telemetryczne pozwala na wyświetlanie wzorców w całym środowisku i zminimalizować swoje średniego czasu wykrywania (MTTD) i Średni czas przywracania (MTTR).

- Przygotowanie [niestandardowe pulpity nawigacyjne](../azure-monitor/learn/tutorial-app-dashboards.md) oparte na typowych metryk i dzienników dla różnych ról w organizacji. Pulpity nawigacyjne można połączyć dane z wszystkich zasobów platformy Azure.
- Przygotowanie [skoroszyty](../azure-monitor/app/usage-workbooks.md) zapewnienie wiedzy między środowiskami deweloperskim i operacji. Może je przygotować jako dynamiczne raporty z wykresy metryk i dzienników zapytań lub przewodniki nawet jako rozwiązywania problemów przygotowane przez deweloperów, pomoc techniczną lub operacje do obsługi podstawowych problemów.

## <a name="continuously-optimize"></a>Zapewnij ciągłą optymalizację
 Monitorowanie jest jednym z podstawowych aspektów popularnych Build-Measure-Learn filozofia, zalecanego stale śledzenie kluczowych wskaźników wydajności i metryki zachowanie użytkowników i następnie fundusze optymalizujesz je za pomocą planowania iteracji. Usługa Azure Monitor pomaga zbierać metryki i dzienniki odpowiednie dla Twojej firmy i dodać nowe punkty danych w ramach następnego wdrożenia, zgodnie z potrzebami.

- Użyj narzędzi w usłudze Application Insights do [śledzenie zachowania użytkowników końcowych i zaangażowania](../azure-monitor/learn/tutorial-users.md).
- Użyj [analizy wpływu](../azure-monitor/app/usage-impact.md) ułatwiające priorytety obszary, które chcesz się skupić się na dysku do ważnych kluczowych wskaźników wydajności.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o składnikach różnica [usługi Azure Monitor](overview.md).
- [Dodawanie ciągłego monitorowania](../azure-monitor/app/continuous-monitoring.md) do potoku wydania.