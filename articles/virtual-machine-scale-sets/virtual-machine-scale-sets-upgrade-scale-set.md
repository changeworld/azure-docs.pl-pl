---
title: Modyfikowanie zestawu skalowania maszyny wirtualnej platformy Azure
description: Dowiedz się, jak modyfikować i aktualizować zestaw skalowania maszyny wirtualnej platformy Azure przy pomocą interfejsów API REST, programu Azure PowerShell i interfejsu wiersza polecenia platformy Azure
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: 66fd656b5175547641150a048e57c978dc06d291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476828"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modify a virtual machine scale set (Modyfikowanie zestawu skalowania maszyn wirtualnych)

W całym cyklu życia aplikacji może być konieczne zmodyfikowanie lub zaktualizowanie zestawu skalowania maszyny wirtualnej. Te aktualizacje mogą obejmować sposób aktualizowania konfiguracji zestawu skalowania lub zmiany konfiguracji aplikacji. W tym artykule opisano sposób modyfikowania istniejącego zestawu skalowania za pomocą interfejsów API REST, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

## <a name="fundamental-concepts"></a>Podstawowe pojęcia

### <a name="the-scale-set-model"></a>Model zestawu skalowania
Zestaw skalowania ma "model zestawu skalowania", który przechwytuje *żądany* stan zestawu skalowania jako całości. Aby zbadać model zestawu skalowania, można użyć 

- INTERFEJS API REST z [zestawami obliczeniowymi/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Program Azure PowerShell z [programem Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Narzędzie cli platformy Azure z [az vmss pokazuje:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/)specyficznych dla języka.

Dokładna prezentacja danych wyjściowych zależy od opcji, które podasz do polecenia. W poniższym przykładzie przedstawiono skondensowane dane wyjściowe próbki z interfejsu wiersza polecenia platformy Azure:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Właściwości te mają zastosowanie do zestawu skalowania jako całości.


### <a name="the-scale-set-instance-view"></a>Widok wystąpienia zestawu skalowania
Zestaw skalowania ma również "widok wystąpienia zestawu skalowania", który przechwytuje bieżący stan *środowiska wykonawczego* zestawu skalowania jako całości. Aby zbadać widok wystąpienia dla zestawu skalowania, można użyć:

- INTERFEJS API REST z [zestawami compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Program Azure PowerShell z [programem Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Narzędzie interfejsu wiersza polecenia platformy Azure z [widokiem wystąpienia az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/) specyficznych dla języka

Dokładna prezentacja danych wyjściowych zależy od opcji, które podasz do polecenia. W poniższym przykładzie przedstawiono skondensowane dane wyjściowe próbki z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Właściwości te zapewniają podsumowanie bieżącego stanu środowiska wykonawczego maszyn wirtualnych w zestawie skalowania, takich jak stan rozszerzeń zastosowanych do zestawu skalowania.


### <a name="the-scale-set-vm-model-view"></a>Widok modelu maszyny Wirtualnej zestawu skalowania
Podobnie jak zestaw skalowania ma widok modelu, każde wystąpienie maszyny Wirtualnej w zestawie skalowania ma swój własny widok modelu. Aby zbadać widok modelu dla określonego wystąpienia maszyny Wirtualnej w zestawie skalowania, można użyć:

- INTERFEJS API REST z [compute/virtualmachinescalesetvms/get w](/rest/api/compute/virtualmachinescalesetvms/get) następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Program Azure PowerShell z [programem Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Narzędzie cli platformy Azure z [az vmss pokazuje:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure.](https://azure.microsoft.com/downloads/)

Dokładna prezentacja danych wyjściowych zależy od opcji, które podasz do polecenia. W poniższym przykładzie przedstawiono skondensowane dane wyjściowe próbki z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Właściwości te opisują konfigurację wystąpienia maszyny Wirtualnej w ramach zestawu skalowania, a nie konfigurację zestawu skalowania jako całości. Na przykład model zestawu `overprovision` skalowania ma jako właściwość, podczas gdy model dla wystąpienia maszyny Wirtualnej w zestawie skalowania nie. Ta różnica jest, ponieważ overprovisioning jest właściwością dla zestawu skalowania jako całości, a nie pojedynczych wystąpień maszyn wirtualnych w zestawie skalowania (aby uzyskać więcej informacji na temat nadmiernej obsługi administracyjnej, zobacz [Zagadnienia dotyczące projektu dla zestawów skalowania).](virtual-machine-scale-sets-design-overview.md#overprovisioning)


### <a name="the-scale-set-vm-instance-view"></a>Widok wystąpienia maszyny Wirtualnej zestawu skalowania
Podobnie jak zestaw skalowania ma widok wystąpienia, każde wystąpienie maszyny Wirtualnej w zestawie skalowania ma swój własny widok wystąpienia. Aby zbadać widok wystąpienia dla określonego wystąpienia maszyny Wirtualnej w zestawie skalowania, można użyć:

- INTERFEJS API REST z [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Program Azure PowerShell z [programem Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Narzędzie wiersza polecenia platformy Azure z [widokiem wystąpienia az vmss](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/)

Dokładna prezentacja danych wyjściowych zależy od opcji, które podasz do polecenia. W poniższym przykładzie przedstawiono skondensowane dane wyjściowe próbki z interfejsu wiersza polecenia platformy Azure:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Właściwości te opisują bieżący stan środowiska wykonawczego wystąpienia maszyny Wirtualnej w ramach zestawu skalowania, który zawiera wszelkie rozszerzenia zastosowane do zestawu skalowania.


## <a name="how-to-update-global-scale-set-properties"></a>Jak zaktualizować właściwości zestawu skali globalnej
Aby zaktualizować właściwość zestawu skali globalnej, należy zaktualizować właściwość w modelu zestawu skalowania. Tę aktualizację można wykonać za pośrednictwem:

- INTERFEJS API REST z [zestawami obliczeniowymi/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) w następujący sposób:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Można wdrożyć szablon Menedżera zasobów z właściwościami z interfejsu API REST, aby zaktualizować właściwości globalnego zestawu skalowania.

- Program Azure PowerShell z [aktualizacją AzVmss:](/powershell/module/az.compute/update-azvmss)

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Narzędzie cli platformy Azure z [aktualizacją az vmss:](/cli/azure/vmss)
    - Aby zmodyfikować właściwość:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Aby dodać obiekt do właściwości listy w zestawie skalowania: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Aby usunąć obiekt z właściwości listy w zestawie skalowania: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Jeśli wcześniej wdrożono zestaw skalowania `az vmss create` za pomocą polecenia, można ponownie uruchomić `az vmss create` polecenie, aby zaktualizować zestaw skalowania. Upewnij się, że wszystkie `az vmss create` właściwości w poleceniu są takie same jak poprzednio, z wyjątkiem właściwości, które chcesz zmodyfikować.

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure.](https://azure.microsoft.com/downloads/)

Po zaktualizowaniu modelu zestawu skalowania nowa konfiguracja ma zastosowanie do wszystkich nowych maszyn wirtualnych utworzonych w zestawie skalowania. Jednak modele dla istniejących maszyn wirtualnych w zestawie skalowania muszą być nadal aktualne z najnowszym modelem zestawu skalowania ogólnego. W modelu dla każdej maszyny Wirtualnej `latestModelApplied` jest właściwością logiczną wywołaną, która wskazuje, czy maszyna wirtualna`true` jest aktualna z najnowszym modelem ogólnego zestawu skalowania ( oznacza, że maszyna wirtualna jest aktualna w przypadku najnowszego modelu).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Jak aktualizować maszyny wirtualne dzięki najnowszemu modelowi zestawu skalowania
Zestawy skalowania mają "zasady uaktualniania", które określają, jak maszyny wirtualne są uaktualniane z najnowszym modelem zestawu skalowania. Trzy tryby dla zasad uaktualniania to:

- **Automatyczne** — w tym trybie zestaw skalowania nie daje żadnych gwarancji dotyczących kolejności maszyn wirtualnych są zmniejszane. Zestaw skalowania może zdjąć wszystkie maszyny wirtualne w tym samym czasie. 
- **Toczenia** — w tym trybie zestaw skalowania rolkach aktualizacji w partiach z opcjonalnym czasem wstrzymania między partiami.
- **Ręcznie** — w tym trybie po zaktualizowaniu modelu zestawu skalowania nic się nie dzieje z istniejącymi maszynami wirtualnymi.
 
Aby zaktualizować istniejące maszyny wirtualne, należy wykonać "ręczne uaktualnienie" każdej istniejącej maszyny Wirtualnej. Możesz wykonać to ręczne uaktualnienie za pomocą:

- INTERFEJS API REST z [zestawami/aktualizacjami obliczeń obliczeniowych/virtualmachinescalesets/update w](/rest/api/compute/virtualmachinescalesets/updateinstances) następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Program Azure PowerShell z [insytą aktualizacji AzVmss:](/powershell/module/az.compute/update-azvmssinstance)
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Narzędzie wiersza polecenia platformy Azure z [wystąpieniami aktualizacji az vmss](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Można również użyć [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/)specyficznych dla języka.

>[!NOTE]
> Klastry sieci szkieletowej usług mogą używać tylko trybu *automatycznego,* ale aktualizacja jest obsługiwana inaczej. Aby uzyskać więcej informacji, zobacz [Uaktualnienia aplikacji sieci szkieletowej usług](../service-fabric/service-fabric-application-upgrade.md).

Istnieje jeden typ modyfikacji właściwości zestawu skalowania globalnego, który nie jest zgodny z zasadami uaktualniania. Zmiany w profilu systemu operacyjnego zestawu skalowania i dysku danych (takie jak nazwa użytkownika i hasło administratora) można zmienić tylko w wersji interfejsu API *2017-12-01* lub nowszej. Te zmiany dotyczą tylko maszyn wirtualnych utworzonych po zmianie modelu zestawu skalowania. Aby aktualne istniejące maszyny wirtualne były aktualne, należy wykonać "reimage" każdej istniejącej maszyny wirtualnej. Możesz to zrobić reimage za pośrednictwem:

- INTERFEJS API REST z [zestawami obliczeniowymi/virtualmachinescalesets/reimage w](/rest/api/compute/virtualmachinescalesets/reimage) następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Program Azure PowerShell z [programem Set-AzVmssVm:](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Narzędzie cli platformy Azure z [reimage az vmss:](https://docs.microsoft.com/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/)specyficznych dla języka.


## <a name="properties-with-restrictions-on-modification"></a>Właściwości z ograniczeniami dotyczącymi modyfikacji

### <a name="create-time-properties"></a>Właściwości czasu tworzenia
Niektóre właściwości można ustawić tylko podczas tworzenia zestawu skalowania. Właściwości te obejmują:

- Strefy dostępności
- Wydawca referencyjny obrazu
- Oferta referencyjna obrazu
- Odwołanie do obrazu sku
- Typ konta magazynu dysku zarządzanego systemu operacyjnego

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Właściwości, które można zmienić tylko na podstawie bieżącej wartości
Niektóre właściwości mogą ulec zmianie, z wyjątkami w zależności od bieżącej wartości. Właściwości te obejmują:

- **singlePlacementGroup** - Jeśli singlePlacementGroup jest true, może być zmodyfikowany do false. Jednak jeśli singlePlacementGroup jest false, **nie może** być modyfikowany do true.
- **podsieci** — podsieć zestawu skalowania może być modyfikowana tak długo, jak oryginalna podsieć i nowa podsieć znajdują się w tej samej sieci wirtualnej.

### <a name="properties-that-require-deallocation-to-change"></a>Właściwości wymagające zmiany w celu zmiany właściwości, które wymagają delokacji
Niektóre właściwości mogą być zmieniane tylko na niektóre wartości, jeśli maszyny wirtualne w zestawie skalowania są cofnięte. Właściwości te obejmują:

- **Nazwa jednostki SKU**— jeśli nowa jednostka SKU maszyny Wirtualnej nie jest obsługiwana na sprzęcie, na którego aktualnie znajduje się zestaw skalowania, należy cofnąć alokację maszyn wirtualnych w zestawie skalowania przed zmodyfikowaniem nazwy jednostki SKU. Aby uzyskać więcej informacji, zobacz [jak zmienić rozmiar maszyny Wirtualnej platformy Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizacje specyficzne dla maszyny Wirtualnej
Niektóre modyfikacje mogą być stosowane do określonych maszyn wirtualnych zamiast właściwości zestawu skali globalnej. Obecnie jedyną aktualizacją specyficzną dla maszyny Wirtualnej, która jest obsługiwana, jest dołączanie/odłączanie dysków danych do/z maszyn wirtualnych w zestawie skalowania. Ta funkcja jest dostępna w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [dokumentację podglądu](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenariusze

### <a name="application-updates"></a>Aktualizacje aplikacji
Jeśli aplikacja jest wdrażana w skali ustawionej za pośrednictwem rozszerzeń, aktualizacja konfiguracji rozszerzenia powoduje, że aplikacja jest aktualizowana zgodnie z zasadami uaktualniania. Na przykład jeśli masz nową wersję skryptu do uruchomienia w rozszerzeniu skryptu niestandardowego, można zaktualizować *fileUris* właściwości, aby wskazać nowy skrypt. W niektórych przypadkach można wymusić aktualizację, nawet jeśli konfiguracja rozszerzenia pozostaje niezmieniona (na przykład zaktualizowano skrypt bez zmiany identyfikatora URI skryptu). W takich przypadkach można zmodyfikować *forceUpdateTag,* aby wymusić aktualizację. Platforma Azure nie interpretuje tej właściwości. Jeśli zmienisz wartość, nie ma wpływu na sposób działania rozszerzenia. Zmiana po prostu wymusza ponowne uruchomienie rozszerzenia. Aby uzyskać więcej informacji na temat *forceUpdateTag*, zobacz [dokumentację interfejsu API REST dla rozszerzeń](/rest/api/compute/virtualmachineextensions/createorupdate). Należy zauważyć, że *forceUpdateTag* może być używany ze wszystkimi rozszerzeniami, a nie tylko rozszerzeniem skryptu niestandardowego.

Jest również typowe dla aplikacji, które mają być wdrażane za pomocą obrazu niestandardowego. Ten scenariusz jest omówiony w poniższej sekcji.

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Jeśli używasz obrazów platformy Azure, można zaktualizować obraz, modyfikując *imageReference* (więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> W przypadku obrazów platformy często określa się "najnowsze" dla wersji referencyjnej obrazu. Podczas tworzenia, skalowania w poziomie i ponownego zadymienia maszyny wirtualne są tworzone przy obliczu najnowszej dostępnej wersji. **Nie oznacza** to jednak, że obraz systemu operacyjnego jest automatycznie aktualizowany wraz z czasem w miarę zwalniania nowych wersji obrazu. Osobna funkcja jest obecnie w wersji zapoznawczej, która zapewnia automatyczne uaktualnienia systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [dokumentację automatycznych uaktualnień systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md).

Jeśli używasz obrazów niestandardowych, możesz zaktualizować obraz, aktualizując *identyfikator obrazuReferencja* (więcej informacji można znaleźć w [dokumentacji interfejsu API REST).](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)

## <a name="examples"></a>Przykłady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizowanie obrazu systemu operacyjnego dla zestawu skalowania
Możesz mieć zestaw skalowania, który uruchamia starą wersję Ubuntu LTS 16.04. Chcesz zaktualizować do nowszej wersji Ubuntu LTS 16.04, takich jak wersja *16.04.201801090*. Właściwość version reference obrazu nie jest częścią listy, więc można bezpośrednio zmodyfikować te właściwości za pomocą jednego z następujących poleceń:

- Program Azure PowerShell z [aktualizacją AzVmss](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Narzędzie cli platformy Azure z [aktualizacją az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternatywnie można zmienić obraz używany przez zestaw skalowania. Na przykład można zaktualizować lub zmienić obraz niestandardowy używany przez zestaw skalowania. Obraz używany jest przez zestaw skalowania, aktualizując właściwość identyfikatora odwołania do obrazu. Właściwość identyfikatora odwołania do obrazu nie jest częścią listy, więc można bezpośrednio zmodyfikować tę właściwość za pomocą jednego z następujących poleceń:

- Program Azure PowerShell z [aktualizacją AzVmss](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Narzędzie cli platformy Azure z [aktualizacją az vmss:](/cli/azure/vmss)

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizowanie modułu równoważenia obciążenia dla zestawu skalowania
Załóżmy, że masz zestaw skalowania z modułem równoważenia obciążenia platformy Azure i chcesz zastąpić moduł równoważenia obciążenia platformy Azure bramą aplikacji platformy Azure. Właściwości modułu równoważenia obciążenia i bramy aplikacji dla zestawu skalowania są częścią listy, dzięki czemu można użyć poleceń do usuwania lub dodawania elementów listy zamiast bezpośrednio modyfikować właściwości:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Te polecenia zakładają, że istnieje tylko jedna konfiguracja IP i moduł równoważenia obciążenia w zestawie skalowania. Jeśli istnieje wiele, może być konieczne użycie indeksu listy innego niż *0*.


## <a name="next-steps"></a>Następne kroki
Typowe zadania zarządzania można również wykonywać w zestawach skalowania za pomocą [interfejsu wiersza polecenia platformy Azure](virtual-machine-scale-sets-manage-cli.md) lub programu Azure [PowerShell.](virtual-machine-scale-sets-manage-powershell.md)
