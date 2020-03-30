---
title: Monitorowanie lokalizacji danych w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano różne lokalizacje, w których dane monitorowania są przechowywane na platformie Azure, w tym na platformie danych usługi Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666619"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorowanie lokalizacji danych w usłudze Azure Monitor

Usługa Azure Monitor jest oparta na [platformie danych](data-platform.md) [dzienników](data-platform-logs.md) i [metryk,](data-platform-metrics.md) zgodnie z opisem na [platformie danych Usługi Azure Monitor.](data-platform.md) Monitorowanie danych z zasobów platformy Azure mogą być zapisywane w innych lokalizacjach, jednak przed ich skopiowaniem do usługi Azure Monitor lub do obsługi dodatkowych scenariuszy. 

## <a name="monitoring-data-locations"></a>Monitorowanie lokalizacji danych

W poniższej tabeli określono różne lokalizacje, w których są wysyłane dane monitorowania na platformie Azure, oraz różne metody uzyskiwania do nich dostępu.

| Lokalizacja | Opis | Metody dostępu |
|:---|:---|:---|:--|
| Metryki usługi Azure Monitor | Baza danych szeregów czasowych, która jest zoptymalizowana pod kątem analizowania danych oznaczonych sygnaturą czasową. | [Eksplorator metryk](metrics-getting-started.md)<br>[Interfejs API metryk usługi Azure Monitor](/rest/api/monitor/metrics) |
| Dzienniki usługi Azure Monitor    | Obszar roboczy usługi Log Analytics oparty na eksploratorze danych platformy Azure, który zapewnia zaawansowany aparat analizy i bogaty język zapytań. | [Analiza dzienników](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| Dziennik aktywności | Dane z dziennika aktywności jest najbardziej przydatne, gdy wysyłane do dzienników usługi Azure Monitor do analizy z innymi danymi, ale jest również zbierane na własną rękę, dzięki czemu można go bezpośrednio wyświetlić w witrynie Azure portal. | [Portal Azure](activity-log-view.md#azure-portal)<br>[Interfejs API zdarzeń monitora platformy Azure](/rest/api/monitor/eventcategories) |
| Azure Storage | Niektóre źródła danych będą zapisywać bezpośrednio w magazynie platformy Azure i wymagają konfiguracji, aby przenieść dane do dzienników. Można również wysyłać dane do magazynu platformy Azure w celu archiwizacji i integracji z systemami zewnętrznymi.  | [Analityka magazynu](/rest/api/storageservices/storage-analytics)<br>[Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Usługa Event Hubs | Wysyłaj dane do usługi Azure Event Hubs, aby przesyłać strumieniowo je do innych lokalizacji. | [Przechwytywanie do magazynu](../../event-hubs/event-hubs-capture-overview.md)  |
| Usługa Azure Monitor dla maszyn wirtualnych | Usługa Azure Monitor dla maszyn wirtualnych przechowuje dane dotyczące kondycji obciążenia w lokalizacji niestandardowej, która jest używana przez jego środowisko monitorowania w witrynie Azure portal. | [Portal Azure](../insights/vminsights-overview.md)<br>[Interfejs API REST monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Interfejs API REST kondycji kondycji zasobów platformy Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alerty | Alerty utworzone przez usługę Azure Monitor. | [Portal Azure](alerts-managing-alert-instances.md)<br>[Interfejs API REST zarządzania alertami](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Następne kroki

- Zobacz różne źródła [danych monitorowania zebranych przez usługę Azure Monitor.](data-sources.md)
- Dowiedz się więcej o [typach danych monitorowania gromadzonych przez usługę Azure Monitor](data-platform.md) oraz o sposobie wyświetlania i analizowania tych danych.
