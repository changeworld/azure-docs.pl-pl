---
title: Platforma danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Monitorowanie danych zbieranych przez usługi Azure Monitor jest podzielony metryki, które są uproszczone i może obsługiwać w scenariuszach w czasie rzeczywistym i dzienników, które są używane do zaawansowanej analizy.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808931"
---
# <a name="azure-monitor-data-platform"></a>Platforma danych w usłudze Azure Monitor

Włączenie observability we współczesnych złożonych środowisk obliczeniowych uruchamiania aplikacji rozproszonych, które opierają się na chmurze i usług lokalnych wymaga zbierania danych operacyjnych z każdej warstwy i każdy składnik systemu rozproszonego. Musisz mieć możliwość wykonania szczegółowych informacji na tych danych i konsolidować je w jedną taflę szkła z różnych perspektyw do obsługi wielu uczestników projektu w Twojej organizacji.

[Usługa Azure Monitor](../overview.md) zbiera i agregują dane z wielu źródeł do wspólną platformę danych, w której może służyć do analiz, wizualizacji i alertów. Zapewnia spójne środowisko na podstawie danych z wielu źródeł, co daje wgląd we wszystkich monitorowanych zasobów, a nawet w przypadku danych z innych usług, które przechowują dane w usłudze Azure Monitor.


![Omówienie usługi Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Observability danych w usłudze Azure Monitor
Metryk, dzienników i ślady rozproszone są często nazywane trzy filary observability. Są to różne rodzaje danych, który narzędzia do monitorowania należy zbierać i analizować, aby zapewnić wystarczającą observability monitorowanych systemu. Observability można osiągnąć przez skorelowanie danych z wielu filary i agregowanie danych w obrębie całego zestawu zasobów są monitorowane. Ponieważ usługa Azure Monitor są przechowywane dane z wielu źródeł, które razem, dane mogą zostać skorelowane i analizować, korzystając ze wspólnego zestawu narzędzi. Jest także danych, w wielu subskrypcji platformy Azure i dzierżaw, oprócz hostowania danych dla innych usług.

Zasoby platformy Azure generuje znacznej ilości danych monitorowania. Usługa Azure Monitor konsoliduje dane oraz dane monitorowania z innych źródeł w jeden metryk lub dzienników platformy. Każdy jest zoptymalizowany pod kątem konkretnych scenariuszach monitorowania, a każdy obsługuje różne funkcje w usłudze Azure Monitor. Funkcje, takie jak analiza danych, wizualizacje lub alerty wymagają zrozumienie różnic, dzięki czemu można zaimplementować wymaganego scenariusza w najbardziej skuteczny i niskie koszty. Szczegółowe informacje w usłudze Azure Monitor, takich jak [usługi Application Insights](../app/app-insights-overview.md) lub [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) mieć narzędzia do analizy, które pozwalają skoncentrować się na danym scenariuszu monitorowania bez konieczności orientowania się różnice między tymi dwoma typami danych. 


### <a name="metrics"></a>Metryki
[Metryki](data-platform-metrics.md) są wartości liczbowe, które opisują niektóre aspekty systemu w określonym punkcie w czasie. Są zbierane w regularnych odstępach czasu i są identyfikowane przez sygnaturę czasową, nazwę, wartość i jedną lub więcej etykiet definiujące. Metryki można agregować przy użyciu różnych algorytmów, w porównaniu z innymi metrykami i analizowane pod kątem trendy wraz z upływem czasu. 

Metryki w usłudze Azure Monitor są przechowywane w bazie danych szeregów czasowych, który jest zoptymalizowany pod kątem analizowania danych z sygnaturami czasowymi. To sprawia, że metryki szczególnie odpowiednie dla alertów i szybkie wykrywanie problemów. One informujące o tym, jak działa system, ale zwykle muszą być łączone z dzienników do identyfikowania głównych przyczyn problemów.

Metryki są dostępne do analizy interakcyjnej w witrynie Azure portal, za pomocą [Eksploratora metryk](../app/metrics-explorer.md). Mogą być dodawane do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md) dla wizualizacji w połączeniu z innymi danymi i używane do niemal w czasie rzeczywistym [alerty](alerts-metric.md).

Przeczytaj więcej na temat usługi Azure Monitor metryk, takich jak swoich źródeł danych w [metryk w usłudze Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Dzienniki
[Dzienniki](data-platform-logs.md) są zdarzenia, które wystąpiły w ramach systemu. One może zawierać różne rodzaje danych i może mieć strukturę lub dowolnego tekstu w formularzu z sygnaturą czasową. Zdarzenia w środowisku wygenerować wpisów dziennika, a system pod dużym obciążeniem zazwyczaj wygeneruje więcej wolumin dziennika może być sporadycznie utworzony.

Dzienniki w usłudze Azure Monitor są przechowywane w obszarze roboczym usługi Log Analytics, która jest oparta na [Eksploratora danych usługi Azure](/azure/data-explorer/) zapewniającą aparatu zaawansowane analizy i [zaawansowany język zapytań](/azure/kusto/query/). Dzienniki zwykle zawierać informacje wystarczające do zapewnienia pełnego kontekstu problemu są identyfikowane i są przydatne do identyfikowania przypadków głównych problemów.

> [!NOTE]
> Jest ważne rozróżnić dzienników monitora platformy Azure i źródła danych dziennika na platformie Azure. Na przykład zdarzeń na poziomie subskrypcji na platformie Azure są zapisywane w [dziennika aktywności](activity-logs-overview.md) można wyświetlić z menu usługi Azure Monitor. Większość zasobów zapisze informacje operacyjne, aby [dziennik diagnostyczny](diagnostic-logs-overview.md) który może przekazywać do innej lokalizacji. Dzienniki platformy Azure Monitor to platforma danych dziennika, która gromadzi Dzienniki aktywności i dzienników diagnostycznych oraz innych danych monitorowania w celu zapewnienia szczegółowej analizy cały zestaw zasobów.


 Możesz pracować z [rejestrowania zapytań](../log-query/log-query-overview.md) interaktywnie przy użyciu [usługi Log Analytics](../log-query/portals.md) w witrynie Azure portal lub Dodaj wyniki do [pulpitu nawigacyjnego platformy Azure](../learn/tutorial-app-dashboards.md) dla wizualizacji w połączeniu z inne dane. Można również utworzyć [alerty dzienników](alerts-log.md) która wyzwoli alert, w oparciu o wyniki kwerendy harmonogramu.

Przeczytaj więcej na temat dzienników monitora platformy Azure, łącznie z ich źródła danych w [dzienników w usłudze Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Rozproszone dane śledzenia
Ślady są serii powiązanych zdarzeń, które należy wykonać żądania użytkownika przez rozproszony system. Mogą one używane, aby określić zachowanie kodu aplikacji oraz wydajności różnych transakcji. Gdy dzienniki często zostanie utworzona przez poszczególne składniki systemu rozproszonego, śledzenia mierzy działanie i wydajność aplikacji przez cały zestaw składników.

Włączono rozproszonego śledzenia w usłudze Azure Monitor [zestawu SDK usługi Application Insights](../app/distributed-tracing.md), a dane śledzenia są przechowywane z innymi danymi dziennika aplikacji zbierane przez usługę Application Insights. Dzięki temu dostępne dla tego samego narzędzia analizy jako inne dane dziennika, w tym dziennika zapytań, pulpity nawigacyjne i alerty.

Przeczytaj więcej na temat rozproszonego śledzenia w [co to jest rozproszonego śledzenia?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Porównanie usługi Azure Monitor metryk i dzienników

W poniższej tabeli porównano metryk i dzienników w usłudze Azure Monitor.

| Atrybut  | Metryki | Dzienniki |
|:---|:---|:---|
| Korzyści | Uproszczona i możliwością scenariuszy niemal w czasie rzeczywistym, takich jak alerty. Idealne rozwiązanie dla szybkiego wykrywania problemów. | Przeanalizowane dzięki zaawansowanemu języka zapytań. Idealne rozwiązanie dla szczegółowej analizy i identyfikowania głównych przyczyn. |
| Dane | Tylko wartości liczbowe | Dane tekstowe lub liczbowe |
| Struktura | Standardowy zestaw właściwości, w tym przedziału czasu, zasobów, które są monitorowane, wartość liczbowa. Niektóre metryki obejmują wiele wymiarów dla dalszych definicji. | Unikatowy zestaw właściwości, w zależności od typu dziennika. |
| Collection | Zebrane w regularnych odstępach czasu. | Mogą być zbierane sporadycznie, ponieważ zdarzenia wyzwalacza rekord ma zostać utworzony. |
| Wyświetl w witrynie Azure portal | Eksplorator metryk | Log Analytics |
| Źródła danych zawierają | Metryki platformy zbierane z zasobów platformy Azure.<br>Aplikacje monitorowane przez usługę Application Insights.<br>Niestandardowe zdefiniowane przez aplikację lub interfejsu API. | Aplikacja i dzienniki diagnostyczne.<br>Rozwiązania do monitorowania.<br>Agenci i rozszerzenia maszyn wirtualnych.<br>Aplikacja żądań i wyjątków.<br>Usługa Azure Security Center.<br>Moduł zbierający dane interfejsu API. |

## <a name="collect-monitoring-data"></a>Zbieranie danych monitorowania
Różne [źródeł danych dla usługi Azure Monitor](data-sources.md) zapisze obszar roboczy usługi Log Analytics (Dzienniki) lub bazy danych metryk usługi Azure Monitor (metryki) lub obu. Niektóre źródła będzie zapisywać bezpośrednio do tych magazynów danych, podczas gdy inne osoby mogą zapisać do innej lokalizacji, takiej jak usługa Azure storage i wymagają pewnych czynności konfiguracyjnych do wypełniania dzienników i metryk. 

Zobacz [metryk w usłudze Azure Monitor](data-platform-metrics.md) i [dzienników w usłudze Azure Monitor](data-platform-logs.md) listę różnych źródeł danych służące do wypełniania każdego typu.


## <a name="stream-data-to-external-systems"></a>Data Stream z systemami zewnętrznymi
Oprócz używania narzędzi na platformie Azure do analizowania danych monitorowania, może być wymagane ją przesłać do zewnętrznego narzędzia, takie jak informacje o zabezpieczeniach i produktów do zarządzania (SIEM) zdarzenia. Przekazywanie ten jest zazwyczaj wykonywane bezpośrednio z monitorowanych zasobów za pomocą [usługi Azure Event Hubs](/azure/event-hubs/). Niektóre źródła można skonfigurować do wysyłania danych bezpośrednio do Centrum zdarzeń za pomocą innego procesu, takie jak aplikacja logiki w celu pobrania niezbędnych danych. Zobacz [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](stream-monitoring-data-event-hubs.md) Aby uzyskać szczegółowe informacje.



## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [metryk w usłudze Azure Monitor](data-platform-metrics.md).
- Przeczytaj więcej na temat [dzienników w usłudze Azure Monitor](data-platform-logs.md).
- Dowiedz się więcej o [dostępne dane monitorowania](data-sources.md) dla różnych zasobów na platformie Azure.
