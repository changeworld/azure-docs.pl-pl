---
title: Wybierz rozmiary maszyn wirtualnych w przypadku pul — Azure Batch | Dokumentacja firmy Microsoft
description: Jak dokonać wyboru z dostępnych rozmiarów maszyn wirtualnych dla węzłów obliczeniowych w pulach usługi Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 033e0865f23034b94e3133e0ba5890eca4e746ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080891"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybierz rozmiar maszyny Wirtualnej dla węzłów obliczeniowych w puli usługi Azure Batch

Po wybraniu węzła rozmiaru puli usługi Azure Batch, można wybrać spośród prawie wszystkich dostępnych rozmiarów maszyn wirtualnych na platformie Azure. Platforma Azure oferuje szeroką gamę rozmiarów dla systemów Linux i Windows maszyn wirtualnych w ramach różnych obciążeń.

Istnieje kilka wyjątków i ograniczenia, wybierając rozmiar maszyny Wirtualnej:

* Niektóre serię maszyn wirtualnych lub rozmiarów maszyn wirtualnych nie są obsługiwane w usłudze Batch.
* Przykładowe rozmiary maszyn wirtualnych są ograniczone i muszą być włączone specjalnie, aby można je przypisać.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serię maszyn wirtualnych i rozmiarach

### <a name="pools-in-virtual-machine-configuration"></a>Pulach w konfiguracji maszyny wirtualnej

Pule usługi Batch w konfiguracji maszyny wirtualnej obsługuje prawie wszystkie rozmiary maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Zobacz poniższą tabelę, aby dowiedzieć się więcej na temat obsługiwanych rozmiarów i ograniczeń.

Wszelkie promocyjne lub niewymienione rozmiarów maszyn wirtualnych w wersji zapoznawczej nie są gwarantowane pomocy technicznej.

| Seria maszyn wirtualnych  | Obsługiwane rozmiary | Tryb alokacji puli konta usługi batch<sup>1</sup> |
|------------|---------|-----------------|
| Podstawowa — seria A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (A0) | Dowolne |
| Seria A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 | Dowolne |
| Seria Av2 | Wszystkie rozmiary | Dowolne |
| Seria B | Brak | Niedostępne |
| Seria DC | Brak | Niedostępne |
| Dv2, seria Dsv2 | Wszystkie rozmiary | Dowolne |
| Dv3 i Seria Dsv3 | Wszystkie rozmiary | Dowolne |
| [Rozmiarów zoptymalizowanych pod kątem pamięci](../virtual-machines/linux/sizes-memory.md) | Brak | Niedostępne |
| Seria Fsv2 | Wszystkie rozmiary | Dowolne |
| Seria H | Wszystkie rozmiary | Dowolne |
| Seria HB | Wszystkie rozmiary | Tryb subskrypcji użytkownika |
| Seria HC | Wszystkie rozmiary | Tryb subskrypcji użytkownika |
| Seria Ls | Wszystkie rozmiary | Dowolne |
| Seria Lsv2 | Brak | Niedostępne |
| Seria M | Maszyna wirtualna Standard_M64ms (o niskim priorytecie tylko), maszyna wirtualna Standard_M128s (o niskim priorytecie tylko) | Dowolne |  
| Seria NCv2<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| Seria NCv3<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| Seria ND<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| NDv2 serii | Wszystkie rozmiary | Tryb subskrypcji użytkownika |
| Seria NV | Wszystkie rozmiary | Dowolne |
| Seria NVv3 | Brak | Niedostępne |
| SAP HANA | Brak | Niedostępne |

<sup>1</sup> niektórych nowszych serię maszyn wirtualnych są obsługiwane częściowo początkowo. Te serie maszyn wirtualnych mogą zostać przydzieleni przez konta usługi Batch z **trybu alokacji puli** równa **subskrypcja użytkownika**. Zobacz [kont zarządzania usługi Batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) Aby uzyskać więcej informacji na temat konfigurowania konta usługi Batch. Zobacz [limity przydziału i limity](batch-quota-limit.md) Aby dowiedzieć się, jak utworzyć żądanie limitu przydziału dla tych częściowo obsługiwane serię maszyn wirtualnych dla **subskrypcja użytkownika** konta usługi Batch.  

<sup>2</sup> rozmiarów maszyn wirtualnych te można przydzielić w pulach usługi Batch w konfiguracji maszyny wirtualnej, ale należy zażądać określonego [zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pulach w konfiguracji usługi w chmurze

Pule usługi Batch w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** dla następujących:

| Seria maszyn wirtualnych  | Nieobsługiwany rozmiar |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, maszyna wirtualna Standard_A2_v2, maszyna wirtualna Standard_A2m_v2 |

## <a name="size-considerations"></a>Ograniczenia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. Dla wielu wystąpień [obciążeń MPI](batch-mpi.md) lub aplikacje CUDA, należy wziąć pod uwagę wyspecjalizowane [HPC](../virtual-machines/linux/sizes-hpc.md) lub [włączonymi procesorami GPU](../virtual-machines/linux/sizes-gpu.md) maszyny Wirtualne o rozmiarach, odpowiednio. (Zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).)

* **Zadań na węzeł** — jest zazwyczaj wybierz węzeł rozmiar zakładając, że jedno zadanie jest uruchamiane w węźle naraz. Jednak może być korzystne ma wiele zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku jest wspólnego, aby wybrać rozmiar węzła wyspecjalizowanymi w celu sprostania zwiększonemu zapotrzebowaniu na równoległe wykonywanie zadań podrzędnych.

* **Ładowanie poziomów dla różnych zadań** — wszystkie węzły w puli mają taki sam rozmiar. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność w poszczególnych regionach** — seria maszyn wirtualnych lub rozmiar może nie być dostępne w regionach, w którym tworzenia kont usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).

* **Przydziały** — [limitów przydziału rdzeni](batch-quota-limit.md#resource-quotas) w partii konta można ograniczyć liczbę węzłów o podanej wielkości, możesz dodać do puli usługi Batch. Aby zażądać zwiększenia limitu przydziału, zobacz [w tym artykule](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny Wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej, w porównaniu z konfiguracji usługi w chmurze.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).
* Aby dowiedzieć się, jak za pomocą rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
