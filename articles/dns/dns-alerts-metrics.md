---
title: Metryki i alerty — usługa Azure DNS
description: Dzięki tej ścieżce szkoleniowej rozpocznij pracę z metrykami i alertami usługi Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937458"
---
# <a name="azure-dns-metrics-and-alerts"></a>Metryki i alerty usługi Azure DNS
Usługa Azure DNS to usługa hostingowa dla domen DNS, która zapewnia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. W tym artykule opisano metryki i alerty dla usługi Azure DNS.

## <a name="azure-dns-metrics"></a>Metryki usługi Azure DNS

Usługa Azure DNS udostępnia klientom metryki umożliwiające im monitorowanie określonych aspektów ich stref DNS hostowanych w usłudze. Ponadto za pomocą metryk dns platformy Azure można skonfigurować i odbierać alerty na podstawie warunków zainteresowania. Metryki są dostarczane za pośrednictwem [usługi Azure Monitor.](../azure-monitor/index.yml) Usługa Azure DNS udostępnia następujące metryki za pośrednictwem usługi Azure Monitor dla stref DNS:

-   QueryVolume (Wyw.
-   Konto RecordSetCount
-   Ulatnianie recordSetCapacityUtilization

Definicję tych [metryk](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) można również wyświetlić na stronie dokumentacji usługi Azure Monitor.
>[!NOTE]
> W tej chwili te metryki są dostępne tylko dla publicznych stref DNS hostowanych w usłudze Azure DNS. Jeśli masz strefy prywatne hostowane w usłudze Azure DNS, te metryki nie będą dostarczać danych dla tych stref. Ponadto metryki i funkcje alertów jest obsługiwany tylko w chmurze publicznej platformy Azure. Wsparcie dla suwerennych chmur nastąpi w późniejszym czasie. 

Najbardziej szczegółowy element, dla którego można zobaczyć metryki, jest strefa DNS. Obecnie nie widać metryk dla poszczególnych rekordów zasobów w strefie.

### <a name="query-volume"></a>Wolumin kwerendy

Metryka *woluminu kwerend* w usłudze Azure DNS pokazuje ilość zapytań DNS (ruch zapytań), które są odbierane przez usługę Azure DNS dla strefy DNS. Jednostką miary jest Liczba, a agregacja jest sumą wszystkich zapytań odebranych w określonym czasie. 

Aby wyświetlić tę metrykę, wybierz środowisko eksploratora metryki (podgląd) na karcie Monitor w witrynie Azure portal. Wybierz strefę DNS z listy rozwijanej Zasób, wybierz metrykę woluminu kwerendy i wybierz pozycję Suma jako agregację. Poniższy zrzut ekranu przedstawia przykład.  Aby uzyskać więcej informacji na temat środowiska Eksploratora metryk i tworzenia [wykresów, zobacz Eksplorator metryk usługi Azure Monitor](../azure-monitor/platform/metrics-charts.md).

![Wolumin kwerendy](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Rysunek: Metryki woluminu zapytań dns platformy Azure*

### <a name="record-set-count"></a>Liczba zestawów rekordów
Metryka *Liczba zestawów rekordów* pokazuje liczbę zestawów rekordów w usłudze Azure DNS dla strefy DNS. Wszystkie zestawy rekordów zdefiniowane w strefie są liczone. Jednostką miary jest Liczba, a agregacja jest maksymalna ze wszystkich rekordów. Aby wyświetlić tę metrykę, wybierz środowisko eksploratora **metryki (podgląd)** na karcie **Monitor** w witrynie Azure portal. Wybierz strefę DNS z listy rozwijanej **Zasób,** wybierz metrykę **Liczba zestawów rekordów,** a następnie wybierz **pozycję Max** jako **agregację**. Aby uzyskać więcej informacji na temat środowiska Eksploratora metryk i tworzenia [wykresów, zobacz Eksplorator metryk usługi Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Liczba zestawów rekordów](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Rysunek: Metryki liczby zestawów rekordów DNS platformy Azure*


### <a name="record-set-capacity-utilization"></a>Rekordowe wykorzystanie pojemności zestawu mocy produkcyjnych
Metryka *wykorzystania zdolności produkcyjnych zestawu rekordów* w usłudze Azure DNS pokazuje procent wykorzystania pojemności zestawu rekordów dla strefy DNS. Każda strefa DNS w usłudze Azure DNS podlega limitowi zestawu rekordów, który określa maksymalną liczbę zestawów rekordów dozwolonych dla strefy (patrz [limity DNS](dns-zones-records.md#limits)). W związku z tym ta metryka pokazuje, jak blisko jesteś do osiągnięcia limitu Recordset. Na przykład jeśli dla strefy DNS skonfigurowano 500 zestawów rekordów, a strefa ma domyślny limit zestawu rekordów wynoszący 5000, metryka RecordSetCapacityUtilization wyświetli wartość 10% (która jest uzyskiwana przez podzielenie 500 przez 5000). Jednostką miary jest **Wartość procentowa,** a typem **Agregacji** jest **maksimum**. Aby wyświetlić tę metrykę, wybierz środowisko eksploratora metryki (podgląd) na karcie Monitor w witrynie Azure portal. Wybierz strefę DNS z listy rozwijanej Zasób, wybierz metrykę Wykorzystanie pojemności zestawu rekordów i wybierz pozycję Maks. Poniższy zrzut ekranu przedstawia przykład. Aby uzyskać więcej informacji na temat środowiska Eksploratora metryk i tworzenia [wykresów, zobacz Eksplorator metryk usługi Azure Monitor](../azure-monitor/platform/metrics-charts.md). 

![Liczba zestawów rekordów](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Rysunek: Metryki wykorzystania pojemności zestawu rekordów DNS platformy Azure*

## <a name="alerts-in-azure-dns"></a>Alerty w usłudze Azure DNS
Usługa Azure Monitor udostępnia możliwość ostrzegania o dostępnych wartościach metryk. Metryki DNS są dostępne w nowym środowiska konfiguracji alertów. Jak opisano szczegółowo w [dokumentacji alertów usługi Azure Monitor,](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)można wybrać strefę DNS jako zasób, wybrać typ sygnału metryki i skonfigurować logikę alertu i inne parametry, takie jak **Okres** i **Częstotliwość**. Można dodatkowo zdefiniować [grupę akcji,](../azure-monitor/platform/action-groups.md) gdy warunek alertu jest spełniony, przy czym alert zostanie dostarczony za pośrednictwem wybranych akcji. Aby uzyskać więcej informacji na temat konfigurowania alertów dla metryk usługi Azure Monitor, zobacz [Tworzenie, wyświetlanie i zarządzanie alertami za pomocą usługi Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure DNS](dns-overview.md).
