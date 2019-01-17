---
title: Profilów aplikacji internetowych uruchomionych na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Profil aplikacji sieci web na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler.
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
ms.openlocfilehash: 3f720cdf369e7377f16bb2ea9cba7e898097cc29
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359786"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profil aplikacji sieci web uruchomionej na maszynie wirtualnej platformy Azure lub skalowania maszyn wirtualnych ustawić za pomocą Application Insights Profiler

Można także wdrożyć usługi Azure Application Insights Profiler od następujących usług:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Wdrażanie Profiler na maszynie wirtualnej lub zestawu skalowania maszyn wirtualnych
W tym artykule pokazano, jak uzyskać Application Insights Profiler uruchomionej na maszyn wirtualnych (VM) lub w zestawie skalowania maszyn wirtualnych platformy Azure. Profiler jest instalowany przy użyciu rozszerzenia diagnostyki Azure dla maszyn wirtualnych. Skonfiguruj rozszerzenia Aby uruchomić Profiler i umieszczenie zestawu SDK usługi Application Insights w aplikacji.

1. Dodaj zestaw SDK usługi Application Insights do swojej [aplikacji ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) lub regularnych [aplikacji .NET](https://docs.microsoft.com/azure/application-insights/windows-services?toc=/azure/azure-monitor/toc.json).  
  Aby wyświetlić profile swoich żądań, konieczne jest wysłanie żądania danych telemetrycznych do usługi Application Insights.

1. Zainstaluj rozszerzenie diagnostyki platformy Azure na maszynie Wirtualnej. Aby uzyskać pełne przykłady szablonów usługi Resource Manager zobacz:  
    * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Kluczowym elementem jest ApplicationInsightsProfilerSink w WadCfg. Aby diagnostyki platformy Azure, Włącz Profiler do wysyłania danych do iKey, należy dodać inny obiekt sink do tej sekcji.
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Wdrożenia zmodyfikowane środowisko definicji wdrożenia.  

   Zastosowanie zmiany zwykle obejmuje pełny szablon wdrożenia lub chmury oparta na usłudze opublikowane za pomocą poleceń cmdlet programu PowerShell lub Visual Studio.  

   Następujące polecenia programu PowerShell są alternatywne podejście do istniejących maszyn wirtualnych, odnoszące się przez rozszerzenie Diagnostyka Azure. Dodaj ProfilerSink wymienionych wcześniej do konfiguracji, który jest zwracany przez polecenie Get-AzureRmVMDiagnosticsExtension, a następnie przekazać zaktualizowany konfiguracji do polecenia Set-AzureRmVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. W przypadku zamierzonej aplikacji działających za pośrednictwem [IIS](https://www.microsoft.com/web/downloads/platform.aspx), Włącz `IIS Http Tracing` funkcji Windows.

   a. Ustanowić dostęp zdalny do środowiska, a następnie użyj [Windows Dodaj funkcje]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna. Lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ustanawianie dostęp zdalny jest problem, można użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Wdrażanie aplikacji.

## <a name="can-profiler-run-on-on-premises-servers"></a>Czy Profiler może uruchamiać na serwerach w środowisku lokalnym?
Mamy planujesz obsługę Application Insights Profiler na serwerach lokalnych.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Profiler, zobacz [Profiler Rozwiązywanie problemów z](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
