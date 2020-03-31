---
title: Wybieranie rozmiarów maszyn wirtualnych dla pul — usługa Azure Batch | Dokumenty firmy Microsoft
description: Jak wybrać jedną z dostępnych rozmiarów maszyn wirtualnych dla węzłów obliczeniowych w pulach usługi Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087051"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybieranie rozmiaru maszyny Wirtualnej dla węzłów obliczeniowych w puli usługi Azure Batch

Po wybraniu rozmiaru węzła dla puli usługi Azure Batch, można wybrać spośród prawie wszystkich rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje szereg rozmiarów dla maszyn wirtualnych z systemem Linux i Windows dla różnych obciążeń.

Istnieje kilka wyjątków i ograniczeń wyboru rozmiaru maszyny Wirtualnej:

* Niektóre rozmiary maszyn wirtualnych lub maszyn wirtualnych nie są obsługiwane w uparcie.
* Niektóre rozmiary maszyn wirtualnych są ograniczone i muszą być specjalnie włączone, zanim będą mogły zostać przydzielone.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie i rozmiary maszyn wirtualnych

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule wsadowe w konfiguracji maszyny wirtualnej obsługują prawie wszystkie rozmiary maszyn wirtualnych[(Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Zobacz poniższą tabelę, aby dowiedzieć się więcej o obsługiwanych rozmiarach i ograniczeniach.

| Serie maszyn wirtualnych  | Obsługiwane rozmiary |
|------------|---------|
| Podstawowa A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (A0) |
| A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 |
| Okręg wyborczy Av2 | Wszystkie rozmiary |
| B | Brak |
| DC | Brak |
| Dv2, DSv2 | Wszystkie rozmiary |
| Dv3, Dsv3 | Wszystkie rozmiary |
| Dav4, Dasv4 | Brak - jeszcze niedostępny |
| Ev3, Esv3 | Wszystkie rozmiary, z wyjątkiem E64is_v3 i E64i_v3 |
| Eav4, Easv4 | Brak - jeszcze niedostępny |
| F, Fs | Wszystkie rozmiary |
| Fsv2 (właśc. | Wszystkie rozmiary |
| G, G | Wszystkie rozmiary |
| H | Wszystkie rozmiary |
| HB<sup>1</sup> | Wszystkie rozmiary |
| HBv2<sup>1</sup> | Wszystkie rozmiary |
| Hc<sup>1</sup> | Wszystkie rozmiary |
| Ls | Wszystkie rozmiary |
| Lsv2 ( Lsv2 ) | Brak - jeszcze niedostępny |
| M<sup>1</sup> | Wszystkie rozmiary, z wyjątkiem M64, M64m, M128, M128m |
| Mv2 (właśc. | Brak - jeszcze niedostępny |
| NC | Wszystkie rozmiary |
| NCv2<sup>1</sup> | Wszystkie rozmiary |
| NCv3<sup>1</sup> | Wszystkie rozmiary |
| ND<sup>1</sup> | Wszystkie rozmiary |
| NDv2<sup>1</sup> | Brak - jeszcze niedostępny |
| NV | Wszystkie rozmiary |
| NVv3<sup>1</sup> | Wszystkie rozmiary |
| NVv4 (własnach nvv | Brak |
| SAP HANA | Brak |

<sup>1</sup> Te rozmiary maszyn wirtualnych można przydzielić w pulach wsadowych w konfiguracji maszyny wirtualnej, ale należy utworzyć nowe konto usługi Batch i zażądać określonego [zwiększenia przydziału](batch-quota-limit.md#increase-a-quota). To ograniczenie zostanie usunięte, gdy przydział vCPU na serię maszyn wirtualnych jest w pełni obsługiwany dla kont usługi Batch.

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla usług w chmurze,](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących pul:

| Serie maszyn wirtualnych  | Nieobsługiwały rozmiary |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Istotne zagadnienia dotyczące rozmiaru

* **Wymagania aplikacji** — należy wziąć pod uwagę właściwości i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z wieloma wystąpieniami lub aplikacji CUDA należy wziąć pod uwagę odpowiednio specjalistyczne rozmiary maszyn wirtualnych [hpc](../virtual-machines/linux/sizes-hpc.md) lub [GPU.](../virtual-machines/linux/sizes-gpu.md) (Zobacz [Użyj wystąpień obsługujących technologię RDMA lub GPU w pulach wsadowych).](batch-pool-compute-intensive-sizes.md)

* **Zadania na węzeł** — typowe jest wybranie rozmiaru węzła przy założeniu, że jedno zadanie jest uruchamiane w węźle naraz. Jednak może być korzystne, aby mieć wiele zadań (i dlatego wiele wystąpień aplikacji) [uruchamiane równolegle w](batch-parallel-node-tasks.md) węzłach obliczeniowych podczas wykonywania zadania. W takim przypadku jest wspólne, aby wybrać rozmiar węzła wielordzeniowego, aby pomieścić zwiększone zapotrzebowanie na równoległe wykonanie zadania.

* **Poziomy obciążenia dla różnych zadań** — wszystkie węzły w puli mają ten sam rozmiar. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność regionu** — seria lub rozmiar maszyny Wirtualnej może nie być dostępna w regionach, w których utworzono konta usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [Produkty dostępne według regionu](https://azure.microsoft.com/regions/services/).

* **Przydziały** — [przydziały rdzeni](batch-quota-limit.md#resource-quotas) na koncie usługi Batch mogą ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli partii. Aby zażądać zwiększenia przydziału, zobacz [ten artykuł](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny Wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać szczegółowy przegląd usługi Batch, zobacz [Tworzenie dużych równoległych rozwiązań obliczeniowych za pomocą usługi Batch](batch-api-basics.md).
* Aby uzyskać informacje na temat używania rozmiarów maszyn wirtualnych intensywnie korzystających z mocy obliczeniowej, zobacz [Używanie wystąpień obsługujących technologię RDMA lub GPU w pulach wsadowych](batch-pool-compute-intensive-sizes.md).
