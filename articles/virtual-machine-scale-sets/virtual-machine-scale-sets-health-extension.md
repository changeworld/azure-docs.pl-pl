---
title: Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak używać rozszerzenia Kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w zestawach skalowania maszyny wirtualnej.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: cb5f1d48bb1a95db004d9da553e19a35071c73b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273736"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Używanie rozszerzenia kondycji aplikacji z zestawami skalowania maszyn wirtualnych
Monitorowanie kondycji aplikacji jest ważnym sygnałem do zarządzania i uaktualniania wdrożenia. Zestawy skalowania maszyny wirtualnej platformy Azure zapewniają obsługę [uaktualnień stopniowych,](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) w tym [automatycznych uaktualnień obrazów systemu operacyjnego,](virtual-machine-scale-sets-automatic-upgrade.md)które opierają się na monitorowaniu kondycji poszczególnych wystąpień w celu uaktualnienia wdrożenia.

W tym artykule opisano, jak można użyć rozszerzenia kondycja aplikacji do monitorowania kondycji aplikacji wdrożonych w zestawach skalowania maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że jesteś zaznajomiony z:
-   Rozszerzenia maszyn [wirtualnych](../virtual-machines/extensions/overview.md) platformy Azure
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawów skalowania maszyny wirtualnej

## <a name="when-to-use-the-application-health-extension"></a>Kiedy używać rozszerzenia Kondycja aplikacji
Rozszerzenie kondycji aplikacji jest wdrażany wewnątrz wystąpienia zestawu skalowania maszyny wirtualnej i raporty na temat kondycji maszyny wirtualnej z wewnątrz wystąpienia zestawu skalowania. Można skonfigurować rozszerzenie do sondowania w punkcie końcowym aplikacji i zaktualizować stan aplikacji w tym wystąpieniu. Ten stan wystąpienia jest sprawdzany przez platformę Azure, aby ustalić, czy wystąpienie kwalifikuje się do operacji uaktualniania.

Ponieważ rozszerzenie raportuje kondycję z poziomu maszyny Wirtualnej, rozszerzenie może być używane w sytuacjach, gdy nie można używać zewnętrznych sond, takich jak sondy kondycji aplikacji (które korzystają z niestandardowych [sond](../load-balancer/load-balancer-custom-probe-overview.md)równoważenia obciążenia platformy Azure).

## <a name="extension-schema"></a>Schemat rozszerzenia

Poniżej przedstawiono schemat rozszerzenia kondycji aplikacji. Rozszerzenie wymaga co najmniej żądania "tcp" lub "http" ze skojarzonym portem lub ścieżką żądania.

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

| Nazwa | Wartość / Przykład | Typ danych
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| wydawca | `Microsoft.ManagedServices` | ciąg |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | ciąg |
| typHandlerVersion | `1.0` | int |

### <a name="settings"></a>Ustawienia

| Nazwa | Wartość / Przykład | Typ danych
| ---- | ---- | ----
| Protokół | `http` lub `tcp` | ciąg |
| port | Opcjonalnie, `http`gdy protokół jest obowiązkowy, gdy protokół jest`tcp` | int |
| requestPath | Obowiązkowe, gdy `http`protokół jest , nie jest dozwolone, gdy protokół jest`tcp` | ciąg |

## <a name="deploy-the-application-health-extension"></a>Wdrażanie rozszerzenia kondycji aplikacji
Istnieje wiele sposobów wdrażania rozszerzenia kondycji aplikacji do zestawów skalowania, jak opisano w poniższych przykładach.

### <a name="rest-api"></a>Interfejs API REST

Poniższy przykład dodaje rozszerzenie kondycji aplikacji (o nazwie myHealthExtension) do rozszerzeniaProfile w modelu zestawu skalowania zestawu skalowania opartego na systemie Windows.

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

Użyj polecenia cmdlet [Add-AzVmssExtension,](/powershell/module/az.compute/add-azvmssextension) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania.

Poniższy przykład dodaje rozszerzenie kondycji `extensionProfile` aplikacji do modelu zestawu skalowania zestawu skalowania opartego na systemie Windows. W przykładzie użyto nowego modułu Az PowerShell.

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

Użyj [zestawu rozszerzeń az vmss,](/cli/azure/vmss/extension#az-vmss-extension-set) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania.

W poniższym przykładzie dodano rozszerzenie kondycji aplikacji do modelu zestawu skalowania zestawu skalowania opartego na systemie Linux.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Zawartość pliku extension.json.

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
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyny wirtualnej.
