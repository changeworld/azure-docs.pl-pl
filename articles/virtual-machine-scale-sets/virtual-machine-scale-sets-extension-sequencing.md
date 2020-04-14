---
title: Używanie sekwencjonowania rozszerzeń z zestawami skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak sekwencjonować inicjowanie obsługi administracyjnej rozszerzeń podczas wdrażania wielu rozszerzeń w zestawach skalowania maszyny wirtualnej.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: 737040699dd62d722b9a9ad4d8915ccb270c2d06
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273753"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Inicjowanie obsługi administracyjnej rozszerzenia sekwencji w zestawach skalowania maszyny wirtualnej
Rozszerzenia maszyn wirtualnych platformy Azure zapewniają funkcje, takie jak konfiguracja i zarządzanie po wdrożeniu, monitorowanie, zabezpieczenia i inne. Wdrożenia produkcyjne zazwyczaj używają kombinacji wielu rozszerzeń skonfigurowanych dla wystąpień maszyn wirtualnych, aby osiągnąć pożądane wyniki.

Podczas korzystania z wielu rozszerzeń na maszynie wirtualnej, ważne jest, aby upewnić się, że rozszerzenia wymagające tych samych zasobów systemu operacyjnego nie próbują uzyskać tych zasobów w tym samym czasie. Niektóre rozszerzenia zależą również od innych rozszerzeń, aby zapewnić wymagane konfiguracje, takie jak ustawienia środowiska i wpisy tajne. Bez prawidłowej kolejności i sekwencjonowania w miejscu, wdrożenia rozszerzenia zależnego może zakończyć się niepowodzeniem.

W tym artykule opisano, jak sekwencjonować rozszerzenia, które mają być skonfigurowane dla wystąpień maszyn wirtualnych w zestawach skalowania maszyny wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że jesteś zaznajomiony z:
-   Rozszerzenia maszyn [wirtualnych](../virtual-machines/extensions/overview.md) platformy Azure
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawów skalowania maszyny wirtualnej

## <a name="when-to-use-extension-sequencing"></a>Kiedy używać sekwencjonowania rozszerzeń
Sekwencjonowanie rozszerzeń w nieojleczej dla zestawów skalowania i o ile nie określono, rozszerzenia mogą być aprowiowane w wystąpieniu zestawu skalowania w dowolnej kolejności.

Na przykład jeśli model zestawu skalowania ma dwa rozszerzenia — ExtensionA i ExtensionB — określone w modelu, może wystąpić jedną z następujących sekwencji inicjowania obsługi administracyjnej:
-   ExtensionA -> ExtensionB
-   ExtensionB -extensiona >A

Jeśli aplikacja wymaga rozszerzenia A zawsze być aprowizgodnie przed rozszerzeniem B, a następnie należy użyć sekwencjonowania rozszerzenia, jak opisano w tym artykule. W przypadku sekwencjonowania rozszerzeń wystąpi tylko jedna sekwencja:
-   ExtensionA - > ExtensionB

Wszelkie rozszerzenia nie określony w zdefiniowanej sekwencji inicjowania obsługi administracyjnej mogą być aprowizowane w dowolnym momencie, w tym przed, po lub podczas zdefiniowanej sekwencji. Sekwencjonowanie rozszerzenia określa tylko, że określone rozszerzenie zostanie zainicjowane po innym określonym rozszerzeniu. Nie ma to wpływu na inicjowanie obsługi administracyjnej innych rozszerzeń zdefiniowanych w modelu.

Na przykład jeśli model zestawu skalowania ma trzy rozszerzenia — rozszerzenie A, rozszerzenie B i rozszerzenie C — określone w modelu, a rozszerzenie C ma zostać zainicjowane po rozszerzeń A, może wystąpić do jednej z następujących sekwencji inicjowania obsługi administracyjnej:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Jeśli trzeba upewnić się, że żadne inne rozszerzenie jest aprowizowana podczas wykonywania zdefiniowanej sekwencji rozszerzenia, zaleca się sekwencjonowanie wszystkich rozszerzeń w modelu zestawu skalowania. W powyższym przykładzie rozszerzenie B można ustawić do aprowizowania po rozszerzeń C, tak aby mogła wystąpić tylko jedna sekwencja:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak korzystać z sekwencjonowania rozszerzeń
Aby sekwencjonowanie inicjowania obsługi administracyjnej rozszerzenia, należy zaktualizować definicję rozszerzenia w modelu zestawu skalowania, aby uwzględnić właściwość "provisionAfterExtensions", która akceptuje tablicę nazw rozszerzeń. Rozszerzenia wymienione w wartości tablicy właściwości muszą być w pełni zdefiniowane w modelu zestawu skalowania.

### <a name="template-deployment"></a>Wdrażanie szablonów
Poniższy przykład definiuje szablon, w którym zestaw skalowania ma trzy rozszerzenia — ExtensionA, ExtensionB i ExtensionC — w taki sposób, że rozszerzenia są aprowiowane w kolejności:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Ponieważ właściwość "provisionAfterExtensions" akceptuje tablicę nazw rozszerzeń, powyższy przykład można zmodyfikować w taki sposób, że ExtensionC jest aprowizowana po ExtensionA i ExtensionB, ale nie jest wymagana kolejność między ExtensionA i ExtensionB. Do osiągnięcia tego scenariusza można użyć następującego szablonu:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>Interfejs API REST
Poniższy przykład dodaje nowe rozszerzenie o nazwie ExtensionC do modelu zestawu skalowania. ExtensionC ma zależności od ExtensionA i ExtensionB, które zostały już zdefiniowane w modelu zestawu skalowania.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Jeśli ExtensionC został zdefiniowany wcześniej w modelu zestawu skalowania i teraz `PATCH` chcesz dodać jego zależności, można wykonać, aby edytować już wdrożone właściwości rozszerzenia.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Zmiany w istniejących wystąpieniach zestawu skalowania są stosowane przy następnym [uaktualnieniu](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia cmdlet [Add-AzVmssExtension,](/powershell/module/az.compute/add-azvmssextension) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia programu Az PowerShell 1.2.0 lub nowszego.

Poniższy przykład dodaje rozszerzenie kondycji `extensionProfile` [aplikacji](virtual-machine-scale-sets-health-extension.md) do modelu w zestawie skalowania zestawu skalowania opartego na systemie Windows. Rozszerzenie kondycji aplikacji zostanie aprowizowana po zainicjowaniu obsługi administracyjnej [rozszerzenia skryptu niestandardowego,](../virtual-machines/extensions/custom-script-windows.md)już zdefiniowanego w zestawie skalowania.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Użyj [zestawu rozszerzeń az vmss,](/cli/azure/vmss/extension#az-vmss-extension-set) aby dodać rozszerzenie Kondycja aplikacji do definicji modelu zestawu skalowania. Sekwencjonowanie rozszerzeń wymaga użycia interfejsu wiersza polecenia platformy Azure 2.0.55 lub nowszego.

W poniższym przykładzie dodano [rozszerzenie kondycji aplikacji](virtual-machine-scale-sets-health-extension.md) do modelu zestawu skalowania zestawu skalowania opartego na systemie Windows. Rozszerzenie kondycji aplikacji zostanie aprowizowana po zainicjowaniu obsługi administracyjnej [rozszerzenia skryptu niestandardowego,](../virtual-machines/extensions/custom-script-windows.md)już zdefiniowanego w zestawie skalowania.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="not-able-to-add-extension-with-dependencies"></a>Nie można dodać rozszerzenia z zależnościami?
1. Upewnij się, że rozszerzenia określone w provisionafterExtensions są zdefiniowane w modelu zestawu skalowania.
2. Upewnij się, że nie są wprowadzane żadne zależności cykliczne. Na przykład następująca sekwencja nie jest dozwolona: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Upewnij się, że wszystkie rozszerzenia, które można podjąć zależności, mają "ustawienia" właściwość w ramach rozszerzenia "właściwości". Na przykład jeśli ExtentionB musi być aprowizowana po ExtensionA, a następnie ExtensionA musi mieć pole "ustawienia" w obszarze ExtensionA "właściwości". Można określić pustą właściwość "ustawienia", jeśli rozszerzenie nie wymaga żadnych wymaganych ustawień.

### <a name="not-able-to-remove-extensions"></a>Nie możesz usunąć rozszerzeń?
Upewnij się, że rozszerzenia usuwane nie są wymienione w ramach provisionAfterExtensions dla innych rozszerzeń.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) w zestawach skalowania maszyny wirtualnej.
