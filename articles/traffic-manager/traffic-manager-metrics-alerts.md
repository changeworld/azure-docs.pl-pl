---
title: Metryki i alerty w usłudze Azure Traffic Manager
description: W tym artykule opisano dostępne metryki dla usługi Traffic Manager na platformie Azure.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: e689398a38030a712ec6beb651131d0424e44476
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071113"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Alerty i metryki usługi Traffic Manager

Usługa Traffic Manager umożliwia równoważenie obciążenia oparte na systemie DNS, który zawiera wiele metod routingu i opcje monitorowania punktu końcowego. W tym artykule opisano metryki i skojarzonych alertach, które są dostępne dla klientów. 

## <a name="metrics-available-in-traffic-manager"></a>Metryk dostępnych w usłudze Traffic Manager 

Usługa Traffic Manager dostarcza następujące metryki na podstawie na profil, że klienci mogą używać, aby zrozumieć ich użycie funkcji usługi Traffic manager i stanu ich punkty końcowe w ramach tego profilu.  

### <a name="queries-by-endpoint-returned"></a>Zapytania według zwracany punkt końcowy
Użyj [ta metryka](../azure-monitor/platform/metrics-supported.md) do wyświetlania liczby zapytań, które profilu usługi Traffic Manager przetwarza w określonym czasie. Można również wyświetlić te same informacje o poziomu szczegółowości punktu końcowego, który pomoże zrozumieć, jak wiele razy punkt końcowy został zwrócony w odpowiedzi na kwerendę z usługi Traffic Manager.

W poniższym przykładzie rysunek 1 przedstawia wszystkie odpowiedzi na kwerendy, które zwraca profilu usługi Traffic Manager. 

  
![Zagregowany widok wszystkich zapytań](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Rysunek 1: Zagregowany widok wszystkich zapytań*
  
Rysunek 2 przedstawia te same informacje, jednak zostanie ona podzielona przez punkty końcowe. W rezultacie możesz zobaczyć wolumin odpowiedzi zapytania, w których został zwrócony określonego punktu końcowego.

![Metryki usługi Traffic Manager — złożenie wolumin zapytań na punkt końcowy](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Rysunek 2: Widok podzielony woluminu zapytania wyświetlanych dla punktu końcowego zwracane*

## <a name="endpoint-status-by-endpoint"></a>Stan punktu końcowego na punkt końcowy
Użyj [ta metryka](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) zrozumienie stanu kondycji punktów końcowych w profilu. Zajmuje się dwie wartości:
 - Użyj **1** Jeśli punkt końcowy jest uruchomiony.
 - Użyj **0** Jeśli punkt końcowy jest wyłączony.

Ta metryka może zostać podany w postaci wartości zagregowanej reprezentuje stan wszystkich metryk (rysunek 3) lub go można podzielić (zobacz rysunek 4) aby wyświetlić stan określonych punktów końcowych. Jeśli poziom agregacji pierwszego, jeśli został wybrany jako **Avg**, wartość ta metryka jest średnią arytmetyczną o stanie wszystkich punktów końcowych. Na przykład, jeśli profil, który ma dwa punkty końcowe i tylko jeden jest w dobrej kondycji, następnie ta metryka ma wartość **0,50** jak pokazano na rysunku 3. 


![Metryki usługi Traffic Manager — złożonego widoku stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Rysunek 3: Widoku złożonego metryki stanu punktu końcowego — agregacji "Średnia" wybrana*


![Metryki usługi Traffic Manager — widok stanu punktu końcowego podzielony](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Rysunek 4: Podziel wgląd w metryki stan punktu końcowego*

Będzie można korzystać z tych metryk za pośrednictwem [usługi Azure Monitor](../azure-monitor/platform/metrics-supported.md)w portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor), i [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights), lub za pomocą sekcji metryki środowisko portalu usługi Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Alerty dotyczące metryk usługi Traffic Manager
Oprócz przetwarzania i wyświetlanie metryk z usługi Traffic Manager, usługi Azure Monitor umożliwia klientom Konfigurowanie i otrzymywać alerty skojarzone z tych metryk. Możesz wybrać, jakie warunki muszą zostać spełnione w te metryki dla wystąpienia alertu, częstotliwość tych warunków muszą być monitorowane i jak alerty powinny być przesyłane w taki sposób, aby użytkownik. Aby uzyskać więcej informacji, zobacz [usługi Azure Monitor alertów dokumentacji](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Azure Monitor](../azure-monitor/platform/metrics-supported.md)
- Dowiedz się, jak [utworzyć wykres przy użyciu usługi Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
