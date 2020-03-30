---
title: Ciągłe monitorowanie za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano określone kroki dotyczące korzystania z usługi Azure Monitor, aby włączyć ciągłe monitorowanie w całym przepływie pracy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: b9ca8a703ed8a84148abd23e90114402d8806bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667197"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Ciągłe monitorowanie za pomocą usługi Azure Monitor

Ciągłe monitorowanie odnosi się do procesu i technologii wymaganych do włączenia monitorowania na każdym etapie cyklu życia operacji DevOps i IT. Pomaga stale zapewniać kondycję, wydajność i niezawodność aplikacji i infrastruktury w miarę przechodzenia z rozwoju do produkcji. Ciągłe monitorowanie opiera się na koncepcjach ciągłej integracji i ciągłego wdrażania (CI/CD), które pomagają w szybszym i bardziej niezawodnym opracowywaniu i dostarczaniu oprogramowania, aby zapewnić ciągłą wartość dla użytkowników.

[Usługa Azure Monitor](overview.md) to ujednolicone rozwiązanie do monitorowania na platformie Azure, które zapewnia pełną możliwość obserwowania stosu w aplikacjach i infrastrukturze w chmurze i lokalnie. Bezproblemowo współpracuje z [programami Visual Studio i Visual Studio Code](https://visualstudio.microsoft.com/) podczas tworzenia i testowania i integruje się z [usługą Azure DevOps](/azure/devops/user-guide/index) do zarządzania wersjami i elementów roboczych podczas wdrażania i operacji. Integruje się nawet z wybranymi narzędziami ITSM i SIEM, aby pomóc w śledzeniu problemów i incydentów w istniejących procesach IT.

W tym artykule opisano określone kroki dotyczące korzystania z usługi Azure Monitor, aby włączyć ciągłe monitorowanie w przepływach pracy. Zawiera łącza do innej dokumentacji, która zawiera szczegółowe informacje na temat implementowania różnych funkcji.


## <a name="enable-monitoring-for-all-your-applications"></a>Włącz monitorowanie wszystkich aplikacji
Aby uzyskać możliwość obserwowalności w całym środowisku, należy włączyć monitorowanie wszystkich aplikacji i usług sieci web. Pozwoli to łatwo wizualizować kompleksowe transakcje i połączenia we wszystkich składnikach.

- [Projekty usługi Azure DevOps](../devops-project/overview.md) zapewniają uproszczone środowisko z istniejącym kodem i repozytorium Git lub wybierz jedną z przykładowych aplikacji, aby utworzyć potok ciągłej integracji (CI) i ciągłego dostarczania (CD) na platformie Azure.
- [Ciągłe monitorowanie w potoku wydania DevOps](../azure-monitor/app/continuous-monitoring.md) umożliwia bramę lub wycofywanie wdrożenia na podstawie danych monitorowania.
- [Monitor stanu](../azure-monitor/app/monitor-performance-live-website-now.md) umożliwia instrumenty live .NET aplikacji w systemie Windows z usługi Azure Application Insights, bez konieczności modyfikowania lub ponownego wdrożenia kodu.
- Jeśli masz dostęp do kodu aplikacji, włącz pełne monitorowanie za pomocą [usługi Application Insights,](../azure-monitor/app/app-insights-overview.md) instalując zestaw SDK usługi Azure Monitor Application Insights dla [platformy .NET](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md)lub [innych języków programowania.](../azure-monitor/app/platforms.md) Dzięki temu można określić niestandardowe zdarzenia, metryki lub widoki strony, które są istotne dla aplikacji i firmy.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Włącz monitorowanie całej infrastruktury
Aplikacje są tylko tak niezawodne, jak ich podstawowej infrastruktury. Po włączeniu monitorowania w całej infrastrukturze pomoże Ci osiągnąć pełną przestrzeganie i ułatwić odnalezienie potencjalnej głównej przyczyny, gdy coś ulegnie awarii. Usługa Azure Monitor ułatwia śledzenie kondycji i wydajności całej infrastruktury hybrydowej, w tym zasobów, takich jak maszyny wirtualne, kontenery, magazyn i sieć.

- Automatycznie otrzymasz [metryki platformy, dzienniki aktywności i dzienniki diagnostyczne](platform/data-sources.md) z większości zasobów platformy Azure bez konfiguracji.
- Włącz głębsze monitorowanie maszyn wirtualnych za pomocą [usługi Azure Monitor dla maszyn wirtualnych.](insights/vminsights-overview.md)
-  Włącz głębsze monitorowanie klastrów AKS za pomocą [usługi Azure Monitor dla kontenerów.](insights/container-insights-overview.md)
- Dodaj [rozwiązania do monitorowania](insights/solutions-inventory.md) dla różnych aplikacji i usług w swoim środowisku.


[Infrastruktura jako kod](/azure/devops/learn/what-is-infrastructure-as-code) jest zarządzanie infrastrukturą w modelu opisowym, przy użyciu tej samej wersji jako zespoły DevOps używać do kodu źródłowego. Zwiększa niezawodność i skalowalność środowiska i umożliwia wykorzystanie podobnych procesów, które były używane do zarządzania aplikacjami.

-  Użyj [szablonów Menedżera zasobów,](platform/template-workspace-configuration.md) aby włączyć monitorowanie i konfigurowanie alertów za pomocą dużego zestawu zasobów.
- Użyj [zasad platformy Azure,](../governance/policy/overview.md) aby wymusić różne reguły nad zasobami. Dzięki temu zasoby te pozostaną zgodne ze standardami firmowymi i umowami o poziomie usług. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Łączenie zasobów w grupach zasobów platformy Azure
Typowa aplikacja na platformie Azure obecnie zawiera wiele zasobów, takich jak maszyny wirtualne i usługi aplikacji lub mikrousług hostowanych w usługach w chmurze, klastrów AKS lub sieci szkieletowej usług. Te aplikacje często wykorzystują zależności, takie jak Centra zdarzeń, Magazyn, SQL i Usługa Service Bus.

- Połącz zasoby w grupach zasobów platformy Azure, aby uzyskać pełną widoczność wszystkich zasobów, które tworzą różne aplikacje. [Usługa Azure Monitor for Resource Groups](../azure-monitor/insights/resource-group-insights.md) zapewnia prosty sposób śledzenia kondycji i wydajności całej aplikacji z pełnym stosem i umożliwia przechodzenie do odpowiednich składników w celu przeprowadzenia dochodzenia lub debugowania.

## <a name="ensure-quality-through-continuous-deployment"></a>Zapewnij jakość dzięki ciągłego wdrażania
Ciągła integracja / ciągłe wdrażanie umożliwia automatyczną integrację i wdrażanie zmian kodu w aplikacji na podstawie wyników automatycznych testów. Usprawnia proces wdrażania i zapewnia jakość wszelkich zmian przed przejściem do produkcji.


- Użyj [potoków platformy Azure,](/azure/devops/pipelines) aby zaimplementować ciągłe wdrażanie i zautomatyzować cały proces od zatwierdzenia kodu do produkcji na podstawie testów ciągłej integracji/ciągłego wdrażania.
- Użyj [quality gates,](/azure/devops/pipelines/release/approvals/gates) aby zintegrować monitorowanie z przed wdrożeniem lub po wdrożeniu. Gwarantuje to, że spełniasz metryki kluczowych kondycji/wydajności (KPI), ponieważ aplikacje przechodzą z dewelopera do produkcji, a wszelkie różnice w środowisku infrastruktury lub skali nie mają negatywnego wpływu na kluczowe wskaźniki wydajności.
- [Obsługa oddzielnych wystąpień monitorowania](../azure-monitor/app/separate-resources.md) między różnymi środowiskami wdrażania, takimi jak Dev, Test, Canary i Prod. Dzięki temu zebrane dane są istotne dla powiązanych aplikacji i infrastruktury. Jeśli chcesz skorelować dane między środowiskami, możesz użyć [wykresów wieloserwowych w Eksploratorze metryk](../azure-monitor/platform/metrics-charts.md) lub utworzyć [kwerendy między zasobami w usłudze Azure Monitor](log-query/cross-workspace-query.md).


## <a name="create-actionable-alerts-with-actions"></a>Tworzenie alertów z zasysanymi akcjami za pomocą akcji
Krytycznym aspektem monitorowania jest proaktywne powiadamianie administratorów o wszelkich bieżących i przewidywanych problemach. 

- Tworzenie [alertów w usłudze Azure Monitor](../azure-monitor/platform/alerts-overview.md) na podstawie dzienników i metryk w celu zidentyfikowania przewidywalnych stanów awarii. Powinieneś mieć na celu uczynienie wszystkich alertów zasysanymi, co oznacza, że reprezentują one rzeczywiste krytyczne warunki i dążyć do zmniejszenia liczby fałszywych alarmów. Progi [dynamiczne](platform/alerts-dynamic-thresholds.md) umożliwia automatyczne obliczanie linii bazowych na danych metryki, a nie definiowanie własnych progów statycznych. 
- Zdefiniuj akcje alertów, aby używać najskuteczniejszych sposobów powiadamiania administratorów. Dostępne [akcje do powiadamiania](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) to SMS, e-maile, powiadomienia push lub połączenia głosowe.
- Użyj bardziej zaawansowanych działań, [aby połączyć się z narzędziem ITSM](platform/itsmc-overview.md) lub innymi systemami zarządzania alertami za pośrednictwem [webhooks](platform/activity-log-alerts-webhook.md).
- Korygowanie sytuacji zidentyfikowanych w alertach, a także za pomocą [elementów runbook usługi Azure Automation](../automation/automation-webhooks.md) lub [aplikacji logiki,](/connectors/custom-connectors/create-webhook-trigger) które można uruchomić z alertu przy użyciu pakietów webhook. 
- Skalowanie [automatyczne](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) umożliwia dynamiczne zwiększanie i zmniejszanie zasobów obliczeniowych na podstawie zebranych metryk.

## <a name="prepare-dashboards-and-workbooks"></a>Przygotowywanie pulpitów nawigacyjnych i skoroszytów
Zapewnienie, że programowanie i operacje mają dostęp do tych samych danych telemetrycznych i narzędzi, umożliwia im wyświetlanie wzorców w całym środowisku i minimalizowanie średniego czasu do wykrycia (MTTD) i średniego czasu przywracania (MTTR).

- Przygotowywanie [niestandardowych pulpitów nawigacyjnych](../azure-monitor/learn/tutorial-app-dashboards.md) na podstawie typowych metryk i dzienników dla różnych ról w organizacji. Pulpity nawigacyjne mogą łączyć dane ze wszystkich zasobów platformy Azure.
- Przygotuj [skoroszyty,](../azure-monitor/app/usage-workbooks.md) aby zapewnić wymianę wiedzy między programami a operacjami. Mogą one być przygotowywane jako raporty dynamiczne z wykresami metrycznymi i zapytaniami dziennika, a nawet jako przewodniki rozwiązywania problemów przygotowane przez deweloperów pomagających działowi obsługi klienta lub operacjom w rozwiązywaniu podstawowych problemów.

## <a name="continuously-optimize"></a>Ciągła optymalizacja
 Monitorowanie jest jednym z podstawowych aspektów popularnej filozofii Build-Measure-Learn, która zaleca ciągłe śledzenie wskaźników KPI i metryk zachowania użytkownika, a następnie dążenie do optymalizacji ich poprzez planowanie iteracji. Usługa Azure Monitor ułatwia zbieranie metryk i dzienników istotnych dla twojej firmy oraz dodawanie nowych punktów danych w następnym wdrożeniu zgodnie z wymaganiami.

- Narzędzia w usłudze Application Insights [umożliwia śledzenie zachowań i zaangażowania użytkowników końcowych.](../azure-monitor/learn/tutorial-users.md)
- Analiza [wpływu](../azure-monitor/app/usage-impact.md) ułatwia ustalanie priorytetów obszarów, na których należy skupić się, aby przejść do ważnych kluczowych wskaźników wydajności.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o składnikach różnicy [w usłudze Azure Monitor](overview.md).
- [Dodaj ciągłe monitorowanie](../azure-monitor/app/continuous-monitoring.md) do potoku wydania.
