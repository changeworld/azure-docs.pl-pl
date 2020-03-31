---
title: 'Usługa Application Insights: języki, platformy i integracje | Dokumenty firmy Microsoft'
description: Języki, platformy i integracje dostępne dla usługi Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136727"
---
# <a name="supported-languages"></a>Obsługiwane języki

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Javascript](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Obsługiwane platformy i struktury

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentacja dla już wdrożonych aplikacji (bezkodowych, opartych na agentach)
* [Zestawy skalowania maszyn wirtualnych platformy Azure i platformy Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Usługa aplikacji platformy Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET — dla aplikacji, które już działają](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Usługi w chmurze azure](../../azure-monitor/app/cloudservices.md), w tym role sieci web i procesu roboczego
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentacja za pomocą kodu (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Aplikacja uniwersalna systemu Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplikacje klasyczne, usługi i role procesów roboczych systemu Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Struktury rejestrowania
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog lub System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J lub Logback](../../azure-monitor/app/java-trace-logs.md)
* [Dodatek LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Eksport i analiza danych
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Analiza strumienia](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nieobsługiwane zestawy SDK
Zdajemy sobie sprawę, że istnieje kilka innych sdk obsługiwanych przez społeczność. Jednak usługa Azure Monitor zapewnia obsługę tylko podczas korzystania z obsługiwanych zestawów SDK wymienionych na tej stronie. Stale oceniamy możliwości rozszerzenia naszej obsługi dla innych języków, więc śledź naszą stronę [Ogłoszenia GitHub,](https://github.com/microsoft/ApplicationInsights-Announcements/issues) aby otrzymywać najnowsze wiadomości sdk. 
