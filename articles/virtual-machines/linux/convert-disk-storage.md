---
title: Konwertuj Azure zarządzanego dyski magazynu ze standardu do premium i na odwrót | Dokumentacja firmy Microsoft
description: Jak przekonwertować Azure zarządzane dyski magazynu ze standardu do premium i odwrotnie, przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2017
ms.locfileid: "23950511"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konwertuj Azure zarządzanego dyski magazynu ze standardu do premium i na odwrót

Zarządzane dysków oferuje dwie opcje magazynu: [Premium](../windows/premium-storage.md) (oparte na dysk SSD) i [standardowe](../windows/standard-storage.md) (oparte na dysk twardy). Umożliwia ona łatwo przełączać się między dwie opcje z minimalnym czasem przestojów na podstawie Twoich potrzeb wydajności. Ta funkcja nie jest dostępna dla niezarządzanego dysków. Ale możesz z łatwością [konwertować na zarządzane dyski](convert-unmanaged-to-managed-disks.md) można łatwo przełączać się między dwie opcje.

W tym artykule przedstawiono sposób konwertowania dysków zarządzanych ze standardu do premium i na odwrót przy użyciu wiersza polecenia platformy Azure. Jeśli musisz zainstalować lub uaktualnić go, zobacz [zainstalować Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc planowanie migracji magazynu dysków w istniejącym oknie obsługi. 
* Jeśli używane są niezarządzane dysków, najpierw [konwertować na zarządzane dyski](convert-unmanaged-to-managed-disks.md) do korzystania z tego artykułu w celu przełączania się między dwie opcje magazynu. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konwertuj wszystkie zarządzane dyski maszyny wirtualnej ze standardu na — wersja premium i na odwrót

W poniższym przykładzie zostanie przedstawiony sposób Przełącz wszystkie dyski maszyny Wirtualnej ze standardu, aby Magazyn w warstwie premium. Aby użyć dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) obsługującym magazyn w warstwie premium. W tym przykładzie również zmienia rozmiar, który obsługuje magazyn w warstwie premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konwertuj dysk zarządzanych ze standardu na — wersja premium i na odwrót

Dla obciążenia i testowania możesz mieć kombinację dysków warstwy standardowa i premium, aby zmniejszyć koszty. Można wykonać je przez uaktualnienie do magazyn w warstwie premium, tylko dyski, które wymagają lepszą wydajność. W poniższym przykładzie zostanie przedstawiony sposób przełącznika jednego dysku maszyny Wirtualnej ze standardu, aby Magazyn w warstwie premium i na odwrót. Aby użyć dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) obsługującym magazyn w warstwie premium. W tym przykładzie również zmienia rozmiar, który obsługuje magazyn w warstwie premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Następne kroki

Podjąć tylko do odczytu kopię maszyny Wirtualnej za pomocą [migawki](snapshot-copy-managed-disk.md).

