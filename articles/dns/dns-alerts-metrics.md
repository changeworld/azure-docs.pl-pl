---
title: Usługa Azure DNS, metryki i alerty | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure DNS, metryki i alerty.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: kumud
ms.openlocfilehash: 54c4df446ee5c1bf8d29dd6c33b304f39ce8f1b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="azure-dns-metrics-and-alerts"></a>Usługa Azure DNS metryki i alerty
Usługa DNS platformy Azure jest usługą hostingu domen DNS, która udostępnia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. W tym artykule opisano metryki i alerty dotyczące usługi Azure DNS.

## <a name="azure-dns-metrics"></a>Metryki usługi Azure DNS

Usługa Azure DNS zawiera metryki dla klientów włączyć je do monitorowania określonych aspektów ich te hostowanego w usłudze DNS. Ponadto z metryk usługi Azure DNS, można skonfigurować i otrzymywać alerty na podstawie warunków zainteresowań. Metryki są udostępniane za pośrednictwem [usługi Azure Monitor](../monitoring-and-diagnostics/index.yml). Usługa Azure DNS zawiera następujące metryki za pośrednictwem Monitora Azure dla stref DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Możesz również sprawdzić [definicji tych metryk](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) na stronie dokumentacji Azure Monitor.
>[!NOTE]
> W tej chwili te metryki są dostępne tylko dla stref DNS publicznego obsługiwanych w usłudze Azure DNS. Jeśli masz stref prywatnego obsługiwanych w usłudze Azure DNS, te metryki nie będzie dostarczać dane dla tych stref. Ponadto metryki i alertów funkcja jest obsługiwana tylko w chmurze publicznej Azure. Wsparcie chmur suwerennych będzie występować w późniejszym czasie. 

Szczegółowym poziomie wymiaru dla tych metryk jest strefy DNS.

### <a name="query-volume"></a>Wolumin zapytania

*Woluminu zapytania* Metryka w usłudze Azure DNS zawiera wolumin zapytania DNS (ruch), który jest odbierane przez usługę Azure DNS dla strefy DNS. Jednostki miary jest licznik i agregacji to suma wszystkich zapytań, które są odbierane przez pewien czas. Aby wyświetlić tej metryki, wybierz interfejs Eksploratora metryk (wersja zapoznawcza) na karcie Monitor w portalu Azure. Wybierz z listy rozwijanej zasobów strefy DNS, wybierz metrykę woluminu zapytania i wybierz Sum jako agregacja. Poniżej przedstawiono przykład zrzut ekranu.  Aby uzyskać więcej informacji na temat Eksploratora metryk doświadczenia i wykresy, zobacz [Eksploratora metryk Monitor Azure](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Wolumin zapytania](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Rysunek: Metryki woluminu zapytania w usłudze Azure DNS*

### <a name="record-set-count"></a>Liczba zestawu rekordów
*Ustawiona liczba rekordów* Metryka przedstawia liczbę zestawów rekordów w usłudze Azure DNS dla swojej strefy DNS. Uwzględniane są wszystkie zdefiniowane zestawami rekordów w strefie. Jednostki miary jest licznik i agregacji jest maksymalnie wszystkie zestawy rekordów. Zaznacz, aby wyświetlić ten pomiar **metryki (wersja zapoznawcza)** interfejs Eksploratora z **Monitor** kartę w portalu Azure. Wybierz swoją strefę DNS z **zasobów** listy rozwijanej, wybierz pozycję **ustawiona liczba rekordów** metryki, a następnie wybierz **Max** jako **agregacji** . Aby uzyskać więcej informacji na temat Eksploratora metryk doświadczenia i wykresy, zobacz [Eksploratora metryk Monitor Azure](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Liczba zestawu rekordów](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Rysunek: Metryk usługi Azure DNS ustawiona liczba rekordów*


### <a name="record-set-capacity-utilization"></a>Użycie wydajności zestawu rekordów
*Wykorzystanie pojemności ustawić rekordu* Metryka w usłudze Azure DNS zawiera wartość procentową wykorzystania możliwości rekordów dla strefy DNS. Każdej strefy DNS w usłudze Azure DNS podlega limit zestawu rekordów, która określa maksymalną liczbę zestawów rekordów, które są dozwolone dla strefy (zobacz [limity DNS](dns-zones-records.md#limits)). W związku z tym ta metryka przedstawia należy jak blisko zostaną naciśnięcie limit zestawu rekordów. Na przykład jeśli masz 500 zestawów rekordów skonfigurowane dla swojej strefy DNS i strefy ma domyślny limit rekordów 5000, metryka RecordSetCapacityUtilization wyświetli wartość 10%, (które są uzyskiwane przez podziału 500 przez 5000). Jest to jednostka miary **procent** i **agregacji** jest typu **maksymalna**. Aby wyświetlić tej metryki, wybierz interfejs Eksploratora metryk (wersja zapoznawcza) na karcie Monitor w portalu Azure. Wybierz swoją strefę DNS z listy rozwijanej zasobów, wybierz metrykę wykorzystanie pojemności ustawić rekordu i wybierz Max jako agregacja. Poniżej przedstawiono przykład zrzut ekranu. Aby uzyskać więcej informacji na temat Eksploratora metryk doświadczenia i wykresy, zobacz [Eksploratora metryk Monitor Azure](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Liczba zestawu rekordów](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Rysunek: Metryk usługi Azure DNS wykorzystanie pojemności ustawić rekordu*

## <a name="alerts-in-azure-dns"></a>Alerty w usłudze Azure DNS
Azure Monitor oferuje możliwość alert z dostępnych wartości metryki. Metryki DNS są dostępne w nowym środowisku konfiguracji alertu. Zgodnie z opisem w części [Azure Monitor alerty dokumentacji](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), wybierz strefę DNS jako zasób, wybierz typ sygnału metryki i Konfigurowanie alertów logiki oraz innych parametrów takich jak **okres**i **częstotliwość**. Dodatkowo można zdefiniować [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md) dla po alertu warunek jest spełniony, według którego alert będą dostarczane za pomocą wybranego działania. Aby uzyskać więcej informacji na temat konfigurowania alertów dla metryki Azure Monitor, zobacz [Utwórz, Wyświetl, alerty i zarządzaj nimi za pomocą monitora Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Azure DNS](dns-overview.md).
