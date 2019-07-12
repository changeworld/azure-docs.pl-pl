---
title: 'Usługi Azure Premium Storage: Projektowanie pod kątem wydajności na maszynach wirtualnych Windows | Dokumentacja firmy Microsoft'
description: Projektowanie aplikacji o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Usługa Premium Storage oferuje obsługę przez dyski o wysokiej wydajności i niskich opóźnieniach dla wyjścia — dużych obciążeń wejścia/uruchomione na maszynach wirtualnych platformy Azure.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 61f84dc871b4d7261dcb57ab430ce6feee9a0c64
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658198"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Czasami prawdopodobnie problem z wydajnością dysku jest faktycznie wąskich gardeł. W takich sytuacjach należy zoptymalizować swoje [wydajność sieci](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Jeśli chcesz przeprowadzić test porównawczy dysku, zobacz nasze artykuł w [testów porównawczych dysku](disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszonej łączności sieciowej, należy się upewnić, że jest ono włączone. Jeśli nie jest włączone, możesz je włączyć na już wdrożone maszyny wirtualne zarówno [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed przystąpieniem do wykonywania, jeśli jesteś nowym użytkownikiem magazynu w warstwie Premium, najpierw przeczytać artykuł [wybierz typ dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i [cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
