---
title: Monitorowanie lokalizacji danych w Azure Monitor | Microsoft Docs
description: Opisuje różne lokalizacje, w których dane monitorowania są przechowywane na platformie Azure, w tym na platformie danych Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972757"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorowanie lokalizacji danych w Azure Monitor

Azure Monitor jest oparta na [platformie danych](data-platform.md) [dzienników](data-platform-logs.md) i [metryk](data-platform-metrics.md) , zgodnie z opisem w [Azure monitor platformę danych](data-platform.md). Dane monitorowania z zasobów platformy Azure mogą być zapisywane w innych lokalizacjach, zanim zostaną skopiowane do Azure Monitor lub do obsługi dodatkowych scenariuszy. 

## <a name="monitoring-data-locations"></a>Monitorowanie lokalizacji danych

W poniższej tabeli przedstawiono różne lokalizacje, w których są wysyłane dane monitorowania na platformie Azure i różne metody uzyskiwania do nich dostępu.

| Lokalizacja | Opis | Metody dostępu |
|:---|:---|:---|:--|
| Metryki Azure Monitor | Baza danych szeregów czasowych zoptymalizowana pod kątem analizowania danych z sygnaturami czasowymi. | [Eksplorator metryk](metrics-getting-started.md)<br>[Interfejs API metryk Azure Monitor](/rest/api/monitor/metrics) |
| Dzienniki usługi Azure Monitor    | Log Analytics obszar roboczy oparty na usłudze Azure Eksplorator danych, który oferuje zaawansowany aparat analityczny i bogaty język zapytań. | [Log Analytics](../log-query/portals.md)<br>[Interfejs API Log Analytics](https://dev.loganalytics.io/)<br>[Interfejs API Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Dziennik aktywności | Dane z dziennika aktywności są najbardziej przydatne, gdy są wysyłane do dzienników Azure Monitor, aby przeanalizować je z innymi danymi, ale są również zbierane we własnym zakresie, dzięki czemu można je wyświetlić bezpośrednio w Azure Portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Interfejs API zdarzeń Azure Monitor](/rest/api/monitor/eventcategories) |
| Azure Storage | Niektóre źródła danych zapisują bezpośrednio do usługi Azure Storage i wymagają skonfigurowania przenoszenia danych do dzienników. Możesz również wysyłać dane do usługi Azure Storage w celu archiwizacji i integracji z systemami zewnętrznymi.  | [Analityka magazynu](/rest/api/storageservices/storage-analytics)<br>[Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Eksplorator usługi Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Centra zdarzeń | Wyślij dane do usługi Azure Event Hubs, aby przesłać strumieniowo do innych lokalizacji. | [Przechwyć do magazynu](../../event-hubs/event-hubs-capture-overview.md)  |
| Usługa Azure Monitor dla maszyn wirtualnych | Azure Monitor dla maszyn wirtualnych przechowuje dane kondycji obciążeń w niestandardowej lokalizacji używanej przez środowisko monitorowania w Azure Portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[Interfejs API REST monitora obciążenia](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Interfejs API REST usługi Azure Resource Health](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Alerty | Alerty utworzone przez Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Interfejs API REST zarządzania alertami](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z różnymi źródłami [danych monitorowania zbieranych przez Azure monitor](data-sources.md).
- Dowiedz się więcej na temat [typów danych monitorowania zbieranych przez Azure monitor](data-platform.md) oraz sposobu wyświetlania i analizowania danych.
