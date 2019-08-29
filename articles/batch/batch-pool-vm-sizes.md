---
title: Wybierz rozmiary maszyn wirtualnych dla pul — Azure Batch | Microsoft Docs
description: Jak wybierać dostępne rozmiary maszyn wirtualnych dla węzłów obliczeniowych w pulach Azure Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f894a7438c56c3830eb62c516447195f10fc3b76
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094665"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Wybieranie rozmiaru maszyny wirtualnej dla węzłów obliczeniowych w puli Azure Batch

Po wybraniu rozmiaru węzła dla puli Azure Batch można wybrać spośród niemal wszystkich rozmiarów maszyn wirtualnych dostępnych na platformie Azure. Platforma Azure oferuje różne rozmiary maszyn wirtualnych z systemem Linux i Windows dla różnych obciążeń.

Istnieje kilka wyjątków i ograniczeń umożliwiających wybranie rozmiaru maszyny wirtualnej:

* Niektóre maszyny wirtualne lub rozmiary maszyn wirtualnych nie są obsługiwane w usłudze Batch.
* Niektóre rozmiary maszyn wirtualnych są ograniczone i należy je włączyć przed przystąpieniem do przydzielenia.

## <a name="supported-vm-series-and-sizes"></a>Obsługiwane serie maszyn wirtualnych i rozmiary

### <a name="pools-in-virtual-machine-configuration"></a>Pule w konfiguracji maszyny wirtualnej

Pule wsadowe w konfiguracji maszyny wirtualnej obsługują niemal wszystkie rozmiary maszyn wirtualnych ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Zapoznaj się z poniższą tabelą, aby dowiedzieć się więcej o obsługiwanych rozmiarach i ograniczeniach.

Wszystkie rozmiary maszyn wirtualnych promocyjnych i w wersji zapoznawczej nie są zagwarantowane dla pomocy technicznej.

| Seria maszyn wirtualnych  | Obsługiwane rozmiary | Tryb alokacji puli kont wsadowych<sup>1</sup> |
|------------|---------|-----------------|
| Podstawowa seria A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (a0) | Any |
| Seria A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 | Any |
| Seria Av2 | Wszystkie rozmiary | Any |
| Seria B | Brak | Niedostępny |
| Seria DC | Brak | Niedostępny |
| Dv2, Dsv2 — seria | Wszystkie rozmiary | Any |
| Dv3, Dsv3 — seria | Wszystkie rozmiary | Any |
| [Rozmiary zoptymalizowane pod kątem pamięci](../virtual-machines/linux/sizes-memory.md) | Brak | Niedostępny |
| Seria Fsv2 | Wszystkie rozmiary | Any |
| Seria H | Wszystkie rozmiary | Any |
| HB-seria<sup>2</sup> | Wszystkie rozmiary | Any |
| HC — seria<sup>2</sup> | Wszystkie rozmiary | Any |
| Seria Ls | Wszystkie rozmiary | Any |
| Seria Lsv2 | Brak | Niedostępny |
| Seria M | Standard_M64ms (tylko niski priorytet), Standard_M128s (tylko niski priorytet) | Any |  
| NCv2 — seria<sup>2</sup> | Wszystkie rozmiary | Any |
| Seria NCV3 — seria<sup>2</sup> | Wszystkie rozmiary | Any |
| ND — seria<sup>2</sup> | Wszystkie rozmiary | Any |
| Seria NDv2 | Wszystkie rozmiary | Tryb subskrypcji użytkownika |
| Seria NV | Wszystkie rozmiary | Any |
| Seria NVv3 | Brak | Niedostępny |
| SAP HANA | Brak | Niedostępny |

<sup>1</sup> niektóre nowsze serie maszyn wirtualnych są początkowo obsługiwane częściowo. Te serie maszyn wirtualnych można przydzielić przez konta usługi Batch z **trybem alokacji puli** ustawionym na **subskrypcję użytkownika**. Zobacz [Zarządzanie kontami usługi Batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) , aby uzyskać więcej informacji na temat konfiguracji konta usługi Batch. Zobacz [przydziały i limity](batch-quota-limit.md) , aby dowiedzieć się, jak zażądać limitu przydziału dla tych częściowo obsługiwanych serii maszyn wirtualnych dla kont usługi Batch **subskrypcji użytkowników** .  

<sup>2</sup> te rozmiary maszyn wirtualnych można przydzielyć w pulach wsadowym w konfiguracji maszyny wirtualnej, ale należy zażądać [zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących:

| Seria maszyn wirtualnych  | Nieobsługiwane rozmiary |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Zagadnienia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z obsługą wiele wystąpień lub aplikacji cuda należy [](../virtual-machines/linux/sizes-hpc.md) odpowiednio rozważyć wyspecjalizowane rozmiary maszyn wirtualnych lub maszyny wirtualne [obsługujące procesor GPU](../virtual-machines/linux/sizes-gpu.md) . (Zobacz [Używanie wystąpień obsługujących funkcję RDMA lub GPU w pulach wsadowym](batch-pool-compute-intensive-sizes.md)).

* **Zadania na węzeł** — typowym zadaniem jest wybranie rozmiaru węzła, przy założeniu, że jedno zadanie jest uruchamiane w węźle w danym momencie. Jednak może być korzystne wykonywanie wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku często należy wybrać rozmiar węzła wielordzeniowego, aby uwzględnić zwiększony popyt wykonywania zadań równoległych.

* **Poziomy obciążenia dla różnych zadań** — ten sam rozmiar wynosi wszystkie węzły w puli. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność regionu** — seria maszyn wirtualnych lub rozmiar może nie być dostępny w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

* **Limity** przydziału — liczba [rdzeni](batch-quota-limit.md#resource-quotas) w ramach konta w usłudze Batch może ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli wsadowej. Aby zażądać zwiększenia limitu przydziału, zobacz [ten artykuł](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

* Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
* Aby uzyskać informacje o używaniu rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Używanie wystąpień z obsługą funkcji RDMA lub procesorów GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
