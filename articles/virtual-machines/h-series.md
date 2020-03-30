---
title: Seria H — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii H.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 03/10/2020
ms.author: lahugh
ms.openlocfilehash: cb05a8c42c5710182f81d4bc1c76b78abe59c882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79470507"
---
# <a name="h-series"></a>Seria H

Maszyny wirtualne serii H są zoptymalizowane pod kątem aplikacji opartych na wysokich częstotliwościach procesora lub dużej pamięci na podstawowe wymagania. Maszyny wirtualne serii H są wyposażone w 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, do 14 GB pamięci RAM na rdzeń procesora i bez hiperwątkowości. Seria H jest wyposażona w 56 Gb/s Mellanox FDR InfiniBand w konfiguracji drzewa tłuszczu bez blokowania, co zapewnia stałą wydajność RDMA. Maszyny wirtualne z serii H obsługują procesory Intel MPI 5.x i MS-MPI.

ACU: 290–300

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednożyłowa (GHz, szczytowa) | Wydajność RDMA (Gb/s) | Obsługa MPI | Pamięć tymczasowa (GB) | Maks. liczba dysków danych | Maksymalne karty sieciowe Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standardowa_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> W przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączona przez sieć FDR InfiniBand.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


## <a name="supported-os-images-linux"></a>Obsługiwane obrazy systemu operacyjnego (Linux)
 
W portalu Azure Marketplace dostępnych jest wiele dystrybucji systemu Linux obsługujących łączność RDMA:
  
* **HpC z obsługą centos** - Dla maszyn wirtualnych z obsługą innych niż SR-IOV, wersja 6.5 HPC oparta na centos lub nowsza wersja, do 7.5 są odpowiednie. W przypadku maszyn wirtualnych z serii H zalecane są wersje od 7.1 do 7.5. Sterowniki RDMA i Intel MPI 5.1 są zainstalowane na maszynie wirtualnej.
  W przypadku maszyn wirtualnych SR-IOV centos-HPC 7.6 jest zoptymalizowany i fabrycznie załadowany ze sterownikami RDMA i różnymi zainstalowanymi pakietami MPI.
  W przypadku innych obrazów maszyn wirtualnych RHEL/CentOS dodaj rozszerzenie InfiniBandLinux, aby włączyć infiniband. To rozszerzenie maszyny wirtualnej systemu Linux instaluje sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) dla łączności RDMA. Następujące polecenie cmdlet programu PowerShell instaluje najnowszą wersję (wersja 1.0) rozszerzenia InfiniBandDriverLinux na istniejącej maszynie wirtualnej obsługującej rdma. Maszyna wirtualna obsługująca rdma nosi nazwę *myVM* i jest wdrażana w grupie zasobów o nazwie *myResourceGroup* w regionie *Zachodnie stany USA* w następujący sposób:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternatywnie rozszerzenia maszyn wirtualnych mogą być zawarte w szablonach usługi Azure Resource Manager w celu łatwego wdrożenia z następującym elementem JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Następujące polecenie instaluje najnowszą wersję rozszerzenia InfiniBandDriverLinux na wszystkich maszynach wirtualnych obsługujących usługę RDMA w istniejącym zestawie skalowania maszyny wirtualnej o nazwie *myVMSS* wdrożonym w grupie zasobów o nazwie *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > Na obrazach HPC opartych na centos aktualizacje jądra są wyłączone w pliku konfiguracyjnym **yum.** Dzieje się tak, ponieważ sterowniki RDMA systemu Linux są dystrybuowane jako pakiet RPM, a aktualizacje sterowników mogą nie działać, jeśli jądro zostanie zaktualizowane.
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15. Sterowniki RDMA są zainstalowane, a pakiety Intel MPI są dystrybuowane na maszynie wirtualnej. Zainstaluj mpi, uruchamiając następujące polecenie:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Serwer 16.04 LTS, 18.04 LTS. Skonfiguruj sterowniki RDMA na maszynie wirtualnej i zarejestruj się w firmie Intel, aby pobrać procesor Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Aby uzyskać więcej informacji na temat włączania infinibandu, konfigurowania interfejsu MPI, zobacz [Włączanie paska InfiniBand](./workloads/hpc/enable-infiniband.md).

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
