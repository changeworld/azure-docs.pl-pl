---
title: Znajdowanie i usuwanie niedołączonych Azure dyski zarządzane i niezarządzane | Dokumentacja firmy Microsoft
description: Jak Znajdowanie i usuwanie niedołączonych dyskami platformy Azure zarządzanych i niezarządzanych (pliki VHD/stronicowych obiektów blob) przy użyciu wiersza polecenia platformy Azure.
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
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 21c15a943974b80469eb9bd71cbaf11a7bc34b4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721836"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Znajdowanie i usuwanie niedołączonych Azure dyski zarządzane i niezarządzane
Jeśli usuniesz maszynę wirtualną (VM) na platformie Azure, domyślnie wszystkie dyski, które są dołączone do maszyny Wirtualnej nie są usuwane. Ta funkcja pomaga zapobiegać utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny Wirtualnej, będziesz płacić za niedołączone dyski. W tym artykule pokazano, jak znaleźć i usunąć wszelkie niedołączone dyski i zredukowanie niepotrzebnych kosztów. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: Znajdowanie i usuwanie niedołączonych dysków 

Poniższy skrypt szuka niedołączone [usługi managed disks](managed-disks-overview.md) , sprawdzając wartość **ManagedBy** właściwości. Jeśli dysk zarządzany jest dołączony do maszyny Wirtualnej, **ManagedBy** właściwość zawiera identyfikator zasobu maszyny wirtualnej. Po odłączeniu, dysk zarządzany **ManagedBy** właściwość ma wartość null. Skrypt sprawdza, czy wszystkie dyski zarządzane w subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany przy użyciu **ManagedBy** właściwość ustawioną na wartość null, skrypt określa, czy dysk jest odłączone.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt, ustawiając **deleteUnattachedDisks** zmiennej na wartość 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niedołączone dyski zarządzane.
>
>Po przejrzeniu niedołączone dyski, następnie ponownie uruchom skrypt i ustaw **deleteUnattachedDisks** zmiennej do 1. Ta akcja służy do usuwania wszystkich niedołączone dyski zarządzane.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: Znajdowanie i usuwanie niedołączonych dysków 

Dyski niezarządzane to pliki VHD, które są przechowywane jako [stronicowe obiekty BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) w [konta usługi Azure storage](../../storage/common/storage-create-storage-account.md). Poniższy skrypt szuka niedołączone dyski niezarządzane (stronicowych obiektów blob), sprawdzając wartość **LeaseStatus** właściwości. Jeśli dysk niezarządzany jest dołączony do maszyny Wirtualnej, **LeaseStatus** właściwość jest ustawiona na **zablokowany**. Po odłączeniu, dysk niezarządzany **LeaseStatus** właściwość jest ustawiona na **odblokowany**. Skrypt sprawdza, czy dysków niezarządzanych w ramach kont magazynu platformy Azure z subskrypcją platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany o **LeaseStatus** właściwością **odblokowany**, skrypt określa, czy dysk jest odłączone.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt, ustawiając **deleteUnattachedVHDs** zmiennej na wartość 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niedołączone niezarządzanych dysków VHD.
>
>Po przejrzeniu niedołączone dyski, następnie ponownie uruchom skrypt i ustaw **deleteUnattachedVHDs** zmiennej do 1. Ta akcja służy do usuwania wszystkich niedołączone niezarządzanych dysków VHD.
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

## <a name="next-steps"></a>Kolejne kroki

[Usuń konto magazynu](../../storage/common/storage-create-storage-account.md)



