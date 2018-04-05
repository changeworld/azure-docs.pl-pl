---
title: Znajdowanie i usuwanie odłączyć dysku systemu Azure zarządzane i niezarządzane | Dokumentacja firmy Microsoft
description: Jak znaleźć i usunąć odłączyć dysku systemu Azure zarządzane i niezarządzane (pliki VHD/stronicowych obiektów blob) przy użyciu wiersza polecenia platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 1718b35aa68937cad4b3ae7e677e300506820bd0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Znajdowanie i usuwanie odłączyć dysku systemu Azure zarządzane i niezarządzane
Po usunięciu maszyny wirtualnej (VM) na platformie Azure, domyślnie nie są usuwane wszystkie dyski, które są dołączone do maszyny Wirtualnej. Ta funkcja pomaga zapobiec utracie danych z powodu usunięcia przypadkowe maszyn wirtualnych. Po usunięciu maszyny Wirtualnej będzie kontynuował opłacać odłączyć dysków. W tym artykule przedstawiono, jak znaleźć i Usuń wszystkie dyski odłączyć i zredukowanie niepotrzebnych kosztów. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: znajdowanie i usuwanie odłączyć dysków 

Poniższy skrypt szuka niedołączonej [dyskach zarządzanych](managed-disks-overview.md) , sprawdzając wartość **zarządzane** właściwości. Jeśli dysków zarządzanych jest podłączony do maszyny Wirtualnej, **zarządzane** właściwość zawiera identyfikator zasobu maszyny wirtualnej. W przypadku dysków zarządzanych odłączyć, **zarządzane** właściwość ma wartość null. Skrypt sprawdza, czy wszystkie dyski zarządzanych w subskrypcji platformy Azure. Jeśli skrypt lokalizuje dysków zarządzanych z **zarządzane** właściwością o wartości null, skrypt określa, czy dysk jest odłączyć.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt przez ustawienie **deleteUnattachedDisks** zmiennej na wartość 0. Ta akcja pozwala znaleźć i wyświetlić niedołączonej zarządzanych dysków.
>
>Po przejrzeniu odłączyć dysków, uruchom ponownie skrypt i ustaw **deleteUnattachedDisks** zmienną do 1. Ta akcja służy do usuwania wszystkich odłączyć dysków zarządzanych.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niezarządzane dysków: znajdowanie i usuwanie odłączyć dysków 

Niezarządzane dyski znajdują się pliki VHD, które są przechowywane jako [stronicowe](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) w [kontami magazynu Azure](../../storage/common/storage-create-storage-account.md). Poniższy skrypt szuka odłączyć dysków niezarządzane (stronicowych obiektów blob), sprawdzając wartość **LeaseStatus** właściwości. Gdy niezarządzany dysku jest dołączony do maszyny Wirtualnej, **LeaseStatus** właściwość jest ustawiona na **zablokowany**. W przypadku niezarządzanych dysku odłączyć, **LeaseStatus** właściwość jest ustawiona na **odblokowany**. Skrypt sprawdza, czy wszystkie dyski niezarządzanego na wszystkich kontach magazynu Azure w subskrypcji platformy Azure. Gdy skrypt lokalizuje niezarządzane dysk z **LeaseStatus** ustawioną właściwość **odblokowany**, skrypt określa, czy dysk jest odłączyć.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt przez ustawienie **deleteUnattachedVHDs** zmiennej na wartość 0. Ta akcja pozwala znaleźć i wyświetlić wszystkich niedołączonej niezarządzane dysków VHD.
>
>Po przejrzeniu odłączyć dysków, uruchom ponownie skrypt i ustaw **deleteUnattachedVHDs** zmienną do 1. Ta akcja służy do usuwania wszystkich niedołączonej niezarządzane dysków VHD.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

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

[Usunięcie konta magazynu](../../storage/common/storage-create-storage-account.md)



