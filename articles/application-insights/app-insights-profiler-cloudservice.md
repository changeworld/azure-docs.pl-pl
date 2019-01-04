---
title: Profil na żywo usług Azure cloud services za pomocą usługi Application Insights | Dokumentacja firmy Microsoft
description: Włącz Application Insights Profiler dla usług w chmurze.
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
ms.openlocfilehash: 264f48ed416ca146ce8f34c2634b77e68e8f077d
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022635"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil na żywo usług Azure cloud services za pomocą usługi Application Insights

Można także wdrożyć usługę Application Insights profiler dla tych usług:
* [Azure App Service](../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler jest instalowany z rozszerzeniem Windows Azure Diagnostics (WAD). Wystarczy skonfigurować narzędzie diagnostyczne, aby zainstalować profiler i wysyłanie profile do zasobu usługi Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Włącz narzędzie profiler dla usługi w chmurze platformy Azure
1. Sprawdź, jak za pomocą [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszej.  Wystarczy potwierdzić, że *ServiceConfiguration.\*.cscfg* pliki mają `osFamily` wartość "5" lub nowszej.
1. Dodaj [zestawu SDK usługi Application Insights do usługi w chmurze](../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Śledzenie żądań z usługą Application Insights:

    Dla ról sieci web platformy ASP.Net usługi Application Insights może automatycznie śledzić żądania.

    Dla ról procesów roboczych [Dodaj kod, aby śledzić żądania.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Skonfiguruj rozszerzenia Windows Azure Diagnostics (WAD), aby włączyć program profilujący.



    1. Znajdź [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* plików dla roli użytkownika aplikacji, jak pokazano poniżej:  

       ![Lokalizacja pliku konfiguracji diagnostyki](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Jeśli nie można znaleźć pliku, aby dowiedzieć się, jak włączyć rozszerzenie diagnostyki w projekcie usługi Azure Cloud Services, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **UWAGA:** Jeśli *diagnostics.wadcfgx* plik zawiera także ujścia innego typu `ApplicationInsights`, wszystkie trzy następujące klucze instrumentacji muszą być zgodne:  
    >  * Klucz, który jest używany przez aplikację.  
    >  * Klucz, który jest używany przez `ApplicationInsights` ujścia.  
    >  * Klucz, który jest używany przez `ApplicationInsightsProfiler` ujścia.  
    >
    > Można znaleźć wartości klucza Instrumentacji rzeczywiste, używanego przez `ApplicationInsights` ujścia *ServiceConfiguration.\*.cscfg* plików.  
    > Po wydaniu programu Visual Studio 15.5 Azure SDK, kluczy instrumentacji, które są używane przez aplikację i `ApplicationInsightsProfiler` konieczność ujścia ze sobą zgodne.
1. Wdrażanie usługi przy użyciu nowej konfiguracji diagnostycznych i Application Insights Profiler zostanie skonfigurowany do uruchamiania w usłudze.
 
## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów programu profilującego w [Profiler Rozwiązywanie problemów z](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).