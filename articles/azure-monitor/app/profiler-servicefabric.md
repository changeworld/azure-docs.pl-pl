---
title: Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights | Dokumentacja firmy Microsoft
description: Włącz Profiler do aplikacji usługi Service Fabric
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: f816086293d663e141b2d6efe5791cc8e37eba36
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358596"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights

Można także wdrożyć Application Insights Profiler od następujących usług:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Application Insights Profiler jest dołączony do usługi Azure Diagnostics. Rozszerzenie diagnostyki platformy Azure można zainstalować przy użyciu szablonu usługi Azure Resource Manager dla klastra usługi Service Fabric. Pobierz [szablon, który instaluje usługi Azure Diagnostics na klaster usługi Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Aby skonfigurować środowisko, wykonaj następujące czynności:

1. Aby upewnić się, że używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszym będą wystarczające, aby potwierdzić, że wdrożonego systemu operacyjnego jest `Windows Server 2012 R2` lub nowszej.

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
  Dla Profiler do zbierania profile swoich żądań aplikacja musi wysyłać dane żądania do usługi Application Insights. Aby uzyskać więcej informacji, przejdź do [zestawu SDK usługi Application Insights dla projektów usługi Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) strony.

1. Ponownego wdrażania aplikacji.

> [PORADA] W przypadku maszyn wirtualnych zamiast powyższych kroków opartych na formacie JSON jest do nawigacji w witrynie Azure portal, **maszyn wirtualnych** > **ustawień diagnostycznych** > **Wychwytywanie** > **zestaw Wyślij dane diagnostyczne do usługi Application Insights na włączone** a następnie wybierz konto usługi Application Insights lub określonych klucz instrumentacji.

## <a name="next-steps"></a>Kolejne kroki

* Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
* Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
* Aby uzyskać pomoc dotyczącą rozwiązywania problemów Profiler, zobacz [Profiler Rozwiązywanie problemów z](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
