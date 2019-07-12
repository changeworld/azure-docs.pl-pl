---
title: O wysokiej wydajności obliczeniowej na maszynach wirtualnych serii H - maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o funkcjach i możliwościach maszyny wirtualne serii H, zoptymalizowane pod kątem HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800064"
---
# <a name="high-performance-computing-on-h-series-vms"></a>O wysokiej wydajności obliczeniowej na maszynach wirtualnych serii H

Wysokowydajnych obliczeń (HPC) na serii HB i maszyny wirtualne z serii HC Włącz najbardziej zoptymalizowane wydajności HPC, maszyn wirtualnych na platformie Azure. HPC zoptymalizowane pod kątem maszyn wirtualnych są używane do rozwiązywaniu niektórych z najbardziej trudnych problemów matematycznych, takich jak: dynamiki płynów, symulacje ropa naftowa i gaz lub modelowania zjawisk pogodowych.

W tym artykule omówiono niektóre kluczowe funkcje HB serii oraz HC maszyny wirtualne z serii, dlaczego dobrze w scenariusze obejmujące rozwiązania HPC i jak zacząć wykonywać te maszyny wirtualne.

## <a name="features-and-capabilities"></a>Funkcje i możliwości

HB serii oraz maszyny wirtualne z serii połączenia Hybrydowego są zaprojektowana w celu zapewnienia najlepszej wydajności HPC skalowalność interface (MPI), przekazywanie komunikatów i koszty wydajności dla obciążeń HPC.

### <a name="message-passing-interface"></a>Interfejsu przekazywania komunikatów

HB HC serie i obsługuje prawie wszystkie wersje i typy MPI. Najbardziej typowe, obsługiwane typy MPI, należą: OpenMPI, MVAPICH2, MPI platformy, Intel MPI i całej pamięci bezpośredniej zdalnego dostępu do zlecenia (RDMA). Aby uzyskać więcej informacji, zobacz [ustawienia interfejsu przekazywania komunikatów dla HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>Funkcję RDMA oraz InfiniBand

Interfejs RDMA jest standardowa HB serii i maszyny wirtualne z serii połączenia Hybrydowego. Wystąpienia z obsługą dostępu RDMA komunikują się za pośrednictwem sieci InfiniBand, operacyjne stawek rozszerzone dane (EDR) dla maszyn wirtualnych z serii HB i HC —. Wystąpienia z obsługą dostępu RDMA może zwiększyć skalowalność i wydajność niektórych aplikacji MPI.

Konfiguracja InfiniBand obsługujące HB serii i maszyny wirtualne z serii połączenia Hybrydowego nie powodują blokowania fat drzewa z zaprojektować średnica niski spójną wydajność funkcji RDMA.

Zobacz [Włącz InfiniBand](enable-infiniband.md) Aby dowiedzieć się więcej o konfigurowaniu InfiniBand na serię HB lub maszyny wirtualne z serii połączenia Hybrydowego.

## <a name="get-started"></a>Wprowadzenie

Najpierw zdecyduj, które maszyn wirtualnych z serii H zamierzasz używać. Aby uzyskać szczegółowe informacje o obliczeniach HPC zoptymalizowane pod kątem maszyn wirtualnych, zobacz [omówienie serii HB](hb-series-overview.md) i [omówienie serii HC](hc-series-overview.md). Specyfikacje, można zobaczyć [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Gdy została wybrana i utworzono maszynę Wirtualną dla aplikacji, należy skonfigurować go, włączając InfiniBand. Aby dowiedzieć się, jak włączyć InfiniBand zarówno Windows, jak i maszyny wirtualne systemu Linux, zobacz [Włącz InfiniBand](enable-infiniband.md).

Kluczowym elementem obciążeń HPC jest MPI. HB HC serie i obsługuje prawie wszystkie wersje i typy MPI. Aby uzyskać więcej informacji, zobacz [ustawienia interfejsu przekazywania komunikatów dla HPC](setup-mpi.md).

Po wybraniu serii maszyny Wirtualnej, skonfiguruj Infiniband i MPI, możesz rozpocząć tworzenie obciążeń HPC.

## <a name="next-steps"></a>Następne kroki

- Przegląd [Przegląd HB serii](hb-series-overview.md) i [omówienie serii HC](hc-series-overview.md) informacje na temat podstawowych różnic oraz specyfikacji.

- Wyższy poziom architektury widoku uruchamiania obciążeń HPC, zobacz [o wysokiej wydajności (HPC) na platformie Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
