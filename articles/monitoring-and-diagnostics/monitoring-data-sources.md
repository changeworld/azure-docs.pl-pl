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
ms.openlocfilehash: 7de2257a5e351cc1c2eac83a0fd0095807ae4afa
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53320765"
---
# <a name="consume-monitoring-data-from-azure"></a>Używanie danych monitorowania na platformie Azure

Na platformie Azure łączymy dane monitorowania w jednym miejscu dzięki usłudze Azure Monitor potoku, ale praktycznie potwierdza, że obecnie nie wszystkie dane monitorowania jest jeszcze dostępna w tym potoku. W tym artykule firma Microsoft zawiera podsumowanie różnych sposobów można programowego dostępu do danych monitorowania z usług platformy Azure.

## <a name="options-for-data-consumption"></a>Opcje do użycia danych

| Typ danych | Kategoria | Obsługiwane usługi | Metod dostępu |
| --- | --- | --- | --- |
| Metryki platformy poziomu usługi Azure Monitor | Metryki | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**INTERFEJS API REST:** [Interfejs API metryk usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Ustawienia diagnostyczne](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Obliczenia metryki systemu operacyjnego gościa (np.) liczniki wydajności) | Metryki | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektów blob:** [Diagnostyka Windows lub Linux, Azure](../azure-monitor/platform/diagnostics-extension-to-storage.md)</li><li>**Centrum zdarzeń:** [Usługi Windows Azure diagnostics](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Metryk niestandardowych lub aplikacji | Metryki | Każda aplikacja Instrumentacją zapewnianą przez usługę Application Insights | <ul><li>**INTERFEJS API REST:** [Usługa Application Insights interfejsu API REST](https://dev.applicationinsights.io/reference)</li></ul> |
| Metryki magazynu | Metryki | Azure Storage | <ul><li>**Tabela magazynu:** [Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dane dotyczące rozliczeń | Metryki | Wszystkich usług platformy Azure | <ul><li>**INTERFEJS API REST:** [Użycie zasobów platformy Azure i interfejsów API usługi RateCard](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Dziennik aktywności | Zdarzenia | Wszystkich usług platformy Azure | <ul><li>**INTERFEJS API REST:** [Usługa Azure Monitor zdarzenia interfejsu API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Profilu dziennika](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Dzienniki diagnostyczne usługi Azure Monitor | Zdarzenia | [Zobacz tutaj](monitoring-diagnostic-logs-schema.md) | <ul><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Ustawienia diagnostyczne](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Obliczenia dzienników systemu operacyjnego gościa (np.) IIS, ETW, syslogs) | Zdarzenia | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektów blob:** [Diagnostyka Windows lub Linux, Azure](../azure-monitor/platform/diagnostics-extension-to-storage.md)</li><li>**Centrum zdarzeń:** [Usługi Windows Azure diagnostics](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Dzienniki usługi App Service | Zdarzenia | Usługi aplikacji | <ul><li>**Plik, tabela lub usługi blob storage:** [Diagnostyka aplikacji sieci Web](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Dzienniki magazynu | Zdarzenia | Azure Storage | <ul><li>**Tabela magazynu:** [Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertów usługi Security Center | Zdarzenia | Azure Security Center | <ul><li>**INTERFEJS API REST:** [Alerty zabezpieczeń](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Raporty usługi Active Directory | Zdarzenia | Usługa Azure Active Directory | <ul><li>**INTERFEJS API REST:** [Usługa Azure Active Directory interfejsu API programu graph](../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Stan zasobu usługi Security Center | Stan | [Wszystkie obsługiwane zasobów](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**INTERFEJS API REST:** [Stany zabezpieczeń](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Kondycja zasobów | Stan | Obsługiwane usługi | <ul><li>**INTERFEJS API REST:** [Kondycja zasobu interfejsu API REST](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alerty metryk usługi Azure Monitor | Powiadomienia | [Zobacz tutaj](monitoring-supported-metrics.md) | <ul><li>**Element Webhook:** [Alerty metryk platformy Azure](../azure-monitor/platform/alerts-webhooks.md)</li></ul> |
| Alerty dziennika aktywności monitora platformy Azure | Powiadomienia | Wszystkich usług platformy Azure | <ul><li>**Element Webhook:** Alerty dziennika aktywności platformy Azure</li></ul> |
| Powiadomienia skalowania automatycznego | Powiadomienia | [Zobacz tutaj](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Element Webhook:** [Schematu ładunek elementu webhook powiadomienia skalowania automatycznego](../azure-monitor/platform/autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Zapytanie wyszukiwania, alerty dzienników | Powiadomienia | Log Analytics | <ul><li>**Element Webhook:** [Akcja elementu Webhook dla reguł alertów dzienników](../monitoring-and-diagnostics/../azure-monitor/platform/alerts-log-webhook.md)</li></ul> |
| Application Insights alertów dotyczących metryk | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [Alerty szczegółowe informacje o aplikacji](../application-insights/app-insights-alerts.md)</li></ul> |
| Testy sieci web programu Application Insights | Powiadomienia | Application Insights | <ul><li>**Element Webhook:** [Alerty szczegółowe informacje o aplikacji](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [metryk usługi Azure Monitor](../azure-monitor/platform/data-collection.md)
- Dowiedz się więcej o [dziennika aktywności platformy Azure](monitoring-overview-activity-logs.md)
- Dowiedz się więcej o [dzienniki diagnostyczne platformy Azure](monitoring-overview-of-diagnostic-logs.md)
