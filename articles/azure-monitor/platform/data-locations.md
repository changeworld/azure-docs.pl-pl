---
title: Monitorowanie lokalizacji danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Zawiera opis różnych lokalizacji, gdzie dane monitorowania są przechowywane na platformie Azure, w tym platformę danych usługi Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 1d92973e32e9c694b1d0488753b9a701e7d71a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66416915"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorowanie lokalizacji danych w usłudze Azure Monitor

Usługa Azure Monitor jest oparty na [platforma danych](data-platform.md) z [dzienniki](data-platform-logs.md) i [metryki](data-platform-metrics.md) zgodnie z opisem w [platforma danych usługi Azure Monitor](data-platform.md). Dane monitorowania z zasobów platformy Azure mogą być zapisane w innych lokalizacjach, albo zanim zostaną one skopiowane do usługi Azure Monitor lub o obsługę dodatkowych scenariuszy. 

## <a name="monitoring-data-locations"></a>Lokalizacje danych monitorowania

W poniższej tabeli przedstawiono różnych lokalizacji danych na platformie Azure monitorowania wysyłania i różnych metod dostępu do niego.

| Lokalizacja | Opis | Metod dostępu |
|:---|:---|:---|:--|
| Azure Monitor Metrics | Baza danych szeregów czasowych, który jest zoptymalizowany pod kątem analizowania danych z sygnaturami czasowymi. | [Eksplorator metryk](metrics-getting-started.md)<br>[Interfejs API metryk usługi Azure Monitor](/rest/api/monitor/metrics) |
| Dzienniki usługi Azure Monitor    | Zaloguj się obszar roboczy analizy, który jest oparty na Eksplorator danych platformy Azure, która udostępnia zaawansowane analizy język zapytań aparatu i zaawansowanego. | [Log Analytics](../log-query/portals.md)<br>[Interfejs API analizy dzienników](https://dev.loganalytics.io/)<br>[Usługa Application Insights interfejsu API](https://dev.applicationinsights.io/reference/get-query) |
| Dziennik aktywności | Dane z dziennika aktywności jest najbardziej użyteczna, gdy wysyłane do usługi Azure Monitor dzienniki analizując je z innymi danymi, ale również są zbierane samodzielnie, dzięki czemu mogą być bezpośrednio wyświetlane w witrynie Azure portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Usługa Azure Monitor zdarzenia interfejsu API](/rest/api/monitor/eventcategories) |
| Azure Storage | Niektóre źródła danych spowoduje zapisu bezpośrednio do usługi Azure storage i wymagają konfiguracji, aby przenieść dane do dzienników. Można również wysyłać dane do usługi Azure storage w celu archiwizowania oraz integracja z systemami zewnętrznymi.  | [Analityka magazynu](/rest/api/storageservices/storage-analytics)<br>[Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Wysłać dane do usługi Azure Event Hubs, przesłać go strumieniowo do innych lokalizacji. | [Funkcję przechwytywania w usłudze Storage](../../event-hubs/event-hubs-capture-overview.md)  |
| Usługa Azure Monitor dla maszyn wirtualnych | Usługa Azure Monitor dla maszyn wirtualnych są przechowywane dane kondycji obciążenie w niestandardowej lokalizacji, używanego przez jego monitorowania środowiska w witrynie Azure portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[Monitor obciążenia interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Kondycja zasobów Azure interfejsu API REST](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alerty | Alerty utworzone przez usługi Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Alerty interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Kolejne kroki

- Zobacz różnych źródłach [dane monitorowania zbierane przez usługi Azure Monitor](data-sources.md).
- Dowiedz się więcej o [typów danych monitorowania zbieranych przez usługi Azure Monitor](data-platform.md) oraz jak wyświetlać i analizować te dane.
