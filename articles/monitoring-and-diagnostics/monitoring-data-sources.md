---
title: Źródła danych na platformie Azure monitorowania
description: Więcej informacji na temat wszystkich monitorowania źródeł danych dostępnych na platformie Azure dzisiaj.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/27/2017
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 7b3cb869f3c220c20db53216c96449d01c16eaf6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263181"
---
# <a name="consume-monitoring-data-from-azure"></a>Wykorzystują dane monitorowania z platformy Azure

Na platformie Azure możemy są łączące dane monitorowania w jednym miejscu z monitorem Azure potoku, ale praktycznie potwierdzić, że obecnie nie wszystkie dane monitorowania są jeszcze dostępne w tym potoku. W tym artykule firma Microsoft będzie podsumowanie różne sposoby, można programowo dostęp do danych monitorowania z usług Azure.

## <a name="options-for-data-consumption"></a>Opcje wykorzystania danych

| Typ danych | Kategoria | Obsługiwane usługi | Metody dostępu |
| --- | --- | --- | --- |
| Azure metryki platformy poziomu monitora | Metryki | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**Interfejs API REST:** [metryki interfejsu API Azure monitora](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Magazyn obiektów blob lub zdarzenia koncentratora:** [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Obliczenia bazy danych metryki systemu operacyjnego gościa (np.) liczniki wydajności) | Metryki | [Windows](../virtual-machines-dotnet-diagnostics.md) i maszyn wirtualnych systemu Linux (v2) [usługi w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi sieci szkieletowej](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektu blob:** [Diagnostyka systemu Windows lub Linux Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum zdarzeń:** [diagnostyki Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Metryki niestandardowe lub aplikacji | Metryki | Wszelkie aplikacje instrumentowane przy użyciu usługi Application Insights | <ul><li>**Interfejs API REST:** [usługi Application Insights interfejsu API REST](https://dev.applicationinsights.io/reference)</li></ul> |
| Metryki magazynu | Metryki | Azure Storage | <ul><li>**Tabela magazynu:** [analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dane dotyczące rozliczeń | Metryki | Wszystkie usługi Azure | <ul><li>**Interfejs API REST:** [API RateCard i użycia zasobów platformy Azure](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Dziennik aktywności | Zdarzenia | Wszystkie usługi Azure | <ul><li>**Interfejs API REST:** [Azure monitorowania zdarzeń interfejsu API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Magazyn obiektów blob lub zdarzenia koncentratora:** [profilu dziennika](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Dzienniki diagnostyczne Azure monitora | Zdarzenia | [Zobacz tutaj](monitoring-diagnostic-logs-schema.md) | <ul><li>**Magazyn obiektów blob lub zdarzenia koncentratora:** [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Obliczenia bazy danych (np dzienniki systemu operacyjnego gościa. IIS, ETW, syslogs) | Zdarzenia | [Windows](../virtual-machines-dotnet-diagnostics.md) i maszyn wirtualnych systemu Linux (v2) [usługi w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi sieci szkieletowej](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektu blob:** [Diagnostyka systemu Windows lub Linux Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum zdarzeń:** [diagnostyki Windows Azure](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Dzienniki usługi aplikacji | Zdarzenia | Usługi aplikacji | <ul><li>**Plik, tabeli lub magazynu obiektów blob:** [sieci Web diagnostyki aplikacji](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Dzienniki magazynu | Zdarzenia | Azure Storage | <ul><li>**Tabela magazynu:** [analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alerty Centrum zabezpieczeń | Zdarzenia | Azure Security Center | <ul><li>**Interfejs API REST:** [alerty zabezpieczeń](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Raportowanie usługi Active Directory | Zdarzenia | Usługa Azure Active Directory | <ul><li>**Interfejs API REST:** [interfejs API graph usługi Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Stan zasobu Centrum zabezpieczeń | Stan | [Wszystkie obsługiwane zasobów](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**Interfejs API REST:** [stany zabezpieczeń](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Kondycja zasobów | Stan | Obsługiwane usługi | <ul><li>**Interfejs API REST:** [kondycja zasobu interfejsu API REST](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure metryki monitorowanie alertów | Powiadomienia | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**Element Webhook:** [Azure metryki alertów](insights-webhooks-alerts.md)</li></ul> |
| Alerty monitora dziennik aktywności platformy Azure | Powiadomienia | Wszystkie usługi Azure | <ul><li>**Element Webhook:** alerty dziennik aktywności platformy Azure</li></ul> |
| Powiadomienia skalowania automatycznego | Powiadomienia | [Zobacz tutaj](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Element Webhook:** [skalowania automatycznego powiadamiania elementu webhook ładunku schematu](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Rejestrowania alertów zapytania wyszukiwania | Powiadomienia | Log Analytics | <ul><li>**Element Webhook:** [alerty analizy dzienników](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Alerty metryki Insights aplikacji | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [alerty usługi Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Testy sieci web Application Insights | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [alerty usługi Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [metryki Azure Monitor](monitoring-overview-metrics.md)
- Dowiedz się więcej o [dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md)
- Dowiedz się więcej o [dzienników diagnostycznych platformy Azure](monitoring-overview-of-diagnostic-logs.md)
