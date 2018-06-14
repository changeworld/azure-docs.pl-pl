---
title: Wybierz rozmiarów maszyn wirtualnych dla pul partii zadań Azure | Dokumentacja firmy Microsoft
description: Wybieranie z dostępnych rozmiarów maszyn wirtualnych dla węzłów obliczeniowych w puli partii zadań Azure
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2018
ms.author: danlep
ms.openlocfilehash: addd1e9314a754b40cc5d49c0299f007580f512f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
ms.locfileid: "29765609"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybierz rozmiar maszyny Wirtualnej dla węzłów obliczeniowych w puli partii zadań Azure

Po wybraniu węzła rozmiaru puli partii zadań Azure można wybrać spośród niemal wszystkich rozmiarów maszyn wirtualnych dostępnej na platformie Azure. Azure oferuje szeroką gamę rozmiary dla systemów Linux i maszyn wirtualnych systemu Windows dla różnych obciążeń. 

Istnieje kilka wyjątków i ograniczenia dotyczące wybierania rozmiar maszyny Wirtualnej:
* Niektóre rodzin maszyny Wirtualnej lub rozmiarów maszyn wirtualnych nie są obsługiwane w partii. 
* Niektóre rozmiarów maszyn wirtualnych są ograniczone i muszą być włączone w szczególności, zanim może zostać przydzielone.


## <a name="supported-vm-families-and-sizes"></a>Obsługiwanych rodzin maszyn wirtualnych i rozmiary

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule partii w konfiguracji maszyny wirtualnej obsługuje wszystkich rozmiarów maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)) *z wyjątkiem* dla następujących:

| Rodzina  | Nieobsługiwany rozmiar  |
|---------|---------|
| Podstawowe A-series | Basic_A0 (A0) |
| Seria A | Standardowa_A0 |
| Seria B | Wszyscy |
| Fsv2 serii<sup>*</sup> | Wszyscy |

<sup>*</sup>W tej serii rozmiarach na plan dla wsparcia w przyszłości.

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule partii w konfiguracji usługi w chmurze obsługuje wszystkie [rozmiarów maszyn wirtualnych dla usług w chmurze](../cloud-services/cloud-services-sizes-specs.md) *z wyjątkiem* dla następujących:

| Rodzina  | Nieobsługiwany rozmiar  |
|---------|---------|
| Seria A | ExtraSmall |
| Seria Av2 | Standard_A2m_v2 Standard_A1_v2, Standard_A2_v2, |

## <a name="restricted-vm-families"></a>Ograniczone rodzin maszyn wirtualnych
Mogą być przydzielone następujących rodzin maszyn wirtualnych w puli partii, ale należy zażądać zwiększenia limitu przydziału określonych (zobacz [w tym artykule](batch-quota-limit.md#increase-a-quota)):
* Seria NCv2
* Seria NCv3
* Seria ND

Rozmiary można używać tylko w pulach w konfiguracji maszyny wirtualnej.

## <a name="size-considerations"></a>Zagadnienia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystykę i wymagania dotyczące aplikacji, należy uruchomić na węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. Dla wielu wystąpień [obciążeń MPI](batch-mpi.md) lub CUDA aplikacji, należy wziąć pod uwagę specjalizowany [HPC](../virtual-machines/linux/sizes-hpc.md) lub [włączone GPU](../virtual-machines/linux/sizes-gpu.md) odpowiednio rozmiary maszyny Wirtualnej. (Zobacz [użycia z funkcją RDMA lub włączone GPU wystąpień w pulach partii](batch-pool-compute-intensive-sizes.md).) 

* **Zadania w każdym węźle** — zazwyczaj, aby wybrać węzeł rozmiar zakładając, że jedno zadanie działa w węźle naraz. Jednak może być korzystne w przypadku wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) na węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku jest wspólne, aby wybrać rozmiar wielordzeniowych węzeł, aby pomieścić zwiększone zapotrzebowanie wykonywanie zadań równoległych.

* **Ładowanie poziomy dla różnych zadań** — wszystkie węzły w puli mają taki sam rozmiar. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul. 

* **Dostępność w danym regionie** -A VM rodziny lub rozmiar mogą nie być dostępne w regionach, gdzie tworzenie konta usługi partia zadań. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [produkty, które są dostępne w regionie](https://azure.microsoft.com/regions/services/).

* **Przydziały** - [rdzenie przydziały](batch-quota-limit.md#resource-quotas) w partii Twojego konta można ograniczyć liczbę węzłów dany rozmiar można dodać do puli partii. Aby zażądać zwiększenia limitu przydziału, zobacz [w tym artykule](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, ma więcej opcji rozmiaru maszyny Wirtualnej po utworzeniu puli w konfiguracji maszyny wirtualnej, w porównaniu z konfiguracji usługi w chmurze.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać szczegółowe informacje o partii, zobacz [Programowanie równoległe na dużą skalę obliczeniowe rozwiązań w partii](batch-api-basics.md).
* Aby dowiedzieć się, jak przy użyciu obliczeniowych rozmiarów maszyn wirtualnych, zobacz [użycia z funkcją RDMA lub włączone GPU wystąpień w pulach partii](batch-pool-compute-intensive-sizes.md). 


