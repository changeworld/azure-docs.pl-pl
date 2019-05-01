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
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 44b965bd60d976d4d28dc5e31d78a1c838d4ee02
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704677"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Wysoka wydajność obliczenia rozmiarów maszyn wirtualnych

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Obsługiwane są tylko wersje 5.x Intel MPI.

> [!NOTE]
> Nowsze wersje (2017, 2018 r.) środowiska uruchomieniowego Intel MPI biblioteki może być lub może nie być zgodny ze sterownikami RDMA systemu Linux platformy Azure.

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
 
### <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

System Azure oferuje kilka opcji tworzenia klastrów HPC maszyn wirtualnych systemu Linux, który może komunikować się za pośrednictwem sieci RDMA, w tym: 

* **Maszyny wirtualne** — wdrażanie maszyn wirtualnych z funkcją RDMA HPC w ten sam zestaw dostępności (podczas użycie modelu wdrażania usługi Azure Resource Manager). Jeśli używasz klasycznego modelu wdrażania, należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

* **Zestawy skalowania maszyn wirtualnych** — skalowania maszyn wirtualnych zestawu, upewnij się, ograniczenie wdrożenia do pojedynczej grupy umieszczania. Na przykład w szablonie usługi Resource Manager, należy ustawić `singlePlacementGroup` właściwość `true`. 

* **Azure CycleCloud** — Tworzenie klastra HPC w [Azure CycleCloud](/azure/cyclecloud/) do uruchamiania zadań MPI w węzłach systemu Linux.

* **Usługa Azure Batch** — tworzenie [usługi Azure Batch](/azure/batch/) węzłów obliczeniowych w puli w celu uruchamiania obciążeń MPI w systemie Linux. Aby uzyskać więcej informacji, zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](../../batch/batch-pool-compute-intensive-sizes.md). Zobacz też [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) projektu do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

* **Pakiet Microsoft HPC Pack** - [pakietu HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) obsługuje wdrożonych na maszynach wirtualnych Azure funkcją RDMA, węzłów obliczeniowych kilku dystrybucje systemu Linux do uruchamiania na zarządzanych przez węzłem systemu Windows Server. Przykład wdrożenia, zobacz [Utwórz pakiet systemu Linux RDMA klastra HPC na platformie Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

W zależności od wybranego narzędzia do zarządzania klastrem dodatkowy system konfiguracji mogą być potrzebne do uruchamiania zadań MPI. Na przykład w klastrze maszyn wirtualnych, konieczne może być ustanowienie relacji zaufania między węzłami klastra, generowanie kluczy SSH lub ustanawianie bez hasła SSH zaufania.

### <a name="network-topology-considerations"></a>Zagadnienia dotyczące topologii sieci
* W przypadku komputerów z obsługą funkcji RDMA maszyn wirtualnych systemu Linux na platformie Azure Eth1 jest zarezerwowana dla ruchu sieciowego RDMA. Nie zmieniaj żadnych ustawień Eth1 lub wszelkie informacje zawarte w pliku konfiguracji odnoszące się do tej sieci. Eth0 jest zarezerwowana do regularnego ruchu sieci platformy Azure.

* Sieć RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. 




## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](../windows/sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.




