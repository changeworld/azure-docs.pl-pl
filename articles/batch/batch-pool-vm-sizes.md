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
ms.date: 01/25/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 43094839c9da9b00c97d1dffd53f98a3acd119d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775733"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybierz rozmiar maszyny Wirtualnej dla węzłów obliczeniowych w puli usługi Azure Batch

Po wybraniu węzła rozmiaru puli usługi Azure Batch, można wybrać spośród prawie wszystkich dostępnych rozmiarów maszyn wirtualnych na platformie Azure. Platforma Azure oferuje szeroką gamę rozmiarów dla systemów Linux i Windows maszyn wirtualnych w ramach różnych obciążeń.

Istnieje kilka wyjątków i ograniczenia, wybierając rozmiar maszyny Wirtualnej:

* Niektóre rodzin maszyn wirtualnych lub rozmiarów maszyn wirtualnych nie są obsługiwane w usłudze Batch. 
* Przykładowe rozmiary maszyn wirtualnych są ograniczone i muszą być włączone specjalnie, aby można je przypisać.

## <a name="supported-vm-families-and-sizes"></a>Obsługiwanych rodzin i rozmiarów maszyn

### <a name="pools-in-virtual-machine-configuration"></a>Pulach w konfiguracji maszyny wirtualnej

Pule usługi Batch w konfiguracji maszyny wirtualnej obsługuje wszystkie rozmiary maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *z wyjątkiem* dla następujących:

| Rodzina  | Nieobsługiwany rozmiar  |
|---------|---------|
| Podstawowa — seria A | Basic_A0 (A0) |
| Seria A | Standardowa_A0 |
| Seria B | Wszyscy |
| Seria DC | Wszyscy |
| Extreme zoptymalizowanych pod kątem pamięci | Wszyscy |
| Seria HB<sup>1,2</sup> | Wszyscy |
| Seria HC<sup>1,2</sup> | Wszyscy |
| Seria Lsv2 | Wszyscy |
| Seria NDv2<sup>1,2</sup> | Wszyscy |
| Seria NVv2<sup>1</sup> | Wszyscy |
| SAP HANA | Wszyscy |


<sup>1</sup> zaplanowane dla pomocy technicznej.  
<sup>2</sup> mogą być używane przez konta usługi Batch, w trybie subskrypcji użytkownika; w trybie subskrypcji użytkownika konta usługi Batch musi mieć ustawiony limit przydziału rdzeni. Zobacz [konfiguracji dla trybu subskrypcji użytkownika](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) Aby uzyskać więcej informacji.

Następujących rozmiarów maszyn wirtualnych są obsługiwane tylko dla węzłów o niskim priorytecie:

| Rodzina  | Obsługiwane rozmiary  |
|---------|---------|
| Seria M | Standardowa_M64ms |
| Seria M | Maszyna wirtualna Standard_M128s |

Inne rozmiary maszyn wirtualnych z rodziny serii M są obecnie obsługiwane.

### <a name="pools-in-cloud-service-configuration"></a>Pulach w konfiguracji usługi w chmurze

Pule usługi Batch w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md) *z wyjątkiem* dla następujących:

| Rodzina  | Nieobsługiwany rozmiar  |
|---------|---------|
| Seria A | ExtraSmall |
| Seria Av2 | Standard_A1_v2, maszyna wirtualna Standard_A2_v2, maszyna wirtualna Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>Ograniczone rodzin maszyn wirtualnych

Następujące rodzin maszyn wirtualnych może zostać alokowany w pule usługi Batch, ale należy zażądać zwiększenia limitu przydziału określone (zobacz [w tym artykule](batch-quota-limit.md#increase-a-quota)):

* Seria NCv2
* Seria NCv3
* Seria ND

Te rozmiary należy używać tylko w pulach w konfiguracji maszyny wirtualnej.

## <a name="size-considerations"></a>Ograniczenia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. Dla wielu wystąpień [obciążeń MPI](batch-mpi.md) lub aplikacje CUDA, należy wziąć pod uwagę wyspecjalizowane [HPC](../virtual-machines/linux/sizes-hpc.md) lub [włączonymi procesorami GPU](../virtual-machines/linux/sizes-gpu.md) maszyny Wirtualne o rozmiarach, odpowiednio. (Zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).)

* **Zadań na węzeł** — jest zazwyczaj wybierz węzeł rozmiar zakładając, że jedno zadanie jest uruchamiane w węźle naraz. Jednak może być korzystne ma wiele zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku jest wspólnego, aby wybrać rozmiar węzła wyspecjalizowanymi w celu sprostania zwiększonemu zapotrzebowaniu na równoległe wykonywanie zadań podrzędnych.

* **Ładowanie poziomów dla różnych zadań** — wszystkie węzły w puli mają taki sam rozmiar. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul. 

* **Dostępność w poszczególnych regionach** — rodzina maszyn wirtualnych lub rozmiaru mogą być niedostępne w regionach, w którym tworzenia kont usługi Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/regions/services/).

* **Przydziały** — [limitów przydziału rdzeni](batch-quota-limit.md#resource-quotas) w partii konta można ograniczyć liczbę węzłów o podanej wielkości, możesz dodać do puli usługi Batch. Aby zażądać zwiększenia limitu przydziału, zobacz [w tym artykule](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny Wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej, w porównaniu z konfiguracji usługi w chmurze.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe omówienie usługi Batch, zobacz [tworzenie rozbudowanych rozwiązań przetwarzania równoległego przy użyciu usługi Batch](batch-api-basics.md).
* Aby dowiedzieć się, jak za pomocą rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Użyj obsługą dostępu RDMA lub włączonymi procesorami GPU wystąpień w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
