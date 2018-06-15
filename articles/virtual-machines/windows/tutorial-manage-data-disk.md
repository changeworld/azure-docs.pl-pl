---
title: Samouczek — zarządzanie dyskami platformy Azure za pomocą programu Azure PowerShell | Microsoft Docs
description: Z tego samouczka dowiesz się, jak tworzyć dyski platformy Azure dla maszyn wirtualnych i zarządzać nimi za pomocą programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ea9d89b7dd94c38b326b83ff1fbf51595d67599a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190632"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Samouczek — zarządzanie dyskami platformy Azure za pomocą programu Azure PowerShell

Maszyny wirtualne platformy Azure przechowują swoje systemy operacyjne, aplikacje i dane na dyskach. Ważne jest, aby podczas tworzenia maszyny wirtualnej wybrać rozmiar dysku i konfigurację odpowiadające oczekiwanemu obciążeniu. W tym samouczku opisano wdrażanie dysków maszyn wirtualnych i zarządzanie nimi. Zapoznasz się z informacjami na temat następujących zagadnień:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek będzie wymagał modułu programu Azure PowerShell w wersji 5.7.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="default-azure-disks"></a>Domyślne dyski platformy Azure

Do tworzonej maszyny wirtualnej platformy Azure automatycznie dołączane są dwa dyski. 

**Dysk systemu operacyjnego** — dyski systemu operacyjnego mogą mieć rozmiar 4 terabajtów i hostują system operacyjny maszyn wirtualnych.  Dysk systemu operacyjnego ma domyślnie przypisaną literę dysku *c:*. Konfiguracja buforowania dysku systemu operacyjnego jest zoptymalizowana pod kątem wydajności systemu operacyjnego. Dysk systemu operacyjnego **nie powinien** hostować aplikacji ani danych. Na potrzeby aplikacji i danych należy użyć dysku z danymi, które zostanie szczegółowo opisany w dalszej części tego artykułu.

**Dysk tymczasowy** — dyski tymczasowe używają dysku SSD, który znajduje się na tym samym hoście platformy Azure co maszyna wirtualna. Dyski tymczasowe są wysoce wydajne i można przy ich użyciu wykonywać operacje takie jak przetwarzanie danych tymczasowych. Jednak jeśli maszyna wirtualna zostanie przeniesiona do nowego hosta, wszystkie dane przechowywane na dysku tymczasowym zostaną usunięte. Rozmiar dysku tymczasowego zależy od rozmiaru maszyny wirtualnej. Dyski tymczasowe mają domyślnie przypisaną literę dysku *d:*.

### <a name="temporary-disk-sizes"></a>Rozmiary dysków tymczasowych

| Typ | Typowe rozmiary | Maksymalny rozmiar dysku tymczasowego (GiB) |
|----|----|----|
| [Zastosowania ogólne](sizes-general.md) | Seria A, B i D | 1600 |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | Seria F | 576 |
| [Optymalizacja pod kątem pamięci](sizes-memory.md) | Seria D, E, G i M | 6144 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md) | Seria L | 5630 |
| [Procesor GPU](sizes-gpu.md) | Seria N | 1440 |
| [Wysoka wydajność](sizes-hpc.md) | Seria A i H | 2000 |

## <a name="azure-data-disks"></a>Dyski z danymi platformy Azure

Dodatkowe dyski z danymi można dodać w celu instalowania aplikacji i przechowywania danych. Dyski z danymi powinny być używane w sytuacji, gdy potrzebny jest trwały i dynamiczny magazyn danych. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 4 terabajty. Liczba dysków z danymi, które można dołączyć do maszyny wirtualnej, zależy od jej rozmiaru. Na każdy procesor wirtualny maszyny wirtualnej można dołączyć dwa dyski z danymi. 

### <a name="max-data-disks-per-vm"></a>Maksymalna liczba dysków z danymi na maszynę wirtualną

| Typ | Typowe rozmiary | Maksymalna liczba dysków z danymi na maszynę wirtualną |
|----|----|----|
| [Zastosowania ogólne](sizes-general.md) | Seria A, B i D | 64 |
| [Optymalizacja pod kątem obliczeń](sizes-compute.md) | Seria F | 64 |
| [Optymalizacja pod kątem pamięci](sizes-memory.md) | Seria D, E, G i M | 64 |
| [Optymalizacja pod kątem magazynu](sizes-storage.md) | Seria L | 64 |
| [Procesor GPU](sizes-gpu.md) | Seria N | 64 |
| [Wysoka wydajność](sizes-hpc.md) | Seria A i H | 64 |

## <a name="vm-disk-types"></a>Typy dysków maszyny wirtualnej

Na platformie Azure dostępne są dwa typy dysków.

### <a name="standard-disk"></a>Dysk w warstwie Standardowa

Magazyn Standard Storage bazuje na dyskach twardych (HDD) i stanowi ekonomiczne, chociaż wciąż wydajne rozwiązanie. Dyski w warstwie Standardowa idealnie nadają się do ekonomicznej obsługi obciążeń tworzenia i testowania.

### <a name="premium-disk"></a>Dysk w warstwie Premium

Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Usługa Premium Storage obsługuje maszyny wirtualne z serii DS, DSv2, GS i FS. Istnieje pięć typów dysków w warstwie Premium (P10, P20, P30, P40, P50); rozmiar dysku określa typ dysku. Podczas wybierania rozmiaru dysku wartość jest zaokrąglana do następnego typu. Jeśli na przykład rozmiar jest mniejszy niż 128 GB, typ dysku to P10, jeśli rozmiar przypada między 129 GB i 512 GB, rozmiar dysku to P20.

### <a name="premium-disk-performance"></a>Wydajność dysku w warstwie Premium

|Typ dysku magazynu Premium Storage | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Rozmiar dysku (zaokrąglony w górę) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) |
| Maksymalna liczba operacji wejścia/wyjścia na sekundę na dysk | 120 | 240 | 500 | 2300 | 5000 | 7500 | 7500 |
Przepływność na dysk | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

W powyższej tabeli podano maksymalną liczbę operacji wejścia/wyjścia na sekundę na dysk, ale wyższą wydajność można osiągnąć przez stosowanie wielu dysków z danymi. Na przykład do maszyny wirtualnej Standard_GS5 można dołączyć 64 dyski z danymi. Jeśli każda z tych dysków ma rozmiar P30, można osiągnąć maksymalnie 80 000 operacji we/wy na sekundę. Aby uzyskać szczegółowe informacje na temat maksymalnej liczby operacji we/wy na sekundę dla maszyny wirtualnej, zobacz [VM types and sizes (Typy i rozmiary maszyn wirtualnych)](./sizes.md).

## <a name="create-and-attach-disks"></a>Tworzenie i dołączanie dysków

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna. W razie potrzeby utwórz maszynę wirtualną za pomocą poniższych poleceń.

Ustaw nazwę użytkownika i hasło potrzebne dla konta administratora na maszynie wirtualnej przy użyciu polecenia [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -AsJob
```

Parametr `-AsJob` umożliwia tworzenie maszyny wirtualnej w tle, co powoduje powrót do wiersza polecenia programu PowerShell. Możesz wyświetlić szczegóły zadań w tle, używając polecenia cmdlet `Job`.

Do tworzenia konfiguracji początkowej służy polecenie [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). W poniższym przykładzie jest konfigurowany dysk o rozmiarze 128 gigabajtów.

```azurepowershell-interactive
$diskConfig = New-AzureRmDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Do tworzenia dysku danych służy polecenie [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```azurepowershell-interactive
$dataDisk = New-AzureRmDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Do pobierania maszyny wirtualnej, do której chcesz dodać dysk z danymi, służy polecenie [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Do dodawania dysku z danymi do konfiguracji maszyny wirtualnej służy polecenie [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzureRmVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Do aktualizowania maszyny wirtualnej służy polecenie [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```azurepowershell-interactive
Update-AzureRmVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Przygotowywanie dysków z danymi

Po dołączeniu dysku do maszyny wirtualnej należy skonfigurować system operacyjny do korzystania z tego dysku. Poniższy przykład pokazuje, jak ręcznie skonfigurować pierwszy dysk dodany do maszyny wirtualnej. Proces ten można też zautomatyzować przy użyciu [niestandardowego rozszerzenia skryptu](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Konfiguracja ręczna

Utwórz połączenie RDP z maszyną wirtualną. Otwórz program PowerShell i uruchom ten skrypt.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące dysków maszyn wirtualnych, takie jak:

> [!div class="checklist"]
> * Dyski systemu operacyjnego i dyski tymczasowe
> * Dyski z danymi
> * Dyski w warstwie Standardowa i Premium
> * Wydajność dysku
> * Dołączanie i przygotowywanie dysków z danymi

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat automatyzowania konfigurowania maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Automatyzowanie konfiguracji maszyny wirtualnej](./tutorial-automate-vm-deployment.md)
