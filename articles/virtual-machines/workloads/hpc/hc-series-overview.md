---
title: Omówienie maszyny Wirtualnej serii HC — usłudze Azure Virtual Machines | Dokumentacja firmy Microsoft
description: Więcej informacji na temat pomocy technicznej (wersja zapoznawcza) dla rozmiaru maszyny Wirtualnej serii połączenia Hybrydowego na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: f96a1179c103dd9dfb4d358572f9a9adbe24b977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809875"
---
# <a name="hc-series-virtual-machine-overview"></a>Omówienie maszyn wirtualnych serii połączenia Hybrydowego

Maksymalizacja wydajności aplikacji HPC w procesory Intel Xeon skalowalne wymaga przydatnego podejście do procesu umieszczania w tej nowej architektury. W tym miejscu możemy konturu naszej implementacji go na maszynach wirtualnych platformy Azure serii HC aplikacji HPC. Firma Microsoft użyje termin "pNUMA" do odwoływania się do fizycznego NUMA domeny, a "vNUMA", aby odwołać się do domeny zwirtualizowanych NUMA. Podobnie będzie używany jest termin "pCore" do odwoływania się do fizycznych rdzeni Procesora i "(rdzeń wirtualny)" do odwoływania się do wirtualizacji rdzeni procesora CPU.

Fizycznie, serwer HC wynosi 2 * 24 Intel Xeon Platinum 8168 rdzeniach w sumie 48 rdzeni fizycznych. Każdy Procesor jest pNUMA jednej domeny i ma unified dostęp do sześciu kanałów DRAM. Funkcja Intel Xeon Platinum procesorów a 4 x pamięci podręcznej L2 większych niż w poprzednich pokoleń (256 KB/core-> 1 MB/core), jednocześnie zmniejszając pamięci podręcznej L3, w porównaniu do poprzednich procesorów Intel (2,5 MB/core-> 1.375 MB/core).

Topologia powyżej niesie ze sobą za pośrednictwem także konfigurację funkcji hypervisor HC serii. Aby zapewnić miejsce dla funkcja hypervisor platformy Azure do działania bez zakłócania maszyny Wirtualnej, firma Microsoft zastrzega sobie, pCores 0-1 i 24-25 (czyli najpierw 2 pCores na gniazdo każdego). Firma Microsoft następnie przypisać domen pNUMA wszystkich rdzeni pozostałych do maszyny Wirtualnej. W efekcie zostanie wyświetlony maszyny Wirtualnej:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` Liczba rdzeni na maszynie Wirtualnej

Maszyna wirtualna ma nie wie, że pCores 0-1 i 24 25 nie zostały nadane jej. W związku z tym udostępnia ona każdego vNUMA tak, jakby był natywną 22 rdzeni.

Intel Xeon Platinum, Gold i Silver procesorów również wprowadzać siatki 2D na struktury sieci komunikacji w obrębie i zewnętrzne do gniazda procesora CPU. Zdecydowanie zaleca się proces przypinania dla uzyskania optymalnej wydajności i spójności. Przypinanie procesu będą działać na maszynach wirtualnych z serii połączenia Hybrydowego, ponieważ dolina podstawowych jest widoczny jako-polega na maszynie Wirtualnej gościa. Więcej w architekturze Intel Xeon SP na: https://bit.ly/2RCYkiE

Na poniższym diagramie przedstawiono podział rdzeni zastrzeżonych dla funkcji Hypervisor Azure i maszyn wirtualnych serii połączenia Hybrydowego.

![Podział rdzeni zarezerwowana dla funkcji Hypervisor Azure i maszyn wirtualnych serii połączenia Hybrydowego](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Wymagania dotyczące sprzętu

| Wymagania dotyczące sprzętu          | Maszyn wirtualnych serii połączenia Hybrydowego                     |
|----------------------------------|----------------------------------|
| Rdzenie                            | 40 (HT wyłączone)                 |
| Procesor CPU                              | Procesor Intel Xeon Platinum 8168 *        |
| Częstotliwość procesora CPU (inne niż AVX)          | 3,7 GHz (jeden rdzeń) 3.4 2,7 GHz (wszystkich rdzeni) |
| Memory (Pamięć)                           | 8 GB/core (352 całkowita)            |
| Dysk lokalny                       | NVMe 700 GB                      |
| Infiniband                       | 100 Gb EDR Mellanox ConnectX-5 ** |
| Sieć                          | 50 Gb Ethernet (można używać 40 Gb) platformy Azure drugiej generacji SmartNIC *** |

## <a name="software-specifications"></a>Specyfikacji oprogramowania

| Specyfikacji oprogramowania     | Maszyn wirtualnych serii połączenia Hybrydowego          |
|-----------------------------|-----------------------|
| Rozmiar zadania MPI maksymalna            | 4400 rdzeni (100 zestawów skalowania maszyn wirtualnych), 8800 (200 zestawów skalowania maszyn wirtualnych) |
| Obsługa MPI                 | MVAPICH2, OpenMPI, MPICH, platforma MPI, Intel MPI  |
| Dodatkowe struktury       | Ujednolicone X komunikacji, libfabric, PGAS |
| Usługa Azure Storage — pomoc techniczna       | Standardowe i Premium (maks. 4 dyski) |
| Obsługa systemu operacyjnego dla funkcji SR-IOV w funkcji RDMA   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Pomoc techniczna platformy Azure CycleCloud    | Tak                         |
| Obsługa usługi Azure Batch         | Tak                         |

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat rozmiarów maszyny Wirtualnej HPC dla [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) i [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) na platformie Azure.

* Dowiedz się więcej o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) na platformie Azure.
