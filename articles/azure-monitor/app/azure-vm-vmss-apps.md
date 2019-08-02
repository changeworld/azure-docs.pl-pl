---
title: Monitorowanie wydajności aplikacji hostowanej na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Monitorowanie wydajności aplikacji dla maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure. Załaduj wykres i czas odpowiedzi, informacje o zależnościach i ustaw alerty wydajności.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597455"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Monitorowanie wydajności aplikacji hostowanej na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure

Włączenie monitorowania w aplikacjach sieci Web opartych na architekturze .NET działających na [platformie azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) i na [platformie Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) jest teraz łatwiejsze niż kiedykolwiek wcześniej. Uzyskaj wszystkie korzyści wynikające z używania Application Insights bez modyfikowania kodu.

W tym artykule opisano Włączanie monitorowania Application Insights przy użyciu rozszerzenia ApplicationMonitoringWindows oraz wstępne wskazówki dotyczące automatyzowania procesu wdrożeń na dużą skalę.

> [!IMPORTANT]
> Rozszerzenie ApplicationMonitoringWindows platformy Azure jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre z nich mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Włącz usługę Application Insights

Istnieją dwa sposoby włączania monitorowania aplikacji dla maszyn wirtualnych platformy Azure i hostowanych aplikacji zestawu skalowania maszyn wirtualnych platformy Azure:

* **Monitorowanie aplikacji opartej na agentach** (Rozszerzenie ApplicationMonitoringWindows).
    * Ta metoda jest najłatwiejsza do włączenia i nie jest wymagana żadna Konfiguracja zaawansowana. Jest on często określany jako "środowisko uruchomieniowe". W przypadku maszyn wirtualnych platformy Azure i zestawów skalowania maszyn wirtualnych platformy Azure zalecamy co najmniej włączenie tego poziomu monitorowania. Po tym, w zależności od konkretnego scenariusza, można sprawdzić, czy jest wymagana Instrumentacja ręczna.
    * Obecnie obsługiwane są tylko aplikacje obsługiwane przez program .NET IIS.

* **Ręczne Instrumentacja aplikacji za pomocą kodu** przez zainstalowanie zestawu SDK Application Insights.

    * Takie podejście jest znacznie bardziej dostosowywalne, ale wymaga [dodania zależności do pakietów NuGet zestawu Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Ta metoda oznacza również, że konieczne jest zarządzanie aktualizacjami do najnowszej wersji pakietów.

    * Jeśli konieczne jest wykonywanie niestandardowych wywołań interfejsu API w celu śledzenia zdarzeń/zależności, które nie są przechwytywane domyślnie przy użyciu monitorowania opartego na agentach, należy użyć tej metody. Zapoznaj się z [interfejsem API dla niestandardowych zdarzeń i metryk](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) , aby dowiedzieć się więcej.

> [!NOTE]
> W przypadku wykrycia zarówno Instrumentacji opartej na agencie, jak i ręcznego zestawu SDK można wykryć tylko ręczne ustawienia Instrumentacji. Ma to na celu uniemożliwienie wysyłania zduplikowanych danych. Aby dowiedzieć się więcej na ten temat, zapoznaj się z [sekcją rozwiązywania problemów](#troubleshooting) poniżej.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Zarządzanie monitorowaniem na podstawie agenta dla aplikacji .NET na maszynie wirtualnej przy użyciu programu PowerShell

Instalowanie lub aktualizowanie rozszerzenia monitorowania aplikacji dla maszyny wirtualnej
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Odinstaluj rozszerzenie monitorowania aplikacji z maszyny wirtualnej
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Zbadaj stan rozszerzenia monitorowania aplikacji dla maszyny wirtualnej
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Pobierz listę zainstalowanych rozszerzeń dla maszyny wirtualnej
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Zarządzanie monitorowaniem opartym na agentach dla aplikacji .NET w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu programu PowerShell

Instalowanie lub aktualizowanie rozszerzenia monitorowania aplikacji dla zestawu skalowania maszyn wirtualnych platformy Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Odinstaluj rozszerzenie monitorowania aplikacji z zestawu skalowania maszyn wirtualnych platformy Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Zbadaj stan rozszerzenia monitorowania aplikacji na potrzeby zestawu skalowania maszyn wirtualnych platformy Azure
```powershell
# Not supported by extensions framework
```

Pobierz listę zainstalowanych rozszerzeń dla zestawu skalowania maszyn wirtualnych platformy Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono Przewodnik rozwiązywania problemów krok po kroku dotyczący monitorowania na podstawie rozszerzeń dla aplikacji platformy .NET działających na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure.

> [!NOTE]
> Aplikacje .NET Core, Java i Node. js są obsługiwane tylko na maszynach wirtualnych platformy Azure i w zestawach skalowania maszyn wirtualnych platformy Azure za pośrednictwem usługi Instrumentacja ręcznego zestawu SDK, w związku z czym poniższe kroki nie mają zastosowania do tych scenariuszy.

Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [wdrożyć aplikację w zestawie skalowania maszyn wirtualnych platformy Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Skonfiguruj testy sieci Web dostępności](monitor-web-app-availability.md) , aby otrzymywać alerty, jeśli punkt końcowy nie działa.
