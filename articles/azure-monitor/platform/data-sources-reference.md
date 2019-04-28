---
title: Źródła danych na platformie Azure monitorowania
description: Dowiedz się więcej o wszystkich monitorowania źródeł danych dostępnych na platformie Azure już dziś.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364519"
---
# <a name="consume-monitoring-data-from-azure"></a>Używanie danych monitorowania na platformie Azure

Na platformie Azure łączymy dane monitorowania w jednym miejscu dzięki usłudze Azure Monitor potoku, ale praktycznie potwierdza, że obecnie nie wszystkie dane monitorowania jest jeszcze dostępna w tym potoku. W tym artykule firma Microsoft zawiera podsumowanie różnych sposobów można programowego dostępu do danych monitorowania z usług platformy Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>Opcje do użycia danych

| Typ danych | Category | Obsługiwane usługi | Metod dostępu |
| --- | --- | --- | --- |
| Metryki platformy poziomu usługi Azure Monitor | Metryki | [Zobacz tutaj](metrics-supported.md) | <ul><li>**INTERFEJS API REST:** [Interfejs API metryk usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Ustawienia diagnostyczne](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Obliczenia metryki systemu operacyjnego gościa (np.) liczniki wydajności) | Metryki | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektów blob:** [Diagnostyka Windows lub Linux, Azure](diagnostics-extension-to-storage.md)</li><li>**Centrum zdarzeń:** [Usługi Windows Azure diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Metryk niestandardowych lub aplikacji | Metryki | Każda aplikacja Instrumentacją zapewnianą przez usługę Application Insights | <ul><li>**INTERFEJS API REST:** [Usługa Application Insights interfejsu API REST](https://dev.applicationinsights.io/reference)</li></ul> |
| Metryki magazynu | Metryki | Azure Storage | <ul><li>**Tabela magazynu:** [Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dane dotyczące rozliczeń | Metryki | Wszystkich usług platformy Azure | <ul><li>**INTERFEJS API REST:** [Użycie zasobów platformy Azure i interfejsów API usługi RateCard](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Dziennik aktywności | Zdarzenia | Wszystkich usług platformy Azure | <ul><li>**INTERFEJS API REST:** [Usługa Azure Monitor zdarzenia interfejsu API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Profilu dziennika](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Dzienniki diagnostyczne usługi Azure Monitor | Zdarzenia | [Zobacz tutaj](diagnostic-logs-schema.md) | <ul><li>**Magazyn obiektów blob lub zdarzenia Centrum:** [Ustawienia diagnostyczne](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Obliczenia dzienników systemu operacyjnego gościa (np.) IIS, ETW, syslogs) | Zdarzenia | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) i maszyn wirtualnych systemu Linux (wersja 2) [usług w chmurze](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela magazynu lub obiektów blob:** [Diagnostyka Windows lub Linux, Azure](diagnostics-extension-to-storage.md)</li><li>**Centrum zdarzeń:** [Usługi Windows Azure diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Dzienniki usługi App Service | Zdarzenia | Usługi aplikacji | <ul><li>**Plik, tabela lub usługi blob storage:** [Diagnostyka aplikacji sieci Web](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Dzienniki magazynu | Zdarzenia | Azure Storage | <ul><li>**Tabela magazynu:** [Analityka magazynu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertów usługi Security Center | Zdarzenia | Azure Security Center | <ul><li>**INTERFEJS API REST:** [Alerty zabezpieczeń](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Raporty usługi Active Directory | Zdarzenia | Usługa Azure Active Directory | <ul><li>**INTERFEJS API REST:** [Usługa Azure Active Directory interfejsu API programu graph](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Stan zasobu usługi Security Center | Stan | [Wszystkie obsługiwane zasobów](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**INTERFEJS API REST:** [Stany zabezpieczeń](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Kondycja zasobów | Stan | Obsługiwane usługi | <ul><li>**INTERFEJS API REST:** [Kondycja zasobu interfejsu API REST](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alerty metryk usługi Azure Monitor | Powiadomienia | [Zobacz tutaj](metrics-supported.md) | <ul><li>**Webhook:** [Alerty metryk platformy Azure](alerts-webhooks.md)</li></ul> |
| Alerty dziennika aktywności monitora platformy Azure | Powiadomienia | Wszystkich usług platformy Azure | <ul><li>**Webhook:** Alerty dziennika aktywności platformy Azure</li></ul> |
| Powiadomienia skalowania automatycznego | Powiadomienia | [Zobacz tutaj](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Schematu ładunek elementu webhook powiadomienia skalowania automatycznego](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Zapytanie wyszukiwania, alerty dzienników | Powiadomienia | Dzienniki usługi Azure Monitor | <ul><li>**Webhook:** [Akcja elementu Webhook dla reguł alertów dzienników](alerts-log-webhook.md)</li></ul> |
| Application Insights alertów dotyczących metryk | Powiadomienia | Application Insights | <ul><li>**Webhook:** [Alerty szczegółowe informacje o aplikacji](../../azure-monitor/app/alerts.md)</li></ul> |
| Testy sieci web programu Application Insights | Powiadomienia | Application Insights | <ul><li>**Webhook:** [Alerty szczegółowe informacje o aplikacji](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [metryk usługi Azure Monitor](data-platform.md)
- Dowiedz się więcej o [dziennika aktywności platformy Azure](activity-logs-overview.md)
- Dowiedz się więcej o [dzienniki diagnostyczne platformy Azure](diagnostic-logs-overview.md)

