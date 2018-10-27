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
ms.openlocfilehash: 10cd05bd40262815e3b27c861982debc18e5b4f3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142423"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Profil aplikacji sieci web uruchomionej na maszynie wirtualnej platformy Azure lub maszyny wirtualnej zestawu skalowania przy użyciu Application Insights Profiler
Można także wdrożyć usługę Application Insights profiler dla tych usług:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Wdrażanie Profiler na maszynie wirtualnej lub zestawu skalowania
Ta strona przeprowadzi Cię przez kroki wymagana można pobrać usługi Application Insights profiler uruchomionej na maszynie Wirtualnej platformy Azure lub skalowania maszyn wirtualnych zestawu. Application Insights Profiler jest instalowany przy użyciu rozszerzenia diagnostyki Azure Windows dla maszyn wirtualnych. Należy skonfigurować rozszerzenia profilera i zestawu SDK aplikacji usługi Insights wbudowanych w aplikacji, aby otrzymywać profilów dla aplikacji sieci web uruchomiony na maszynie Wirtualnej.

1. Dodaj application Insights SDK do swojej [aplikacji ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) lub regularnych [aplikacji .NET.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Użytkownik musi wysyłać dane telemetryczne żądania do usługi Application Insights Zobacz profile dla żądań.
1. Zainstaluj rozszerzenie Diagnostyka Azure Windows na maszynie Wirtualnej. Aby uzyskać pełne przykłady szablonów usługi Resource Manager zobacz:  
    * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. Wdrożenia zmodyfikowane środowisko definicji wdrożenia.  

   Aby zastosować zmiany, zazwyczaj polega na wdrożeniu pełny szablon lub chmury usługi publikowania za pomocą poleceń cmdlet programu PowerShell lub programu Visual Studio.  

   Następujące polecenia programu powershell są alternatywne podejście do istniejących maszyn wirtualnych, odnoszące się przez rozszerzenie Diagnostyka Azure:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. W przypadku zamierzonej aplikacji działających za pośrednictwem [IIS](https://www.microsoft.com/web/downloads/platform.aspx), Włącz `IIS Http Tracing` funkcji Windows, wykonując następujące czynności:  

   a. Ustanowić dostęp zdalny do środowiska, a następnie użyj [Windows Dodaj funkcje]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) okna lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ustanawianie dostęp zdalny jest problem, można użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Wdrażanie aplikacji.

## <a name="enable-profiler-on-on-premises-servers"></a>Włącz Profiler na serwerach lokalnych

Włączanie Profiler na lokalny serwer jest również nazywany uruchomionej Application Insights Profiler w trybie autonomicznym. Nie jest związany z modyfikacje rozszerzenia diagnostyki platformy Azure.

Mamy zamiar świadczył oficjalną pomoc techniczną Profiler dla serwerów lokalnych. Jeśli interesuje Cię eksperymentowanie z tego scenariusza, możesz to zrobić [Pobierz kod pomocy technicznej](https://github.com/ramach-msft/AIProfiler-Standalone). Jesteśmy *nie* odpowiedzialna za utrzymanie kodu, lub reagowania na problemy i żądania funkcji, które są związane z kodem.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Uzyskaj pomoc dotyczącą rozwiązywania problemów programu profilującego w [Profiler Rozwiązywanie problemów z](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
