---
title: Migrowanie klasycznej maszyny Wirtualnej do ARM dysku zarządzanego maszyny Wirtualnej | Dokumentacja firmy Microsoft
description: Przeprowadź migrację jednej maszyny Wirtualnej platformy Azure z klasycznego modelu wdrażania do usługi Managed Disks w modelu wdrażania usługi Resource Manager.
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
ms.openlocfilehash: b69cc0bcff8f791e0740b30f5521a2a7b36af3b1
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437661"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>Migrowanie klasycznej maszyny Wirtualnej pod kątem używania dysku zarządzanego 


Ta sekcja ułatwia Migrowanie istniejących maszyn wirtualnych platformy Azure z klasycznego modelu wdrażania do [Managed Disks](managed-disks-overview.md) w modelu wdrażania usługi Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Planowanie migracji do usługi Managed Disks

Ta sekcja ułatwia najlepszych decyzji o typach maszyn wirtualnych i dysków.


### <a name="location"></a>Lokalizacja

Wybierz lokalizację, w której są dostępne dyski Managed Disks. Jeśli migrujesz do dysków zarządzanych, wspierane przez usługę Premium storage, upewnij się również Premium storage jest dostępna w danym regionie. Zobacz [byRegion usług platformy Azure](https://azure.microsoft.com/regions/#services) dla aktualnych informacji o dostępnych lokalizacji.

### <a name="vm-sizes"></a>Rozmiary maszyn wirtualnych

Jeśli użytkownik migruje do usługi Managed Disks, użycie magazynu Premium storage, musi być aktualizowana rozmiar maszyny Wirtualnej do magazynu w warstwie Premium stanie rozmiaru dostępne w regionie, w którym znajduje się maszyna wirtualna. Przejrzyj rozmiarów maszyn wirtualnych, które są zdolne do magazynu w warstwie Premium. Specyfikacje rozmiaru maszyny Wirtualnej platformy Azure są wymienione w [rozmiary maszyn wirtualnych](sizes.md).
Sprawdź charakterystyki wydajności maszyn wirtualnych, które współpracują z magazynu w warstwie Premium i wybierz odpowiedni rozmiar maszyny Wirtualnej, najlepiej pasujące do obciążenia. Upewnij się, że jest dostępna wystarczająca przepustowość na maszynie Wirtualnej do kierowania ruchu dysku.

### <a name="disk-sizes"></a>Rozmiary dysków

**Premium**

Istnieje siedem typy magazynu w warstwie Premium, które mogą być używane z maszyny Wirtualnej i każdy z nich ma określone operacje We/Wy i przepływność limitów. Należy wziąć pod uwagę te limity podczas wybierania typu dysku Premium dla swojej maszyny Wirtualnej, w zależności od potrzeb aplikacji pod względem wydajności, wydajność, skalowalność i ładuje szczytowego.

| Typ magazynu dysków Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Rozmiar dysku           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Przepływność na dysk | 25 MB na sekundę  | 50 MB na sekundę  | 100 MB na sekundę | 150 MB na sekundę | 200 MB na sekundę | 250 MB na sekundę | 250 MB na sekundę | 

**Standardowa**

Istnieje siedem typów dysków w warstwie standardowa, które mogą być używane z maszyny Wirtualnej. Każdy z nich mają innej pojemności, ale mają limity przepływności i tej samej operacji We/Wy. Wybierz typ Standardowy Managed disks, które są oparte na potrzeby aplikacji związane z pojemnością.

| Typ dysku standardowego  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Rozmiar dysku           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Przepływność na dysk | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 60 MB na sekundę | 


### <a name="disk-caching-policy"></a>Zasady buforowania dysku 

**Premium Managed Disks**

Domyślnie zasady buforowania dysku jest *tylko do odczytu* wszystkich dysków w warstwie Premium danych, a *odczytu i zapisu* dla dysku systemu operacyjnego w warstwie Premium dołączonych do maszyny Wirtualnej. To ustawienie konfiguracji jest zalecane, aby osiągnąć optymalną wydajność dla aplikacji systemu IOs. Dla dysków z danymi zapisu przy odczycie czy tylko do zapisu (takich jak pliki dziennika programu SQL Server) należy wyłączyć buforowanie dysku, dzięki czemu można osiągnąć lepszą wydajność aplikacji.

### <a name="pricing"></a>Cennik

Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Ceny dysków zarządzanych w warstwie Premium jest taka sama jak dysków niezarządzanych w warstwie premium. Ale ceny dysków zarządzanych w warstwie standardowa jest inny niż niezarządzane dyski w warstwie standardowa.


## <a name="checklist"></a>Lista kontrolna

1.  Jeśli użytkownik migruje do usługi Premium Managed Disks, upewnij się, że będzie ona dostępna w regionie, w którym jest przeprowadzana migracja do.

2.  Zdecyduj, nowych seriach maszyn wirtualnych, które będą używane. W przypadku migracji do usługi Premium Managed Disks powinno być zdolny do usługi Premium Storage.

3.  Należy określić dokładny rozmiar maszyny Wirtualnej, który będzie używany, które są dostępne w regionie, w których w przypadku migracji do. Rozmiar maszyny Wirtualnej musi być wystarczająco duży, aby obsługiwać liczbę dysków z danymi, które należy. Na przykład jeśli cztery dyski z danymi, maszyna wirtualna musi mieć co najmniej dwa rdzenie. Ponadto należy wziąć pod uwagę przetwarzania mocy obliczeniowej, pamięci i wymagania dotyczące przepustowości sieci.

4.  Mieć bieżące szczegóły maszyny Wirtualnej zawsze pod ręką, w tym listę dysków i odpowiednie obiekty BLOB dysków VHD.

Przygotowanie aplikacji dla przestojów. Aby przeprowadzić migrację czystego, masz Zatrzymaj przetwarzanie wszystkich w obecnym systemie. Następnie możesz pobrać go do stanu spójności, które można migrować do nowej platformy. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.


## <a name="migrate-the-vm"></a>Migrowanie maszyny Wirtualnej

Przygotowanie aplikacji dla przestojów. Aby przeprowadzić migrację czystego, masz Zatrzymaj przetwarzanie wszystkich w obecnym systemie. Następnie możesz pobrać go do spójnego stanu, które można migrować do nowej platformy. Czas trwania przestoju zależy od ilości danych na dyskach, aby przeprowadzić migrację.

Ta część wymaga programu Azure PowerShell module w wersji 6.0.0 lub nowszej. Uruchom polecenie ` Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Należy również uruchomić polecenie `Connect-AzureRmAccount` w celu nawiązania połączenia z platformą Azure.


Utwórz zmienne typowych parametrów.

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

Tworzenie zarządzanego dysku systemu operacyjnego przy użyciu wirtualnego dysku twardego z klasycznej maszyny Wirtualnej. Upewnij się, że podano pełny identyfikator URI wirtualnego dysku twardego systemu operacyjnego do parametru $osVhdUri. Wprowadź też **- AccountType** jako **Premium_LRS** lub **Standard_LRS** oparte na typie dysków (premium lub standardowa) w przypadku migracji do.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

Dołącz dysk systemu operacyjnego do nowej maszyny Wirtualnej.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

Tworzenie zarządzanego dysku danych na podstawie pliku VHD danych i dodaj go do nowej maszyny Wirtualnej.

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

Tworzenie nowej maszyny Wirtualnej przez ustawienie publicznego adresu IP, sieci wirtualnej i karty sieciowej.

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>Mogą istnieć dodatkowe kroki niezbędne do obsługi aplikacji, który jest nie być dostępne w tym przewodniku.
>
>

## <a name="next-steps"></a>Kolejne kroki

- Nawiąż połączenie z maszyną wirtualną. Aby uzyskać instrukcje, zobacz [jak połączyć i zaloguj się na maszynie wirtualnej platformy Azure, systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

