---
title: Azure Windows rozmiarów maszyn wirtualnych — HPC | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnych Windows komputerowych o wysokiej wydajności maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck;amverma
ms.openlocfilehash: ad490084b34a8bf6e89c7feb14d5cd2e70a8138f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755319"
---
# <a name="high-performance-compute-vm-sizes"></a>Rozmiary maszyn wirtualnych obliczeń o wysokiej wydajności

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **System operacyjny** — system Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** — Microsoft MPI (MS-MPI) 2012 R2 lub nowszym, Intel MPI biblioteki 5.x

  Na maszynach wirtualnych włączone bez do funkcji SR-IOV obsługiwane implementacje MPI, należy użyć interfejsu bezpośredniego sieci firmy Microsoft (ND) do komunikacji między wystąpieniami. Funkcja SR-IOV włączone rozmiarów maszyn wirtualnych (HB i serii HC) na platformie Azure umożliwia niemal dowolną wersję MPI, które ma być używany z Mellanox OFED. 

* **Rozszerzenie maszyny Wirtualnej InfiniBandDriverWindows** — na maszynach wirtualnych z funkcją RDMA, Dodaj rozszerzenie InfiniBandDriverWindows, aby umożliwić InfiniBand. To rozszerzenie maszyny Wirtualnej Windows instaluje interfejsu Network Direct Windows sterowników (na maszynach wirtualnych z innych niż-funkcja SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych z funkcji SR-IOV) dla łączności RDMA.
W przypadku niektórych wdrożeń wystąpienia A8 i A9 rozszerzenia HpcVmDrivers jest automatycznie dodawany. Należy pamiętać, że jest on przestarzały z rozszerzenia maszyny Wirtualnej HpcVmDrivers; nie będzie można zaktualizować. Aby dodać rozszerzenie maszyny Wirtualnej do maszyny Wirtualnej, można użyć [programu Azure PowerShell](/powershell/azure/overview) polecenia cmdlet. 

  Poniższe polecenie instaluje najnowsze rozszerzenia w wersji 1.0 w InfiniBandDriverWindows istniejącej obsługą dostępu RDMA maszyny wirtualnej o nazwie *myVM* wdrożone w grupie zasobów o nazwie *myResourceGroup* w  *Zachodnie stany USA* regionu:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```
  Alternatywnie rozszerzenia maszyn wirtualnych może obejmować w szablonach usługi Azure Resource Manager w celu łatwiejszego wdrażania przy użyciu następującego elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Aby uzyskać więcej informacji, zobacz [rozszerzeniach i funkcjach maszyn wirtualnych](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Możesz także pracować z rozszerzeniami dla maszyn wirtualnych wdrożonych w [klasycznego modelu wdrażania](classic/manage-extensions.md).

* **Przestrzeń adresowa sieci RDMA** — sieci RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. Do uruchamiania aplikacji MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, przestrzeń adresową sieci wirtualnej nie nakłada sieci RDMA.


### <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

System Azure oferuje kilka opcji tworzenia klastrów HPC maszyn wirtualnych Windows, który może komunikować się za pośrednictwem sieci RDMA, w tym: 

* **Maszyny wirtualne** — wdrażanie maszyn wirtualnych z funkcją RDMA HPC w ten sam zestaw dostępności (podczas użycie modelu wdrażania usługi Azure Resource Manager). Jeśli używasz klasycznego modelu wdrażania, należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

* **Zestawy skalowania maszyn wirtualnych** — zestaw skalowania maszyn wirtualnych zestawu, upewnij się, ograniczenie wdrożenia do pojedynczej grupy umieszczania. Na przykład w szablonie usługi Resource Manager, należy ustawić `singlePlacementGroup` właściwość `true`. 

* **Azure CycleCloud** — Tworzenie klastra HPC w [Azure CycleCloud](/azure/cyclecloud/) do uruchamiania zadań MPI w węzłach Windows.

* **Usługa Azure Batch** — tworzenie [usługi Azure Batch](/azure/batch/) węzłów obliczeniowych w puli w celu uruchamiania obciążeń MPI w systemie Windows Server. Aby uzyskać więcej informacji, zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](../../batch/batch-pool-compute-intensive-sizes.md). Zobacz też [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) projektu do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

* **Pakiet Microsoft HPC Pack** - [pakietu HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) zawiera środowiska uruchomieniowego MPI MS, który używa sieci Azure RDMA po wdrożeniu na maszynach wirtualnych z funkcją RDMA Windows. Na przykład wdrożenia, zobacz [Konfigurowanie klastra Windows RDMA za pomocą pakietu HPC Pack do uruchamiania aplikacji MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](../virtual-machines-windows-sizes-memory.md)
- [Optymalizacja pod kątem magazynu](../virtual-machines-windows-sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać listy kontrolne do wystąpień obliczeniowych za pomocą pakietu HPC Pack w systemie Windows Server, zobacz [Konfigurowanie klastra Windows RDMA za pomocą pakietu HPC Pack do uruchamiania aplikacji MPI](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

- Aby korzystać z wystąpień obliczeniowych, podczas uruchamiania aplikacji MPI w usłudze Azure Batch, zobacz [użyć zadania obejmujące wiele wystąpień do uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w usłudze Azure Batch](../../batch/batch-mpi.md).

- Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.
