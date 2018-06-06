---
title: Znajdowanie i usuwanie odłączyć dysku systemu Azure zarządzane i niezarządzane | Dokumentacja firmy Microsoft
description: Jak znaleźć i usunąć odłączyć dysku systemu Azure zarządzane i niezarządzane (pliki VHD/stronicowych obiektów blob) przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 17262978c9600b75a1ddf945cf170fea6ac4f8ce
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756936"
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

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Niezarządzane dysków: znajdowanie i usuwanie odłączyć dysków 

Niezarządzane dyski znajdują się pliki VHD, które są przechowywane jako [stronicowe](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) w [kontami magazynu Azure](../../storage/common/storage-create-storage-account.md). Poniższy skrypt szuka odłączyć dysków niezarządzane (stronicowych obiektów blob), sprawdzając wartość **LeaseStatus** właściwości. Gdy niezarządzany dysku jest dołączony do maszyny Wirtualnej, **LeaseStatus** właściwość jest ustawiona na **zablokowany**. W przypadku niezarządzanych dysku odłączyć, **LeaseStatus** właściwość jest ustawiona na **odblokowany**. Skrypt sprawdza, czy wszystkie dyski niezarządzanego na wszystkich kontach magazynu Azure w subskrypcji platformy Azure. Gdy skrypt lokalizuje niezarządzane dysk z **LeaseStatus** ustawioną właściwość **odblokowany**, skrypt określa, czy dysk jest odłączyć.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt przez ustawienie **deleteUnattachedVHDs** zmiennej na wartość 0. Ta akcja pozwala znaleźć i wyświetlić wszystkich niedołączonej niezarządzane dysków VHD.
>
>Po przejrzeniu odłączyć dysków, uruchom ponownie skrypt i ustaw **deleteUnattachedVHDs** zmienną do 1. Ta akcja służy do usuwania wszystkich niedołączonej niezarządzane dysków VHD.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [usunięcie konta magazynu](../../storage/common/storage-create-storage-account.md) i [zidentyfikować oddzielone dysków przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)



