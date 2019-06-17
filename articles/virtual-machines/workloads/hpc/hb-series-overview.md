---
title: Omówienie maszyn wirtualnych serii HB — usłudze Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o obsłudze wersji zapoznawczej dla maszyn wirtualnych serii HB rozmiaru na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: bf143aa316a3d373a6303865cdc39ee0aaf27d87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809920"
---
# <a name="hb-series-virtual-machines-overview"></a>Omówienie maszyn wirtualnych z serii HB

Maksymalizacja wydajności aplikacji o wysokiej wydajności obliczeń (HPC) AMD EPYC wymaga umieszczenia odpowiedniego podejścia pamięci miejscowość i procesów. Poniżej opisano firma Microsoft architektury AMD EPYC i naszej implementacji go na platformie Azure dla aplikacji HPC. Firma Microsoft użyje termin "pNUMA" do odwoływania się do fizycznego NUMA domeny, a "vNUMA", aby odwołać się do domeny zwirtualizowanych NUMA.

Fizycznie, seria HB wynosi 2 * 32 EPYC 7551 rdzeniach w sumie 64 rdzeni fizycznych. Te 64 rdzeni są podzielone na 16 domen pNUMA (8 na gniazdo), z których każdy jest cztery rdzenie i znane jako "Procesora złożona" (lub "CCX"). Każdy CCX ma swój własny cache L3, czyli o tym, jak system operacyjny zostanie wyświetlony granic pNUMA/vNUMA. Dwa sąsiadujące CCXs udostępnia dostęp do dwóch kanałów DRAM fizycznych (32 GB pamięci DRAM na serwerach HB serii).

Aby zapewnić miejsce dla funkcja hypervisor platformy Azure do działania bez zakłócania maszyny Wirtualnej, firma Microsoft zastrzega sobie pNUMA fizyczne domeny 0 (CCX pierwszy). Firma Microsoft następnie przypisać domen pNUMA 1 – 15 (pozostałe jednostki CCX) dla maszyny Wirtualnej. Maszyna wirtualna zostanie wyświetlony:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` Liczba rdzeni na maszynie Wirtualnej

Maszyny Wirtualnej, nie wie, że pNUMA 0 nie została podana do niego. Maszyna wirtualna rozumie pNUMA 1 – 15 jako vNUMA 0-14, za pomocą 7 vNUMA na vNUMA vSocket od 0 do 8 na vSocket 1. To asymetryczne, system operacyjny powinien rozruchu i działa normalnie. W dalszej części tego przewodnika firma Microsoft wydać polecenie najlepszy sposób do uruchamiania aplikacji MPI w tym asymetrycznego układzie NUMA.

Przypinanie procesu będą działać na maszyny wirtualne z serii HB, ponieważ uwidaczniamy bazowego dolina jako-polega na maszynie Wirtualnej gościa. Zdecydowanie zaleca się proces przypinania dla uzyskania optymalnej wydajności i spójności.

Zobacz więcej informacji w [architektury AMD EPYC](https://bit.ly/2Epv3kC) i [mikroukładu wielu architektur](https://bit.ly/2GpQIMb) w serwisie LinkedIn. Aby uzyskać więcej informacji, zobacz [przewodnika dostrajania HPC dla procesorów EPYC AMD](https://bit.ly/2T3AWZ9).

Na poniższym diagramie przedstawiono podział rdzeni zastrzeżonych dla funkcji Hypervisor Azure i maszyn wirtualnych serii HB.

![Podział zarezerwowana dla funkcji Hypervisor Azure i maszyn wirtualnych serii HB rdzeni](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Wymagania dotyczące sprzętu

| Specyfikacje sprzętu                | Maszyn wirtualnych serii HB                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 60 (SMT wyłączone)                |
| Procesor CPU                              | AMD EPYC 7551*                   |
| Częstotliwość procesora CPU (inne niż AVX)          | ~2.55 GHz (pojedynczego + wszystkich rdzeni)   |
| Memory (Pamięć)                           | 4 GB/core (240 całkowita)            |
| Dysk lokalny                       | NVMe 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Sieć                          | 50 Gb Ethernet (można używać 40 Gb) platformy Azure drugiej generacji SmartNIC *** |

## <a name="software-specifications"></a>Specyfikacji oprogramowania

| Specyfikacje SW           |Maszyn wirtualnych serii HB           |
|-----------------------------|-----------------------|
| Rozmiar zadania MPI maksymalna            | 6000 rdzeni (100 zestawów skalowania maszyn wirtualnych) 12000 rdzeni (200 zestawów skalowania maszyn wirtualnych)  |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, platforma MPI, Intel MPI  |
| Dodatkowe struktury       | Ujednolicone X komunikacji, libfabric, PGAS |
| Usługa Azure Storage — pomoc techniczna       | Standardowe i Premium (maks. 4 dyski) |
| Obsługa systemu operacyjnego dla funkcji SR-IOV w funkcji RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Pomoc techniczna platformy Azure CycleCloud    | Tak                         |
| Obsługa usługi Azure Batch         | Tak                         |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat rozmiarów maszyny Wirtualnej HPC dla [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
