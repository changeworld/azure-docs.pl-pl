---
title: Tworzenie alertów wydajności dzięki usłudze Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć niestandardowe alerty platformy Azure, oparta na zapytaniach dzienników pamięci i mocy obliczeniowej Procesora kontenerów z usługi Azure Monitor.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: magoedte
ms.openlocfilehash: c275d50ab927895eca3aa018b71ab6989a4dde5a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238858"
---
# <a name="how-to-set-up-alerts-in-azure-monitor-for-container-performance-problems"></a>Jak skonfigurować alerty w usłudze Azure Monitor dla kontenera problemy z wydajnością
Usługa Azure Monitor dla kontenerów monitorów wydajności obciążeń kontenerów wdrożona albo w usłudze Azure Container instances lub zarządzane klastry Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). 

W tym artykule opisano sposób włączania alerty, gdy wykorzystanie procesora i pamięci na węzłach klastra przekroczy zdefiniowany próg.

## <a name="create-alert-on-cluster-cpu-or-memory-utilization"></a>Utwórz alert w klastrze procesora CPU lub użycie pamięci przez program
Aby alert, gdy wykorzystanie procesora CPU lub pamięci jest wysoka dla klastra, należy utworzyć regułę alertu pomiaru metryki, która opiera się poza dziennika kwerendy dostarczone. Zapytania porównania Data i godzina obecny przy użyciu `now` operatora i zacznie godzinę wstecz. Wszystkie daty, przechowywane w usłudze Azure Monitor dla kontenerów są w formacie UTC.  

Przed rozpoczęciem, jeśli użytkownik nie jest zaznajomiony z alertami w usłudze Azure Monitor, zobacz [Przegląd alertów na platformie Microsoft Azure](../platform/alerts-overview.md). Aby dowiedzieć się więcej o alertach za pomocą dziennika zapytań, zobacz [alerty dzienników w usłudze Azure Monitor](../platform/alerts-unified-log.md)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **Monitor** w okienku po lewej stronie w witrynie Azure portal. W obszarze **Insights** zaznacz **kontenery**.    
3. Z **monitorowania klastrów** , a następnie wybierz klaster z listy, klikając nazwę klastra.
4. W okienku po lewej stronie w obszarze **monitorowanie** zaznacz **dzienniki** można otworzyć usługi Azure Monitor rejestruje strony, która umożliwia pisanie i wykonywania zapytań w usłudze Azure Log Analytics.
5. Na **dzienniki** kliknij **+ Nowa reguła alertu**.
6. W obszarze **warunek** , kliknij przycisk pod warunkiem wstępnie zdefiniowanych dziennik niestandardowy **jest zawsze wtedy, gdy dziennik wyszukiwania niestandardowego <logic undefined>** . **Przeszukiwania dzienników niestandardowych** typu sygnału jest automatycznie wybierany do nas, ponieważ firma Microsoft zainicjowane, tworzenie reguły alertu bezpośrednio ze strony dzienniki usługi Azure Monitor.  
7. Wklej pojedynczo zapytań poniżej do **zapytania wyszukiwania** pola. Następujące zapytanie oblicza średnie wykorzystanie procesora CPU jako średnia wykorzystanie procesora CPU węzłach Członkowskich co minutę.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'cpuCapacityNanoCores';
    let usageCounterName = 'cpuUsageNanoCores';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

    Następujące zapytanie oblicza średnie wykorzystanie pamięci jako średnia wykorzystania pamięci węzłach Członkowskich co minutę.

    ```
    let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let capacityCounterName = 'memoryCapacityBytes';
    let usageCounterName = 'memoryRssBytes';
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    // cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
    | distinct ClusterName, Computer
    | join hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime
       | where TimeGenerated >= startDateTime
       | where ObjectName == 'K8SNode'
       | where CounterName == capacityCounterName
       | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
       | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
    ) on Computer
    | join kind=inner hint.strategy=shuffle (
       Perf
       | where TimeGenerated < endDateTime + trendBinSize
       | where TimeGenerated >= startDateTime - trendBinSize
       | where ObjectName == 'K8SNode'
       | where CounterName == usageCounterName
       | project Computer, UsageValue = CounterValue, TimeGenerated
    ) on Computer
    | where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
    | project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
    | summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
    ```

8. Skonfiguruj alert przy użyciu następujących informacji:

    a. Z listy rozwijanej **Na podstawie** wybierz pozycję **Pomiar metryki**. Pomiar metryki utworzy alert dla każdego obiektu w zapytaniu z wartością, która przekracza określony próg.  
    b. Dla **warunek**, wybierz opcję **większa** i wprowadź **75** jako początkowej, podstawowej **próg** lub wprowadź wartość, która spełnia Twoje kryteria.  
    c. W obszarze **wyzwalacz alertu na podstawie** zaznacz **kolejne naruszenia** i z listy rozwijanej wybierz opcję **większa** wprowadź wartość **2**.  
    d. W obszarze **Evaluated na podstawie** sekcję, zmodyfikuj **okres** wartości do 60 minut. Reguła będzie uruchamiany co pięć minut i zwracają rekordy, które zostały utworzone w ciągu ostatniej godziny od bieżącego czasu. Ustawienie okresu na szerszy zakres może spowodować potencjalne opóźnienie danych i gwarantuje, że zapytanie zwróci dane w celu uniknięcia fałszywie negatywnego wyniku, jeśli alert nigdy nie został uruchomiony. 

9. Kliknij przycisk **Gotowe**, aby zakończyć tworzenie reguły alertu.
10. Podaj nazwę alertu w **Nazwa reguły alertu** pola. Określ **opis** ze szczegółami dotyczącymi szczegółowe informacje o alercie, a następnie wybierz odpowiednią ważność z opcji dostępnych.
11. Aby aktywować regułę alertu od razu po utworzeniu, zaakceptuj wartość domyślną w polu **Włącz regułę po utworzeniu**.
12. W ostatnim kroku, wybierz istniejącą lub Utwórz nową **grupy akcji**, dzięki któremu podjęcie te same akcje każdorazowo alert jest wyzwalany i mogą być używane dla każdej reguły, należy zdefiniować. Konfigurowanie zależnie od działu IT lub DevOps operacje zarządza zdarzenia. 
13. Kliknij przycisk **Utwórz regułę alertu**, aby zdefiniować regułę alertu. Rozpoczyna ona działanie od razu.

## <a name="next-steps"></a>Kolejne kroki

* Sprawdź, co [dziennika przykłady zapytań](container-insights-analyze.md#search-logs-to-analyze-data) Dowiedz się więcej o wstępnie zdefiniowanych zapytań lub przykładów, aby ocenić i użyć lub dostosować w innych sytuacjach alertu. 
* Aby kontynuować, jak używać usługi Azure Monitor i monitorowanie innych aspektów wybranego działania klastra usługi AKS, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md)
