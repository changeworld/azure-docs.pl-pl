---
title: Migrowanie klasyczne maszyny Wirtualnej do dysków zarządzanych w ARM maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Przeprowadź migrację jednej maszyny Wirtualnej platformy Azure z klasycznym modelu wdrażania do zarządzanych dysków w modelu wdrażania usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: 1241f893ca69e3ddaf464e66943caa2697e6d8e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="manually-migrate-a-classic-vm-to-a-new-arm-managed-disk-vm-from-the-vhd"></a>Ręcznie Migrowanie klasyczne maszyny Wirtualnej do nowej ARM zarządzane dysku maszyny Wirtualnej z dysku VHD 


Ta sekcja pomoże Ci do dokonania migracji istniejących maszyn wirtualnych platformy Azure z klasycznym modelu wdrażania do [dysków zarządzanych](managed-disks-overview.md) w modelu wdrażania usługi Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planowanie migracji do zarządzanych dysków

Ta sekcja umożliwia podjęcie najlepszych decyzji w typach maszyny Wirtualnej i dysku.


### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne dyski zarządzanych Azure. W przypadku migracji dysków zarządzanych w warstwie Premium również upewnij się, że magazyn w warstwie Premium jest dostępna w regionie, w którym jest planowana migracja do. Zobacz [byRegion usług Azure](https://azure.microsoft.com/regions/#services) aktualne informacje o dostępnych lokalizacji.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

W przypadku migracji dysków zarządzanych w warstwie Premium, należy zaktualizować rozmiaru maszyny wirtualnej do magazyn w warstwie Premium obsługuje rozmiaru dostępna w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są funkcją Magazyn w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Przejrzyj charakterystyki wydajności maszyn wirtualnych, które pracy z magazyn w warstwie Premium i wybierz najbardziej odpowiedni rozmiar maszyny Wirtualnej, który najlepiej odpowiada obciążenie. Należy upewnić się, że wystarczającą przepustowość dostępne na maszynie Wirtualnej do kierowania ruchu dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Dysków zarządzanych w warstwie Premium**

Istnieje siedem typów dysków zarządzane premium, które mogą być używane z maszyny Wirtualnej i każdy ma określonych IOPs i przepływność limity. Należy wziąć pod uwagę następujące limity podczas wybierania typu dysku Premium dla maszyny Wirtualnej na podstawie potrzeb aplikacji pod względem wydajności, wydajności, skalowalności i ładuje godzinami szczytu.

| Typ dysków Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB / s  | 100 MB na sekundę | 150 MB na sekundę | 200 MB / s | 250 MB na sekundę | 250 MB na sekundę | 

**Dyski standardowe zarządzanych**

Istnieje siedem typów zarządzane standardowych dysków, które mogą być używane z maszyny Wirtualnej. Każdej z nich ma inną wydajności, ale ma tego samego IOPS i limity przepustowości. Wybierz typ dyski standardowe zarządzane na podstawie potrzeb wydajność aplikacji.

| Typ dysku standardowego  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 


### <a name="disk-caching-policy"></a>Dyskowej pamięci podręcznej zasad 

**Dysków zarządzanych w warstwie Premium**

Domyślnie jest dyskowej pamięci podręcznej zasad *tylko do odczytu* dla wszystkich danych dysków Premium i *odczytu i zapisu* dla dysku systemu operacyjnego Premium dołączony do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane w celu osiągnięcia optymalnej wydajności dla aplikacji systemu IOs. W przypadku dysków ciężki zapisu lub w trybie tylko do zapisu danych (takich jak pliki dziennika programu SQL Server) Wyłącz buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Przegląd [ceny dysków zarządzanych](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Cen dysków zarządzanych w warstwie Premium jest taka sama jak niezarządzane dysków Premium. Ale ceny dyski standardowe zarządzanych jest inny niż dyski standardowe niezarządzane.


## <a name="checklist"></a>Lista kontrolna

1.  W przypadku migracji do zarządzanych dysków Premium upewnij się, że jest dostępna w regionie, który w przypadku migracji do.

2.  Zdecyduj, nowej serii maszyny Wirtualnej, który będzie używany. Magazyn w warstwie Premium obsługuje powinno być migrowania do dysków zarządzanych w warstwie Premium.

3.  Określ dokładnie rozmiar maszyny Wirtualnej, który będzie używany, które są dostępne w regionie, w którym w przypadku migracji do. Rozmiar maszyny Wirtualnej musi być wystarczająco duży, aby obsługiwał liczba dysków danych, do których masz. Na przykład jeśli masz cztery dysków danych maszyny Wirtualnej musi mieć co najmniej dwa rdzenie. Należy również rozważyć przetwarzania zasilania, pamięci i musi przepustowości sieci.

4.  Mieć bieżące szczegóły maszyny Wirtualnej pod ręką, w tym listę dysków i odpowiednie obiekty BLOB dysków VHD.

Przygotowanie aplikacji dla przestoju. Przeprowadzenie czystej migracji, należy zatrzymać wszystkie przetwarzania w systemie. Następnie możesz pobrać go do spójnego stanu, które można migrować do nowej platformie. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.


## <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

Przygotowanie aplikacji dla przestoju. Przeprowadzenie czystej migracji, należy zatrzymać wszystkie przetwarzania w systemie. Następnie możesz pobrać go do spójnego stanu, które można migrować do nowej platformie. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.


1.  Najpierw należy ustawić wspólne parametry:

    ```powershell
    $resourceGroupName = 'yourResourceGroupName'
    
    $location = 'your location' 
    
    $virtualNetworkName = 'yourExistingVirtualNetworkName'
    
    $virtualMachineName = 'yourVMName'
    
    $virtualMachineSize = 'Standard_DS3'
    
    $adminUserName = "youradminusername"
    
    $adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force
    
    $imageName = 'yourImageName'
    
    $osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
    
    $dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'
    
    $dataDiskName = 'dataDisk1'
    ```

2.  Tworzenie zarządzanego dysku systemu operacyjnego przy użyciu wirtualnego dysku twardego z klasycznym maszyny Wirtualnej.

    Upewnij się, że podano pełny identyfikator URI wirtualnego dysku twardego systemu operacyjnego do parametru $osVhdUri. Wprowadź też **- AccountType** jako **PremiumLRS** lub **StandardLRS** na podstawie typu dysków (Premium lub Standard) w przypadku migracji do.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $osVhdUri) '
    -ResourceGroupName $resourceGroupName
    ```

3.  Dołączenie dysku systemu operacyjnego do nowej maszyny Wirtualnej.

    ```powershell
    $VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $osDisk.Id '
    -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

4.  Tworzenie dysku danych zarządzanych z pliku VHD danych i dodaj go do nowej maszyny Wirtualnej.

    ```powershell
    $dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig '
    -AccountType PremiumLRS -Location $location -CreationDataCreateOption Import '
    -SourceUri $dataVhdUri ) -ResourceGroupName $resourceGroupName
    
    $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName '
    -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
    ```

5.  Tworzenie nowej maszyny Wirtualnej przez ustawienie publicznego adresu IP, sieci wirtualnej i karty sieciowej.

    ```powershell
    $publicIp = New-AzureRmPublicIpAddress -Name ($VirtualMachineName.ToLower()+'_ip') '
    -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
    
    $vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName
    
    $nic = New-AzureRmNetworkInterface -Name ($VirtualMachineName.ToLower()+'_nic') '
    -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id '
    -PublicIpAddressId $publicIp.Id
    
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id
    
    New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
    ```

> [!NOTE]
>Mogą istnieć dodatkowe kroki niezbędne do obsługi tej aplikacji, która jest nie obejmuje się w tym przewodniku.
>
>

## <a name="next-steps"></a>Kolejne kroki

- Połączenie z maszyną wirtualną. Aby uzyskać instrukcje, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

