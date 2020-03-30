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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343042"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>Interfejs wiersza polecenia

Możesz utworzyć migawkę przyrostową za pomocą interfejsu wiersza polecenia platformy Azure, będziesz potrzebować najnowszej wersji interfejsu wiersza polecenia platformy Azure. 

W systemie Windows następujące polecenie zainstaluje lub zaktualizuje istniejącą instalację do najnowszej wersji:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
W systemie Linux instalacja interfejsu wiersza polecenia będzie się różnić w zależności od wersji systemu operacyjnego.  Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) dla określonej wersji systemu Linux.

Aby utworzyć migawkę przyrostową, należy użyć `--incremental` [migawki az create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) z parametrem.

Poniższy przykład tworzy migawkę przyrostową, `<yourDesiredSnapShotNameHere>`zamień , `<yourResourceGroupNameHere>``<exampleDiskName>`i `<exampleLocation>` z własnymi wartościami, a następnie uruchom przykład:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Można zidentyfikować przyrostowe migawki z tego `SourceResourceId` samego `SourceUniqueId` dysku z właściwości i migawek. `SourceResourceId`to identyfikator zasobu usługi Azure Resource Manager dysku nadrzędnego. `SourceUniqueId`jest wartością dziedziczoną `UniqueId` z właściwości dysku. Jeśli chcesz usunąć dysk, a następnie utworzyć nowy dysk o tej `UniqueId` samej nazwie, zmieni się wartość właściwości.

Można użyć `SourceResourceId` `SourceUniqueId` i utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Poniższy przykład będzie lista wszystkich migawek przyrostowych skojarzonych z określonego dysku, ale wymaga pewnej konfiguracji.

W tym przykładzie użyto jq do wykonywania zapytań o dane. Aby uruchomić przykład, należy [zainstalować jq](https://stedolan.github.io/jq/download/).

Zamień `<yourResourceGroupNameHere>` i `<exampleDiskName>` z wartościami, a następnie można użyć następującego przykładu, aby wyświetlić listę istniejących migawek przyrostowych, o ile zainstalowano również jq:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
