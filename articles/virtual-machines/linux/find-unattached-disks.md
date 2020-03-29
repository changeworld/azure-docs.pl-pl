---
title: Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure
description: Jak znaleźć i usunąć dyski zarządzane i niezarządzane platformy Azure (VHDs/page blobs) przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.topic: article
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6cf48c53e7b5c1cc8537abeda164460de66abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945144"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
Po usunięciu maszyny wirtualnej (VM) na platformie Azure, domyślnie wszystkie dyski, które są dołączone do maszyny wirtualnej nie są usuwane. Ta funkcja pomaga zapobiec utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny Wirtualnej nadal będziesz płacić za dyski niezłączone. W tym artykule pokazano, jak znaleźć i usunąć wszystkie niezałączone dyski i zmniejszyć niepotrzebne koszty. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: znajdowanie i usuwanie niezałączonych dysków 

Poniższy skrypt wyszukuje nieprzyłączone [dyski zarządzane,](managed-disks-overview.md) sprawdzając wartość właściwości **ManagedBy.** Gdy dysk zarządzany jest dołączony do maszyny Wirtualnej, **Właściwość ManagedBy** zawiera identyfikator zasobu maszyny Wirtualnej. Gdy dysk zarządzany jest nieprzyłączony, **ManagedBy** właściwość ma wartość null. Skrypt sprawdza wszystkie dyski zarządzane w subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany z **managedby** właściwość ustawiona na wartość null, skrypt określa, że dysk jest nieprzyłączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedDisks** na 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niezałączonych dysków zarządzanych.
>
>Po przejrzeniu wszystkich dysków nieprzyłączonych uruchom skrypt ponownie i ustaw zmienną **deleteUnattachedDisks** na 1. Ta akcja umożliwia usunięcie wszystkich niezałączonych dysków zarządzanych.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: znajdowanie i usuwanie dysków niepodłączonych 

Dyski niezarządzane to pliki VHD, które są przechowywane jako [obiekty blob stron](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [kontach magazynu platformy Azure.](../../storage/common/storage-create-storage-account.md) Poniższy skrypt wyszukuje niezarządzane dyski niezarządzane (stronicowe obiekty blob) przez sprawdzenie wartości właściwości **LeaseStatus.** Gdy dysk niezarządzany jest dołączony do maszyny Wirtualnej, właściwość **LeaseStatus** jest ustawiona na **Zablokowane**. Gdy dysk niezarządzany nie jest przyłączony, właściwość **LeaseStatus** jest ustawiona na **Odblokowany**. Skrypt sprawdza wszystkie dyski niezarządzane na wszystkich kontach magazynu platformy Azure w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany z właściwością **LeaseStatus** ustawioną na **Odblokowany,** skrypt określa, że dysk jest niezłączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedVHDs** na 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niezałączonych niezarządzanych wirtualnych identyfikatorów wirtualnych.
>
>Po przejrzeniu wszystkich niezałączonych dysków uruchom ponownie skrypt i ustaw zmienną **deleteUnattachedVHDs** na 1. Ta akcja umożliwia usunięcie wszystkich niezałączonych niezarządzanych wirtualnych identyfikatorów wirtualnych.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Następne kroki

[Usuwanie konta magazynu](../../storage/common/storage-create-storage-account.md)



