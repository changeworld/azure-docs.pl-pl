---
title: Monitorowanie wydajności maszyn wirtualnych platformy Azure — usługa Azure Application Insights
description: Monitorowanie wydajności aplikacji dla zestawów skalowania maszyn wirtualnych platformy Azure i platformy Azure. Czas ładowania wykresu i odpowiedzi, informacje o zależnościach i ustawianie alertów dotyczących wydajności.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661332"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Wdrażanie agenta usługi Azure Monitor Application Insights na maszynach wirtualnych platformy Azure i zestawach skalowania maszyny wirtualnej platformy Azure

Włączenie monitorowania w aplikacjach sieci web opartych na platformie .NET uruchomionych na [maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/) i [zestawach skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) jest teraz łatwiejsze niż kiedykolwiek. Uzyskaj wszystkie korzyści wynikające z korzystania z usługi Application Insights bez modyfikowania kodu.

W tym artykule przedstawiono włączenie monitorowania usługi Application Insights przy użyciu agenta usługi Application Insights i zawiera wstępne wskazówki dotyczące automatyzacji procesu dla wdrożeń na dużą skalę.

> [!IMPORTANT]
> Agent usługi Azure Application Insights dla platformy .NET jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie zaleca się jej dla obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyny wirtualnej platformy Azure hostowanych aplikacji:

* **Bez kodu** za pośrednictwem agenta aplikacji Insights
    * Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna zaawansowana konfiguracja. Jest często określane jako monitorowanie "środowiska uruchomieniowego".

    * W przypadku maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure zaleca się co najmniej włączenie tego poziomu monitorowania. Następnie, na podstawie określonego scenariusza, można ocenić, czy instrumentacja ręczna jest potrzebna.

    * Agent usługi Application Insights agent automatycznie zbiera te same sygnały zależności out-of-the-box jak .NET SDK. Aby dowiedzieć się więcej, zobacz [automatyczne zbieranie zależności.](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)
        > [!NOTE]
        > Obecnie obsługiwane są tylko aplikacje hostowane przez programy .Net IIS. Użyj SDK do instruowania ASP.NET aplikacji Core, Java i Node.js hostowanych na maszynach wirtualnych platformy Azure i zestawach skalowania maszyn wirtualnych.

* **Oparty na kodzie** za pośrednictwem SDK

    * Takie podejście jest znacznie bardziej konfigurowalne, ale wymaga [dodania zależności od pakietów NuGet SDK SDK aplikacji.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) Ta metoda oznacza również, że musisz samodzielnie zarządzać aktualizacjami do najnowszej wersji pakietów.

    * Jeśli musisz wykonać niestandardowe wywołania interfejsu API do śledzenia zdarzeń/zależności nie przechwycone domyślnie z monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla zdarzeń niestandardowych i metryki artykuł,](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) aby dowiedzieć się więcej.

> [!NOTE]
> Jeśli zarówno monitorowanie oparte na agencie, jak i ręczne instrumentamencie oparte na SDK zostaną wykryte, honorowane będą tylko ręczne ustawienia instrumentacji. Ma to zapobiec wysyłaniu zduplikowanych danych. Aby dowiedzieć się więcej o tym zapoznaj się z [poniższą sekcją rozwiązywania problemów.](#troubleshooting)

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Zarządzanie agentem usługi Application Insights dla aplikacji platformy .NET na maszynach wirtualnych platformy Azure przy użyciu programu PowerShell

> [!NOTE]
> Przed zainstalowaniem agenta usługi Application Insights potrzebny jest ciąg połączenia. [Utwórz nowy zasób usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) lub skopiuj ciąg połączenia z istniejącego zasobu aplikacji.

> [!NOTE]
> Jesteś nowym w powershell? Zapoznaj się z [przewodnikiem Wprowadzenie](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Instalowanie lub aktualizowanie agenta usługi Application Insights jako rozszerzenia dla maszyn wirtualnych platformy Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Agent usługi Application Insights można zainstalować lub zaktualizować jako rozszerzenie w wielu maszynach wirtualnych na dużą skalę przy użyciu pętli programu Powershell.

Odinstalowywanie rozszerzenia agenta usługi Application Insights z maszyny wirtualnej platformy Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Stan rozszerzenia agenta usługi Query Application Insights dla maszyny wirtualnej platformy Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Pobierz listę zainstalowanych rozszerzeń dla maszyny wirtualnej platformy Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Można również wyświetlić zainstalowane rozszerzenia w [bloku maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) w portalu.

> [!NOTE]
> Sprawdź instalację, klikając strumień metryk na żywo w zasobie usługi Application Insights skojarzonym z ciągiem połączenia użytym do wdrożenia rozszerzenia agenta usługi Application Insights. Jeśli wysyłasz dane z wielu maszyn wirtualnych, wybierz docelowe maszyny wirtualne platformy Azure w obszarze Nazwa serwera. Może upłynąć do minuty, aby dane zaczęły przepływać.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Zarządzanie agentem usługi Application Insights dla aplikacji platformy .NET w zestawach skalowania maszyny wirtualnej platformy Azure przy użyciu programu PowerShell

Instalowanie lub aktualizowanie agenta usługi Application Insights jako rozszerzenia zestawu skalowania maszyny wirtualnej platformy Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Odinstaluj rozszerzenie monitorowania aplikacji z zestawów skalowania maszyny wirtualnej platformy Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Stan rozszerzenia monitorowania aplikacji kwerendy dla zestawów skalowania maszyny wirtualnej platformy Azure
```powershell
# Not supported by extensions framework
```

Pobierz listę zainstalowanych rozszerzeń dla zestawów skalowania maszyny wirtualnej platformy Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Znajdź porady dotyczące rozwiązywania problemów z rozszerzeniem agenta monitorowania aplikacji usługi Application Insights dla aplikacji platformy .NET uruchomionych na maszynach wirtualnych platformy Azure i zestawach skalowania maszyn wirtualnych.

> [!NOTE]
> Aplikacje .NET Core, Java i Node.js są obsługiwane tylko na maszynach wirtualnych platformy Azure i zestawach skalowania maszyny wirtualnej platformy Azure za pomocą ręcznego instrumentacji opartego na zestawie SDK i dlatego poniższe kroki nie mają zastosowania do tych scenariuszy.

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [wdrożyć aplikację w zestawie skalowania maszyny wirtualnej platformy Azure.](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)
* [Skonfiguruj testy sieci Web dostępności,](monitor-web-app-availability.md) aby otrzymywać alerty, jeśli punkt końcowy nie działa.
