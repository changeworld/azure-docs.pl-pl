---
title: Rozmiar maszyny Wirtualnej systemu Linux platformy Azure - HPC | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnych Linux komputerowych o wysokiej wydajności maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie Vcpu, dysków z danymi i karty sieciowe, a także przepustowości przepływności i sieć magazynu rozmiarów w tej serii.
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
ms.openlocfilehash: a24cb03cd30b212650a36cd5ac40977de5eea11e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Wysoka wydajność obliczeniowe rozmiarów maszyn wirtualnych

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Obsługiwane są tylko wersje 5.x Intel MPI. Nowsze wersje (2017, 2018) Intel MPI biblioteki środowiska uruchomieniowego nie są zgodne z sterowniki RDMA systemu Linux platformy Azure.


### <a name="distributions"></a>Dystrybucje
 
Wdróż obliczeniowych maszyn wirtualnych z jednego z obrazów w portalu Azure Marketplace obsługującej funkcję RDMA połączenia:
  
* **Ubuntu** -LTS Ubuntu Server 16.04. Konfigurowanie sterowników RDMA na maszynie Wirtualnej i rejestrowanie z Intel, aby pobrać Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server** -SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 w przypadku HPC, SLES 12 SP1 w przypadku HPC (Premium). RDMA sterowniki są zainstalowane i Intel MPI pakiety są dystrybuowane na maszynie Wirtualnej. Zainstaluj MPI, uruchamiając następujące polecenie:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **Na podstawie centOS HPC** — na podstawie CentOS HPC 6.5 lub nowszy (H-serii, jest zalecane w wersji 7.1 lub nowszej). Sterowniki RDMA i Intel MPI 5.1 są zainstalowane na maszynie Wirtualnej.  
 
  > [!NOTE]
  > Na obrazach na podstawie CentOS HPC aktualizacji jądra są wyłączone w **yum** pliku konfiguracji. To dlatego sterowniki Linux RDMA są dystrybuowane jako pakiet RPM, a aktualizacje sterowników może nie działać w przypadku jądra jest aktualizowany.
  > 
 
### <a name="cluster-configuration"></a>Konfiguracja klastra 
    
Dodatkowy system konfiguracji jest wymagany do uruchomienia zadań MPI na klastrowanych maszyn wirtualnych. Na przykład w klastrze maszyn wirtualnych, należy ustanowić relację zaufania między węzły obliczeniowe. W przypadku typowych ustawień, zobacz [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Zagadnienia dotyczące topologii sieci
* W przypadku komputerów z obsługą RDMA maszyn wirtualnych systemu Linux na platformie Azure Eth1 jest zarezerwowana dla ruchu sieciowego RDMA. Nie należy zmieniać ustawienia Eth1 lub wszelkie informacje zawarte w pliku konfiguracji odnoszące się do tej sieci. Eth0 jest zarezerwowana dla regularnych Azure ruchu sieciowego.

* Sieciowych RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. 


## <a name="using-hpc-pack"></a>Przy użyciu pakietu HPC
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), rozwiązania firmy Microsoft wolnego HPC klastra i zadania zarządzania, jest jedną z opcji można użyć wystąpienia obliczeniowych w systemie Linux. Najnowsze wersje Obsługa HPC Pack kilka dystrybucje systemu Linux na obliczeniowe węzłów wdrożone w maszynach wirtualnych platformy Azure, zarządzane przez system Windows Server węzła głównego. Węzły obliczeniowe z funkcją RDMA Linux uruchomiony MPI firmy Intel HPC Pack można zaplanować i uruchamiać aplikacje, które uzyskują dostęp do sieci RDMA MPI systemu Linux. Zobacz [wprowadzenie węzły obliczeniowe systemu Linux w klastrze HPC Pack na platformie Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć wdrażanie i z protokołem RDMA w systemie Linux przy użyciu obliczeniowych rozmiarów, zobacz [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.




