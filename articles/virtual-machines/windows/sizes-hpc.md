---
title: Azure Windows rozmiarów maszyn wirtualnych — HPC | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnych Windows komputerowych o wysokiej wydajności maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 31e81741d2a627888e478b3871bdbab4e6b6d6f5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902643"
---
# <a name="high-performance-compute-vm-sizes"></a>Rozmiary maszyn wirtualnych obliczeń o wysokiej wydajności

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **System operacyjny** — system Windows Server 2016, Windows Server 2012 R2, Windows Server 2012

* **MPI** — Microsoft MPI (MS-MPI) 2012 R2 lub nowszym, Intel MPI biblioteki 5.x

  Obsługiwane implementacje MPI przy użyciu interfejsu Network Direct Microsoft interfejsu do komunikacji między wystąpieniami. 

* **Przestrzeń adresowa sieci RDMA** — sieci RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. Do uruchamiania aplikacji MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, przestrzeń adresową sieci wirtualnej nie nakłada sieci RDMA.

* **Rozszerzenie maszyny Wirtualnej HpcVmDrivers** — na maszynach wirtualnych z funkcją RDMA, Dodaj rozszerzenie HpcVmDrivers, aby zainstalować sterowniki urządzeń sieciowych Windows dla łączności RDMA. (W przypadku niektórych wdrożeń wystąpienia A8 i A9 rozszerzenia HpcVmDrivers jest automatycznie dodawany.) Aby dodać rozszerzenie maszyny Wirtualnej do maszyny Wirtualnej, można użyć [programu Azure PowerShell](/powershell/azure/overview) polecenia cmdlet. 

  
  Poniższe polecenie instaluje najnowsze rozszerzenia w wersji 1.1 w HpcVMDrivers istniejącej obsługą dostępu RDMA maszyny wirtualnej o nazwie *myVM* wdrożone w grupie zasobów o nazwie *myResourceGroup* w  *Zachodnie stany USA* regionu:

  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  Aby uzyskać więcej informacji, zobacz [rozszerzeniach i funkcjach maszyn wirtualnych](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Możesz także pracować z rozszerzeniami dla maszyn wirtualnych wdrożonych w [klasycznego modelu wdrażania](classic/manage-extensions.md).


## <a name="using-hpc-pack"></a>Za pomocą pakietu HPC Pack

[Pakiet Microsoft HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), rozwiązanie firmy Microsoft wolnego HPC klastra i zadania zarządzania, stanowi jedną z opcji w celu utworzenia klastra obliczeniowego na platformie Azure do uruchamiania aplikacji MPI z systemem Windows i innych obciążeń HPC. HPC Pack 2012 R2 i nowszych obejmują środowiska uruchomieniowego dla MPI MS, który używa sieci Azure RDMA po wdrożeniu na maszynach wirtualnych z funkcją RDMA.



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




