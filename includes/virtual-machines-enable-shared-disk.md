---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4487beffd0e6a0643f4d3d88f5bb342f23eb848f
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202257"
---
## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Wdrażanie udostępnionego dysku platformy Azure

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, użyj nowej właściwości `maxShares` i zdefiniuj `>1`wartości. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości `maxShares`.

Przed użyciem następującego szablonu Zastąp `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`i `[parameters('maxShares')]` własnymi wartościami.

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

### <a name="using-azure-shared-disks-with-your-vms"></a>Używanie dysków udostępnionych platformy Azure z maszynami wirtualnymi

Po wdrożeniu udostępnionego dysku z `maxShares>1`można zainstalować ten dysk na co najmniej jednej z maszyn wirtualnych.

> [!IMPORTANT]
> Wszystkie maszyny wirtualne, które udostępniają dysk, muszą być wdrożone w tej samej [grupie umieszczania sąsiedztwa](../articles/virtual-machines/windows/proximity-placement-groups.md).

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

## <a name="supported-scsi-pr-commands"></a>Obsługiwane polecenia SCSI PR

Po zainstalowaniu dysku udostępnionego na maszynach wirtualnych w klastrze można ustanowić kworum i odczyt/zapis na dysku przy użyciu żądania ściągnięcia SCSI. Następujące polecenia żądania ściągnięcia są dostępne w przypadku korzystania z dysków udostępnionych platformy Azure:

Aby współdziałać z dyskiem, należy zacząć od listy Akcja zastrzeżenia trwałego:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

W przypadku korzystania z PR_RESERVE, PR_PREEMPT_RESERVATION lub PR_RELEASE_RESERVATION Podaj jeden z następujących typów trwałych:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

W przypadku korzystania z PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION lub PR_RELEASE-rezerwacji należy również podać klucz trwały.


## <a name="next-steps"></a>Następne kroki

Jeśli interesuje Cię próbę przeprowadzenia udostępniania dysków, [Utwórz konto w wersji zapoznawczej](https://aka.ms/shareddisksignup).