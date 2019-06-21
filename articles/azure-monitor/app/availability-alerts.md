---
title: Skonfigurować alerty dotyczące dostępności za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304910"
---
# <a name="availability-alerts"></a>Alerty dostępności

Usługa [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Go może generować alerty, jeśli aplikacja nie odpowiada lub zbyt wolno odpowiada.

## <a name="enable-alerts"></a>Włączanie alertów

Alerty są teraz automatycznie domyślnie włączone, ale w celu skonfigurowania pełni alert najpierw trzeba utworzyć test dostępności.

![Utwórz środowisko](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), preferencje ważność i powiadomień regułę alertu z [grup akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **musi być** skonfigurowane w środowisko alertów. Bez wykonać następujące kroki subskrybent otrzyma tylko powiadomienia w portalu.

1. Po zapisaniu test dostępności, w szczegółach karty kliknij wielokropek przez test, która właśnie wykonana. Kliknij "Edytuj alert".

   ![Edytuj po zapisaniu](./media/availability-alerts/edit-alert.png)

2. Ustaw poziom ważności, opis reguły, a co najważniejsze — grupę akcji, która ma preferencje powiadamiania, którego chcesz użyć dla tej reguły alertu.

   ![Edytuj po zapisaniu](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alert po wystąpieniu X z Y lokalizacji, raportowanie błędów

X z Y lokalizacji, reguła alertu jest domyślnie włączone w [nowe alerty ujednolicone środowisko](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), podczas tworzenia nowego testu dostępności. Możesz zrezygnować z przez wybranie opcji "klasyczny" lub wyłączenie reguły alertu.

> [!NOTE]
> Konfigurowanie grup akcji, aby otrzymywać powiadomienia po wyzwoleniu alertu, wykonując kroki opisane powyżej. Bez tego kroku subskrybent otrzyma tylko powiadomienia w portalu po wyzwoleniu reguły.
>

### <a name="alert-on-availability-metrics"></a>Alert po wystąpieniu metryki dostępności

Za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), można alert po wystąpieniu segmentowanych dostępności agregacji i testowania oraz metryki czasu trwania:

1. Wybierz zasób usługi Application Insights w środowisku metryki, a następnie wybierz metrykę dostępności:

    ![Wybór metryki dostępności](./media/availability-alerts/select-metric.png)

2. Konfigurowanie alertów, którą opcję z menu spowoduje przejście do nowego środowiska którym można wybrać określonych testów lub lokalizacji, aby skonfigurować reguły alertu dotyczącego. Można również skonfigurować grupy akcji dla tej reguły alertu, w tym miejscu.

### <a name="alert-on-custom-analytics-queries"></a>Alert po wystąpieniu zapytań analitycznych niestandardowe

Za pomocą [nowe alerty ujednoliconego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), może generować alerty na [dzienników niestandardowych kwerend](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Za pomocą niestandardowych zapytań może generować alerty na dowolny warunek, który pomoże Ci w uzyskaniu najbardziej niezawodny sygnał problemy z dostępnością. Dotyczy to również szczególnie ma to zastosowanie, w przypadku wysyłania wyniki dostępności niestandardowych przy użyciu zestawu SDK TrackAvailability. 

> [!Tip]
> Metryki dotyczące dostępności danych obejmują żadnych wyników dostępności niestandardowych, które mogą przesyłać przez wywołanie metody nasz zestaw SDK TrackAvailability. Możesz użyć alertów dotyczących obsługi różnych metryk w ramach alertu wyniki dostępności niestandardowych.
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W wersji dedykowanej [artykule dotyczącym rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wieloetapowy test witryny](availability-multistep.md)
* [Testy sieci web ping adresu URL](monitor-web-app-availability.md)

