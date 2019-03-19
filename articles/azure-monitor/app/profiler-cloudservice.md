---
title: Profil na żywo usług Azure Cloud Services za pomocą usługi Application Insights | Dokumentacja firmy Microsoft
description: Włącz Application Insights Profiler dla usług Azure Cloud Services.
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
ms.openlocfilehash: 2e13f1f09fcdfb68a99e705511e3659f1632132e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895485"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil na żywo usług Azure Cloud Services za pomocą usługi Application Insights

Można także wdrożyć Application Insights Profiler od następujących usług:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje usługi Service Fabric platformy Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler jest instalowany z rozszerzenie diagnostyki platformy Azure. Wystarczy Konfigurowanie diagnostyki Azure, aby zainstalować Profiler i wysłać profile do zasobu usługi Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Włącz Profiler dla usług Azure Cloud Services
1. Upewnij się, że używasz [platformy .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszej. Wystarczy potwierdzić, że *ServiceConfiguration.\*.cscfg* pliki mają `osFamily` wartość "5" lub nowszej.

1. Dodaj [usługi Application Insights SDK do usługi Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

   >**W programie profiler, który jest dostarczany w najnowszej wersji WAD dla usług Cloud Services znajduje się błąd.** Aby można było używać programu profilującego z usługą w chmurze, obsługuje on tylko zestawu SDK usługi AI do wersji 2.7.2. Jeśli używasz nowszą wersję zestawu SDK usługi AI będziesz mieć, wróć do obszaru 2.7.2 Aby można było używać programu profilującego. Jeśli używasz programu Visual Studio na starszą wersję zestawu SDK aplikacji usługi Insights może wystąpić błąd przekierowanie powiązania w czasie wykonywania. Jest to spowodowane "newVersion" w pliku web.config dla Microsoft.ApplicationInsights powinna być równa "2.7.2.0" po zmiany na starszą wersję zestawu SDK sztucznej Inteligencji, ale nie zostaje zaktualizowana automatycznie.

1. Śledzenie żądań z usługą Application Insights:

    * Dla ról sieci web platformy ASP.NET usługi Application Insights może automatycznie śledzić żądania.

    * Dla ról procesów roboczych [Dodaj kod, aby śledzić żądania](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Skonfiguruj rozszerzenia usługi Azure Diagnostics, aby umożliwić Profiler, wykonując następujące czynności:

    a. Znajdź [diagnostyki Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* plików dla roli użytkownika aplikacji, jak pokazano poniżej:  

      ![Lokalizacja pliku konfiguracji diagnostyki](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Jeśli nie można znaleźć pliku, zobacz [Konfigurowanie diagnostyki dla usług Azure Cloud Services i Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Dodaj następujący kod `SinksConfig` sekcji jako element podrzędny `WadCfg`:  

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

    > [!NOTE]
    > Jeśli *diagnostics.wadcfgx* plik zawiera także ujścia innego typu dotycząca usługi Application Insights, wszystkie trzy następujące klucze instrumentacji muszą być zgodne:  
    > * Klucz, który jest używany przez aplikację. 
    > * Klucz, który jest używany przez obiekt sink dotycząca usługi Application Insights. 
    > * Klucz, który jest używany przez obiekt sink ApplicationInsightsProfiler. 
    >
    > Można znaleźć wartości klucza Instrumentacji rzeczywiste, używanego przez `ApplicationInsights` ujścia *ServiceConfiguration.\*.cscfg* plików. 
    > Po wydaniu programu Visual Studio 15.5 Azure SDK kluczy instrumentacji, które są używane przez aplikację, jak i ujście ApplicationInsightsProfiler muszą ze sobą zgodne.

1. Wdrożyć usługę w taki sposób, przy użyciu nowej konfiguracji Diagnostics i Application Insights Profiler jest skonfigurowany do uruchamiania w usłudze.
 
## <a name="next-steps"></a>Kolejne kroki

* Generowanie ruchu do aplikacji (np. launch [testu dostępności](monitor-web-app-availability.md)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
* Zobacz [ślady Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
* Aby rozwiązywać problemy Profiler, zobacz [Profiler Rozwiązywanie problemów z](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
