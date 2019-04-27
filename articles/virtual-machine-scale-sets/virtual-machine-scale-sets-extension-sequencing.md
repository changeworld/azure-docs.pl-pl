---
title: Sekwencjonowanie rozszerzenia za pomocą zestawów skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sekwencjonować aprowizacja rozszerzenia w przypadku wdrażania wielu rozszerzeń w zestawach skalowania maszyn wirtualnych.
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
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620176"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Ustawia sekwencji aprowizacja rozszerzenia w skali maszyny wirtualnej
Rozszerzenia maszyny wirtualnej platformy Azure zapewniają możliwości, takie jak konfiguracji po wdrożeniu i zarządzania, monitorowania, zabezpieczeń i więcej. Wdrożenia produkcyjne zazwyczaj korzystają z kombinacją wielu rozszerzeń skonfigurowane dla wystąpień maszyn wirtualnych do osiągnięcia zakładanych wyników.

Podczas korzystania z wielu rozszerzeń w maszynie wirtualnej jest istotne dla zapewnienia rozszerzeń wymagających tych samych zasobów systemu operacyjnego nie są próby uzyskania tych zasobów, w tym samym czasie. Niektóre rozszerzenia również są zależne od innych rozszerzeń, aby zapewnić wymagane konfiguracje, takie jak ustawienia środowiska i wpisów tajnych. Bez poprawne porządkowanie i sekwencjonowania w miejscu zależnych rozszerzenie wdrożenia może zakończyć się niepowodzeniem.

Ten artykuł szczegółowo opisuje sposób zakończeniu sekwencjonowania rozszerzeń można skonfigurować dla wystąpień maszyn wirtualnych w zestawach skalowania maszyn wirtualnych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że znasz:
-   Maszyna wirtualna platformy Azure [rozszerzenia](../virtual-machines/extensions/overview.md)
-   [Modyfikowanie](virtual-machine-scale-sets-upgrade-scale-set.md) zestawy skalowania maszyn wirtualnych

## <a name="when-to-use-extension-sequencing"></a>Kiedy należy używać sekwencjonowania rozszerzeń
Sekwencjonowanie rozszerzeń w nie ustawia obowiązkowe w przypadku skalowania, a jeśli nie podano, rozszerzeń mogą być udostępniane na wystąpienia w zestawie skalowania w dowolnej kolejności.

Na przykład jeśli model zestaw skalowania ma dwóch rozszerzeń — ExtensionA i ExtensionB — określone w modelu, następnie jedną z następujących sekwencji inicjowania obsługi administracyjnej może wystąpić:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Jeśli aplikacja wymaga rozszerzenia zawsze być przygotowana przed Extension b., należy używać sekwencjonowania rozszerzeń, zgodnie z opisem w tym artykule. Za pomocą sekwencjonowania rozszerzeń teraz nastąpi tylko jednej sekwencji:
-   ExtensionA - > ExtensionB

Wszystkie rozszerzenia, które nie są określone w zdefiniowanej kolejności inicjowania obsługi administracyjnej może aprowizowane w dowolnym momencie, w tym przed i za nimi, podczas zdefiniowanej kolejności. Tylko sekwencjonowania rozszerzeń Określa, że określone rozszerzenie będą udostępniane po innego określonego rozszerzenia. Nie ma wpływu, aprowizacja dowolne inne rozszerzenie zdefiniowane w modelu.

Na przykład jeśli z modelu zestawu skalowania ma trzy rozszerzenia — rozszerzenia A, B rozszerzenia i rozszerzenia C — określone w modelu, i rozszerzenia C jest ustawiona na można zainicjować obsługi administracyjnej po rozszerzenia, następnie jedną z następujących sekwencji inicjowania obsługi administracyjnej może wystąpić:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Musisz upewnić się, że nie inne rozszerzenie zostanie zainicjowana podczas wykonywania sekwencji zdefiniowane rozszerzenia, zaleca się sekwencjonowania wszystkie rozszerzenia w modelu zestawu skalowania. W powyższym przykładzie Extension b. można ustawić na wartość aprowizowano po rozszerzenia C, tak, może wystąpić tylko jednej sekwencji:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Jak używać sekwencjonowania rozszerzeń
Zainicjowanie obsługi sekwencji rozszerzenie, należy zaktualizować definicję rozszerzenia w modelu zestawu skalowania, aby dołączyć właściwości "provisionAfterExtensions", który przyjmuje tablicę rozszerzenia nazw. Rozszerzenia wymienione w wartości właściwości tablicy musi być całkowicie zdefiniowany w modelu zestawu skalowania.

### <a name="template-deployment"></a>Wdrażanie szablonu
W poniższym przykładzie zdefiniowano szablon, w której zestaw skalowania ma trzy rozszerzenia — ExtensionA ExtensionB i ExtensionC — taki sposób, że rozszerzenia są aprowizowane w kolejności:
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

Ponieważ właściwość "provisionAfterExtensions" przyjmuje tablicę nazwy rozszerzeń, powyższego przykładu mogą być modyfikowane w taki sposób, że ustanowiono ExtensionC po ExtensionA i ExtensionB, ale kolejność nie jest wymagany między ExtensionA i ExtensionB. Następujący szablon może służyć do realizacji tego scenariusza:

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
Poniższy przykład dodaje nowe rozszerzenie o nazwie ExtensionC modelu zestawu skalowania. ExtensionC ma zależności na ExtensionA i ExtensionB, które już zostały zdefiniowane w modelu zestawu skalowania.

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

Jeśli ExtensionC została zdefiniowana wcześniej na zestaw skalowania modelu, a teraz chcesz, aby dodać jego zależności, można wykonać `PATCH` do edytowania właściwości już wdrożonej rozszerzeniem.

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
Zmiany w istniejących wystąpieniach zestawu skalowania są stosowane na następnej [uaktualnienia](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Użyj [AzVmssExtension Dodaj](/powershell/module/az.compute/add-azvmssextension) modelu definicji zestawu polecenia cmdlet, aby dodać rozszerzenie kondycji aplikacji do skalowania. Sekwencjonowania rozszerzeń wymaga użycia programu Az PowerShell 1.2.0 lub nowszej.

W poniższym przykładzie dodano [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md) do `extensionProfile` w skali Ustaw modelu zestawu skalowania z systemem Windows. Rozszerzenie kondycji aplikacji, które będą udostępniane po zainicjowaniu obsługi administracyjnej [rozszerzenia niestandardowego skryptu](../virtual-machines/extensions/custom-script-windows.md), już zdefiniowany w zestawie skalowania.

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
Użyj [az vmss rozszerzenia zestawu](/cli/azure/vmss/extension#az-vmss-extension-set) można dodać rozszerzenia kondycji aplikacji do definicji modelu zestawu skalowania. Sekwencjonowania rozszerzeń wymaga użycia interfejsu wiersza polecenia Azure 2.0.55 lub powyżej.

W poniższym przykładzie dodano [kondycji aplikacji rozszerzenia](virtual-machine-scale-sets-health-extension.md) do skalowania Ustaw modelu zestawu skalowania z systemem Windows. Rozszerzenie kondycji aplikacji, które będą udostępniane po zainicjowaniu obsługi administracyjnej [rozszerzenia niestandardowego skryptu](../virtual-machines/extensions/custom-script-windows.md), już zdefiniowany w zestawie skalowania.

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
1. Upewnij się, że określone w provisionAfterExtensions rozszerzenia są zdefiniowane w modelu zestawu skalowania.
2. Upewnij się, nie zależności cykliczne wprowadzono. Na przykład nie jest dozwolona następująca sekwencja: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Upewnij się, że wszystkie rozszerzenia, które należy wykonać zależności, właściwość "ustawienia" w obszarze "właściwości" rozszerzenia. Na przykład jeśli ExtentionB musi być obsługiwana po ExtensionA, ExtensionA posiadanie pole "ustawienia" w obszarze ExtensionA "properties". Jeśli rozszerzenie nie zmusza do wymaganych ustawień, można określić właściwości pusty "ustawienia".

### <a name="not-able-to-remove-extensions"></a>Nie można usunąć rozszerzenia?
Upewnij się, rozszerzenia usuwany nie są wymienione w obszarze provisionAfterExtensions dla wszystkich innych rozszerzeń.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [wdrożyć aplikację](virtual-machine-scale-sets-deploy-app.md) ustawia skalowania maszyn wirtualnych.
