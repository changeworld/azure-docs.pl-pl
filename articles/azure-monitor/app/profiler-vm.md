---
title: Profilów aplikacji internetowych uruchomionych na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Profil aplikacji sieci web na Maszynie wirtualnej platformy Azure za pomocą Application Insights Profiler.
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
ms.openlocfilehash: ab30351bfff9c5bbf070a1e8a54a4919e4d2231a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226264"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profil aplikacji sieci web uruchomionej na maszynie wirtualnej platformy Azure lub skalowania maszyn wirtualnych ustawić za pomocą Application Insights Profiler

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Można także wdrożyć usługi Azure Application Insights Profiler od następujących usług:
* [Usługa Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Wdrażanie Profiler na maszynie wirtualnej lub zestawu skalowania maszyn wirtualnych
W tym artykule pokazano, jak uzyskać Application Insights Profiler uruchomionej na maszyn wirtualnych (VM) lub w zestawie skalowania maszyn wirtualnych platformy Azure. Profiler jest instalowany przy użyciu rozszerzenia diagnostyki Azure dla maszyn wirtualnych. Skonfiguruj rozszerzenia Aby uruchomić Profiler i umieszczenie zestawu SDK usługi Application Insights w aplikacji.

1. Dodaj zestaw SDK usługi Application Insights do swojej [aplikacji ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

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

   Następujące polecenia programu PowerShell są alternatywne podejście do istniejących maszyn wirtualnych, które w ogóle przez rozszerzenie Diagnostyka Azure. Do konfiguracji, który jest zwracany przez polecenie Get-AzVMDiagnosticsExtension, należy dodać ProfilerSink wymienionych wcześniej. Następnie przekazać zaktualizowany konfiguracji do polecenia Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ustaw Profiler ujścia, za pomocą Eksploratora zasobów Azure
Jeszcze nie ma możliwości ustawienia ujścia Application Insights Profiler z poziomu portalu. Zamiast przy użyciu programu powershell, takie jak opisano powyżej, można użyć usługi Azure Resource Explorer można ustawić ujścia. Ale Uwaga: możesz wdrożyć maszynę Wirtualną, obiekt sink zostaną utracone. Należy zaktualizować konfiguracji używanych podczas wdrażania maszyny Wirtualnej, aby zachować to ustawienie.

1. Sprawdź czy przez rozszerzenie Diagnostyka Azure Windows jest zainstalowana, wyświetlając rozszerzenia zainstalowane dla maszyny wirtualnej.  

    ![Sprawdź, czy zainstalowano rozszerzenie funkcji WAD][wadextension]

1. Znajdź rozszerzenia diagnostyki maszyny Wirtualnej dla maszyny Wirtualnej. Rozwiń swoje grupy zasobów, Microsoft.Compute virtualMachines, Nazwa maszyny wirtualnej i rozszerzenia.  

    ![Przejdź do konfiguracji WAD w Eksploratorze zasobów Azure][azureresourceexplorer]

1. Dodaj obiekt sink Application Insights Profiler do SinksConfig węźle WadCfg. Jeśli nie masz jeszcze sekcji SinksConfig, może być konieczne dodanie jednego. Należy pamiętać o określeniu odpowiedniego klucz Instrumentacji usługi Application Insights w ustawieniach. Musisz zmienić tryb eksploratory do odczytu/zapisu w prawym górnym rogu, a następnie naciśnij klawisz niebieski przycisk "Edytuj".

    ![Dodaj Application Insights Profiler ujścia][resourceexplorersinksconfig]

1. Po zakończeniu edycji konfiguracji, naciśnij klawisz "Put". W przypadku pomyślnego put środku ekranu pojawi się zielony znacznik wyboru.

    ![Wyślij żądanie put, aby zastosować zmiany][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Czy Profiler może uruchamiać na serwerach w środowisku lokalnym?
Mamy planujesz obsługę Application Insights Profiler na serwerach lokalnych.

## <a name="next-steps"></a>Kolejne kroki

- Generowanie ruchu do aplikacji (np. launch [testu dostępności](monitor-web-app-availability.md)). Poczekaj 10 do 15 minut dla śladów uruchomić do wysłania do wystąpienia usługi Application Insights.
- Zobacz [ślady Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów Profiler, zobacz [Profiler Rozwiązywanie problemów z](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
