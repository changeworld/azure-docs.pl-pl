---
title: Rozmiary maszyn wirtualnych z systemem Windows Azure — HPC | Microsoft Docs
description: Wyświetla różne rozmiary dostępne dla maszyn wirtualnych o wysokiej wydajności systemu Windows na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: 6fd08ca912c14a50064f4b6da18334d8bf9df0ca
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871589"
---
# <a name="high-performance-compute-vm-sizes"></a>Rozmiary maszyn wirtualnych o wysokiej wydajności

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **System operacyjny** — Windows Server 2016 na wszystkich powyższych maszynach wirtualnych serii HPC. System Windows Server 2012 R2, Windows Server 2012 są również obsługiwane na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (z tym wyjątkiem HB i HC).

* **MPI** — rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure (HB, HC) umożliwiają użycie niemal wszystkich wersji MPI, które mają być używane z Mellanox OFED.
W przypadku maszyn wirtualnych z obsługą funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikacji między wystąpieniami. W związku z tym obsługiwane są tylko programy Microsoft MPI (MS-MPI) 2012 R2 lub nowsze i Intel MPI 5. x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami usługi Azure RDMA.

* **Rozszerzenie maszyny wirtualnej InfiniBandDriverWindows** — na maszynach wirtualnych obsługujących funkcję RDMA Dodaj rozszerzenie InfiniBandDriverWindows, aby włączyć funkcję InfiniBand. To rozszerzenie maszyny wirtualnej systemu Windows instaluje sterowniki funkcji bezpośrednie sieci systemu Windows (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) na potrzeby łączności RDMA.
W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy pamiętać, że rozszerzenie maszyny wirtualnej HpcVmDrivers jest przestarzałe; nie zostanie ona zaktualizowana. Aby dodać rozszerzenie maszyny wirtualnej do maszyny wirtualnej, można użyć poleceń cmdlet [Azure PowerShell](/powershell/azure/overview) . 

  Następujące polecenie instaluje najnowszą wersję 1,0 rozszerzenia InfiniBandDriverWindows na istniejącej maszynie wirtualnej obsługującej funkcję RDMA o nazwie *myVM* wdrożonej w grupie zasobów o nazwie Moja *zasobów* w regionie *zachodnie stany USA* :

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternatywnie rozszerzenia maszyn wirtualnych można dołączać do szablonów Azure Resource Manager, aby ułatwić wdrażanie, z następującym elementem JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Następujące polecenie instaluje najnowszą wersję 1,0 rozszerzenia InfiniBandDriverWindows na wszystkich maszynach wirtualnych z obsługą funkcji RDMA w istniejącym zestawie skalowania maszyn wirtualnych o nazwie *myVMSS* wdrożoną w grupie zasobów o nazwie Moja *zasobów*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Aby uzyskać więcej informacji, zobacz [rozszerzenia i funkcje maszyny wirtualnej](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Możesz również korzystać z rozszerzeń dla maszyn wirtualnych wdrożonych w [klasycznym modelu wdrażania](classic/manage-extensions.md).

* **Przestrzeń adresów sieciowych RDMA** — sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchamiać aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.


### <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Platforma Azure oferuje kilka opcji tworzenia klastrów maszyn wirtualnych z systemem Windows HPC, które mogą komunikować się za pomocą sieci RDMA, w tym: 

* **Maszyny wirtualne** — Wdróż maszyny wirtualne z obsługą funkcji RDMA w tym samym zestawie dostępności (w przypadku korzystania z Azure Resource Manager modelu wdrażania). W przypadku korzystania z klasycznego modelu wdrażania należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

* **Zestawy skalowania maszyn wirtualnych** — w zestawie skalowania maszyn wirtualnych należy ograniczyć wdrożenie do pojedynczej grupy umieszczania. Na przykład w szablonie Menedżer zasobów Ustaw `singlePlacementGroup` właściwość na. `true` 

* **MPI między maszynami wirtualnymi** — jeśli jest to wymagane między maszynami wirtualnymi, upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie dostępności lub na maszynie wirtualnej tego samego zestawu skalowania.

* **Azure CycleCloud** — Tworzenie klastra HPC w [usłudze Azure CycleCloud](/azure/cyclecloud/) w celu uruchamiania zadań MPI w węzłach systemu Windows.

* **Azure Batch** — utwórz pulę [Azure Batch](/azure/batch/) do uruchamiania obciążeń MPI w węzłach obliczeniowych systemu Windows Server. Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpień obsługujących funkcję RDMA lub GPU w pulach usługi Batch](../../batch/batch-pool-compute-intensive-sizes.md). Zobacz również projekt [stoczni usługi Batch](https://github.com/Azure/batch-shipyard) do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

* **Pakiet Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) zawiera środowisko uruchomieniowe dla MS-MPI, które używa sieci usługi Azure RDMA w przypadku wdrożenia na maszynach wirtualnych z systemem Windows z obsługą funkcji RDMA. Na przykład wdrożenia, zobacz [Konfigurowanie klastra funkcji Windows RDMA przy użyciu pakietu HPC Pack do uruchamiania aplikacji MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Inne rozmiary
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić listy kontrolne do używania wystąpień intensywnie korzystających z obliczeń z pakietem HPC Pack w systemie Windows Server, zobacz [Konfigurowanie klastra funkcji Windows RDMA przy użyciu pakietu HPC Pack w celu uruchamiania aplikacji MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Aby korzystać z wystąpień intensywnie korzystających z obliczeń podczas uruchamiania aplikacji MPI przy użyciu Azure Batch, zobacz temat [Używanie zadań z wielu wystąpień w celu uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w programie Azure Batch](../../batch/batch-mpi.md).

- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
