---
title: Zmodyfikuj zestaw skali maszyny wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmodyfikuj i zaktualizuj zestaw interfejsów API REST, programu Azure PowerShell i Azure CLI 2.0 skalowania maszyny wirtualnej platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: 91eccd2b4587d7cb926ca506ae2f2e5b91ea1f3e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Zmodyfikuj zestaw skali maszyny wirtualnej
W całym cyklu życia aplikacji należy zmodyfikować lub zaktualizować zestawu skalowania maszyny wirtualnej. Aktualizacje te mogą obejmować jak zaktualizować konfigurację zestawu skali lub zmienić konfigurację aplikacji. W tym artykule opisano sposób modyfikowania istniejących zestaw interfejsów API REST, programu Azure PowerShell lub Azure CLI 2.0 skalowania.

## <a name="fundamental-concepts"></a>podstawowe pojęcia

### <a name="the-scale-set-model"></a>Modelu zestawu skali
Zestaw skali ma "skali zestaw model" przechwytujący *żądany* ustawiony stan skali jako całość. Aby odpytać modelu dla zestawu skalowania, można użyć 

- Interfejs API REST z [get-compute/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Program Azure Powershell z [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI 2.0 z [Pokaż vmss az](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub danego języka [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/).

Dokładny opis dane wyjściowe zależy od opcji podane przez użytkownika do polecenia. W poniższym przykładzie przedstawiono skrócone przykładowe dane wyjściowe z 2.0 interfejsu wiersza polecenia platformy Azure:

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

Te właściwości stosowane do skalowania, ustawić jako całość.


### <a name="the-scale-set-instance-view"></a>Widok wystąpienia zestawu skali
Skalę, również ustawić się "skali zestaw wystąpienie programu wyświetlić" przechwytujący bieżącego *środowiska uruchomieniowego* ustawiony stan skali jako całość. Aby zbadać widok wystąpienia zestawu skali, można użyć:

- Interfejs API REST z [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Program Azure Powershell z [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI 2.0 z [az vmss get-— widok wystąpienia](/cli/azure/vmss#az_vmss_get_instance_view):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub danego języka [Azure SDK](https://azure.microsoft.com/downloads/)

Dokładny opis dane wyjściowe zależy od opcji podane przez użytkownika do polecenia. W poniższym przykładzie przedstawiono skrócone przykładowe dane wyjściowe z 2.0 interfejsu wiersza polecenia platformy Azure:

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

Te właściwości zawierają Podsumowanie bieżącego stanu środowiska uruchomieniowego w zestawie skalowania, takich jak stan rozszerzenia stosowany do zestawu skalowania maszyn wirtualnych.


### <a name="the-scale-set-vm-model-view"></a>Widok modelu maszyny Wirtualnej zestawu skalowania
Podobnie jak zestaw skali ma widok modelu, każdej maszyny Wirtualnej w zestawie skalowania ma własną widok modelu. Aby zbadać widok modelu dla zestawu skalowania, można użyć:

- Interfejs API REST z [get-compute/virtualmachinescalesetvms](/rest/api/compute/virtualmachinescalesetvms/get) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Program Azure Powershell z [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 z [Pokaż vmss az](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/).

Dokładny opis dane wyjściowe zależy od opcji podane przez użytkownika do polecenia. W poniższym przykładzie przedstawiono skrócone przykładowe dane wyjściowe z 2.0 interfejsu wiersza polecenia platformy Azure:

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

Te właściwości opisano w konfiguracji maszyny Wirtualnej, nie konfiguracji skali ustawić jako całość. Na przykład modelu zestawu skali ma `overprovision` jako właściwość, a nie w modelu dla maszyny Wirtualnej w zestawie skalowania. Ta różnica wynika nadmiarowe Inicjowanie obsługi administracyjnej jest właściwością skali Ustaw jako całości, nie poszczególnych maszyn wirtualnych w zestawie skalowania (Aby uzyskać więcej informacji na temat nadmiarowe Inicjowanie obsługi administracyjnej, zobacz [zagadnienia dotyczące zestawy skalowania projektowania](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Widok wystąpienia maszyny Wirtualnej zestawu skali
Podobnie jak zestaw skali ma widok wystąpienia każdej maszyny Wirtualnej w zestawie skalowania ma własną widok wystąpienia. Aby zbadać widok wystąpienia zestawu skali, można użyć:

- Interfejs API REST z [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) w następujący sposób:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Program Azure Powershell z [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI 2.0 z [az vmss get-— widok wystąpienia](/cli/azure/vmss#az_vmss_get_instance_view)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [Azure SDK](https://azure.microsoft.com/downloads/)

Dokładny opis dane wyjściowe zależy od opcji podane przez użytkownika do polecenia. W poniższym przykładzie przedstawiono skrócone przykładowe dane wyjściowe z 2.0 interfejsu wiersza polecenia platformy Azure:

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

Te właściwości opisują bieżący stan maszyny Wirtualnej, która obejmuje wszystkie rozszerzenia stosowany do zestawu skalowania środowiska wykonawczego.


## <a name="how-to-update-global-scale-set-properties"></a>Jak zaktualizować skali globalnej Ustawianie właściwości
Aby zaktualizować właściwości zestawu skali globalnej, należy zaktualizować właściwości w modelu zestawu skali. Można wykonać tej aktualizacji przy użyciu:

- Interfejs API REST z [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) w następujący sposób:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Można wdrożyć szablonu usługi Resource Manager z właściwości z interfejsu API REST, aby zaktualizować właściwości zestawu skali globalnej.

- Program Azure Powershell z [AzureRmVmss aktualizacji](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI 2.0 z [az vmss zaktualizować](/cli/azure/vmss#az_vmss_update):
    - Aby zmodyfikować właściwości:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Aby dodać obiekt do listy właściwości w zestawie skali: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Aby usunąć obiekt z listy właściwości w zestawie skali: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Jeśli wdrożono zestaw z skalowania `az vmss create` polecenia, można uruchomić `az vmss create` polecenie ponownie, aby zaktualizować zestaw skali. Upewnij się, że wszystkie właściwości w `az vmss create` polecenia są takie same jak poprzednio, z wyjątkiem właściwości, które chcesz zmodyfikować.

- Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/).

Po zaktualizowaniu modelu zestawu skali, nowa konfiguracja dotyczy wszelkie nowe maszyny wirtualne utworzone w zestawie skalowania. Jednak modeli dla istniejących maszyn wirtualnych w zestawie skalowania nadal należy doprowadzić instalując najnowsze ogólną skali zestaw modelu. W modelu dla każdej maszyny Wirtualnej jest właściwością logiczną o nazwie `latestModelApplied` wskazująca, czy maszyna wirtualna jest aktualny i najnowszych ogólną skali zestaw modelu (`true` oznacza, że maszyna wirtualna jest aktualny i najnowszego modelu).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Sposobu objęcia pomocą najnowszego modelu zestawu skali maszyny wirtualne
Zestawy skalowania ma "zasad uaktualniania" określające, jak zostały podane aktualne z najnowszego modelu zestawu skali maszyny wirtualne. Są trzy tryby dla zasad uaktualniania:

- **Automatyczne** — w tym trybie zestaw skalowania sprawia, że nie gwarantuje o zamówieniu trwa obniżył maszyn wirtualnych. Zestaw skali może wyłączyć wszystkie maszyny wirtualne w tym samym czasie. 
- **Wycofanie** — w tym trybie zestaw skalowania wprowadza aktualizacji w partiach o czasie Wstrzymaj opcjonalne między partie.
- **Ręczne** — w tym trybie, podczas aktualizowania modelu zestawu skali, nic się nie dzieje do istniejących maszyn wirtualnych.
 
Aby zaktualizować istniejące maszyny wirtualne, należy to robić "Ręczne uaktualnianie" każdej istniejącej maszyny Wirtualnej. Możliwość to ręczne uaktualnienie z:

- Interfejs API REST z [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Program Azure Powershell z [AzureRmVmssInstance aktualizacji](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 z [vmss az update wystąpienia](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Można również użyć określonego języka [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Klastrów sieci szkieletowej usług można używać tylko *automatyczne* tryb, ale aktualizacja przebiega inaczej. Aby uzyskać więcej informacji, zobacz [ uaktualnień aplikacji usługi sieć szkieletowa](../service-fabric/service-fabric-application-upgrade.md).

Istnieje jeden typ modyfikacji właściwości zestawu skali globalnej, który nie jest zgodna z zasad uaktualniania. Zmiany w zestawie skali profilu systemu operacyjnego (takich jak nazwa użytkownika i hasło) można zmienić tylko w wersji interfejsu API *2017-12-01* lub nowszym. Te zmiany dotyczą tylko maszyny wirtualne utworzone po zmianie w skali modelu. Aby przenieść istniejące maszyny wirtualne, które są aktualne, należy to robić "odtworzenia z obrazu" każdej istniejącej maszyny wirtualnej. Możesz zrobić to odtworzenia z obrazu za pomocą:

- Interfejs API REST z [compute/virtualmachinescalesets/odtworzenia z obrazu](/rest/api/compute/virtualmachinescalesets/reimage) w następujący sposób:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Program Azure Powershell z [AzureRmVmssVm zestaw](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI 2.0 z [odtworzenia z obrazu vmss az](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Można również użyć określonego języka [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Właściwości z ograniczeń modyfikacji

### <a name="create-time-properties"></a>Właściwości czasu tworzenia
Niektóre właściwości można ustawić tylko podczas tworzenia zestawu skalowania. Te właściwości obejmują:

- Strefy dostępności
- Obraz odwołanie wydawcy
- Oferta odwołanie do obrazu

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Właściwości, które można zmienić tylko na podstawie bieżącej wartości
Niektóre właściwości mogą zmienić wyjątków w zależności od bieżącej wartości. Te właściwości obejmują:

- **singlePlacementGroup** — Jeśli singlePlacementGroup ma wartość true, mogą zostać zmienione na wartość false. Jednak jeśli singlePlacementGroup ma wartość false, on **nie mogą** można zmodyfikować na wartość true.
- **podsieci** — podsieci zestaw skalowania, które mogą zostać zmodyfikowane tak długo, jak oryginalny podsieci i nową podsieć znajdują się w tej samej sieci wirtualnej.

### <a name="properties-that-require-deallocation-to-change"></a>Właściwości, które wymagają zmiany cofania alokacji
Niektóre właściwości mogą można zmienić tylko niektóre wartości, jeśli w zestawie skalowania maszyn wirtualnych są alokację. Te właściwości obejmują:

- **Nazwa jednostki SKU**— Jeśli nowej jednostki SKU maszyny Wirtualnej nie jest obsługiwana na sprzęcie zestaw skalowania jest obecnie, musisz cofnąć maszyn wirtualnych w skali ustawiona przed zmodyfikowaniem nazwa jednostki SKU. Aby uzyskać więcej informacji, zobacz [sposób zmiany rozmiaru maszyny Wirtualnej platformy Azure](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>Aktualizacje specyficzne dla maszyny Wirtualnej
Pewne zmiany mogą być stosowane do określonych maszyn wirtualnych, zamiast właściwości zestawu skali globalnej. Obecnie tylko ma dołączania/odłączania dysków danych do/z maszyn wirtualnych w zestawie skalowania aktualizacji specyficzne dla maszyny Wirtualnej, która jest obsługiwana. Ta funkcja jest dostępna w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [dokumentację w wersji preview](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenariusze

### <a name="application-updates"></a>Aktualizacje aplikacji
Jeśli aplikacja jest wdrażana do skali ustawiana za pośrednictwem rozszerzenia, aktualizacja Konfiguracja rozszerzenia powoduje, że aplikacja do aktualizacji zgodnie z zasadami uaktualnienia. Na przykład, jeśli masz nową wersję skryptu do uruchomienia w niestandardowe rozszerzenie skryptu, można zaktualizować *fileUris* właściwości, aby wskazywały nowy skrypt. W niektórych przypadkach możesz wymusić aktualizację, nawet jeśli jest bez zmian konfiguracji rozszerzenia (na przykład możesz zaktualizować skryptu bez zmiany na identyfikator URI skryptu). W takich przypadkach można modyfikować *wartość forceUpdateTag* Aby wymusić aktualizację. Platformy Azure nie ma możliwości interpretowania tej właściwości. Jeśli zmienisz wartość Brak nie wpływa na sposób uruchamiania rozszerzenia. Zmiana po prostu wymusza rozszerzenia, aby ponownie uruchomić. Aby uzyskać więcej informacji na temat *wartość forceUpdateTag*, zobacz [dokumentacja interfejsu API REST dla rozszerzeń](/rest/api/compute/virtualmachineextensions/createorupdate).

Jest również wspólne dla aplikacji można wdrożyć za pomocą niestandardowego obrazu. W tym scenariuszu zostało opisane w poniższej sekcji.

### <a name="os-updates"></a>Aktualizacje systemu operacyjnego
Jeśli używasz obrazy platformy Azure, można zaktualizować obraz, modyfikując *elementu imageReference* (więcej informacji, zobacz [dokumentacja interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Obrazy platformy jest często stosowanym rozwiązaniem określ "najnowszej" wersji odwołanie do obrazu. Podczas tworzenia, skalowanie w poziomie, a odtworzenia z obrazu, maszyny wirtualne są tworzone z najnowszej dostępnej wersji. Jednak go **nie** oznacza, że obraz systemu operacyjnego jest automatycznie aktualizowany w czasie, jak wydawane są nowe wersje obrazu. Oddzielne funkcja jest obecnie w wersji zapoznawczej, która zapewnia automatycznych uaktualnień systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [dokumentacji automatycznych uaktualnień systemu operacyjnego](virtual-machine-scale-sets-automatic-upgrade.md).

Jeśli używasz niestandardowych obrazów, obraz można zaktualizować przez aktualizację *elementu imageReference* identyfikator (więcej informacji, zobacz [dokumentacja interfejsu API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Przykłady

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualizowanie obrazu systemu operacyjnego dla zestawu skalowania
Może być zestaw skalowania, który działa starsza wersja programu Ubuntu LTS 16.04. Aby zaktualizować do nowszej wersji systemu Ubuntu LTS 16.04, takich jak wersja *16.04.201801090*. Właściwość version odwołanie do obrazu nie jest częścią listy, więc można bezpośrednio zmodyfikować te właściwości z jednym z następujących poleceń:

- Program Azure Powershell z [AzureRmVmss aktualizacji](/powershell/module/azurerm.compute/update-azurermvmss) w następujący sposób:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI 2.0 z [az vmss zaktualizować](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualizacja usługi równoważenia obciążenia dla zestawu skalowania
Załóżmy, że masz skali ustawić usługi równoważenia obciążenia Azure i chcesz zastąpić modułu równoważenia obciążenia Azure z bramy aplikacji Azure. Moduł równoważenia obciążenia i właściwości bramy aplikacji dla zestawu skalowania są częścią listy, dzięki czemu można użyć polecenia Usuń lub Dodaj elementy listy zamiast bezpośrednie modyfikowanie właściwości:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local powershell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local powershell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Interfejs wiersza polecenia platformy Azure 2.0:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Tych poleceniach założono, że w zestawie skali jest tylko jeden IP konfiguracji i obciążenia modułu równoważenia. Jeśli dostępnych jest wiele, może być konieczne zastosowanie indeks listy inne niż *0*.


## <a name="next-steps"></a>Kolejne kroki
Można również wykonywać typowe zadania zarządzania na zestawy skalowania o [Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md) lub [programu Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).