---
title: Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się, jak używać rozszerzenia kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w zestawach skalowania maszyn wirtualnych.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: e074d76f9ed095725d99bddc9eb21925f4b3697c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114487"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych
Monitorowanie kondycji aplikacji jest ważnym sygnałem do zarządzania wdrożeniem i uaktualniania go. Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają [](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) obsługę uaktualnień stopniowych, takich jak [automatyczne uaktualnienia obrazu systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md), które polegają na monitorowaniu kondycji poszczególnych wystąpień w celu uaktualnienia wdrożenia.

W tym artykule opisano, jak można użyć rozszerzenia kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w ramach zestawów skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   [Rozszerzenia](../virtual-machines/extensions/overview.md) maszyny wirtualnej platformy Azure
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawów skalowania maszyn wirtualnych

## <a name="when-to-use-the-application-health-extension"></a>Kiedy używać rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażane wewnątrz wystąpienia zestawu skalowania maszyn wirtualnych i raportuje kondycję maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i zaktualizować stan aplikacji w tym wystąpieniu. To wystąpienie jest sprawdzane przez platformę Azure w celu ustalenia, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie raportuje kondycję z maszyny wirtualnej, rozszerzenie może być używane w sytuacjach, w których nie można używać sond zewnętrznych, takich jak sondy kondycji aplikacji (które korzystają z [sond](../load-balancer/load-balancer-custom-probe-overview.md)Azure Load Balancer niestandardowych).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniższy kod JSON przedstawia schemat rozszerzenia kondycji aplikacji. Rozszerzenie wymaga co najmniej jednego żądania "TCP" lub "http" ze skojarzonym portem lub ścieżką żądania.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Wartości właściwości

| Name (Nazwa) | Wartość / przykład | Typ danych
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | string |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (system Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ustawienia

| Name | Wartość / przykład | Typ danych
| ---- | ---- | ----
| protocol | `http` lub `tcp` | string |
| port | Opcjonalne, gdy protokół `http`jest, obowiązkowy, gdy protokół jest`tcp` | int |
| requestPath | Obowiązkowe, gdy protokół `http`jest, niedozwolony, gdy protokół jest`tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Wdróż rozszerzenie kondycji aplikacji
Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji w zestawach skalowania, jak opisano w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dodaje rozszerzenie kondycji aplikacji (o nazwie myHealthExtension) do extensionProfile w modelu zestawu skalowania zestawu skalowania na podstawie systemu Windows.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Służy `PATCH` do edytowania już wdrożonego rozszerzenia.

### <a name="azure-powershell"></a>Azure PowerShell

Użyj polecenia cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) , aby dodać rozszerzenie kondycja aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie kondycji aplikacji do `extensionProfile` modelu zestawu skalowania na podstawie zestawu skalowania systemu Windows. W przykładzie zastosowano nowy AZ PowerShell module.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [AZ VMSS Extension Set](/cli/azure/vmss/extension#az-vmss-extension-set) , aby dodać rozszerzenie kondycji aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie kondycja aplikacji do modelu zestawu skalowania opartego na systemie Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Zawartość pliku rozszerzenia. JSON.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Dane wyjściowe wykonania rozszerzenia są rejestrowane w plikach znalezionych w następujących katalogach:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Dzienniki również okresowo przechwytują stan kondycji aplikacji.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyn wirtualnych.
