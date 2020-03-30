---
title: Aplikacje sieci szkieletowej usługi Azure na żywo na żywo z usługą Application Insights
description: Włącz profiler dla aplikacji sieci szkieletowej usług
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671617"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Aplikacje sieci szkieletowej usług Azure na żywo na żywo z usługą Application Insights

Profiler usługi Application Insights można również wdrożyć w następujących usługach:
* [Usługa aplikacji platformy Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Maszyny wirtualne platformy Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Konfigurowanie definicji wdrożenia środowiska

Profiler usługi Application Insights jest dołączony do diagnostyki platformy Azure. Rozszerzenie diagnostyki platformy Azure można zainstalować przy użyciu szablonu usługi Azure Resource Manager dla klastra sieci szkieletowej usług. Pobierz [szablon, który instaluje diagnostykę platformy Azure w klastrze sieci szkieletowej usług](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Aby skonfigurować środowisko, należy podjąć następujące działania:

1. Profiler obsługuje program .NET Framework i .Net Core. Jeśli używasz programu .NET Framework, upewnij się, że używasz [programu .NET Framework 4.6.1 lub nowszego.](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) Wystarczy potwierdzić, że wdrożony system `Windows Server 2012 R2` operacyjny jest lub później. Profiler obsługuje .NET Core 2.1 i nowsze aplikacje.

1. Wyszukaj rozszerzenie [diagnostyki platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) w pliku szablonu wdrożenia.

1. Dodaj następującą `SinksConfig` sekcję jako `WadCfg`element podrzędny . Zastąp `ApplicationInsightsProfiler` wartość właściwości własnym kluczem instrumentacji usługi Application Insights:  

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

      Aby uzyskać informacje dotyczące dodawania rozszerzenia diagnostyki do szablonu wdrożenia, zobacz [Używanie monitorowania i diagnostyki za pomocą szablonów Maszyny Wirtualnej systemu Windows i Usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Wdrażanie klastra sieci szkieletowej usług przy użyciu szablonu usługi Azure Resource Manager.  
  Jeśli ustawienia są poprawne, profiler usługi Application Insights zostanie zainstalowany i włączony po zainstalowaniu rozszerzenia diagnostyki platformy Azure. 

1. Dodaj usługę Application Insights do aplikacji sieci szkieletowej usług.  
  Aby profiler zbierał profile dla żądań, aplikacja musi śledzić operacje za pomocą usługi Application Insights. W przypadku interfejsów API bezstanowych można zapoznać się z instrukcjami [śledzenia żądań profilowania](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Aby uzyskać więcej informacji na temat śledzenia operacji niestandardowych w innych rodzajach aplikacji, zobacz [śledzenie operacji niestandardowych za pomocą pliku Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Ponowne wdrożenie aplikacji.


## <a name="next-steps"></a>Następne kroki

* Generowanie ruchu do aplikacji (na przykład uruchomienie [testu dostępności).](monitor-web-app-availability.md) Następnie poczekaj od 10 do 15 minut, aż ślady zaczną być wysyłane do wystąpienia usługi Application Insights.
* Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
* Aby uzyskać pomoc dotyczącą rozwiązywania problemów z profilem, zobacz [Rozwiązywanie problemów z profilem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
