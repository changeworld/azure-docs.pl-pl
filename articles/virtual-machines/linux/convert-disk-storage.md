---
title: Konwertowanie magazynu dysków zarządzanych między dyskami SSD klasy podstawowej i premium
description: Jak konwertować magazyn dysków zarządzanych platformy Azure ze standardowego na premium lub premium na standard przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431501"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Konwertowanie magazynu dysków zarządzanych platformy Azure ze standardowego na premium lub premium na standard

Istnieją cztery typy dysków zarządzanych platformy Azure: dyski Azure ultra SSD (wersja zapoznawcza), dysk SSD w wersji premium, standardowy dysk SSD i standardowy dysk twardy. Możesz przełączać się między trzema typami dysków GA (premium SSD, standard SSD i standard HDD) w zależności od potrzeb dotyczących wydajności. Nie można jeszcze przełączyć się z lub na ultra SSD, musisz wdrożyć nowy.

Ta funkcja nie jest obsługiwana dla dysków niezarządzanych. Można jednak łatwo [przekonwertować dysk niezarządzany na dysk zarządzany,](convert-unmanaged-to-managed-disks.md) aby móc przełączać się między typami dysków.

W tym artykule pokazano, jak konwertować dyski zarządzane z standardowego na premium lub premium na standard przy użyciu interfejsu wiersza polecenia platformy Azure. Aby zainstalować lub uaktualnić narzędzie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Konwersja dysku wymaga ponownego uruchomienia maszyny wirtualnej (VM), więc zaplanuj migrację magazynu dysku podczas istniejącego okna konserwacji.
* W przypadku dysków niezarządzanych należy najpierw [przekonwertować na dyski zarządzane, aby](convert-unmanaged-to-managed-disks.md) przełączać się między opcjami magazynowania.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Przełączanie wszystkich dysków zarządzanych maszyny Wirtualnej między usługą Premium a Standard

W tym przykładzie pokazano, jak przekonwertować wszystkie dyski maszyny Wirtualnej z magazynu standardowego na magazyn w wersji Premium lub z magazynu Premium na Standardowy. Aby korzystać z dysków zarządzanych w układce, maszyna wirtualna musi używać [rozmiaru maszyny Wirtualnej,](sizes.md) który obsługuje magazyn w klasie Premium. W tym przykładzie przełącza się również do rozmiaru, który obsługuje magazynu w wersji Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełączanie poszczególnych dysków zarządzanych między standardem a usługą Premium

W przypadku obciążenia deweloper/test można mieć kombinację dysków standard i premium, aby zmniejszyć koszty. Można uaktualnić tylko te dyski, które wymagają lepszej wydajności. W tym przykładzie pokazano, jak przekonwertować jeden dysk maszyny Wirtualnej z magazynu standardowego na magazyn w wersji Premium lub z magazynu Premium na Standardowy. Aby korzystać z dysków zarządzanych w układce, maszyna wirtualna musi używać [rozmiaru maszyny Wirtualnej,](sizes.md) który obsługuje magazyn w klasie Premium. W tym przykładzie przełącza się również do rozmiaru, który obsługuje magazynu w wersji Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Przełączanie dysków zarządzanych między standardowym dyskiem twardym a standardowym dyskiem SSD

W tym przykładzie pokazano, jak przekonwertować pojedynczy dysk maszyn wirtualnych ze standardowego dysku twardego na standardowy dysk SSD lub ze standardowego dysku SSD na standardowy dysk twardy.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Przełączanie dysków zarządzanych między standardem a usługą Premium w witrynie Azure portal

Wykonaj następujące kroki:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz maszynę wirtualną z listy **maszyn wirtualnych**.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz **pozycję Zatrzymaj** w górnej części **okienka Przegląd maszyny** Wirtualnej i poczekaj na zatrzymanie maszyny Wirtualnej.
4. W okienku maszyny Wirtualnej wybierz pozycję **Dyski** z menu.
5. Wybierz dysk, który chcesz przekonwertować.
6. Z menu **wybierz opcję Konfiguracja.**
7. Zmień **typ konta** ze **standardowego dysku twardego** na dysk **SSD premium** lub z premium **SSD** na **standardowy dysk twardy.**
8. Wybierz **pozycję Zapisz**i zamknij okienko dysku.

Aktualizacja typu dysku jest natychmiastowa. Po konwersji możesz ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

Tworzenie kopii maszyny wirtualnej tylko do odczytu przy użyciu [migawek](snapshot-copy-managed-disk.md).
