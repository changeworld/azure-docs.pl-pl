---
title: Rozmiary maszyn wirtualnych systemu Linux platformy Azure — HPC | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarach, dostępne dla systemu Linux o wysokiej wydajności obliczeniowej maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: jonbeck
ms.openlocfilehash: 441e99b86e9560d47af8ea18a2633e3f37a05e94
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2018
ms.locfileid: "34653845"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Wysoka wydajność obliczenia rozmiarów maszyn wirtualnych

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Obsługiwane są tylko wersje 5.x Intel MPI. Nowsze wersje (2017, 2018 r.) Intel MPI biblioteki środowiska uruchomieniowego nie są zgodne ze sterownikami RDMA systemu Linux platformy Azure.


### <a name="distributions"></a>Dystrybucje
 
Wdróż intensywnych obliczeń maszyny Wirtualnej z jednego z obrazów w portalu Azure Marketplace, obsługująca łączność RDMA:
  
* **Ubuntu** — Ubuntu Server 16.04 LTS. Konfigurowanie sterowników RDMA na maszynie Wirtualnej i zarejestrowanie firmy Intel, Intel MPI pobierania:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 dla HPC, SLES 12 z dodatkiem SP3 dla HPC (Premium), SLES 12 z dodatkiem SP3 dla HPC, SLES 12 z dodatkiem SP1 dodatku SP1 dla HPC (Premium). RDMA sterowniki są zainstalowane i Intel MPI pakiety są dystrybuowane na maszynie Wirtualnej. Zainstaluj MPI, uruchamiając następujące polecenie:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC opartych na systemie centOS** -opartych na systemie CentOS 6.5 obliczenia HPC lub nowszy (w przypadku serii H jest zalecane w wersji 7.1 lub nowszej). Sterowniki RDMA i Intel MPI 5.1 są zainstalowane na maszynie Wirtualnej.  
 
  > [!NOTE]
  > W obrazach opartych na systemie CentOS HPC aktualizacji jądra są wyłączone w **yum** pliku konfiguracji. Jest to spowodowane sterowniki RDMA systemu Linux są dystrybuowane jako pakiet RPM i aktualizacje sterowników może nie działać, jeśli jest aktualizowana w jądrze.
  > 
 
### <a name="cluster-configuration"></a>Konfiguracja klastra 
    
Konfiguracja systemu dodatkowe jest potrzebna do uruchamiania zadań MPI w klastrowanych maszyn wirtualnych. Na przykład w klastrze maszyn wirtualnych, należy ustanowić relację zaufania między węzły obliczeniowe. W przypadku typowych ustawień, zobacz [Konfigurowanie klastra RDMA systemu Linux do uruchamiania aplikacji MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Zagadnienia dotyczące topologii sieci
* W przypadku komputerów z obsługą funkcji RDMA maszyn wirtualnych systemu Linux na platformie Azure Eth1 jest zarezerwowana dla ruchu sieciowego RDMA. Nie zmieniaj żadnych ustawień Eth1 lub wszelkie informacje zawarte w pliku konfiguracji odnoszące się do tej sieci. Eth0 jest zarezerwowana do regularnego ruchu sieci platformy Azure.

* Sieć RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. 


## <a name="using-hpc-pack"></a>Za pomocą pakietu HPC Pack
[Pakiet HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), rozwiązanie firmy Microsoft wolnego HPC klastra i zadania zarządzania, stanowi jedną z opcji umożliwiają użycie wystąpień obliczeniowych z systemem Linux. Zainstalowane najnowsze wersje pakietu HPC Pack pomocy technicznej kilka dystrybucje systemu Linux do uruchamiania na wdrożonych na maszynach wirtualnych platformy Azure, zarządzane przez system Windows Server węzłem węzłów obliczeniowych. Z węzłów obliczeniowych z obsługą dostępu RDMA systemu Linux systemem Intel MPI pakietu HPC Pack można zaplanować i uruchomić Linux MPI aplikacji uzyskujących dostęp do sieci RDMA. Zobacz [wprowadzenie do węzłów obliczeniowych systemu Linux w klastrze pakietu HPC Pack na platformie Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](../windows/sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę, wdrażania i używania rozmiary wystąpień intensywnie funkcję RDMA w systemie Linux, zobacz [Konfigurowanie klastra RDMA systemu Linux do uruchamiania aplikacji MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.




