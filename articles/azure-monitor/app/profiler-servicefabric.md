---
title: Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights | Dokumentacja firmy Microsoft
description: Włącz Profiler do aplikacji usługi Service Fabric
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 5c01c2721a29bf142ee0ba53c9bc29ec66a7278f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727911"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights

Można także wdrożyć Application Insights Profiler od następujących usług:
* [Usługa Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Usługa Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Application Insights Profiler jest dołączony do usługi Azure Diagnostics. Rozszerzenie diagnostyki platformy Azure można zainstalować przy użyciu szablonu usługi Azure Resource Manager dla klastra usługi Service Fabric. Pobierz [szablon, który instaluje usługi Azure Diagnostics na klaster usługi Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Aby skonfigurować środowisko, wykonaj następujące czynności:

1. Profiler obsługuje środowiska .NET Framework i.Net Core. Jeśli używasz środowiska .NET Framework, upewnij się, używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszej. Wystarczy upewnić się, że wdrożonego systemu operacyjnego jest `Windows Server 2012 R2` lub nowszej. Profiler obsługuje platformy .NET Core 2.1 i nowszych aplikacji.

1. Wyszukaj [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozszerzenia w pliku szablonu wdrożenia.

1. Dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`. Zastąp `ApplicationInsightsProfiler` wartość właściwości przy użyciu własnego klucza Instrumentacji usługi Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Aby dowiedzieć się, jak dodawanie rozszerzenia diagnostyki do wdrożenia szablonu, zobacz [Użyj monitorowania i diagnostyki za pomocą szablonów maszyn wirtualnych Windows i usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Wdrażanie klastra usługi Service Fabric przy użyciu szablonu usługi Azure Resource Manager.  
  Ustawienia są prawidłowe, Application Insights Profiler zostanie zainstalowana i włączona po zainstalowaniu rozszerzenia diagnostyki Azure. 

1. Dodaj usługę Application Insights do aplikacji usługi Service Fabric.  
  Profiler do zbierania profile swoich żądań aplikacja musi być śledzenia operacji za pomocą usługi Application Insights. Bezstanowe interfejsów API, możesz zapoznać się z instrukcjami dotyczącymi [śledzenia żądań dla profilowania](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Aby uzyskać więcej informacji na temat śledzenie operacji niestandardowych w innych rodzajów aplikacji, zobacz [śledzenie operacji niestandardowych za pomocą zestawu SDK .NET usługi Application Insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Ponownego wdrażania aplikacji.


## <a name="next-steps"></a>Kolejne kroki

* Generowanie ruchu do aplikacji (np. launch [testu dostępności](monitor-web-app-availability.md)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
* Zobacz [ślady Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
* Aby uzyskać pomoc dotyczącą rozwiązywania problemów Profiler, zobacz [Profiler Rozwiązywanie problemów z](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
