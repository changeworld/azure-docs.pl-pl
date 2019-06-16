---
title: Znajdowanie i usuwanie niedołączonych Azure dyski zarządzane i niezarządzane | Dokumentacja firmy Microsoft
description: Jak Znajdowanie i usuwanie niedołączonych dyskami platformy Azure zarządzanych i niezarządzanych (pliki VHD/stronicowych obiektów blob) przy użyciu programu Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f4cd419e06ba44b16d24f36e937002dce5196622
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726057"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Znajdowanie i usuwanie niedołączonych Azure dyski zarządzane i niezarządzane

Jeśli usuniesz maszynę wirtualną (VM) na platformie Azure, domyślnie wszystkie dyski, które są dołączone do maszyny Wirtualnej nie są usuwane. Ta funkcja pomaga zapobiegać utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny Wirtualnej, będziesz płacić za niedołączone dyski. W tym artykule pokazano, jak znaleźć i usunąć wszelkie niedołączone dyski i zredukowanie niepotrzebnych kosztów.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: Znajdowanie i usuwanie niedołączonych dysków

Poniższy skrypt szuka niedołączone [usługi managed disks](managed-disks-overview.md) , sprawdzając wartość **ManagedBy** właściwości. Jeśli dysk zarządzany jest dołączony do maszyny Wirtualnej, **ManagedBy** właściwość zawiera identyfikator zasobu maszyny wirtualnej. Po odłączeniu, dysk zarządzany **ManagedBy** właściwość ma wartość null. Skrypt sprawdza, czy wszystkie dyski zarządzane w subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany przy użyciu **ManagedBy** właściwość ustawioną na wartość null, skrypt określa, czy dysk jest odłączone.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt, ustawiając **deleteUnattachedDisks** zmiennej na wartość 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niedołączone dyski zarządzane.
>
>Po przejrzeniu niedołączone dyski, następnie ponownie uruchom skrypt i ustaw **deleteUnattachedDisks** zmiennej do 1. Ta akcja służy do usuwania wszystkich niedołączone dyski zarządzane.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: Znajdowanie i usuwanie niedołączonych dysków

Dyski niezarządzane to pliki VHD, które są przechowywane jako [stronicowe obiekty BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) w [konta usługi Azure storage](../../storage/common/storage-create-storage-account.md). Poniższy skrypt szuka niedołączone dyski niezarządzane (stronicowych obiektów blob), sprawdzając wartość **LeaseStatus** właściwości. Jeśli dysk niezarządzany jest dołączony do maszyny Wirtualnej, **LeaseStatus** właściwość jest ustawiona na **zablokowany**. Po odłączeniu, dysk niezarządzany **LeaseStatus** właściwość jest ustawiona na **odblokowany**. Skrypt sprawdza, czy dysków niezarządzanych w ramach kont magazynu platformy Azure z subskrypcją platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany o **LeaseStatus** właściwością **odblokowany**, skrypt określa, czy dysk jest odłączone.

>[!IMPORTANT]
>Najpierw należy uruchomić skrypt, ustawiając **deleteUnattachedVHDs** zmiennej na wartość 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niedołączone niezarządzanych dysków VHD.
>
>Po przejrzeniu niedołączone dyski, następnie ponownie uruchom skrypt i ustaw **deleteUnattachedVHDs** zmiennej do 1. Ta akcja służy do usuwania wszystkich niedołączone niezarządzanych dysków VHD.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs -eq 1){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
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

Aby uzyskać więcej informacji, zobacz [Usuń konto magazynu](../../storage/common/storage-create-storage-account.md) i [zidentyfikować oddzielone dysków przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
