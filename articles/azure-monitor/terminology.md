---
title: Aktualizacje terminologii platformy Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano ostatnie terminologii zmiany usług monitorowania platformy Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847988"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Usługa Azure Monitor nazewnictwa i terminologia zmiany
Istotne zmiany zostały dokonane do usługi Azure Monitor niedawno z różnymi usługami konsolidowana Aby uprościć monitorowanie dla klientów platformy Azure. W tym artykule opisano ostatnie nazwy i terminologii zmian w dokumentacji usługi Azure Monitor.

## <a name="february-2019---log-analytics-terminology"></a>Lutego 2019 - terminologia usługi Log Analytics
Po konsolidacji różnych usług w ramach usługi Azure Monitor Przenosimy kolejnego kroku, modyfikując terminologii w naszej dokumentacji, aby lepiej opisać, usługa Azure Monitor i jego różnych składników. 

### <a name="log-analytics"></a>Log Analytics
Dzienników platformy Azure Monitor, dane są nadal przechowywane w obszarze roboczym usługi Log Analytics nadal pobierane i analizowane przez tę samą usługę Log Analytics, ale Zmieniamy termin _usługi Log Analytics_ w wielu miejscach do _dzienniki usługi Azure Monitor_ . Pojęcie to lepiej odzwierciedla swojej roli w usłudze Azure Monitor i zapewnia lepsze spójność [metryk w usłudze Azure Monitor](platform/data-platform-metrics.md).

Termin _dziennika analizy_ teraz dotyczy ona głównie strony w witrynie Azure portal umożliwia pisanie i uruchamianie zapytań i analizować dane dzienników. Jest odpowiednikiem funkcjonalności [Eksploratora metryk](platform/metrics-charts.md), czyli stronę w witrynie Azure portal umożliwia analizowanie danych metryk.

### <a name="log-analytics-workspaces"></a>Obszary robocze usługi log Analytics
[Obszary robocze](platform/manage-access.md) , przechowywania danych dziennika w usłudze Azure Monitor nadal są określane jako obszarów roboczych usługi Log Analytics. **Usługi Log Analytics** menu w witrynie Azure portal została zmieniona na **obszarów roboczych usługi Log Analytics** i gdzie można [utworzyć nowych obszarów roboczych](learn/quick-create-workspace.md) i skonfigurować źródła danych. Analizowanie dzienników i innych danych monitorowania w **usługi Azure Monitor** i konfigurowanie obszaru roboczego w **obszarów roboczych usługi Log Analytics**.

### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Rozwiązania do zarządzania](insights/solutions.md) nazwa została zmieniona na _rozwiązania do monitorowania_, która lepiej opisano ich funkcje.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Sierpnia 2018 - Konsolidacja monitorowania usług w usłudze Azure Monitor
Usługa log Analytics i usługi Application Insights ma został dołączony do usługi Azure Monitor zapewnienie pojedynczego zintegrowane rozwiązanie do monitorowania zasobów platformy Azure i środowisk hybrydowych. Funkcja nie została usunięta z tych usług, a użytkownicy mogą wykonywać te same scenariusze, które są zawsze zakończyła się bez utraty lub naruszenie żadnych funkcji.

Dokumentacja dla każdej z tych usług został dołączony do jednego zestawu elementów zawartości dla usługi Azure Monitor. Pomoże to czytnika w znajdowaniu całą zawartość dla określonego scenariusza monitorowania w jednym miejscu zamiast konieczności odwoływać się do wielu zestawów zawartości. Zgodnie z rozwojem skonsolidowane usługi zawartość będzie stają się bardziej zintegrowane.

Inne funkcje, które były traktowane jako część usługi Log Analytics, takich jak agentów i widoki zostały również założony jako funkcje usługi Azure Monitor. Ich funkcjonalność nie zmienił się inne niż potencjalne ulepszenia do swojego środowiska pracy w witrynie Azure portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Kwietnia 2018 r. — marki wycofania z pakietu Operations Management Suite
Operations Management Suite (OMS) to uwzględniała następujące usługi zarządzania platformy Azure do celów licencjonowania:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Nowy cennik został wprowadzony w przypadku tych usług](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), i tworzenia pakietów usługi OMS nie jest już dostępna dla nowych klientów. Zmieniono żadne z usług, które były częścią pakietu OMS z wyjątkiem konsolidacji w usłudze Azure Monitor opisanych powyżej. 




## <a name="next-steps"></a>Kolejne kroki

- Odczyt [Omówienie usługi Azure Monitor](overview.md) , który opisuje jego różne składniki i funkcje.
- Dowiedz się więcej o [przejścia portalu pakietu OMS](../log-analytics/log-analytics-oms-portal-transition.md).