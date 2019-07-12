---
title: Rozmiary maszyn wirtualnych systemu Linux platformy Azure — HPC | Dokumentacja firmy Microsoft
description: Wyświetla listę różnych rozmiarach, dostępne dla systemu Linux o wysokiej wydajności obliczeniowej maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
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
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695598"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Wysoka wydajność obliczenia rozmiarów maszyn wirtualnych

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Funkcja SR-IOV włączone rozmiarów maszyn wirtualnych na platformie Azure Zezwalaj na prawie każdym flavor MPI, które ma być używany.
Na maszynach wirtualnych włączone bez do funkcji SR-IOV obsługiwane są tylko wersje 5.x Intel MPI. Nowsze wersje (2017, 2018 r.) środowiska uruchomieniowego Intel MPI biblioteki może być lub może nie być zgodny ze sterownikami RDMA systemu Linux platformy Azure.


### <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
 
W portalu Azure Marketplace zawiera wiele dystrybucji systemu Linux, które obsługują łączności funkcji RDMA:
  
* **HPC opartych na systemie centOS** — bez do funkcji SR-IOV jest włączona w przypadku maszyn wirtualnych opartych na systemie CentOS wersji 6.5 obliczenia HPC lub jego nowsza wersja maksymalnie 7.5 są odpowiednie. W przypadku maszyn wirtualnych serii H są zalecane w wersji 7.1 lub 7.5. Sterowniki RDMA i Intel MPI 5.1 są zainstalowane na maszynie Wirtualnej.
  W przypadku maszyn wirtualnych funkcji SR-IOV 7.6 CentOS HPC pochodzi zoptymalizowane i wstępnie załadowane sterowniki RDMA i zainstalowane różne pakiety MPI.
  Innych obrazów systemu RHEL/CentOS maszyny Wirtualnej Dodaj rozszerzenie InfiniBandLinux umożliwiające InfiniBand. To rozszerzenie maszyny Wirtualnej systemu Linux instaluje sterowniki Mellanox OFED (na maszynach wirtualnych z funkcji SR-IOV) dla łączności RDMA. Następujące polecenie cmdlet programu PowerShell zainstalowanie najnowszej wersji (w wersji 1.0) rozszerzenia InfiniBandDriverLinux istniejącej maszyny wirtualnej funkcją RDMA. Obsługa funkcji maszyna wirtualna ma nazwę *myVM* i jest wdrażana w grupie zasobów o nazwie *myResourceGroup* w *zachodnie stany USA* regionu, w następujący sposób:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternatywnie rozszerzenia maszyn wirtualnych może obejmować w szablonach usługi Azure Resource Manager w celu łatwiejszego wdrażania przy użyciu następującego elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Poniższe polecenie instaluje najnowsze rozszerzenia InfiniBandDriverLinux w wersji 1.0 na wszystkich maszynach wirtualnych z funkcją RDMA w istniejącej maszyny Wirtualnej zestawu skalowania o nazwie *myVMSS* wdrożone w grupie zasobów o nazwie *myResourceGroup*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > W obrazach opartych na systemie CentOS HPC aktualizacji jądra są wyłączone w **yum** pliku konfiguracji. Jest to spowodowane sterowniki RDMA systemu Linux są dystrybuowane jako pakiet RPM i aktualizacje sterowników może nie działać, jeśli jest aktualizowana w jądrze.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 dla HPC, SLES 12 z dodatkiem SP3 dla HPC (Premium), SLES 12 z dodatkiem SP3 dla HPC, SLES 12 z dodatkiem SP1 z dodatkiem SP1 dla HPC (Premium), SLES 12 z dodatkiem SP4 i SLES 15. RDMA sterowniki są zainstalowane i Intel MPI pakiety są dystrybuowane na maszynie Wirtualnej. Zainstaluj MPI, uruchamiając następujące polecenie:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** — Ubuntu Server 16.04 LTS, 18.04 LTS. Konfigurowanie sterowników RDMA na maszynie Wirtualnej i zarejestrowanie firmy Intel, Intel MPI pobierania:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Aby uzyskać więcej informacji na temat włączania InfiniBand, konfigurowanie MPI, zobacz [Włącz InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

System Azure oferuje kilka opcji tworzenia klastrów HPC maszyn wirtualnych systemu Linux, który może komunikować się za pośrednictwem sieci RDMA, w tym: 

* **Maszyny wirtualne** — wdrażanie maszyn wirtualnych z funkcją RDMA HPC w ten sam zestaw dostępności (podczas użycie modelu wdrażania usługi Azure Resource Manager). Jeśli używasz klasycznego modelu wdrażania, należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

* **Zestawy skalowania maszyn wirtualnych** — zestaw skalowania maszyn wirtualnych zestawu, upewnij się, ograniczenie wdrożenia do pojedynczej grupy umieszczania. Na przykład w szablonie usługi Resource Manager, należy ustawić `singlePlacementGroup` właściwość `true`. 

* **MPI między maszynami wirtualnymi** — w przypadku komunikacji MPI w razie potrzeby między maszyny wirtualne (VM), upewnij się, że maszyny wirtualne są w tym samym zestawie dostępności lub maszynę wirtualną, takie same zestawu skalowania.

* **Azure CycleCloud** — Tworzenie klastra HPC w [Azure CycleCloud](/azure/cyclecloud/) do uruchamiania zadań MPI w węzłach systemu Linux.

* **Usługa Azure Batch** — tworzenie [usługi Azure Batch](/azure/batch/) węzłów obliczeniowych w puli w celu uruchamiania obciążeń MPI w systemie Linux. Aby uzyskać więcej informacji, zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](../../batch/batch-pool-compute-intensive-sizes.md). Zobacz też [usługa Batch Shipyard](https://github.com/Azure/batch-shipyard) projektu do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

* **Pakiet Microsoft HPC Pack** - [pakietu HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) obsługuje wdrożonych na maszynach wirtualnych Azure funkcją RDMA, węzłów obliczeniowych kilku dystrybucje systemu Linux do uruchamiania na zarządzanych przez węzłem systemu Windows Server. Przykład wdrożenia, zobacz [Utwórz pakiet systemu Linux RDMA klastra HPC na platformie Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Zagadnienia dotyczące sieci
* W innych niż-funkcja SR-IOV obsługą funkcji RDMA maszyn wirtualnych systemu Linux na platformie Azure, eth1 jest zarezerwowana dla ruchu sieciowego RDMA. Nie zmieniaj żadnych ustawień eth1 lub wszelkie informacje zawarte w pliku konfiguracji odnoszące się do tej sieci.
* W funkcji SR-IOV włączonych maszyn wirtualnych (HB i serii HC), ib0 jest zarezerwowany dla ruchu sieciowego RDMA.
* Sieć RDMA na platformie Azure rezerwuje 172.16.0.0/16 przestrzeni adresowej. Do uruchamiania aplikacji MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, przestrzeń adresową sieci wirtualnej nie nakłada sieci RDMA.
* W zależności od wybranego narzędzia do zarządzania klastrem dodatkowy system konfiguracji mogą być potrzebne do uruchamiania zadań MPI. Na przykład w klastrze maszyn wirtualnych, konieczne może być ustanowienie relacji zaufania między węzłami klastra, przez generowanie kluczy SSH, albo poprzez utworzenie bez hasła logowania do protokołu SSH.


## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](../windows/sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat sposobu instalacji, optymalizacji i skalowania [obciążeń HPC](../workloads/hpc/configure.md) na platformie Azure.
- Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.
