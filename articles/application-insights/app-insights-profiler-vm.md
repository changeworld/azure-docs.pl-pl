---
title: Profil aplikacji sieci web działających na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Profil aplikacji sieci web na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: d55ff92fcac2d52cd12ae82a7c11f83824b3a201
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824697"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profil aplikacji sieci web uruchomionej na maszynie wirtualnej platformy Azure lub maszyny wirtualnej zestawu skalowania przy użyciu Application Insights Profiler
Można także wdrożyć usługę Application Insights profiler dla tych usług:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Wdrażanie Profiler na maszynie wirtualnej lub zestawu skalowania
Ta strona przeprowadzi Cię przez kroki wymagana można pobrać usługi Application Insights profiler uruchomionej na maszynie Wirtualnej platformy Azure lub skalowania maszyn wirtualnych zestawu. Application Insights Profiler jest instalowany przy użyciu rozszerzenia diagnostyki Azure Windows dla maszyn wirtualnych. Rozszerzenie musi być skonfigurowane do uruchamiania programu profilującego i zestawu SDK aplikacji usługi Insights jest wbudowana w aplikację.

1. Dodaj application Insights SDK do swojej [aplikacji ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) lub regularnych [aplikacji .NET.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Użytkownik musi wysyłać dane telemetryczne żądania do usługi Application Insights Zobacz profile dla żądań.
1. Zainstaluj rozszerzenie Diagnostyka Azure Windows na maszynie Wirtualnej. Aby uzyskać pełne przykłady szablonów usługi Resource Manager zobacz:  
    * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    Kluczowym elementem jest ApplicationInsightsProfilerSink w WadCfg. Dodaj inny obiekt sink do tej sekcji, aby poinformować WAD, aby włączyć program profilujący do wysyłania danych do iKey.
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

   Aby zastosować zmiany, zazwyczaj obejmuje to pełny szablon wdrożenia lub usługi w chmurze na podstawie opublikowane za pomocą poleceń cmdlet programu PowerShell lub Visual Studio.  

   Następujące polecenia programu powershell są alternatywne podejście do istniejących maszyn wirtualnych, odnoszące się przez rozszerzenie Diagnostyka Azure. Wystarczy dodać ProfilerSink, jak wspomniano powyżej, do konfiguracji, który jest zwracany przez polecenie Get-AzureRmVMDiagnosticsExtension. Następnie przekazać zaktualizowany konfiguracji do polecenia Set-AzureRmVMDiagnosticsExcension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. W przypadku zamierzonej aplikacji działających za pośrednictwem [IIS](https://www.microsoft.com/web/downloads/platform.aspx), Włącz `IIS Http Tracing` funkcji Windows.

   a. Ustanowić dostęp zdalny do środowiska, a następnie użyj [Windows Dodaj funkcje]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ustanawianie dostęp zdalny jest problem, można użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Wdrażanie aplikacji.

## <a name="can-profiler-run-on-on-premises-servers"></a>Czy program profilujący może uruchamiać na serwerach w środowisku lokalnym?
Nie mamy żadnych planów obsługuje Application Insights Profiler dla serwerów lokalnych.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów programu profilującego w [Profiler Rozwiązywanie problemów z](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
