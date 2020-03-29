---
title: Omówienie maszyny wirtualnej z serii HB — maszyny wirtualne platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obsłudze w wersji zapoznawczej dla rozmiaru maszyny wirtualnej z serii HB na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707771"
---
# <a name="hb-series-virtual-machines-overview"></a>Omówienie maszyn wirtualnych serii HB

Maksymalizacja wydajności aplikacji hpc (high performance compute) w procesorze AMD EPYC wymaga przemyślanego podejścia do lokalizacji pamięci i rozmieszczenia procesów. Poniżej przedstawiamy architekturę AMD EPYC i naszą implementację na platformie Azure dla aplikacji HPC. Użyjemy terminu "pNUMA" w odniesieniu do fizycznej domeny NUMA, a "vNUMA" w odniesieniu do zwirtualizowanej domeny NUMA.

Fizycznie seria HB to 2 * 32-rdzeniowe procesory EPYC 7551 dla łącznie 64 rdzeni fizycznych. Te 64 rdzenie są podzielone na 16 domen pNUMA (8 na gniazdo), z których każdy jest cztery rdzenie i znany jako "CPU Complex" (lub "CCX"). Każdy CCX ma własną pamięć podręczną L3, czyli jak system operacyjny zobaczy granicę pNUMA/vNUMA. Para sąsiadujących CCXs współudzieli dostęp do dwóch kanałów fizycznej pamięci DRAM (32 GB pamięci DRAM na serwerach z serii HB).

Aby zapewnić miejsce dla hypervisor platformy Azure do pracy bez zakłócania maszyny Wirtualnej, możemy zarezerwować fizyczną domenę pNUMA 0 (pierwszy CCX). Następnie przypisujemy domeny pNUMA 1-15 (pozostałe jednostki CCX) dla maszyny Wirtualnej. Na maszynie wirtualnej zobaczymy:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`rdzenie na maszynę wirtualną

Sama maszyna wirtualna nie wie, że pNUMA 0 nie została mu przekazana. Maszyna wirtualna rozumie pNUMA 1-15 jako vNUMA 0-14, z 7 vNUMA na vSocket 0 i 8 vNUMA na vSocket 1. Chociaż jest to asymetryczne, system operacyjny powinien uruchomić się i działać normalnie. W dalszej części tego przewodnika poinstruujemy, jak najlepiej uruchamiać aplikacje MPI w tym asymetrycznym układzie NUMA.

Przypinanie procesu będzie działać na maszynach wirtualnych z serii HB, ponieważ udostępniamy podstawową silikon jako — jest dla maszyny wirtualnej gościa. Zdecydowanie zalecamy przypinanie procesu w celu uzyskania optymalnej wydajności i spójności.

Zobacz więcej na [temat architektury AMD EPYC](https://bit.ly/2Epv3kC) i [architektury wieloukładowej](https://bit.ly/2GpQIMb) na LinkedIn. Aby uzyskać bardziej szczegółowe informacje, zobacz [Przewodnik hpc tuning dla procesorów AMD EPYC](https://bit.ly/2T3AWZ9).

Na poniższym diagramie przedstawiono segregację rdzeni zarezerwowanych dla funkcji Hypervisor platformy Azure i maszyny wirtualnej z serii HB.

![Segregacja rdzeni zarezerwowanych dla platformy Azure Hypervisor i maszyny wirtualnej z serii HB](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Specyfikacja HW                | Maszyna wirtualna serii HB                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 60 (wyłączono SMT)                |
| Procesor CPU                              | AMD EPYC 7551*                   |
| Częstotliwość procesora (nie-AVX)          | ~2,55 GHz (pojedyncze + wszystkie rdzenie)   |
| Memory (Pamięć)                           | 4 GB/r (łącznie 240)            |
| Dysk lokalny                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5** |
| Network (Sieć)                          | 50 Gb Ethernet (40 Gb do użytecznego) Azure drugi Gen SmartNIC *** |

## <a name="software-specifications"></a>Specyfikacje oprogramowania

| Specyfikacja SW           |Maszyna wirtualna serii HB           |
|-----------------------------|-----------------------|
| Maksymalny rozmiar zadania MPI            | 6000 rdzeni (100 zestawów skalowania maszyn wirtualnych) 12000 rdzeni (200 zestawów skalowania maszyn wirtualnych)  |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, Platforma MPI, Intel MPI  |
| Dodatkowe ramy       | Ujednolicona komunikacja X, libfabric, PGAS |
| Pomoc techniczna usługi Azure Storage       | Std + Premium (maksymalnie 4 dyski) |
| Obsługa systemu operacyjnego dla SRIOV RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Pomoc techniczna usługi Azure CycleCloud    | Tak                         |
| Pomoc techniczna wsadowa platformy Azure         | Tak                         |

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o rozmiarach maszyn wirtualnych HPC dla [systemów Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC na](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) platformie Azure.
