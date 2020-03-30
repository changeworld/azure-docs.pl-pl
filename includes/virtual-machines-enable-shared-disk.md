---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471706"
---
## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Wdrażanie udostępnionego dysku platformy Azure

Aby wdrożyć dysk zarządzany z włączoną funkcją `maxShares` dysku udostępnionego, użyj nowej właściwości i zdefiniuj wartość `>1`. Dzięki temu dysk można udostępnić na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk jest odinstalowany ze wszystkich maszyn wirtualnych. Zobacz [rozmiary dysku](#disk-sizes) dla dozwolonych `maxShares`wartości dla .

Przed użyciem następującego `[parameters('dataDiskName')]` `[resourceGroup().location]`szablonu zastąp , `[parameters('dataDiskSizeGB')]`, i `[parameters('maxShares')]` własnymi wartościami.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Korzystanie z dysków udostępnionych platformy Azure z maszynami wirtualnymi

Po wdrożeniu udostępnionego `maxShares>1`dysku z programem można zainstalować dysk na jednej lub kilku maszynach wirtualnych.

> [!IMPORTANT]
> Wszystkie maszyny wirtualne współużytkowane na dysku muszą być wdrożone w tej samej [grupie miejsc docelowych zbliżeniowych](../articles/virtual-machines/windows/proximity-placement-groups.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Obsługiwane polecenia PR SCSI

Po zamontowaniu udostępnionego dysku na maszynach wirtualnych w klastrze można ustanowić kworum i odczyt/zapis na dysku przy użyciu funkcji PR SCSI. Następujące polecenia PR są dostępne podczas korzystania z dysków udostępnionych platformy Azure:

Aby wchodzić w interakcje z dyskiem, zacznij od listy akcji trwała rezerwacja:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

W przypadku korzystania z PR_RESERVE, PR_PREEMPT_RESERVATION lub PR_RELEASE_RESERVATION podaj jeden z następujących typów rezerwacji trwałej:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

Należy również podać klucz trwałej rezerwacji podczas korzystania z PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION lub PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Następne kroki

Jeśli chcesz spróbować dysków udostępnionych, zarejestruj się w [naszej wersji zapoznawczej.](https://aka.ms/AzureSharedDiskPreviewSignUp)