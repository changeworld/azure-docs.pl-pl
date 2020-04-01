---
title: Rozmiary maszyn wirtualnych platformy Azure — HPC | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów dostępnych dla maszyn wirtualnych do obliczeń o wysokiej wydajności na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: amverma
ms.reviewer: jonbeck
ms.openlocfilehash: df22c857571e51bb886ff1d25db185a306999540
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420871"
---
# <a name="high-performance-computing-vm-sizes"></a>Rozmiary maszyn wirtualnych o wysokiej wydajności

Maszyny wirtualne platformy Azure serii H (VM) zostały zaprojektowane z myślą o zapewnieniu wydajności klasy liderów, skalowalności MPI i wydajności kosztowej dla różnych rzeczywistych obciążeń HPC.

[Seria HBv2](hbv2-series.md) Maszyny wirtualne są wyposażone w 200 Gb/s Mellanox HDR InfiniBand, podczas gdy zarówno maszyny wirtualne z serii HB, jak i HC są wyposażone w 100 Gb/s Mellanox EDR InfiniBand. Każdy z tych typów maszyn wirtualnych są połączone w drzewie tłuszczu bez blokowania dla zoptymalizowanej i spójnej wydajności RDMA. Maszyny wirtualne HBv2 obsługują routing adaptacyjny i dynamiczny transport połączony (DCT, dodatkowo w przypadku standardowych transportów RC i UD). Te funkcje zwiększają wydajność aplikacji, skalowalność i spójność, a ich użycie jest zdecydowanie zalecane.

[Seria HB](hb-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, jawna analiza elementów skończonych i modelowanie pogody. Maszyny wirtualne HB są wyposażone w 60 rdzeni procesorów AMD EPYC 7551, 4 GB pamięci RAM na rdzeń procesora i brak hiperwątkowości. Platforma AMD EPYC zapewnia ponad 260 GB/s przepustowości pamięci.

[Seria HC](hc-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na gęstych obliczeniach, takich jak niejawna analiza elementów skończonych, dynamika molekularna i chemia obliczeniowa. Maszyny wirtualne HC są wyposażone w 44 rdzenie procesora Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora i brak hiperwątkowości. Platforma Intel Xeon Platinum obsługuje bogaty ekosystem narzędzi programowych firmy Intel, takich jak biblioteka jądra matematycznego Intel.

[Seria H](h-series.md) Maszyny wirtualne są zoptymalizowane pod kątem aplikacji opartych na wysokich częstotliwościach procesora lub dużej pamięci na podstawowe wymagania. Maszyny wirtualne serii H są wyposażone w 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, 7 lub 14 GB pamięci RAM na rdzeń procesora i bez hiperwątkowości. Seria H jest wyposażona w 56 Gb/s Mellanox FDR InfiniBand w konfiguracji drzewa tłuszczu bez blokowania, co zapewnia stałą wydajność RDMA. Maszyny wirtualne z serii H obsługują procesory Intel MPI 5.x i MS-MPI.

> [!NOTE]
> Maszyny wirtualne A8 – A11 są planowane na emeryturę w dniu 3/2021. Aby uzyskać więcej informacji, zobacz [Przewodnik po migracji HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>Wystąpienia z obsługą technologii RDMA

Większość rozmiarów maszyn wirtualnych HPC (HBv2, HB, HC, H16r, H16mr, A8 i A9) jest wyposażona w interfejs sieciowy do łączności RDMA (Remote Direct Memory Access). Wybrane [seria N]https://docs.microsoft.com/azure/virtual-machines/nc-series) ( rozmiary oznaczone jako "r", takie jak konfiguracje NC24rs (NC24rs_v3, NC24rs_v2 i NC24r) są również w stanie RDMA. Ten interfejs jest dodatkiem do standardowego interfejsu sieciowego platformy Azure dostępnego w innych rozmiarach maszyn wirtualnych.

Ten interfejs umożliwia wystąpieniom obsługującym rdma komunikowanie się za pośrednictwem sieci InfiniBand (IB), działającej z szybkością HDR dla HBv2, szybkości EDR dla hb, HC, FDR dla maszyn wirtualnych H16r, H16mr i RDMA obsługujących n serii oraz stawek QDR dla maszyn wirtualnych A8 i A9. Te funkcje RDMA mogą zwiększyć skalowalność i wydajność niektórych aplikacji interfejsu MPI (Message Passing Interface). Aby uzyskać więcej informacji na temat szybkości, zobacz szczegóły w tabelach na tej stronie.

> [!NOTE]
> W usłudze Azure HPC istnieją dwie klasy maszyn wirtualnych w zależności od tego, czy są one włączone SR-IOV dla infiniband. Obecnie SR-IOV dla infiniband włączone maszyny wirtualne są: HBv2, HB, HC i NCv3. Pozostałe maszyny wirtualne z włączoną funkcją InfiniBand nie są włączone SR-IOV.
> RDMA przez IB jest obsługiwana dla wszystkich maszyn wirtualnych obsługujących rdma.
> Protokół IP za IB jest obsługiwany tylko na maszynach wirtualnych obsługujących technologię SR-IOV.

- **System operacyjny** - Linux jest bardzo dobrze obsługiwany dla maszyn wirtualnych HPC, distros, takich jak CentOS, RHEL, Ubuntu, SUSE są wspólne. Jeśli chodzi o obsługę systemu Windows, system Windows Server 2016 jest obsługiwany na wszystkich maszynach wirtualnych z serii HPC. Windows Server 2012 R2, Windows Server 2012 są również obsługiwane na maszynach wirtualnych z włączoną funkcją SR-IOV.

- **MPI** — rozmiary maszyn wirtualnych SR-IOV włączone na platformie Azure (HBv2, HB, HC, NCv3) umożliwiają użycie niemal dowolnego smaku mpi z mellanox OFED.
Na maszynach wirtualnych z włączoną funkcją SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikowania się między maszynami wirtualnymi. W związku z tym obsługiwane są tylko wersje Microsoft MPI (MS-MPI) 2012 R2 lub nowsze oraz wersje Intel MPI 5.x. Nowsze wersje (2017, 2018) biblioteki wykonawczej Intel MPI mogą być zgodne ze sterownikami RDMA platformy Azure lub nie.

- **InfiniBandDriver<Linux| Rozszerzenie maszyn wirtualnych systemu Windows>** — na maszynach wirtualnych obsługujących usługę RDMA dodaj infiniBandDriver<Linux| Windows> rozszerzenie, aby włączyć infiniband. W systemie Linux rozszerzenie InfiniBandDriverLinux VM instaluje sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) w celu łączności RDMA. W systemie Windows rozszerzenie maszyny Wirtualnej InfiniBandDriverWindows instaluje sterowniki Windows Network Direct (na maszynach wirtualnych innych niż SR-IOV) lub sterowniki Mellanox OFED (na maszynach wirtualnych SR-IOV) dla łączności RDMA.
W niektórych wdrożeniach wystąpień A8 i A9 rozszerzenie HpcVmDrivers jest dodawane automatycznie. Należy zauważyć, że rozszerzenie HpcVmDrivers VM jest przestarzałe; nie będzie aktualizowana.
Aby dodać rozszerzenie maszyny Wirtualnej do maszyny Wirtualnej, można użyć poleceń cmdlet [programu Azure PowerShell.](/powershell/azure/overview) 

  Następujące polecenie instaluje najnowszą wersję 1.0 InfiniBandDriverWindows rozszerzenie na istniejącej maszynie wirtualnej obsługującej rdma o nazwie *myVM* wdrożonej w grupie zasobów o nazwie *myResourceGroup* w regionie *Zachodnie stany USA:*

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  ```

  Alternatywnie rozszerzenia maszyn wirtualnych mogą być zawarte w szablonach usługi Azure Resource Manager w celu łatwego wdrożenia, z następującym elementem JSON:

  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverWindows",
  "typeHandlerVersion": "1.0",
  } 
  ```

  Następujące polecenie instaluje najnowszą wersję 1.0 Rozszerzenie InfiniBandDriverWindows na wszystkich maszynach wirtualnych obsługujących usługę RDMA w istniejącym zestawie skalowania maszyny wirtualnej o nazwie *myVMSS* wdrożonym w grupie zasobów o nazwie *myResourceGroup*:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```

  Aby uzyskać więcej informacji, zobacz [Rozszerzenia i funkcje maszyny wirtualnej](./extensions/overview.md). Można również pracować z rozszerzeniami dla maszyn wirtualnych wdrożonych w [klasycznym modelu wdrażania](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic).

- **Przestrzeń adresowa sieci RDMA** — sieć RDMA na platformie Azure rezerwuje przestrzeń adresową 172.16.0.0/16. Aby uruchomić aplikacje MPI w wystąpieniach wdrożonych w sieci wirtualnej platformy Azure, upewnij się, że przestrzeń adresowa sieci wirtualnej nie nakłada się na sieć RDMA.

## <a name="cluster-configuration-options"></a>Opcje konfiguracji klastra

Platforma Azure udostępnia kilka opcji tworzenia klastrów maszyn wirtualnych HPC systemu Windows, które mogą komunikować się za pomocą sieci RDMA, w tym: 

- **Maszyny wirtualne** — wdrażanie maszyn wirtualnych HPC obsługujących technologię RDMA w tym samym zestawie skalowania lub dostępności (podczas korzystania z modelu wdrażania usługi Azure Resource Manager). Jeśli używasz klasycznego modelu wdrażania, wdrożyć maszyny wirtualne w tej samej usłudze w chmurze.

- **Zestawy skalowania maszyny wirtualnej** — w zestawie skalowania maszyny wirtualnej (VMSS) upewnij się, że ograniczenie wdrożenia do jednej grupy miejsc docelowych. Na przykład w szablonie Menedżera `singlePlacementGroup` zasobów `true`ustaw właściwość na . Należy zauważyć, że maksymalny rozmiar VMSS, który można obrócić z `singlePlacementGroup` właściwości jest ograniczona do `true` 100 maszyn wirtualnych domyślnie. Jeśli twoje potrzeby w zakresie skali zadań HPC są wyższe niż 100 maszyn wirtualnych w jednej dzierżawie maszyn wirtualnych, możesz zażądać zwiększenia, [otwórz żądanie obsługi klienta online](../azure-supportability/how-to-create-azure-support-request.md) bez żadnych opłat.

- **MPI wśród maszyn wirtualnych** — jeśli RDMA (np. przy użyciu komunikacji MPI) jest wymagane między maszynami wirtualnymi (maszyny wirtualne), upewnij się, że maszyny wirtualne znajdują się w tym samym zestawie skalowania maszyny wirtualnej lub dostępności.

- **Azure CycleCloud** — tworzenie klastra HPC w [usłudze Azure CycleCloud](/azure/cyclecloud/) do uruchamiania zadań MPI.

- **Usługa Azure Batch** — tworzenie puli [partii azure](/azure/batch/) do uruchamiania obciążeń MPI. Aby używać wystąpień intensywnie korzystających z mocy obliczeniowej podczas uruchamiania aplikacji MPI z usługą Azure Batch, zobacz [Uruchamianie aplikacji interfejsu MPI (Message Passing Interface) w usłudze Azure Batch za pomocą zadań wielu wystąpień.](../batch/batch-mpi.md)

- **Pakiet HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) firmy Microsoft zawiera środowisko wykonawcze dla systemu MS-MPI, które używa sieci Azure RDMA po wdrożeniu na maszynach wirtualnych z systemem Linux obsługujących usługę RDMA. Na przykład wdrożenia, zobacz [Konfigurowanie klastra RDMA systemu Linux z pakietem HPC Pack w celu uruchamiania aplikacji MPI](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

- **Subskrypcja platformy Azure** — aby wdrożyć więcej niż kilka wystąpień intensywnie korzystających z mocy obliczeniowej, należy wziąć pod uwagę subskrypcję płatności zgodnie z rzeczywistym użyciem lub inne opcje zakupu. Jeśli używasz [bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/), możesz użyć ograniczonej liczby rdzeni obliczeniowych platformy Azure.

- **Ceny i dostępność** — te rozmiary maszyn wirtualnych są oferowane tylko w warstwie cenowej Standardowa. Sprawdź [dostępność produktów dostępnych w pozycjonach](https://azure.microsoft.com/global-infrastructure/services/) w regionach platformy Azure.

- **Przydział rdzeni** — może być konieczne zwiększenie przydziału rdzeni w subskrypcji platformy Azure z wartości domyślnej. Subskrypcja może również ograniczyć liczbę rdzeni, które można wdrożyć w niektórych rodzinach rozmiarów maszyn wirtualnych, w tym w serii H. Aby zażądać zwiększenia przydziału, [otwórz bezpłatne żądanie obsługi klienta online.](../azure-supportability/how-to-create-azure-support-request.md) (Domyślne limity mogą się różnić w zależności od kategorii subskrypcji).

  > [!NOTE]
  > Jeśli masz potrzeby dotyczące pojemności na dużą skalę, skontaktuj się z pomocą techniczną platformy Azure. Przydziały platformy Azure są limitami kredytowymi, a nie gwarancjami pojemności. Niezależnie od przydziału opłaty są naliczane tylko za używane rdzenie.
  
- **Sieć wirtualna** — [sieć wirtualna](https://azure.microsoft.com/documentation/services/virtual-network/) platformy Azure nie jest wymagana do używania wystąpień intensywnie korzystających z obliczeń. Jednak w przypadku wielu wdrożeń potrzebujesz co najmniej chmurowej sieci wirtualnej platformy Azure lub połączenia lokacja lokacja lokacja, jeśli potrzebujesz dostępu do zasobów lokalnych. W razie potrzeby utwórz nową sieć wirtualną, aby wdrożyć wystąpienia. Dodawanie maszyn wirtualnych intensywnie korzystających z mocy obliczeniowej do sieci wirtualnej w grupie koligacji nie jest obsługiwane.

- **Zmiana rozmiaru** — ze względu na ich specjalistyczny sprzęt można zmienić rozmiar wystąpień intensywnie korzystających z obliczeń w tej samej rodzinie rozmiarów (seria H lub seria A intensywnie korzystająca z obliczeń). Na przykład można zmienić rozmiar maszyny Wirtualnej serii H tylko z jednego rozmiaru serii H do drugiego. Ponadto zmiana rozmiaru z rozmiaru nieobjętego obliczeń na rozmiar intensywnie korzystający z obliczeń nie jest obsługiwana.  


## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o optymalizacji aplikacji HPC na platformę Azure i kilku przykładach na stronie [HPC Workloads] (https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/overview) 

- Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
