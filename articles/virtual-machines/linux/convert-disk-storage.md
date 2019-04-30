---
title: Konwertuj Azure zarządzane dyski magazynu od planu Standard do Premium lub Premium na standardową | Dokumentacja firmy Microsoft
description: Sposób konwertowania Azure zarządzane dyski magazynu od planu Standard do Premium lub Premium na standardową przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 2e7eb455a53abbe2df6ff72f091a599665732429
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63765746"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konwertuj Azure zarządzane dyski magazynu od planu Standard do Premium lub Premium na standardową

Istnieją cztery [typów dysków](disks-types.md) dysków zarządzanych dla platformy Azure: Magazyn dyskowy Ultra platformy Azure, dysk SSD w warstwie Premium, SSD w warstwie standardowa i Standard dysk twardy. Można łatwo przełączać się między dysku Premium SSD, SSD w warstwie standardowa i standardowy dysk twardy, zgodnie z potrzebami wydajności przy niewielkim przestoju. Ta funkcja nie jest obsługiwana dla dysków niezarządzanych lub Ultra Magazyn dyskowy. Ale możesz z łatwością [convert niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md) mieć możliwość przełączania się między typy dysków.

W tym artykule pokazano, jak do przekonwertowania dysków zarządzanych warstwy standardowej na Premium lub Premium na standardową przy użyciu wiersza polecenia platformy Azure. Aby zainstalować lub uaktualnić narzędzie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Dysk, konwersja będzie wymagać ponownego uruchomienia maszyny wirtualnej (VM), więc należy zaplanować migrację magazynu na dysku w trakcie istniejącego już okna obsługi.
* W przypadku dysków niezarządzanych pierwszy [konwersji do usługi managed disks](convert-unmanaged-to-managed-disks.md) , dzięki czemu można przełączać się między opcjami magazynu.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Przełącz wszystkie zarządzane dyski maszyny Wirtualnej między Premium i standardowa

W tym przykładzie pokazano, jak można przekonwertować wszystkie dyski maszyny Wirtualnej z warstwy standardowa do magazynu w warstwie Premium lub wersji Premium do magazynu w warstwie standardowa. Aby korzystać z dysków zarządzanych w warstwie Premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę Premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę Premium storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełącz poszczególne dysków zarządzanych warstw standardowa i Premium

Tworzenie i testowanie obciążenia można mieszać Standard i Premium dysków w celu zmniejszenia kosztów. Można uaktualnić tylko te dyski, wymagających lepszą wydajność. Ten przykład przedstawia sposób konwertowania z jednego dysku maszyny Wirtualnej z warstwy standardowa do magazynu w warstwie Premium lub wersji Premium do magazynu w warstwie standardowa. Aby korzystać z dysków zarządzanych w warstwie Premium, musisz użyć maszyny Wirtualnej [rozmiar maszyny Wirtualnej](sizes.md) , która obsługuje usługę Premium storage. W tym przykładzie zmienia także rozmiar, który obsługuje usługę Premium storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Przełącz dysków zarządzanych między standardowych dysków Twardych i SSD w warstwie standardowa

Ten przykład przedstawia sposób konwertowania pojedynczego dysku maszyny Wirtualnej z standardowych dysków Twardych do SSD w warstwie standardowa lub SSD w warstwie standardowa do standardowych dysków Twardych.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Przełącz dysków zarządzanych warstw standardowa i Premium w witrynie Azure portal

Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz maszynę Wirtualną z listy **maszyn wirtualnych**.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz opcję **zatrzymać** w górnej części maszyny Wirtualnej **Przegląd** okienko i zaczekaj, aż maszyny Wirtualnej zatrzymać.
4. W okienku dla maszyny Wirtualnej wybierz **dysków** z menu.
5. Wybierz dysk, który ma zostać przekonwertowany.
6. Wybierz **konfiguracji** z menu.
7. Zmiana **typ konta** z **standardowych dysków Twardych** do **dysku Premium SSD** lub **dysku Premium SSD** do **standardowych dysków Twardych**.
8. Wybierz **Zapisz**i zamknąć okienko dysku.

Aktualizacja typu dysku jest natychmiastowe. Możesz ponownie uruchomić maszynę Wirtualną po konwersji.

## <a name="next-steps"></a>Kolejne kroki

Utwórz kopię tylko do odczytu, maszyny wirtualnej przy użyciu [migawek](snapshot-copy-managed-disk.md).
