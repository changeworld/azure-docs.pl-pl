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
ms.subservice: disks
ms.openlocfilehash: 6b78027191d72c10b20c9d09a92c82be4a9e3e05
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650805"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Konwertuj Azure zarządzane dyski magazynu od planu standard do warstwy premium i odwrotnie

Dyski zarządzane oferuje cztery [typ dysku](disks-types.md) opcje: Dysku Ultra półprzewodnikowym (SSD), w warstwie Premium SSD, standardów dysków SSD i dysków standardowych dysków twardych (HDD). Dzięki temu można łatwo przełączać się między opcjami przy minimalnych przestojach w oparciu o potrzeby związane z wydajnością. To nie jest obsługiwana w przypadku dysków niezarządzanych. Ale możesz z łatwością [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) można łatwo przełączać się między typy dysków.

W tym artykule przedstawiono sposób konwertowania dysków zarządzanych warstwy standardowej na premium i odwrotnie przy użyciu wiersza polecenia platformy Azure. Jeśli musisz zainstalować lub uaktualnić go, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli). 

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

## <a name="convert-managed-disks-between-standard-and-premium-in-azure-portal"></a>Konwertowanie z dysków zarządzanych warstw standardowa i premium w witrynie Azure portal

Możesz również przekonwertować dyski zarządzane warstw standardowa i premium w witrynie Azure portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę Wirtualną z listy **maszyn wirtualnych** w portalu.
3. Jeśli maszyna wirtualna nie zostanie zatrzymana, kliknij przycisk **zatrzymać** górnej części bloku omówienia maszyn wirtualnych i zaczekaj, aż maszyny Wirtualnej zatrzymać.
3. W bloku maszyny wirtualnej, wybierz **dysków** z menu.
4. Wybierz dysk, który ma zostać przekonwertowany.
5. Wybierz **konfiguracji** z menu.
6. Zmiana **typ konta** z **standardowych dysków Twardych** do **dysku Premium SSD**i na odwrót.
7. Kliknij przycisk **Zapisz** i zamknąć blok dysku.

Aktualizacja typu dysku jest efektywne natychmiastowe. Możesz ponownie uruchomić maszynę Wirtualną po konwersji.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kopię tylko do odczytu maszyny wirtualnej przy użyciu [migawek](snapshot-copy-managed-disk.md).

