---
title: Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure — HPC
description: Wyświetla listę różnych rozmiarów dostępnych w przypadku maszyn wirtualnych o wysokiej wydajności z systemem Linux na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 214ab48b6b0dca37eff3b3f155aaa92afc7fee16
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034892"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Wymiary maszyn wirtualnych o wysokiej wydajności obliczeniowej

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure zezwalają na użycie niemal wszystkich wersji MPI.
Na maszynach wirtualnych z obsługą wirtualizacji SR-IOV obsługiwane są tylko wersje Intel MPI 5. x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami RDMA systemu Linux.


### <a name="supported-os-images"></a>Obsługiwane obrazy systemu operacyjnego
 
Portal Azure Marketplace ma wiele dystrybucji systemu Linux obsługujących łączność RDMA:
  
* **CentOS HPC** — dla maszyn wirtualnych z obsługą wirtualizacji SR-IOV, CentOS w wersji 6,5 HPC lub nowszej, do 7,5 są odpowiednie. W przypadku maszyn wirtualnych z serii H zaleca się używanie wersji 7,1 do 7,5. Sterowniki RDMA i Intel MPI 5,1 są zainstalowane na maszynie wirtualnej.
  W przypadku maszyn wirtualnych SR-IOV CentOS-HPC 7,6 jest zoptymalizowane i wstępnie załadowane ze sterownikami RDMA i zainstalowanymi różnymi pakietami MPI.
  W przypadku innych obrazów maszyn wirtualnych RHEL/CentOS Dodaj rozszerzenie InfiniBandLinux, aby włączyć funkcję InfiniBand. To rozszerzenie maszyny wirtualnej z systemem Linux instaluje sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) na potrzeby łączności RDMA. Następujące polecenie cmdlet programu PowerShell instaluje najnowszą wersję (w wersji 1,0) rozszerzenia InfiniBandDriverLinux na istniejącej maszynie wirtualnej z obsługą funkcji RDMA. Maszyna wirtualna z obsługą funkcji RDMA ma nazwę *myVM* i jest wdrażana w grupie zasobów o nazwie Moja *zasobów* w regionie *zachodnie stany USA* w następujący sposób:

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  Alternatywnie rozszerzenia maszyn wirtualnych można dołączać do szablonów Azure Resource Manager, aby ułatwić wdrażanie za pomocą następującego elementu JSON:
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  Następujące polecenie instaluje najnowszą wersję 1,0 rozszerzenia InfiniBandDriverLinux na wszystkich maszynach wirtualnych z obsługą funkcji RDMA w istniejącym zestawie skalowania maszyn wirtualnych o nazwie *myVMSS* wdrożoną w grupie zasobów o nazwie Moja *zasobów*:
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > W obrazach HPC opartych na CentOS aktualizacje jądra są wyłączone w pliku konfiguracyjnym **yum** . Wynika to z faktu, że sterowniki RDMA systemu Linux są dystrybuowane jako pakiet RPM, a aktualizacje sterowników mogą nie zadziałać, jeśli jądro zostało zaktualizowane.
  >
  

* **SUSE Linux Enterprise Server** -SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15. Sterowniki RDMA są zainstalowane i pakiety Intel MPI są dystrybuowane na maszynie wirtualnej. Zainstaluj program MPI, uruchamiając następujące polecenie:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** -Ubuntu Server 16,04 LTS, 18,04 LTS. Skonfiguruj sterowniki RDMA na maszynie wirtualnej i zarejestruj się w firmie Intel, aby pobrać firmę Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  Aby uzyskać więcej informacji na temat włączania funkcji InfiniBand, konfigurowania MPI, zobacz [enable InfiniBand](../workloads/hpc/enable-infiniband.md).


### <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

System Azure oferuje kilka opcji tworzenia klastrów maszyn wirtualnych z systemem Linux HPC, które mogą komunikować się za pomocą sieci RDMA, w tym: 

* **Maszyny wirtualne** — Wdróż maszyny wirtualne z obsługą funkcji RDMA w tym samym zestawie dostępności (w przypadku korzystania z Azure Resource Manager modelu wdrażania). W przypadku korzystania z klasycznego modelu wdrażania należy wdrożyć maszyny wirtualne w tej samej usłudze w chmurze. 

* **Zestawy skalowania maszyn wirtualnych** — w zestawie skalowania maszyn wirtualnych należy ograniczyć wdrożenie do pojedynczej grupy umieszczania. Na przykład w szablonie Menedżer zasobów ustaw właściwość `singlePlacementGroup` na `true`. 

* **MPI między maszynami wirtualnymi** — jeśli jest to wymagane między maszynami wirtualnymi, upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie dostępności lub na maszynie wirtualnej tego samego zestawu skalowania.

* **Azure CycleCloud** — Tworzenie klastra HPC w [usłudze Azure CycleCloud](/azure/cyclecloud/) w celu uruchamiania zadań MPI w węzłach systemu Linux.

* **Azure Batch** — utwórz pulę [Azure Batch](/azure/batch/) do uruchamiania obciążeń MPI w węzłach obliczeniowych systemu Linux. Aby uzyskać więcej informacji, zobacz [Korzystanie z wystąpień obsługujących funkcję RDMA lub GPU w pulach usługi Batch](../../batch/batch-pool-compute-intensive-sizes.md). Zobacz również projekt [stoczni usługi Batch](https://github.com/Azure/batch-shipyard) do uruchamiania obciążeń opartych na kontenerach w usłudze Batch.

* Pakiet **Microsoft HPC pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) obsługuje kilka dystrybucji systemu Linux do uruchomienia w węzłach obliczeniowych wdrożonych na maszynach wirtualnych platformy Azure obsługujących funkcję RDMA zarządzanym przez węzeł główny systemu Windows Server. Aby zapoznać się z przykładowym wdrożeniem, zobacz [Tworzenie klastra programu HPC Pack w systemie Linux na platformie Azure](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).


### <a name="network-considerations"></a>Zagadnienia dotyczące sieci
* W przypadku maszyn wirtualnych z systemem Linux, które nie obsługują funkcji RDMA na platformie Azure, eth1 jest zarezerwowany dla ruchu sieciowego RDMA. Nie należy zmieniać żadnych ustawień eth1 ani żadnych informacji w pliku konfiguracyjnym odnoszących się do tej sieci.
* Na maszynach wirtualnych z obsługą wirtualizacji SR-IOV (HB i HC) ib0 jest zarezerwowany dla ruchu sieciowego RDMA.
* Sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchamiać aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.
* W zależności od wybranego narzędzia do zarządzania klastrami może być wymagana dodatkowa konfiguracja systemu do uruchamiania zadań MPI. Na przykład w klastrze maszyn wirtualnych może być konieczne ustanowienie relacji zaufania między węzłami klastra przez wygenerowanie kluczy SSH lub przez ustanowienie logowania za pomocą protokołu SSH.


## <a name="other-sizes"></a>Inne rozmiary
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Procesor GPU](../windows/sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat sposobu konfigurowania, optymalizowania i skalowania [obciążeń HPC](../workloads/hpc/configure.md) na platformie Azure.
- Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.
