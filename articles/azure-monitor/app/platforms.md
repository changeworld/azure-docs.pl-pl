---
title: 'Application Insights: Języki, platformy i integracje | Microsoft Docs'
description: Języki, platformy i integracje dostępne dla Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: dd2fb0c9ef157e3706ca3d902f7feebaf4c2f279
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478907"
---
# <a name="supported-languages"></a>Obsługiwane języki

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Obsługiwane platformy i struktury

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentacja dla już wdrożonych aplikacji (bez kodu, oparta na agentach)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych platformy Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Usługa Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET — dla aplikacji, które już działają](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Cloud Services platformy Azure](../../azure-monitor/app/cloudservices.md), w tym role sieci Web i procesu roboczego
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentacja poprzez kod (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Aplikacja uniwersalna systemu Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplikacje klasyczne, usługi i role procesów roboczych systemu Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Struktury rejestrowania
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog lub System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J lub Logback](../../azure-monitor/app/java-trace-logs.md)
* [Dodatek LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Eksportowanie i analiza danych
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-community-sdks"></a>Nieobsługiwane zestawy SDK społeczności

Istnieje wiele zestawów SDK dla społeczności Application Insights. Zestawy SDK Wspólnoty nie są oficjalnie obsługiwane/utrzymywane przez firmę Microsoft. Nie możemy zapewnić wsparcia dla żadnego zestawu SDK, który nie znajduje się na liście oficjalnie obsługiwanych. 