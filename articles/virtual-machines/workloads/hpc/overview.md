---
title: Obliczenia o wysokiej wydajności na maszynach wirtualnych z serii H — maszyny wirtualne platformy Azure
description: Dowiedz się więcej o funkcjach i możliwościach maszyn wirtualnych serii H zoptymalizowanych pod kątem HPC.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271017"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Obliczenia o wysokiej wydajności na maszynach wirtualnych serii H

Obliczenia o wysokiej wydajności (HPC) na maszynach wirtualnych serii HB i HC umożliwiają najbardziej zoptymalizowaną wydajność hpc wszystkich maszyn wirtualnych na platformie Azure. Maszyny wirtualne zoptymalizowane pod kątem HPC są używane do rozwiązywania niektórych najtrudniejszych problemów matematycznych, takich jak: dynamika płynów, symulacje ropy naftowej i gazu oraz modelowanie pogody.

W tym artykule omówiono niektóre kluczowe funkcje maszyn wirtualnych serii HB i HC, dlaczego te maszyny wirtualne działają dobrze w scenariuszach HPC i jak rozpocząć pracę.

## <a name="features-and-capabilities"></a>Funkcje i możliwości

Maszyny wirtualne serii HB i HC zostały zaprojektowane z myślą o zapewnieniu najlepszej wydajności HPC, skalowalności interfejsu przekazywania komunikatów (MPI) i efektywności kosztowej dla obciążeń HPC.

### <a name="message-passing-interface"></a>Interfejs przekazywania wiadomości

Seria HB i seria HC obsługują prawie wszystkie typy i wersje MPI. Niektóre z najbardziej typowych, obsługiwanych typów MPI to: OpenMPI, MVAPICH2, Platform MPI, Intel MPI i wszystkie czasowniki zdalnego bezpośredniego dostępu do pamięci (RDMA). Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu przekazywania wiadomości dla HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA i InfiniBand

Interfejs RDMA jest standardem w maszynach wirtualnych serii HB i HC. Wystąpienia obsługujące rdma komunikują się za pośrednictwem sieci InfiniBand, działającej z większą szybkością transmisji danych (EDR) dla maszyn wirtualnych serii HB i HC. Wystąpienia obsługujące rdma mogą zwiększyć skalowalność i wydajność niektórych aplikacji MPI.

Konfiguracja InfiniBand obsługująca maszyny wirtualne serii HB i HC to nieblokujące drzewa tłuszczowe o niskiej średnicy, co zapewnia stałą wydajność RDMA.

Zobacz [Włącz infiniband,](enable-infiniband.md) aby dowiedzieć się więcej o konfigurowaniu infinibandu na maszynach wirtualnych z serii HB lub HC.

## <a name="get-started"></a>Wprowadzenie

Najpierw zdecyduj, której maszyny wirtualnej z serii H będziesz używać. Aby uzyskać szczegółowe informacje na temat maszyn wirtualnych zoptymalizowanych pod kątem HPC, zobacz [omówienie serii HB](hb-series-overview.md) i [omówienie serii HC.](hc-series-overview.md) Aby uzyskać dane techniczne, zobacz [Rozmiary maszyn wirtualnych obliczeń o wysokiej wydajności](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Po wybraniu i utworzeniu maszyny Wirtualnej dla aplikacji należy ją skonfigurować, włączając infiniband. Aby dowiedzieć się, jak włączyć infiniband na maszynach wirtualnych z systemem Windows i Linux, zobacz [Włączanie infinibandu](enable-infiniband.md).

Kluczowym elementem obciążeń HPC jest MPI. Seria HB i seria HC obsługują prawie wszystkie typy i wersje MPI. Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu przekazywania wiadomości dla HPC](setup-mpi.md).

Po wybraniu serii maszyn wirtualnych, skonfigurowaniu infinibandu i mpi możesz rozpocząć tworzenie obciążeń HPC.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem serii HB](hb-series-overview.md) i [przeglądem serii HC,](hc-series-overview.md) aby dowiedzieć się więcej o kluczowych różnicach i specyfikacjach.

- Aby uzyskać wyższy poziom, widok architektury uruchamiania obciążeń HPC, zobacz [Obliczenia o wysokiej wydajności (HPC) na platformie Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
