---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224575"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>Tworzenie przyrostowej migawki (wersja zapoznawcza) dla dysków zarządzanych

Migawki przyrostowe (wersja zapoznawcza) są kopiami zapasowymi w czasie dla dysków zarządzanych, które w razie potrzeby składają się tylko ze wszystkich zmian od ostatniej migawki. Podczas próby pobrania lub użycia migawki przyrostowej jest używany pełny dysk VHD. Ta nowa możliwość tworzenia migawek dysków zarządzanych może potencjalnie spowodować, że nie są już wymagane do przechowywania całego dysku przy każdej pojedynczej migawce, chyba że zostanie wybrana opcja. Podobnie jak regularne migawki, migawki przyrostowe mogą służyć do tworzenia pełnego dysku zarządzanego lub do regularnej migawki.

Istnieje kilka różnic między migawką przyrostową i regularną migawką. Migawki przyrostowe zawsze korzystają ze standardowego magazynu HDD, niezależnie od typu magazynu dysku, a regularne migawki mogą korzystać z dysków SSD w warstwie Premium. Jeśli używasz zwykłych migawek na Premium Storage do skalowania wdrożeń maszyn wirtualnych, zalecamy używanie obrazów niestandardowych w ramach magazynu w warstwie Standardowa w [galerii obrazów udostępnionych](../articles/virtual-machines/linux/shared-image-galleries.md). Pomożemy Ci w osiągnięciu bardziej ogromnej skali z niższym kosztem. Ponadto migawki przyrostowe potencjalnie oferują lepszą niezawodność za pomocą [magazynu Strefowo nadmiarowego](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Jeśli ZRS jest dostępny w wybranym regionie, migawka przyrostowa będzie używać ZRS automatycznie. Jeśli ZRS nie jest dostępny w regionie, migawka domyślnie będzie [magazynem lokalnie nadmiarowy](../articles/storage/common/storage-redundancy-lrs.md) (LRS). Można zastąpić to zachowanie i wybrać jeden z nich ręcznie, ale nie jest to zalecane.

Migawki przyrostowe oferują również funkcję różnicową, która jest unikatowo dostępna dla dysków zarządzanych. Umożliwiają one uzyskanie zmian między dwiema przyrostowymi migawkami tych samych dysków zarządzanych, w dół do poziomu bloku. Można użyć tej funkcji, aby zmniejszyć rozmiary danych podczas kopiowania migawek między regionami.

Jeśli jeszcze nie zarejestrowano się w wersji zapoznawczej i chcesz zacząć korzystać z migawek przyrostowych, Wyślij wiadomość AzureDisks@microsoft.com e-mail na adres, aby uzyskać dostęp do publicznej wersji zapoznawczej.

## <a name="restrictions"></a>Ograniczenia

- Nie można obecnie utworzyć migawek przyrostowych po zmianie rozmiaru dysku.
- Obecnie nie można przenosić migawek przyrostowych między subskrypcjami.
- Obecnie można generować identyfikatory URI SAS maksymalnie pięć migawek określonej rodziny migawek w danym momencie.
- Nie można utworzyć migawki przyrostowej dla określonego dysku poza subskrypcją tego dysku.
- Do siedmiu migawek przyrostowych na dysk można utworzyć co pięć minut.
- Można utworzyć łączną liczbę migawek przyrostowych 200 dla jednego dysku.

## <a name="powershell"></a>PowerShell

Za pomocą Azure PowerShell można utworzyć przyrostową migawkę. Najnowszą wersję programu PowerShell można zainstalować lokalnie. Potrzebna będzie Najnowsza wersja Azure PowerShell, następujące polecenie zainstaluje je lub zaktualizuje istniejącą instalację do najnowszej wersji:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po zakończeniu instalacji zaloguj się do sesji programu PowerShell przy użyciu `az login`polecenia.

`<yourDiskNameHere>`Zastąp `<yourResourceGroupNameHere>`wartości, `<yourDesiredSnapShotNameHere>` i wartościami, aby utworzyć przyrostową migawkę, możesz użyć następującego skryptu:

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

Za pomocą szablonów Azure Resource Manager można także utworzyć przyrostową migawkę. Należy upewnić się, że apiVersion jest ustawiona na **2019-03-01** i że właściwość przyrostowa jest również ustawiona na wartość true. Poniższy fragment kodu stanowi przykład tworzenia przyrostowej migawki z szablonami Menedżer zasobów:

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

## <a name="cli"></a>Interfejs wiersza polecenia

Migawkę przyrostową można utworzyć za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu polecenia [AZ Snapshot Create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create). Przykładowe polecenie będzie wyglądać następująco:

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

Można również zidentyfikować migawki przyrostowe w interfejsie wiersza polecenia przy użyciu `--query` parametru polecenia [AZ Snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show). Tego parametru można użyć do bezpośredniego zapytania o właściwości **Identyfikator sourceresourceid** i **SourceUniqueId** migawek. Identyfikator sourceresourceid jest IDENTYFIKATORem zasobu Azure Resource Manager dysku nadrzędnego. **SourceUniqueId** to wartość dziedziczona ze właściwości **unikatowego** dysku. Jeśli usuniesz dysk, a następnie utworzysz dysk o tej samej nazwie, wartość właściwości **unikatowej** zmieni się.

Przykłady tych zapytań będą wyglądać następująco:

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze nie zarejestrowano się w wersji zapoznawczej i chcesz zacząć korzystać z migawek przyrostowych, Wyślij wiadomość AzureDisks@microsoft.com e-mail na adres, aby uzyskać dostęp do publicznej wersji zapoznawczej.
