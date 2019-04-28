---
title: Modyfikowanie zestawu skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmodyfikować i aktualizowanie maszyn wirtualnych zestawu skalowania przy użyciu interfejsów API REST, programu Azure PowerShell i wiersza polecenia platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: manayar
ms.openlocfilehash: 71899a9d6782c4700c287458c85ec83bd1516a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803139"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modyfikowanie zestawu skalowania maszyn wirtualnych

W całym cyklem życia aplikacji może być konieczne modyfikują lub aktualizują zestawu skalowania maszyn wirtualnych. Te aktualizacje mogą obejmować jak zaktualizowanie konfiguracji zestawu skalowania lub zmienić konfigurację aplikacji. W tym artykule opisano sposób modyfikowania istniejącego zestawu skalowania przy użyciu interfejsów API REST, programu Azure PowerShell lub wiersza polecenia platformy Azure.

## <a name="fundamental-concepts"></a>Podstawowe pojęcia

### <a name="the-scale-set-model"></a>W modelu zestawu skalowania
Zestaw skalowania jest "modelu zestawu skalowania" przechwytujący *żądaną* stan skalowania Ustaw jako całości. Aby zbadać model dla zestawu skalowania, można użyć 

- Interfejs API REST przy użyciu [get-compute/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [Get AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Wiersza polecenia platformy Azure za pomocą [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub określonego języka [zestawami SDK Azure](https://azure.microsoft.com/downloads/).

Dokładny opis danych wyjściowych, zależy od opcji, podanego polecenia. Poniższy przykład przedstawia przykładowe skrócone dane wyjściowe z wiersza polecenia platformy Azure:

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

Te właściwości mają zastosowanie do zestawu jako całość skalowania.


### <a name="the-scale-set-instance-view"></a>Widok wystąpienia zestawu skalowania
Ma również zestaw skalowania "wystąpienia w zestawie skalowania wyświetlić" przechwytujący bieżącego *środowiska uruchomieniowego* stan skalowania Ustaw jako całości. Aby zbadać widok wystąpienia zestawu skalowania, można użyć:

- Interfejs API REST przy użyciu [obliczeń/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [Get AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Wiersza polecenia platformy Azure za pomocą [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub określonego języka [zestawy SDK platformy Azure](https://azure.microsoft.com/downloads/)

Dokładny opis danych wyjściowych, zależy od opcji, podanego polecenia. Poniższy przykład przedstawia przykładowe skrócone dane wyjściowe z wiersza polecenia platformy Azure:

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

Te właściwości tej sekcji przedstawiono podsumowanie bieżącego stanu środowiska uruchomieniowego maszyn wirtualnych w zestawie skalowania, takie jak stan rozszerzenia zastosowany do zestawu skalowania.


### <a name="the-scale-set-vm-model-view"></a>Widok modelu maszyny Wirtualnej zestawu skalowania
Podobnie jak zestaw skalowania jest widokiem modelu, poszczególnych wystąpień maszyn wirtualnych w zestawie skalowania ma swój własny widok modelu. Aby zbadać widok modelu dla określonego wystąpienia maszyny Wirtualnej w zestawie skalowania, można użyć:

- Interfejs API REST przy użyciu [get-compute/virtualmachinescalesetvms](/rest/api/compute/virtualmachinescalesetvms/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [Get AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Wiersza polecenia platformy Azure za pomocą [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawami SDK Azure](https://azure.microsoft.com/downloads/).

Dokładny opis danych wyjściowych, zależy od opcji, podanego polecenia. Poniższy przykład przedstawia przykładowe skrócone dane wyjściowe z wiersza polecenia platformy Azure:

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

Te właściwości opisują konfigurację wystąpienia maszyny Wirtualnej w zestawie skalowania, a nie konfiguracji skalowania Ustaw jako całości. Na przykład modelu zestawu skalowania ma `overprovision` jako właściwość, a nie w modelu dla wystąpień maszyn wirtualnych w zestawie skalowania. Różnica ta ma ponieważ celi właściwość jest w przypadku skalowania Ustaw jako całości, nie do poszczególnych wystąpień maszyn wirtualnych w zestawie skalowania (Aby uzyskać więcej informacji na temat udostępniania, zobacz [zagadnienia dotyczące projektu zestawów skalowania](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Widok wystąpienia maszyny Wirtualnej zestawu skalowania
Podobnie jak ma widok wystąpienia w zestawie skalowania, każde wystąpienie maszyny Wirtualnej w zestawie skalowania ma swój własny widok wystąpienia. Aby zbadać widok wystąpienia dla konkretnego wystąpienia maszyny Wirtualnej w zestawie skalowania, można użyć:

- Interfejs API REST przy użyciu [obliczeń/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [Get AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Wiersza polecenia platformy Azure przy użyciu [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawy SDK platformy Azure](https://azure.microsoft.com/downloads/)

Dokładny opis danych wyjściowych, zależy od opcji, podanego polecenia. Poniższy przykład przedstawia przykładowe skrócone dane wyjściowe z wiersza polecenia platformy Azure:

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

Te właściwości opisują bieżący stan środowiska uruchomieniowego wystąpienia maszyny Wirtualnej w zestawie skalowania, która obejmuje wszystkie rozszerzenia, stosowane do zestawu skalowania.


## <a name="how-to-update-global-scale-set-properties"></a>Jak zaktualizować globalną skalę, ustaw właściwości
Aby zaktualizować właściwość zestawu skali globalnej, należy zaktualizować właściwości w modelu zestawu skalowania. Można wykonać tej aktualizacji za pomocą:

- Interfejs API REST przy użyciu [obliczeń/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) w następujący sposób:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Możesz wdrożyć szablon usługi Resource Manager z właściwościami z interfejsu API REST, aby zaktualizować właściwości zestawu skali globalnej.

- Program Azure PowerShell za pomocą [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Wiersza polecenia platformy Azure za pomocą [aktualizacji az vmss](/cli/azure/vmss):
    - Aby zmodyfikować właściwości:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Aby dodać obiekt do listy właściwości w zestawie skalowania: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Aby usunąć obiekt z listy właściwości w zestawie skalowania: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Jeśli wcześniej wdrożono zestawu skalowania przy użyciu `az vmss create` polecenia, można uruchomić `az vmss create` polecenie ponownie, aby zaktualizować zestawu skalowania. Upewnij się, że wszystkie właściwości w `az vmss create` polecenia są takie same, tak jak poprzednio, z wyjątkiem właściwości, które chcesz zmodyfikować.

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawami SDK Azure](https://azure.microsoft.com/downloads/).

Po zaktualizowaniu modelu zestawu skalowania, nowa konfiguracja dotyczy wszystkie nowe maszyny wirtualne utworzone w zestawie skalowania. Jednak modele dla istniejących maszyn wirtualnych w zestawie skalowania muszą nadal można przełączyć aktualne przy użyciu najnowszych ogólną modelu zestawu skalowania. W modelu dla każdej maszyny Wirtualnej jest właściwość typu boolean o nazwie `latestModelApplied` oznacza to, czy maszyna wirtualna jest aktualny i najnowsza wersja ogólną modelu zestawu skalowania (`true` oznacza, że maszyna wirtualna jest aktualny i najnowszy model).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Jak przenieść aktualne przy użyciu najnowszego modelu zestawu skalowania maszyn wirtualnych
Zestawy skalowania ma "zasad uaktualniania" które określają, jak są przenoszone aktualne przy użyciu najnowszego modelu zestawu skalowania maszyn wirtualnych. Są trzy tryby dla zasad uaktualniania:

- **Automatyczne** — w tym trybie, zestaw skalowania nie udziela żadnych gwarancji o zamówieniu wprowadzanej maszyn wirtualnych. Zestaw skalowania może podjąć w dół wszystkie maszyny wirtualne w tym samym czasie. 
- **Stopniowe** — w tym trybie zestawu skalowania wprowadza aktualizację w partiach o czas wstrzymania opcjonalne między partii.
- **Ręczne** — w tym trybie, podczas aktualizacji modelu zestawu skalowania, nic się nie dzieje do istniejących maszyn wirtualnych.
 
Aby zaktualizować istniejące maszyny wirtualne, należy wykonać "ręcznego uaktualnienia" każdej istniejącej maszyny Wirtualnej. Możesz zrobić to Ręczne uaktualnianie za pomocą:

- Interfejs API REST przy użyciu [obliczeń/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [AzVmssInstance aktualizacji](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Wiersza polecenia platformy Azure przy użyciu [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Możesz również użyć określonego języka [zestawami SDK Azure](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Klastry usługi Service Fabric można używać tylko *automatyczne* trybu, ale aktualizacja jest traktowany inaczej. Aby uzyskać więcej informacji, zobacz [uaktualnień aplikacji usługi Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Istnieje jeden typ zmianami w skali globalnej zestaw właściwości, które nie jest zgodna z zasad uaktualniania. Zmiany do zestawu skalowania, profilu systemu operacyjnego (na przykład nazwa użytkownika administratora i hasło) można zmienić tylko w wersji interfejsu API *2017-12-01* lub nowszej. Te zmiany dotyczą tylko maszyny wirtualne utworzone po zmianie w skali modelu. Aby wyświetlić istniejące maszyny wirtualne, które są aktualne, należy wykonać "reimage" każdej istniejącej maszyny Wirtualnej. Możesz zrobić to odtworzenia z obrazu za pomocą:

- Interfejs API REST przy użyciu [obliczeń/virtualmachinescalesets/odtworzenia z obrazu](/rest/api/compute/virtualmachinescalesets/reimage) w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Program Azure PowerShell za pomocą [AzVmssVm zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Wiersza polecenia platformy Azure za pomocą [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Możesz również użyć określonego języka [zestawami SDK Azure](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Właściwości za pomocą ograniczeń modyfikacji

### <a name="create-time-properties"></a>Właściwości czasu tworzenia
Niektóre właściwości można ustawić tylko podczas tworzenia zestawu skalowania. Te właściwości obejmują:

- Strefy dostępności
- Wydawca odwołanie do obrazu
- Obraz odniesienie ofertę
- Zarządzany typ konta magazynu dysku systemu operacyjnego

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Właściwości, które można zmienić tylko na podstawie bieżącej wartości
Niektóre właściwości mogą zmienić wyjątków w zależności od wartości bieżącej. Te właściwości obejmują:

- **singlePlacementGroup** — Jeśli to ustawienie singlePlacementGroup jest równa true, może być zmieniony na wartość false. Jednak jeśli singlePlacementGroup ma wartość FAŁSZ, jego **nie mogą** można modyfikować na wartość true.
- **podsieci** — podsieć zestawu skalowania, które mogą zostać zmienione tak długo, jak podsieć, oryginalnym i Nowa podsieć znajdują się w tej samej sieci wirtualnej.

### <a name="properties-that-require-deallocation-to-change"></a>Właściwości, które wymagają dezalokacji zmiany
Niektóre właściwości mogą go zmienić tylko do określonych wartości, jeśli są dealokowane maszyn wirtualnych w zestawie skalowania. Te właściwości obejmują:

- **Nazwa jednostki SKU**— w przypadku nowej jednostki SKU maszyny Wirtualnej nie jest obsługiwana na sprzęcie, zestaw skalowania jest obecnie włączona, należy cofnąć przydział maszyny wirtualne w zestawie przed przystąpieniem do modyfikacji nazwa jednostki SKU skalowania. Aby uzyskać więcej informacji, zobacz [jak zmienić rozmiar maszyny Wirtualnej platformy Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizacje specyficznych dla maszyny Wirtualnej
Pewne zmiany mogą być stosowane dla konkretnych maszyn wirtualnych zamiast właściwości zestawu skali globalnej. Obecnie tylko aktualizacji specyficznych dla maszyny Wirtualnej, która jest obsługiwana jest dołączania/odłączania dysków danych do i z maszyn wirtualnych w zestawie skalowania. Ta funkcja jest dostępna w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [dokumentację w wersji zapoznawczej](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenariusze

### <a name="application-updates"></a>Aktualizacje aplikacji
Jeśli aplikacja jest wdrażana zestawu za pośrednictwem rozszerzeń skalowania, aktualizacja konfiguracji rozszerzenia powoduje, że aplikacja aktualizacji zgodnie z zasadami uaktualnienia. Na przykład jeśli masz nową wersję skrypt do uruchomienia w niestandardowe rozszerzenie skryptu, można zaktualizować *fileUris* właściwości, aby wskazywał nowy skrypt. W niektórych przypadkach możesz chcieć wymusić aktualizację, nawet jeśli konfiguracji rozszerzenia jest bez zmian (na przykład zaktualizowany skryptu bez zmiany identyfikatora URI skryptu). W takich przypadkach można zmodyfikować *forceUpdateTag* Aby wymusić aktualizację. Platforma Azure nie interpretuje tej właściwości. Jeśli zmienisz wartość, nie ma żadnego wpływu na sposób uruchamiania rozszerzenia. Zmiana po prostu wymusza rozszerzenia do ponownego uruchomienia. Aby uzyskać więcej informacji na temat *forceUpdateTag*, zobacz [dokumentację interfejsu API REST dla rozszerzeń](/rest/api/compute/virtualmachineextensions/createorupdate). Należy pamiętać, że *forceUpdateTag* mogą być używane z wszystkie rozszerzenia, nie tylko rozszerzenia niestandardowego skryptu.

Jest również częsty problem w aplikacji można wdrożyć za pomocą obrazu niestandardowego. W tym scenariuszu zostało opisane w poniższej sekcji.

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Jeśli używasz obrazów platformy Azure, można zaktualizować obrazu, modyfikując *imageReference* (więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Przy użyciu obrazów platformy jest powszechne, aby określić "latest" dla wersji odwołanie do obrazu. Po utworzeniu i skalować odtworzenia z obrazu, maszyny wirtualne są tworzone przy użyciu najnowszej dostępnej wersji. Jednak go **nie** oznacza, że obraz systemu operacyjnego jest automatycznie aktualizowana wraz z upływem czasu, jak wydawane są nowe wersje obrazu. Osobna funkcja jest obecnie w wersji zapoznawczej, który zapewnia automatyczne uaktualnianie systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [dokumentacji automatycznych uaktualnień systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md).

Jeśli używasz niestandardowych obrazów, można zaktualizować obrazu, aktualizując *imageReference* identyfikator (więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Przykłady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizacja obrazu systemu operacyjnego dla zestawu skalowania
Może mieć zestawu skalowania, który uruchamia starą wersję Ubuntu LTS 16.04. Aby zaktualizować do nowszej wersji systemu Ubuntu LTS 16.04, takie jak wersja *16.04.201801090*. Właściwości wersji obrazu odwołanie nie jest częścią listy, dzięki czemu mogą bezpośrednio modyfikować te właściwości, za pomocą jednego z następujących poleceń:

- Program Azure PowerShell za pomocą [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Wiersza polecenia platformy Azure za pomocą [aktualizacji az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternatywnie można zmienić obraz korzysta z zestawu skalowania. Na przykład możesz chcieć zaktualizować lub zmienić obraz niestandardowy używany przez zestaw skalowania. Możesz zmienić obraz, który korzysta z zestawu skalowania, aktualizując właściwości Identyfikator odwołania obrazu. Właściwość Identyfikatora obrazu odniesienia nie jest częścią listy, dzięki czemu mogą bezpośrednio modyfikować tę właściwość za pomocą jednego z następujących poleceń:

- Program Azure PowerShell za pomocą [AzVmss aktualizacji](/powershell/module/az.compute/update-azvmss) w następujący sposób:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Wiersza polecenia platformy Azure za pomocą [aktualizacji az vmss](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Zaktualizuj moduł równoważenia obciążenia dla zestawu skalowania
Załóżmy, że masz zestaw skalowania z usługą Azure Load Balancer i chcesz zastąpić modułu równoważenia obciążenia platformy Azure za pomocą usługi Azure Application Gateway. Moduł równoważenia obciążenia i właściwości bramy Application Gateway dla zestawu skalowania są częścią listy, aby można było używać poleceń, aby usunąć lub dodać elementy listy zamiast bezpośrednio modyfikować właściwości:

- Program Azure Powershell:

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

- Interfejs wiersza polecenia platformy Azure:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Tych poleceniach założono, że istnieje tylko jeden IP konfiguracji i obciążenia równoważenia w zestawie skalowania. Jeśli dostępnych jest wiele, może być konieczne użycie indeks listy w innych niż *0*.


## <a name="next-steps"></a>Kolejne kroki
Można również wykonywać typowe zadania zarządzania na zestawach skalowania z [wiersza polecenia platformy Azure](virtual-machine-scale-sets-manage-cli.md) lub [programu Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
