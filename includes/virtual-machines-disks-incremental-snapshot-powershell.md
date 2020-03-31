---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299463"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Obsługiwane regiony
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Za pomocą programu Azure PowerShell można utworzyć migawkę przyrostową. Potrzebna będzie najnowsza wersja programu Azure PowerShell, następujące polecenie zainstaluje ją lub zaktualizuje istniejącą instalację do najnowszej:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po zainstalowaniu zaloguj się do sesji `az login`programu PowerShell za pomocą programu .

Aby utworzyć migawkę przyrostową za pomocą programu Azure PowerShell, ustaw konfigurację za `-Incremental` pomocą [new-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) z parametrem, `-Snapshot` a następnie przekaż go jako zmienną do [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) za pośrednictwem parametru.

Zamień `<yourDiskNameHere>` `<yourDesiredSnapShotNameHere>` i `<yourResourceGroupNameHere>`wartości, a następnie można użyć następującego skryptu, aby utworzyć migawkę przyrostową:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

Można zidentyfikować przyrostowe migawki z tego `SourceResourceId` samego `SourceUniqueId` dysku z właściwości i migawek. `SourceResourceId`to identyfikator zasobu usługi Azure Resource Manager dysku nadrzędnego. `SourceUniqueId`jest wartością dziedziczoną `UniqueId` z właściwości dysku. Jeśli chcesz usunąć dysk, a następnie utworzyć nowy dysk o tej `UniqueId` samej nazwie, zmieni się wartość właściwości.

Można użyć `SourceResourceId` `SourceUniqueId` i utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Zamień `<yourResourceGroupNameHere>` na wartość, a następnie możesz użyć następującego przykładu, aby wyświetlić listę istniejących migawek przyrostowych:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Można również użyć szablonów usługi Azure Resource Manager, aby utworzyć migawkę przyrostową. Musisz upewnić się, że apiVersion jest ustawiona na **2019-03-01** i że właściwość przyrostowa jest również ustawiona na true. Poniższy urywek jest przykładem tworzenia migawki przyrostowej za pomocą szablonów Menedżera zasobów:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod demonstrujący różnicowe możliwości migawek przyrostowych, za pomocą platformy .NET, zobacz [Kopiowanie kopii zapasowych dysków zarządzanych platformy Azure do innego regionu z możliwością różnicowania migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
