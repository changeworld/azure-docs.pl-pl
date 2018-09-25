---
title: Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie | Dokumentacja firmy Microsoft
description: Jak konwertować Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie, przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 8bd5a6012ef13d4cd63d4e5e63b510e55e75ab63
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987275"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie

Managed Disks oferuje trzy opcje magazynu: [dysku Premium SSD](../windows/premium-storage.md), standardowe SSD(Preview) i [standardowych dysków Twardych](../windows/standard-storage.md). Dzięki temu można łatwo przełączać się między opcjami przy minimalnych przestojach w oparciu o potrzeby związane z wydajnością. To nie jest obsługiwana w przypadku dysków niezarządzanych. Ale możesz z łatwością [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) można łatwo przełączać się między typy dysków.

W tym artykule przedstawiono sposób konwertowania dysków zarządzanych warstwy standardowej na premium i odwrotnie przy użyciu wiersza polecenia platformy Azure. Jeśli musisz zainstalować lub uaktualnić go, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Konwersja wymaga ponownego uruchomienia maszyny wirtualnej, więc należy zaplanować migrację magazynu dysków podczas istniejącego okna obsługi. 
* Jeśli używasz dysków niezarządzanych, najpierw [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) na potrzeby przełączania się między opcjami magazynu w tym artykule. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Konwertuj zarządzane dyski maszyny Wirtualnej od planu standard do warstwy premium i odwrotnie

Poniższy przykład pokazuje, jak przełączyć wszystkie dyski maszyny wirtualnej z warstwy standardowa do usługi premium storage. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage.

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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Konwertuj dysk zarządzany od planu standard do warstwy premium i odwrotnie

Tworzenie i testowanie obciążenia możesz mieć kombinację dysków standardowa i premium, aby zmniejszyć koszt. Można wykonać je przez uaktualnienie do usługi premium storage, dyski, które wymagają lepszą wydajność. Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej od planu standard do premium storage i na odwrót. Aby korzystać z dysków zarządzanych w warstwie premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę premium storage.

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Konwertuj dysk zarządzany ze standardowych dysków Twardych na SSD w warstwie standardowa i na odwrót

Poniższy przykład pokazuje, jak przełączyć jednego dysku maszyny wirtualnej ze standardowych dysków Twardych na SSD w warstwie standardowa.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Konwertowanie za pomocą witryny Azure portal

Można również przeprowadzić konwersję dysków niezarządzanych do dysków zarządzanych przy użyciu witryny Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę Wirtualną z listy maszyn wirtualnych w portalu.
3. W bloku maszyny wirtualnej, wybierz **dysków** z menu.
4. W górnej części **dysków** bloku wybierz **migracji do usługi managed disks**.
5. Jeśli maszyna wirtualna znajduje się w zestawie dostępności, będzie ostrzeżenie na **migracji do usługi managed disks** bloku, który należy przekonwertować najpierw zestawu dostępności. Ostrzeżenie powinien mieć łącze, można kliknąć w celu konwersji zestawu dostępności. Po konwersji zestawu dostępności lub maszyna wirtualna nie znajduje się w zestawie dostępności, kliknij przycisk **migracji** można uruchomić procesu migracji dysków do usługi managed disks. 

Maszyny Wirtualnej zostanie zatrzymana i uruchomiona ponownie po zakończeniu migracji.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kopię tylko do odczytu maszyny wirtualnej przy użyciu [migawek](snapshot-copy-managed-disk.md).

