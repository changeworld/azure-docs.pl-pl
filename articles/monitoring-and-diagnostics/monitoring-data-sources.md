---
title: Źródła danych na platformie Azure monitorowania
description: Dowiedz się więcej o wszystkich monitorowania źródeł danych dostępnych na platformie Azure już dziś.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 3820a42e44896f75a2191b0426f2afa5a40461d7
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248037"
---
# <a name="consume-monitoring-data-from-azure"></a>Używanie danych monitorowania na platformie Azure

Na platformie Azure łączymy dane monitorowania w jednym miejscu dzięki usłudze Azure Monitor potoku, ale praktycznie potwierdza, że obecnie nie wszystkie dane monitorowania jest jeszcze dostępna w tym potoku. W tym artykule firma Microsoft zawiera podsumowanie różnych sposobów można programowego dostępu do danych monitorowania z usług platformy Azure.

## <a name="options-for-data-consumption"></a>Opcje do użycia danych

| Typ danych | Kategoria | Obsługiwane usługi | Metod dostępu |
| --- | --- | --- | --- |
| Metryki platformy poziomu usługi Azure Monitor | Metryki | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**Interfejs API REST:** [metryki interfejsu API usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Obliczenia metryki systemu operacyjnego gościa (np.) liczniki wydajności) | Metryki | [Windows](../virtual-machines-dotnet-diagnostics.md) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektu blob:** [diagnostyki Windows lub Linux, Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum zdarzeń:** [usługi Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Metryk niestandardowych lub aplikacji | Metryki | Każda aplikacja Instrumentacją zapewnianą przez usługę Application Insights | <ul><li>**Interfejs API REST:** [Insights aplikacji interfejsu API REST](https://dev.applicationinsights.io/reference)</li></ul> |
| Metryki magazynu | Metryki | Azure Storage | <ul><li>**Tabela magazynu:** [analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dane dotyczące rozliczeń | Metryki | Wszystkich usług platformy Azure | <ul><li>**Interfejs API REST:** [API RateCard i użycie zasobów platformy Azure](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Dziennik aktywności | Zdarzenia | Wszystkich usług platformy Azure | <ul><li>**Interfejs API REST:** [zdarzeń usługi Azure Monitor interfejsu API](https://docs.microsoft.com/en-us/rest/api/monitor/eventcategories)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [profilu dziennika](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Dzienniki diagnostyczne usługi Azure Monitor | Zdarzenia | [Zobacz tutaj](monitoring-diagnostic-logs-schema.md) | <ul><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [ustawień diagnostycznych](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Obliczenia dzienników systemu operacyjnego gościa (np.) IIS, ETW, syslogs) | Zdarzenia | [Windows](../virtual-machines-dotnet-diagnostics.md) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektu blob:** [diagnostyki Windows lub Linux, Azure](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum zdarzeń:** [usługi Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Dzienniki usługi App Service | Zdarzenia | Usługi aplikacji | <ul><li>**Plik, tabela lub usługi blob storage:** [sieci Web diagnostyki aplikacji](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Dzienniki magazynu | Zdarzenia | Azure Storage | <ul><li>**Tabela magazynu:** [analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertów usługi Security Center | Zdarzenia | Azure Security Center | <ul><li>**Interfejs API REST:** [alerty zabezpieczeń](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Raporty usługi Active Directory | Zdarzenia | Usługa Azure Active Directory | <ul><li>**Interfejs API REST:** [interfejsu API programu graph usługi Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Stan zasobu usługi Security Center | Stan | [Wszystkie obsługiwane zasobów](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**Interfejs API REST:** [stany zabezpieczeń](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Kondycja zasobów | Stan | Obsługiwane usługi | <ul><li>**Interfejs API REST:** [kondycja zasobu interfejsu API REST](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alerty metryk usługi Azure Monitor | Powiadomienia | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**Element Webhook:** [alertów dotyczących metryk platformy Azure](insights-webhooks-alerts.md)</li></ul> |
| Alerty dziennika aktywności monitora platformy Azure | Powiadomienia | Wszystkich usług platformy Azure | <ul><li>**Element Webhook:** alertów dzienników aktywności platformy Azure</li></ul> |
| Powiadomienia skalowania automatycznego | Powiadomienia | [Zobacz tutaj](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Element Webhook:** [schematu ładunek elementu webhook powiadomienia skalowania automatycznego](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Zapytanie wyszukiwania, alerty dzienników | Powiadomienia | Log Analytics | <ul><li>**Element Webhook:** [Akcja elementu Webhook dla reguł alertów dzienników](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Application Insights alertów dotyczących metryk | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [alertów usługi Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Testy sieci web programu Application Insights | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [alertów usługi Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [metryk usługi Azure Monitor](monitoring-overview-metrics.md)
- Dowiedz się więcej o [dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md)
- Dowiedz się więcej o [dzienniki diagnostyczne platformy Azure](monitoring-overview-of-diagnostic-logs.md)
