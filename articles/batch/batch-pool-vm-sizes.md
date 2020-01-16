---
title: Wybierz rozmiary maszyn wirtualnych dla pul — Azure Batch | Microsoft Docs
description: Jak wybierać dostępne rozmiary maszyn wirtualnych dla węzłów obliczeniowych w pulach Azure Batch
services: batch
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: jushiman
ms.custom: seodec18
ms.openlocfilehash: 34ab09f7d8d47804992b8ef6864bfea60d1c9b4d
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026611"
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
| Podstawowa seria A | Wszystkie rozmiary *z wyjątkiem* Basic_A0 (a0) | Dowolne |
| Seria A | Wszystkie rozmiary *z wyjątkiem* Standard_A0 | Dowolne |
| Seria Av2 | Wszystkie rozmiary | Dowolne |
| Seria B | Brak | Niedostępne |
| Seria DC | Brak | Niedostępne |
| Dv2, DSv2 — seria | Wszystkie rozmiary | Dowolne |
| Dv3, Dsv3 — seria | Wszystkie rozmiary | Dowolne |
| EV3, Esv3 — seria | Wszystkie rozmiary | Dowolne |
| Seria Fsv2 | Wszystkie rozmiary | Dowolne |
| Seria H | Wszystkie rozmiary | Dowolne |
| HB-seria<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| HC — seria<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| Seria Ls | Wszystkie rozmiary | Dowolne |
| Seria Lsv2 | Brak | Niedostępne |
| Seria M | Standard_M64ms (tylko niski priorytet), Standard_M128s (tylko niski priorytet) | Dowolne |
| Seria Mv2 | Brak | Niedostępne |
| Seria NC | Wszystkie rozmiary | Dowolne |
| NCv2 — seria<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| Seria NCV3 — seria<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| ND — seria<sup>2</sup> | Wszystkie rozmiary | Dowolne |
| Seria NDv2 | Wszystkie rozmiary | Tryb subskrypcji użytkownika |
| Seria NV | Wszystkie rozmiary | Dowolne |
| Seria NVv3 | Brak | Niedostępne |
| SAP HANA | Brak | Niedostępne |

<sup>1</sup> niektóre nowsze serie maszyn wirtualnych są początkowo obsługiwane częściowo. Te serie maszyn wirtualnych można przydzielić przez konta usługi Batch z **trybem alokacji puli** ustawionym na **subskrypcję użytkownika**. Zobacz [Zarządzanie kontami usługi Batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) , aby uzyskać więcej informacji na temat konfiguracji konta usługi Batch. Zobacz [przydziały i limity](batch-quota-limit.md) , aby dowiedzieć się, jak zażądać limitu przydziału dla tych częściowo obsługiwanych serii maszyn wirtualnych dla kont usługi Batch **subskrypcji użytkowników** .  

<sup>2</sup> te rozmiary maszyn wirtualnych można przydzielyć w pulach wsadowym w konfiguracji maszyny wirtualnej, ale należy zażądać [zwiększenia limitu przydziału](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pule w konfiguracji usługi w chmurze

Pule wsadowe w konfiguracji usługi w chmurze obsługują wszystkie [rozmiary maszyn wirtualnych dla Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **z wyjątkiem** następujących:

| Seria maszyn wirtualnych  | Nieobsługiwane rozmiary |
|------------|-------------------|
| Seria A   | Bardzo małe       |
| Seria Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Istotne zagadnienia dotyczące rozmiaru

* **Wymagania dotyczące aplikacji** — należy wziąć pod uwagę charakterystyki i wymagania aplikacji, które będą uruchamiane w węzłach. Takie czynniki jak to, czy aplikacja jest wielowątkowa oraz ile pamięci zużywa, mogą pomóc w wyborze najbardziej odpowiedniego i ekonomicznego rozmiar węzła. W przypadku [obciążeń MPI](batch-mpi.md) z obsługą wiele wystąpień lub aplikacji cuda należy odpowiednio [rozważyć wyspecjalizowane](../virtual-machines/linux/sizes-hpc.md) rozmiary maszyn wirtualnych lub maszyny wirtualne [obsługujące procesor GPU](../virtual-machines/linux/sizes-gpu.md) . (Zobacz [Używanie wystąpień obsługujących funkcję RDMA lub GPU w pulach wsadowym](batch-pool-compute-intensive-sizes.md)).

* **Zadania na węzeł** — typowym zadaniem jest wybranie rozmiaru węzła, przy założeniu, że jedno zadanie jest uruchamiane w węźle w danym momencie. Jednak może być korzystne wykonywanie wielu zadań (i w związku z tym wiele wystąpień aplikacji) [równolegle](batch-parallel-node-tasks.md) w węzłach obliczeniowych podczas wykonywania zadania. W tym przypadku często należy wybrać rozmiar węzła wielordzeniowego, aby uwzględnić zwiększony popyt wykonywania zadań równoległych.

* **Poziomy obciążenia dla różnych zadań** — ten sam rozmiar wynosi wszystkie węzły w puli. Jeśli planujesz uruchamiać aplikacje z różnymi wymaganiami systemowymi i/lub poziomami obciążenia, zalecamy użycie oddzielnych pul.

* **Dostępność regionu** — seria maszyn wirtualnych lub rozmiar może nie być dostępny w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/).

* **Limity** przydziału — liczba [rdzeni](batch-quota-limit.md#resource-quotas) w ramach konta w usłudze Batch może ograniczać liczbę węzłów o danym rozmiarze, które można dodać do puli wsadowej. Aby zażądać zwiększenia limitu przydziału, zobacz [ten artykuł](batch-quota-limit.md#increase-a-quota). 

* **Konfiguracja puli** — ogólnie rzecz biorąc, masz więcej opcji rozmiaru maszyny wirtualnej podczas tworzenia puli w konfiguracji maszyny wirtualnej w porównaniu z konfiguracją usługi w chmurze.

## <a name="next-steps"></a>Następne kroki

* Szczegółowe omówienie usługi Batch można znaleźć w temacie [programowanie równoległych rozwiązań obliczeniowych na dużą skalę za pomocą usługi Batch](batch-api-basics.md).
* Aby uzyskać informacje o używaniu rozmiarów maszyn wirtualnych intensywnie korzystających z obliczeń, zobacz [Używanie wystąpień z obsługą funkcji RDMA lub procesorów GPU w pulach usługi Batch](batch-pool-compute-intensive-sizes.md).
