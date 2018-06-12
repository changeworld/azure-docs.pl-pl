---
title: Metryki i alertów w usługi Azure Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano metryki dostępny dla Menedżera ruchu na platformie Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: 424782be2d814df6d598591198b5005fb494d3da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304207"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Menedżer ruchu metryki i alerty

Menedżer ruchu zapewnia obciążenia opartego na DNS równoważenia, która obejmuje wiele metod routingu i opcje monitorowania punktu końcowego. W tym artykule opisano metryki i skojarzonych alertach, które są dostępne dla klientów. 

## <a name="metrics-available-in-traffic-manager"></a>Metryki dostępne w usłudze Traffic Manager 

Menedżer ruchu zawiera następujące metryki na podstawie na profil, które mogą być używane przez klientów, aby zrozumieć ich użycie funkcji Menedżera ruchu i stanu ich punktów końcowych przy użyciu tego profilu.  

### <a name="queries-by-endpoint-returned"></a>Zapytania przez punkt końcowy zwrócił
Użyj [ta metryka](../monitoring-and-diagnostics/monitoring-supported-metrics.md) do wyświetlenia liczba zapytań, które zostały przetworzone przez profil Menedżera ruchu w określonym czasie. Można również wyświetlić te same informacje o punkcie końcowym poziomu zwróconą pomaga zrozumieć sposób punktu końcowego wiele razy w odpowiedzi zapytania z Menedżera ruchu.

W poniższym przykładzie rysunek 1 zawiera wszystkie odpowiedzi zapytania, które zostały zwrócone przez profil Menedżera ruchu. 

  
![Metryki usługi Traffic Manager — zagregowany widok wszystkich zapytań](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Rysunek 1: Agregacji widok z wszystkich zapytań*
  
Rysunek 2 przedstawia te same informacje, jednak jest dzielony przez punkty końcowe. W związku z tym widać wolumin odpowiedzi zapytania, w których został zwrócony określonego punktu końcowego.

![Metryki usługi Traffic Manager — złożenie zapytania woluminu na punkt końcowy](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Rysunek 2: Podzielony widok kwerendy woluminu wyświetlanych na punkt końcowy zwrócił*

## <a name="endpoint-status-by-endpoint"></a>Stan punktu końcowego przez punkt końcowy
Użyj [ta metryka](../monitoring-and-diagnostics/monitoring-supported-metrics.md) do danych o stanie kondycji punktów końcowych w profilu. Trwa dwóch wartości:
 - Użyj **1** , jeśli punkt końcowy jest uruchomiony.
 - Użyj **0** Jeśli punkt końcowy jest wyłączony.

Ta metryka może zostać podany w postaci wartości zagregowanej reprezentuje stan wszystkie metryki (rysunek 3) lub jego może zostać podzielony (patrz rysunek 4) do określenia stanu określonych punktów końcowych. W przypadku pierwszej, jeśli jako wybrany jest poziom agregacji **Avg**, wartość tego metryka jest średnią arytmetyczną o stanie wszystkich punktów końcowych. Na przykład, jeśli profil ma dwa punkty końcowe i tylko jeden z nich jest w dobrej kondycji, ta metryka ma wartość **0,50** jak pokazano na rysunku 3. 


![Metryki usługi Traffic Manager — złożonego widoku stanu punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Rysunek 3: Złożonego widoku metryki stan punktu końcowego — wybrane agregacji "Średni"*


![Metryki usługi Traffic Manager — złożenie stan punktu końcowego](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Rysunek 4: Dzielenie widoku metryki stan punktu końcowego*

Będzie można korzystać z tych metryk za pośrednictwem [usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)w portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/), [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/monitor), i [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), lub części metryk obsługi portalu Menedżera ruchu.

## <a name="alerts-on-traffic-manager-metrics"></a>Alerty dotyczące metryk Menedżera ruchu
Oprócz przetwarzania i wyświetlania metryk usługi Traffic Manager, Azure Monitor służy do konfigurowania i odbierania alertów skojarzonych z tych metryk. Można wybrać, jakie warunki muszą zostać spełnione w tych metryk alertu występuje, częstotliwość tych warunków muszą być monitorowane i jak alerty mają być wysyłane do Ciebie. Aby uzyskać więcej informacji, zobacz [Azure Monitor alerty dokumentacji](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [Azure monitorowania usługi](../monitoring-and-diagnostics/monitoring-supported-metrics.md)
- Dowiedz się, jak [utworzyć nowy wykres za pomocą monitora Azure](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart)