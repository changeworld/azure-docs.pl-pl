---
title: Aktualizacje terminologii usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano ostatnie zmiany terminologii wprowadzone w usługach monitorowania platformy Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274140"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Zmiany w nazewnictwie i terminologii usługi Azure Monitor
W ostatnim czasie wprowadzono istotne zmiany w usłudze Azure Monitor, a różne usługi są konsolidowane w celu uproszczenia monitorowania dla klientów platformy Azure. W tym artykule opisano ostatnie zmiany nazwy i terminologii w dokumentacji usługi Azure Monitor.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Październik 2019 - Dziennik diagnostyczny do dziennika zasobów
"Dzienniki diagnostyczne" zostały zmienione na "dzienniki zasobów", aby lepiej dopasować to, co jest faktycznie zbierane. Termin "ustawienia diagnostyczne" pozostaje ten sam.  

## <a name="february-2019---log-analytics-terminology"></a>Luty 2019 - Terminologia log analytics
Po konsolidacji różnych usług w ramach usługi Azure Monitor, robimy kolejny krok, modyfikując terminologii w naszej dokumentacji, aby lepiej opisać usługę Azure Monitor i jej różnych składników. 

### <a name="log-analytics"></a>Log Analytics
Dane dziennika usługi Azure Monitor są nadal przechowywane w obszarze roboczym usługi Log Analytics i są nadal zbierane i analizowane przez tę samą usługę analizy dzienników, ale zmieniamy termin _Analiza dzienników_ w wielu miejscach na _dzienniki usługi Azure Monitor._ Termin ten lepiej odzwierciedla jego rolę w usłudze Azure Monitor i zapewnia lepszą spójność z [metrykami w usłudze Azure Monitor.](platform/data-platform-metrics.md)

Termin _analiza dziennika_ teraz dotyczy przede wszystkim strony w witrynie Azure Portal używane do pisania i uruchamiania zapytań i analizowania danych dziennika. Jest to funkcjonalny odpowiednik [eksploratora metryk](platform/metrics-charts.md), który jest stroną w witrynie Azure portal używane do analizowania danych metryk.

### <a name="log-analytics-workspaces"></a>Obszary robocze usługi Log Analytics
[Obszary robocze,](platform/manage-access.md) w których przechowują dane dziennika w usłudze Azure Monitor, są nadal nazywane obszarami roboczymi usługi Log Analytics. Menu **Usługa Log Analytics** w witrynie Azure portal została zmieniona na obszary **robocze usługi Log Analytics** i jest miejscem, w którym można tworzyć nowe obszary [robocze](learn/quick-create-workspace.md) i konfigurować źródła danych. Analizuj dzienniki i inne dane monitorowania w **usłudze Azure Monitor** i konfiguruj obszar roboczy w **obszarach roboczych usługi Log Analytics.**

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Rozwiązania do zarządzania](insights/solutions.md) zostały zmienione na _rozwiązania monitorujące,_ które lepiej opisują ich funkcjonalność.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Sierpień 2018 r. — konsolidacja usług monitorowania w usłudze Azure Monitor
Usługa Log Analytics i usługa Application Insights zostały skonsolidowane w usłudze Azure Monitor w celu zapewnienia jednego zintegrowanego środowiska monitorowania zasobów platformy Azure i środowisk hybrydowych. Żadna funkcjonalność nie została usunięta z tych usług, a użytkownicy mogą wykonywać te same scenariusze, które zawsze zostały ukończone bez utraty lub naruszenia jakichkolwiek funkcji.

Dokumentacja dla każdej z tych usług została skonsolidowana w jeden zestaw zawartości dla usługi Azure Monitor. Pomoże to czytelnikowi w znalezieniu całej zawartości dla określonego scenariusza monitorowania w jednej lokalizacji, w przeciwieństwie do konieczności odwoływania się do wielu zestawów zawartości. Wraz z rozwojem skonsolidowanej usługi zawartość stanie się bardziej zintegrowana.

Inne funkcje, które zostały uznane za część usługi Log Analytics, takie jak agenci i widoki, również zostały przesunięty jako funkcje usługi Azure Monitor. Ich funkcjonalność nie zmieniła się inne niż potencjalne ulepszenia ich środowiska w witrynie Azure portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Kwiecień 2018 - Wycofanie marki Operations Management Suite
Pakiet zarządzania operacjami (OMS) był pakietem następujących usług zarządzania platformy Azure do celów licencjonowania:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Wprowadzono nowe ceny dla tych usług,](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)a pakiet oms nie jest już dostępny dla nowych klientów. Żadna z usług, które były częścią usługi OMS zostały zmienione, z wyjątkiem konsolidacji w usłudze Azure Monitor opisane powyżej. 




## <a name="next-steps"></a>Następne kroki

- Przeczytaj [omówienie usługi Azure Monitor,](overview.md) który opisuje jego różnych składników i funkcji.
- Dowiedz się więcej o [przejściu portalu OMS](../log-analytics/log-analytics-oms-portal-transition.md).
