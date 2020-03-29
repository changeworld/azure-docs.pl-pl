---
title: Metryki i alerty w usłudze Azure Traffic Manager
description: W tym artykule zapoznaj się z metrykami i alertami dostępnymi dla usługi Traffic Manager na platformie Azure.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938584"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Dane i alerty menedżera ruchu

Usługa Traffic Manager zapewnia równoważenie obciążenia oparte na systemie DNS, które obejmuje wiele metod routingu i opcji monitorowania punktów końcowych. W tym artykule opisano metryki i skojarzone alerty, które są dostępne dla klientów. 

## <a name="metrics-available-in-traffic-manager"></a>Wskaźniki dostępne w Usłudze Traffic Manager 

Usługa Traffic Manager udostępnia następujące metryki na podstawie profilu, których klienci mogą używać do zrozumienia ich użycia Menedżera ruchu i stanu ich punktów końcowych w tym profilu.  

### <a name="queries-by-endpoint-returned"></a>Zwracane zapytania według punktu końcowego
Ta [metryka służy](../azure-monitor/platform/metrics-supported.md) do wyświetlania liczby zapytań przetwarzanych przez profil usługi Traffic Manager w określonym okresie. Można również wyświetlić te same informacje na poziomie punktu końcowego ziarnistość, która pomaga zrozumieć, ile razy punkt końcowy został zwrócony w odpowiedzi na zapytania z usługi Traffic Manager.

W poniższym przykładzie rysunek 1 wyświetla wszystkie odpowiedzi na zapytania, które zwraca profil usługi Traffic Manager. 

  
![Widok zbiorczy wszystkich zapytań](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Rysunek 1: Widok agregatu ze wszystkimi zapytaniami*
  
Rysunek 2 wyświetla te same informacje, jednak jest podzielony według punktów końcowych. W rezultacie można zobaczyć ilość odpowiedzi na zapytania, w którym zwrócono określony punkt końcowy.

![Metryki menedżera ruchu — widok podzielony woluminu zapytania na punkt końcowy](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Rysunek 2: Widok podziału z woluminem zapytania wyświetlanym na zwrócony punkt końcowy*

## <a name="endpoint-status-by-endpoint"></a>Stan punktu końcowego według punktu końcowego
Ta [metryka](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) służy do zrozumienia stanu kondycji punktów końcowych w profilu. Potrzeba dwóch wartości:
 - użyć **1,** jeśli punkt końcowy jest w górę.
 - użyć **0,** jeśli punkt końcowy jest w dół.

Ta metryka może być wyświetlana jako wartość zagregowana reprezentująca stan wszystkich metryk (rysunek 3) lub może być podzielona (patrz rysunek 4), aby pokazać stan określonych punktów końcowych. Jeśli pierwszy, jeśli poziom agregacji jest wybrany jako **Avg**, wartość tej metryki jest średnia arytmetyczna stanu wszystkich punktów końcowych. Na przykład jeśli profil ma dwa punkty końcowe i tylko jeden jest w dobrej kondycji, ta metryka ma wartość **0,50,** jak pokazano na rysunku 3. 


![Metryki menedżera ruchu — widok złożony stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Rysunek 3: Widok złożony metryki stanu punktu końcowego – wybrana agregacja "Średnia"*


![Metryki menedżera ruchu — widok podzielony stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Rysunek 4: Widok podziału metryk stanu punktu końcowego*

Te metryki można używać za pośrednictwem [portalu usługi Azure Monitor,](../azure-monitor/platform/metrics-supported.md) [interfejsu API REST,](https://docs.microsoft.com/rest/api/monitor/) [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor)i programu Azure [PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)lub za pośrednictwem sekcji metryki usługi Traffic Manager w portalu.

## <a name="alerts-on-traffic-manager-metrics"></a>Alerty dotyczące metryk programu Traffic Manager
Oprócz przetwarzania i wyświetlania metryk z usługi Traffic Manager usługa Azure Monitor umożliwia klientom konfigurowanie i odbieranie alertów skojarzonych z tymi metrykami. Można wybrać, jakie warunki muszą być spełnione w tych metrykach, aby alert występuje, jak często te warunki muszą być monitorowane i jak alerty powinny być wysyłane do Ciebie. Aby uzyskać więcej informacji, zobacz [dokumentację alertów usługi Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Monitor](../azure-monitor/platform/metrics-supported.md)
- Dowiedz się, jak [utworzyć wykres za pomocą usługi Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
