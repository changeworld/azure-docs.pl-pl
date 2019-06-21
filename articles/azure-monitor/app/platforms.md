---
title: 'Application Insights: języki, platformy i integracje | Microsoft Docs'
description: Języki, platformy i integracje dostępne dla usługi Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/16/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 99245dd7628aa4d25e44266a3e798e2f96501f1e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275384"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Rozszerzenie Developer Analytics: języki, platformy i integracje
Poniższe elementy to znane implementacje usługi [Application Insights](../../azure-monitor/app/app-insights-overview.md), w tym niektóre innych firm.

## <a name="languages---officially-supported-by-application-insights-team"></a>Języki — oficjalnie obsługiwane przez zespół usługi Application Insights
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Strony sieci Web w języku JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Języki — obsługiwane przez społeczności
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [Go](https://github.com/Microsoft/ApplicationInsights-go)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Coś innego](#projects)

## <a name="platforms-and-frameworks"></a>Platformy i struktury
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET — dla aplikacji, które już działają](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Usługa Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* Usługi [Azure Cloud Services](../../azure-monitor/app/cloudservices.md) &#151; w tym role sieci Web i procesu roboczego
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Stos SAFE](https://safe-stack.github.io/docs/template-azure-ai/)
* [Aplikacja uniwersalna systemu Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplikacje klasyczne, usługi i role procesów roboczych systemu Windows](../../azure-monitor/app/windows-desktop.md)
* [Coś innego](#projects)

## <a name="logging-frameworks"></a>Struktury rejestrowania
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog lub System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J lub Logback](../../azure-monitor/app/java-trace-logs.md)
* [Semantic Logging (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) — integruje się z usługą [Semantic Logging Application Block](https://msdn.microsoft.com/library/dn440729.aspx)
* [Testowanie obciążeniowe w chmurze](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Dodatek LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="content-management-systems"></a>Systemy zarządzania zawartością
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Eksportowanie i analiza danych
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Tworzenie własnych zestawów SDK
Jeśli brakuje zestawu SDK dla potrzebnego języka lub platformy, może zechcesz taki utworzyć? Przejrzyj kod istniejących zestawów SDK wymienionych w [projekcie zestawu SDK usługi Application Insights SDK w witrynie GitHub](https://github.com/Microsoft/AppInsights-Home).
