---
title: Omówienie maszyny wirtualnej z serii HC — maszyny wirtualne platformy Azure| Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsłudze w wersji zapoznawczej dla rozmiaru maszyny wirtualnej z serii HC na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906745"
---
# <a name="hc-series-virtual-machine-overview"></a>Omówienie maszyny wirtualnej z serii HC

Maksymalizacja wydajności aplikacji HPC w przypadku procesorów Intel Xeon Scalable wymaga przemyślanego podejścia do przetwarzania rozmieszczenia tej nowej architektury. W tym miejscu przedstawiamy naszą implementację na maszynach wirtualnych z serii HC platformy Azure dla aplikacji HPC. Użyjemy terminu "pNUMA" w odniesieniu do fizycznej domeny NUMA, a "vNUMA" w odniesieniu do zwirtualizowanej domeny NUMA. Podobnie użyjemy terminu "pCore" w odniesieniu do fizycznych rdzeni procesora i "rdzeni wirtualnych", aby odnieść się do zwirtualizowanych rdzeni procesora.

Fizycznie serwer HC to 2 * 24-rdzeniowe procesory Intel Xeon Platinum 8168 dla łącznie 48 rdzeni fizycznych. Każdy procesor jest jedną domeną pNUMA i ma ujednolicony dostęp do sześciu kanałów pamięci DRAM. Procesory Intel Xeon Platinum są wyposażone w 4-krotnie większą pamięć podręczną L2 niż w poprzednich generacjach (256 KB/core -> 1 MB/core), jednocześnie zmniejszając pamięć podręczną L3 w porównaniu z wcześniejszymi procesorami Intel (2,5 MB/r-core -> 1,375 MB/r).Intel Xeon Platinum CPU.

Powyższa topologia przenosi się również do konfiguracji hipernadzorcy serii HC. Aby zapewnić miejsce dla hypervisor platformy Azure do pracy bez zakłócania maszyny Wirtualnej, możemy zarezerwować pCores 0-1 i 24-25 (czyli pierwsze 2 pCores na każdym gnieździe). Następnie przypisujemy domeny pNUMA wszystkie pozostałe rdzenie do maszyny Wirtualnej. W związku z tym maszyna wirtualna zobaczy:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`rdzenie na maszynę wirtualną

Maszyna wirtualna nie ma wiedzy, że pCores 0-1 i 24-25 nie zostały mu dane. W ten sposób eksponuje każdy vNUMA tak, jakby natywnie miał 22 rdzenie.

Procesory Intel Xeon Platinum, Gold i Silver wprowadzają również sieć mesh 2D on-die do komunikacji wewnątrz gniazda procesora i poza nim. Zdecydowanie zalecamy przypinanie procesu w celu uzyskania optymalnej wydajności i spójności. Przypinanie procesu będzie działać na maszynach wirtualnych z serii HC, ponieważ podstawowy krzem jest narażony jako — jest dla maszyny wirtualnej gościa. Aby dowiedzieć się więcej, zobacz [Architektura Intel Xeon SP](https://bit.ly/2RCYkiE).

Na poniższym diagramie przedstawiono segregację rdzeni zarezerwowanych dla funkcji Hypervisor platformy Azure i maszyny wirtualnej z serii HC.

![Segregacja rdzeni zarezerwowanych dla usługi Azure Hypervisor i maszyny wirtualnej z serii HC](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Specyfikacje sprzętu          | Maszyna wirtualna serii HC                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 44 (HT wyłączone)                 |
| Procesor CPU                              | Intel Xeon Platinum 8168*        |
| Częstotliwość procesora (nie-AVX)          | 3,7 GHz (jednordzeniowy), 2,7-3,4 GHz (wszystkie rdzenie) |
| Memory (Pamięć)                           | 8 GB/r (łącznie 352)            |
| Dysk lokalny                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Network (Sieć)                          | 50 Gb Ethernet (40 Gb do użytecznego) Azure drugi Gen SmartNIC *** |

## <a name="software-specifications"></a>Specyfikacje oprogramowania

| Specyfikacje oprogramowania     | Maszyna wirtualna serii HC          |
|-----------------------------|-----------------------|
| Maksymalny rozmiar zadania MPI            | 13200 rdzeni (300 maszyn wirtualnych w jednym VMSS z singlePlacementGroup=true) |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, Platforma MPI, Intel MPI  |
| Dodatkowe ramy       | Ujednolicona komunikacja X, libfabric, PGAS |
| Pomoc techniczna usługi Azure Storage       | Std + Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Pomoc techniczna usługi Azure CycleCloud    | Tak                         |
| Pomoc techniczna wsadowa platformy Azure         | Tak                         |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o rozmiarach maszyn wirtualnych HPC dla [systemów Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC na](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) platformie Azure.
