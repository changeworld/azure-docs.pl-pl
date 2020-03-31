---
title: Profil usługi w chmurze na żywo na żywo z usługami application insights | Dokumenty firmy Microsoft
description: Włącz profiler usługi Application Insights dla usług w chmurze platformy Azure.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671668"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil usług w chmurze na żywo na żywo z usługami application insights

Profiler usługi Application Insights można również wdrożyć w następujących usługach:
* [Usługa aplikacji platformy Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplikacje sieci szkieletowej usług Azure](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Profiler usługi Application Insights jest zainstalowany z rozszerzeniem diagnostyki platformy Azure. Wystarczy skonfigurować diagnostykę platformy Azure, aby zainstalować program Profiler i wysłać profile do zasobu usługi Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Włącz profiler dla usług w chmurze platformy Azure
1. Sprawdź, czy używasz [programu .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) lub nowszego. Jeśli używasz rodziny systemu operacyjnego 4, musisz zainstalować program .NET Framework 4.6.1 lub nowszy z [zadaniem uruchamiania](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet). Rodzina systemu operacyjnego 5 domyślnie zawiera zgodną wersję programu .NET Framework. 

1. Dodaj [zestaw SDK usługi Usługi Azure Cloud Services.](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)

    **Naprawiono błąd w profilu, który jest dostarczany w wad dla usług w chmurze.** Najnowsza wersja wad (1.12.2.0) dla usług w chmurze współpracuje ze wszystkimi najnowszymi wersjami SDK usługi App Insights. Hosty usługi w chmurze uaktualnią wad automatycznie, ale nie jest natychmiastowe. Aby wymusić uaktualnienie, można ponownie wdrożyć usługę lub ponownie uruchomić węzeł.

1. Śledzenie żądań za pomocą usługi Application Insights:

    * W przypadku ról sieci Web ASP.NET usługa Application Insights może automatycznie śledzić żądania.

    * W przypadku ról procesu roboczego [dodaj kod do śledzenia żądań](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Skonfiguruj rozszerzenie diagnostyki platformy Azure, aby włączyć program Profiler:

    a. Znajdź plik [diagnostyki platformy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *Azure.wadcfgx* dla roli aplikacji, jak pokazano poniżej:  

      ![Lokalizacja pliku konfiguracji diagnostyki](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Jeśli nie możesz znaleźć pliku, zobacz [Konfigurowanie diagnostyki usług w chmurze i maszyn wirtualnych platformy Azure.](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

    b. Dodaj następującą `SinksConfig` sekcję jako `WadCfg`element podrzędny:  

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
    > Jeśli plik *diagnostics.wadcfgx* zawiera również inny zlew typu ApplicationInsights, wszystkie trzy z następujących kluczy instrumentacji muszą być zgodne:  
    > * Klucz, który jest używany przez aplikację. 
    > * Klucz, który jest używany przez applicationinsights sink. 
    > * Klucz, który jest używany przez ApplicationInsightsProfiler sink. 
    >
    > Rzeczywistą wartość klucza instrumentacji, która jest `ApplicationInsights` używana przez zlew, można znaleźć w *pliku\*ServiceConfiguration. . plików cscfg.* 
    > Po wydaniu zestawie SDK platformy Azure programu Visual Studio 15.5 tylko klucze instrumentacji, które są używane przez aplikację i obiekt Sink ApplicationInsightsProfiler muszą się do siebie dopasować.

1. Wdrażanie usługi przy użyciu nowej konfiguracji diagnostyki, a profiler usługi Application Insights jest skonfigurowany do uruchamiania w usłudze.
 
## <a name="next-steps"></a>Następne kroki

* Generowanie ruchu do aplikacji (na przykład uruchomienie [testu dostępności).](monitor-web-app-availability.md) Następnie poczekaj od 10 do 15 minut, aż ślady zaczną być wysyłane do wystąpienia usługi Application Insights.
* Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
* Aby rozwiązać problemy z profilem, zobacz [Rozwiązywanie problemów z profilem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
