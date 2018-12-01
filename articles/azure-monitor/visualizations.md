---
title: Wizualizowanie danych z usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Zawiera podsumowanie informacji o dostępnych metod w celu wizualizacji danych przechowywanych w usłudze Azure Monitor, włącznie z danymi ze sklepu metryki i usługi Log Analytics.
author: bwren
manager: carmonm
editor: ''
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/15/2018
ms.author: bwren
ms.openlocfilehash: b2011003306e72ac9fb243b640e388e6f3d5e7d3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722211"
---
# <a name="visualizing-data-from-azure-monitor"></a>Wizualizowanie danych z usługi Azure Monitor
Ten artykuł zawiera podsumowanie dostępnych metod w celu wizualizacji danych przechowywanych w usłudze Azure Monitor. Obejmuje to [metryki w magazynie Azure metryki](../azure-monitor/platform/data-collection.md#metrics) i [rejestrować dane w usłudze Log Analytics](../azure-monitor/platform/data-collection.md#logs). 

Wizualizacji, takich jak wykresy i diagramy ułatwia analizowanie danych monitorowania, aby przejść do szczegółów problemów i zidentyfikować wzorce. W zależności od używanego narzędzia może również mieć możliwość udostępniać wizualizacje innym użytkownikom w organizacji i poza nią.

## <a name="azure-dashboards"></a>Pulpitów nawigacyjnych platformy Azure
[Pulpitów nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards.md) to technologia dashboarding głównej na platformie Azure. Są one szczególnie użyteczne w dostarczaniu jedną taflę szkła za pośrednictwem infrastruktury platformy Azure i usług, co pozwala szybko zidentyfikować ważne problemy.

![Pulpit nawigacyjny](media/visualizations/dashboard.png)

### <a name="advantages"></a>Zalety
- Głęboka integracja na platformie Azure. Wizualizacje można przypiąć do pulpitów nawigacyjnych z wielu stronach platformy Azure, łącznie z Eksploratora metryk, usługi Log Analytics i usługi Application Insights.
- Obsługuje zarówno metryk i dzienników.
- Połącz dane z wielu źródeł, takich jak dane wyjściowe z [Eksploratora metryk](../monitoring-and-diagnostics/monitoring-metric-charts.md), [zapytań usługi Log Analytics](../log-analytics/log-analytics-queries.md), i [mapuje](../application-insights/app-insights-app-map.md) i [dostępności]()w usłudze Application Insights.
- Opcja osobistym lub udostępnione pulpity nawigacyjne. Zintegrowana z platformą Azure [uwierzytelniania opartego na rolach (RBAC)](../role-based-access-control/overview.md).
- Automatyczne odświeżanie. Odświeżanie metryk zależy od zakresu czasu z co najmniej pięć minut. Odśwież dzienniki na jedną minutę.
- Metryki sparametryzowanego pulpity nawigacyjne z sygnatury czasowej i parametry niestandardowe.
- Opcje układu.
- Tryb pełnoekranowy.


### <a name="limitations"></a>Ograniczenia
- Ograniczone kontroli nad wizualizacje usługi Log Analytics przy użyciu tabel danych nie są obsługiwane. Łączna liczba serii danych jest ograniczona do 10 z dalszych serii danych zgrupowane w obszarze _innych_ przedziału.
- Nie niestandardowych parametrów obsługę wykresy usługi Log Analytics.
- Log Analytics wykresy są ograniczone do ostatnich 30 dni.
- Log Analytics wykresy tylko można przypiąć do udostępnionych pulpitów nawigacyjnych.
- Nie interakcję z danymi pulpitu nawigacyjnego.
- Ograniczone kontekstowych Przechodzenie do szczegółów.

## <a name="azure-monitor-views"></a>Widoki usługi Azure Monitor
[Widoki w usłudze Azure Monitor](../azure-monitor/platform/view-designer.md) pozwalają na tworzenie niestandardowych wizualizacji przy użyciu dane dzienników przechowywane w usłudze Log Analytics. Są one używane przez [rozwiązania do monitorowania](../azure-monitor/insights/solutions.md) przedstawienie danych pobierają.

![Widok](media/visualizations/view.png)

### <a name="advantages"></a>Zalety
- Bogate wizualizacje danych usługi Log Analytics.
- Eksportowanie i importowanie widoków, aby przenieść je do innych grup zasobów i subskrypcji.
- Integruje się z dziennik analityczny model zarządzania obszarami roboczymi i rozwiązania do monitorowania.
- [Filtry](../azure-monitor/platform/view-designer-filters.md) niestandardowych parametrów.
- Interakcyjne obsługuje wielopoziomowe testowania odzyskiwania po awarii w (Widok przejście do innego widoku)

### <a name="limitations"></a>Ograniczenia
- Obsługuje dzienniki, ale nie metryki.
- Brak widoków osobistych. Dostępne dla wszystkich użytkowników mających dostęp do obszaru roboczego.
- Nie automatycznego odświeżania.
- Ograniczone opcje układu.
- Brak obsługi zapytań w aplikacjach usługi Application Insights i obszary robocze usługi Log Analytics.
- Zapytania są ograniczone w odpowiedzi rozmiar 8MB, a zapytanie czas wykonywania 110 sekund.



## <a name="application-insights-workbooks"></a>Application Insights skoroszytów
[Skoroszyty](../application-insights/app-insights-usage-workbooks.md) są interaktywne dokumentów, które zawierają szczegółowe informacje w danych, analizy i współpracy w ramach zespołu. Szczegółowe przykłady, w którym skoroszyty są przydatne rozwiązywania problemów przewodniki i postmortem zdarzenia.

![Skoroszyt](media/visualizations/workbook.png)

### <a name="advantages"></a>Zalety
- Obsługuje zarówno metryk i dzienników.
- Obsługuje parametry umożliwiające interaktywne raporty, w którym zaznaczenie elementu w tabeli będzie dynamicznie aktualizować skojarzone wykresy i wizualizacje.
- Przepływ podobne do dokumentu.
- Opcja w przypadku osobistych lub udostępnione skoroszyty.
- Łatwy w użyciu i współpracy przyjaznego środowiska tworzenia.
- Szablony obsługują galerii publicznej szablonów opartych na usłudze GitHub.

### <a name="limitations"></a>Ograniczenia
- Nie automatycznego odświeżania.
- Nie gęstą układ, takich jak pulpity nawigacyjne, które powodują, że skoroszyty mniej przydatne jako jedną taflę szkła. Mają więcej zapewniające lepszy wgląd w dane.


## <a name="power-bi"></a>Power BI
[Usługa Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) jest szczególnie przydatne podczas tworzenia biznesowymi pulpitów nawigacyjnych i raportów, a także raporty, analizy trendów długoterminowych kluczowego wskaźnika wydajności. Możesz [Importuj wyniki zapytania usługi Log Analytics](../log-analytics/log-analytics-powerbi.md) do usługi Power BI zestawu danych, dzięki czemu można wykorzystać jej funkcje, takie jak łączenie danych z różnych źródeł oraz udostępnianie raportów w sieci web i urządzeń przenośnych.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Zalety
- Bogate wizualizacje.
- Ekstensywna interaktywność, w tym powiększenia i filtrowanie krzyżowe.
- Łatwe udostępnianie w całej organizacji.
- Integracja z innymi danymi z wielu źródeł danych.
- Lepsza wydajność za pomocą wyników z pamięci podręcznej w module.


### <a name="limitations"></a>Ograniczenia
- Obsługuje dzienniki, ale nie metryki.
- Brak integracji platformy Azure. Nie można zarządzać pulpitów nawigacyjnych i modeli za pomocą usługi Azure Resource Manager.
- Wyniki zapytania należy zaimportować do modelu usługi Power BI, aby skonfigurować. Ograniczenia dotyczące rozmiaru wyników i odświeżania.
- Odświeżanie danych ograniczone z 8 razy dziennie.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) to otwarta platforma, która excels w operacyjne pulpity nawigacyjne. Jest to szczególnie przydatne w przypadku wykrywanie i izolowanie i sklasyfikowaniu zdarzenia operacyjne. Możesz dodać [wtyczki źródła danych Grafana usługi Azure Monitor](../monitoring-and-diagnostics/monitor-send-to-grafana.md) do subskrypcji platformy Azure, aby wizualizować dane metryki platformy Azure.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Zalety
- Bogate wizualizacje.
- Zaawansowany ekosystem źródeł danych.
- Interakcyjność danych, w tym powiększyć.
- Obsługuje parametry.

### <a name="limitations"></a>Ograniczenia
- Obsługuje metryki, ale niedostępne są dzienniki.
- Brak integracji platformy Azure. Nie można zarządzać pulpitów nawigacyjnych i modeli za pomocą usługi Azure Resource Manager.
- Koszty obsługi dodatkowej infrastruktury Grafana lub dodatkowych kosztów Grafana chmury.


## <a name="build-your-own-custom-application"></a>Tworzenie niestandardowych aplikacji
Można uzyskać dostęp do danych metryk platformy Azure i usługi Log Analytics za pośrednictwem jego interfejsu API za pomocą dowolnego klienta REST, co pozwala na tworzenie własnych niestandardowych witryn sieci Web i aplikacji.

### <a name="advantages"></a>Zalety
- Pełna elastyczność interfejsu użytkownika, wizualizacji, interakcyjność i funkcje.
- Możesz łączyć metryki i rejestrowanie danych z innych źródeł danych.

### <a name="disadvantages"></a>Wady
- Znaczący inżynieryjny nakład pracy wymagany.


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [dane zebrane przez usługi Azure Monitor](../azure-monitor/platform/data-collection.md).
- Dowiedz się więcej o [pulpitów nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards.md).
- Dowiedz się więcej o [widoków w usłudze Azure Monitor](../azure-monitor/platform/view-designer.md).
- Dowiedz się więcej o [skoroszytów w usłudze Application Insights](../application-insights/app-insights-usage-workbooks.md).
- Dowiedz się więcej o [importowania danych dziennika w usłudze Power BI](../log-analytics/log-analytics-powerbi.md).
- Dowiedz się więcej o [wtyczki źródła danych Grafana usługi Azure Monitor](../monitoring-and-diagnostics/monitor-send-to-grafana.md).
