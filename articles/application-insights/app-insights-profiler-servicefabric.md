---
title: Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights | Dokumentacja firmy Microsoft
description: Włącz narzędzie profiler do aplikacji usługi Service Fabric
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
ms.openlocfilehash: 1aaaa022d99e0d28056a2c846ce94e2349756799
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017858"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil aplikacji usługi Azure Service Fabric na żywo z usługą Application Insights

Można także wdrożyć usługę Application Insights profiler dla tych usług:
* [Azure App Service](../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Application Insights Profiler jest dołączony przy użyciu Windows Azure Diagnostics (WAD). Za pomocą szablonu usługi Azure RM dla klastra usługi Service Fabric można zainstalować rozszerzenia WAD. Brak tutaj przykładowy szablon: [**Szablon, który instaluje narzędzie diagnostyczne na klaster usługi Service Fabric.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Aby skonfigurować środowisko, wykonaj następujące czynności:
1. Aby upewnić się, że używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszym będą wystarczające, aby potwierdzić, że wdrożonego systemu operacyjnego jest `Windows Server 2012 R2` lub nowszej.

1. Wyszukaj [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) rozszerzenia w szablonie wdrażania pliku, a następnie dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`. Zastąp `ApplicationInsightsProfiler` wartość właściwości przy użyciu własnego klucza Instrumentacji usługi Application Insights:  

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
1. Wdrażanie klastra usługi Service Fabric przy użyciu szablonu usługi Azure Resource Manager. Ustawienia sieci są prawidłowe, Application Insights Profiler zostanie zainstalowana i włączona po zainstalowaniu rozszerzenia WAD. 
1. Dodaj usługę Application Insights do aplikacji usługi Service Fabric. Aplikacja musi wysyłania żądania danych do usługi application insights, aby profiler, aby zbierał profile swoich żądań. Instrukcje można znaleźć [tutaj.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)
1. Ponownego wdrażania aplikacji.

> [PORADA] Dla maszyn wirtualnych zamiast powyższych kroków opartych na notacji json jest nawigowanie w portalu Azure w celu **maszyn wirtualnych** > **ustawień diagnostycznych** >  **Wychwytywanie** > Ustaw Wyślij dane diagnostyczne do usługi Application Insights do **włączone** i wybierz konto usługi Application Insights lub określonych klucz instrumentacji.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów programu profilującego w [Profiler Rozwiązywanie problemów z](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).