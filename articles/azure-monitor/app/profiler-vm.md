---
title: Profilowanie aplikacji sieci Web na maszynie Wirtualnej Platformy Azure — profiler usługi Application Insights
description: Profiluj aplikacje sieci Web na maszynie Wirtualnej platformy Azure przy użyciu programu Profiler usługi Application Insights.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671583"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Profilowanie aplikacji sieci Web uruchomionych na maszynie wirtualnej platformy Azure lub skalowaniu maszyny wirtualnej ustawionej przy użyciu programu Profiler usługi Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Można również wdrożyć usługi Azure Application Insights Profiler w następujących usługach:
* [Usługa aplikacji platformy Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Wdrażanie programu Profiler na maszynie wirtualnej lub zestawie skalowania maszyny wirtualnej
W tym artykule pokazano, jak uzyskać application insights profiler uruchomiony na maszynie wirtualnej platformy Azure (VM) lub platformy Azure zestaw skalowania maszyny wirtualnej. Profiler jest zainstalowany z rozszerzeniem diagnostyki platformy Azure dla maszyn wirtualnych. Skonfiguruj rozszerzenie do uruchamiania programu Profiler i skompiluj sdk usługi Application Insights w aplikacji.

1. Dodaj sdk usługi Application Insights do [aplikacji ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net).

   Aby wyświetlić profile dla żądań, należy wysłać dane telemetryczne żądania do usługi Application Insights.

1. Zainstaluj rozszerzenie diagnostyki platformy Azure na maszynie wirtualnej. Aby uzyskać pełne przykłady szablonów Menedżera zasobów, zobacz:  
   * [Maszyna wirtualna](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Zestaw skalowania maszyn wirtualnych](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     Kluczową częścią jest ApplicationInsightsProfilerSink w WadCfg. Aby usługa Azure Diagnostics włączyć program Profiler do wysyłania danych do iKey, dodaj inny ujście do tej sekcji.
    
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

1. Wdrażanie definicji wdrożenia zmodyfikowanego środowiska.  

   Stosowanie modyfikacji zwykle obejmuje pełne wdrożenie szablonu lub publikowanie w ramach usługi w chmurze za pośrednictwem poleceń cmdlet programu PowerShell lub programu Visual Studio.  

   Następujące polecenia programu PowerShell są alternatywnym podejściem dla istniejących maszyn wirtualnych, które dotykają tylko rozszerzenia diagnostyki platformy Azure. Dodaj wcześniej wspomniano ProfilerSink do konfiguracji, która jest zwracana przez Get-AzVMDiagnosticsExtension polecenia. Następnie przekaż zaktualizowaną konfigurację do polecenia Set-AzVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Jeśli zamierzona aplikacja jest uruchomiona za `IIS Http Tracing` pośrednictwem [usług IIS,](https://www.microsoft.com/web/downloads/platform.aspx)włącz funkcję systemu Windows.

   a. Ustal zdalny dostęp do środowiska, a następnie użyj okna [Dodaj funkcje systemu Windows.]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) Lub uruchom następujące polecenie w programie PowerShell (jako administrator):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Jeśli ustanowienie dostępu zdalnego jest problem, można użyć [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) aby uruchomić następujące polecenie:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Wdrażanie aplikacji.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Ustawianie ujścia programu Profiler przy użyciu Eksploratora zasobów platformy Azure
Nie mamy jeszcze sposobu, aby ustawić ujście profilera usługi Application Insights z portalu. Zamiast używać programu PowerShell, jak opisano powyżej, można użyć Eksploratora zasobów platformy Azure, aby ustawić ujście. Należy jednak pamiętać, jeśli ponownie wdrożysz maszynę wirtualną, obiekt sink zostanie utracony. Aby zachować to ustawienie, należy zaktualizować konfigurację używany podczas wdrażania maszyny Wirtualnej.

1. Sprawdź, czy rozszerzenie diagnostyki systemu Windows Azure jest zainstalowane, wyświetlając rozszerzenia zainstalowane dla maszyny wirtualnej.  

    ![Sprawdź, czy jest zainstalowane rozszerzenie WAD][wadextension]

2. Znajdź rozszerzenie diagnostyki maszyny Wirtualnej dla maszyny Wirtualnej. Przejdź [https://resources.azure.com](https://resources.azure.com)do . Rozwiń swoją grupę zasobów, Microsoft.Compute virtualMachines, nazwa maszyny wirtualnej i rozszerzenia.  

    ![Przejdź do konfiguracji WAD w Eksploratorze zasobów platformy Azure][azureresourceexplorer]

3. Dodaj ujście profilowania aplikacji insights do sinksConfig węzła w wadcfg. Jeśli nie masz jeszcze SinksConfig sekcji, może być konieczne dodanie jednego. Pamiętaj, aby określić prawidłowy iKey usługi Application Insights w ustawieniach. Musisz przełączyć tryb eksploratorów na Odczyt/Zapis w prawym górnym rogu i nacisnąć niebieski przycisk "Edytuj".

    ![Dodawanie ujścia profilera usługi Application Insights][resourceexplorersinksconfig]

4. Po zakończeniu edycji konfigura, naciśnij przycisk "Put". Jeśli odłkliwienie zakończy się pomyślnie, na środku ekranu pojawi się zielony znacznik.

    ![Wyślij żądanie wprowadzenia, aby zastosować zmiany][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Czy profiler może działać na serwerach lokalnych?
Nie planujemy obsługi aplikacji Profiler usługi Application Insights dla serwerów lokalnych.

## <a name="next-steps"></a>Następne kroki

- Generowanie ruchu do aplikacji (na przykład uruchomienie [testu dostępności).](monitor-web-app-availability.md) Następnie poczekaj od 10 do 15 minut, aż ślady zaczną być wysyłane do wystąpienia usługi Application Insights.
- Zobacz [ślady profilera](profiler-overview.md?toc=/azure/azure-monitor/toc.json) w witrynie Azure portal.
- Aby uzyskać pomoc dotyczącą rozwiązywania problemów z profilem, zobacz [Rozwiązywanie problemów z profilem](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
