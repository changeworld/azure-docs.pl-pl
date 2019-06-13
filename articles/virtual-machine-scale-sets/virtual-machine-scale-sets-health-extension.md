---
title: Użyj rozszerzenia kondycji aplikacji za pomocą zestawów skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać rozszerzenia kondycji aplikacji do monitorowania kondycji aplikacji wdrożonych na zestawach skalowania maszyn wirtualnych.
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
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619828"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Inicjowanie zestawów rozszerzenie skalowania maszyn wirtualnych za pomocą kondycji aplikacji
Monitorowanie kondycji aplikacji jest ważne sygnał dla uaktualnienie wdrożenie i zarządzanie nimi. Zestawy skalowania maszyn wirtualnych platformy Azure zapewniają obsługę [uaktualnień stopniowych](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) w tym [automatyczne uaktualnienia obrazu systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md), które polegają na monitorowanie kondycji poszczególnych wystąpień, aby uaktualnić wdrożenie .

W tym artykule opisano, jak można użyć rozszerzenia kondycji aplikacji do monitorowania kondycji aplikacji wdrożonych na zestawach skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   Maszyna wirtualna platformy Azure [rozszerzenia](../virtual-machines/extensions/overview.md)
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawy skalowania maszyn wirtualnych

## <a name="when-to-use-the-application-health-extension"></a>Kiedy należy używać rozszerzenia kondycji aplikacji
Rozszerzenie kondycji aplikacji jest wdrażany w wystąpieniu zestawu skalowania maszyny wirtualnej i raportów o kondycji maszyny Wirtualnej z wewnątrz wystąpienia w zestawie skalowania. Można skonfigurować rozszerzenia sondowania w punkcie końcowym usługi aplikacji i aktualizuje stan aplikacji, w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure, aby ustalić, czy wystąpienie kwalifikują się do operacji uaktualnienia.

Jako rozszerzenie raporty o kondycji z na maszynie wirtualnej, rozszerzenia mogą być używane w sytuacjach, w której zewnętrzny sondy przykład sondy kondycji aplikacji (które korzystanie z niestandardowych usługi Azure Load Balancer [sondy](../load-balancer/load-balancer-custom-probe-overview.md)) nie może być używane.

## <a name="extension-schema"></a>Schemat rozszerzenia

Następujący kod JSON zawiera schemat dla rozszerzenia kondycji aplikacji. Rozszerzenie wymaga co najmniej "tcp" lub "http" żądania z portem skojarzone lub ścieżka żądania, odpowiednio.

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
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ustawienia

| Name (Nazwa) | Wartość / przykład | Typ danych
| ---- | ---- | ----
| protocol | `http` lub `tcp` | string |
| port | Opcjonalnie, protokołu `http`obowiązujący protokołu `tcp` | int |
| requestPath | Obowiązkowe protokołu `http`, niedozwolone, gdy protokół jest `tcp` | string |

## <a name="deploy-the-application-health-extension"></a>Wdrażanie rozszerzenia kondycji aplikacji
Istnieje wiele sposobów wdrażania kondycji aplikacji, Ustawia rozszerzenie do skalowania, zgodnie z opisem w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dodaje rozszerzenia kondycji aplikacji (przy użyciu nazwy myHealthExtension) do extensionProfile w modelu zestawu skalowania zestawu skalowania z systemem Windows.

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
Użyj `PATCH` do edytowania już wdrożonej rozszerzeniem.

### <a name="azure-powershell"></a>Azure PowerShell

Użyj [AzVmssExtension Dodaj](/powershell/module/az.compute/add-azvmssextension) modelu definicji zestawu polecenia cmdlet, aby dodać rozszerzenie kondycji aplikacji do skalowania.

W poniższym przykładzie dodano rozszerzenie kondycji aplikacji `extensionProfile` w zestawu skalowania modelu zestawu skalowania z systemem Windows. W przykładzie użyto nowego modułu programu PowerShell Az.

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

Użyj [az vmss rozszerzenia zestawu](/cli/azure/vmss/extension#az-vmss-extension-set) można dodać rozszerzenia kondycji aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenia kondycji aplikacji do modelu zestawu skalowania zestawu skalowania z systemem Windows.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów
Dane wyjściowe wykonywania rozszerzenia jest rejestrowany wpis pliki znajdujące się w następujących katalogach:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Dzienniki okresowo przechwytywać stan kondycji aplikacji.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) ustawia skalowania maszyn wirtualnych.
