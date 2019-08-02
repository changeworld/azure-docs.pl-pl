---
title: 'Premium Storage platformy Azure: Projektowanie pod kątem wydajności na maszynach wirtualnych z systemem Windows | Microsoft Docs'
description: Projektuj aplikacje o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Premium Storage oferuje wysoką wydajność, obsługę dysków o niskich opóźnieniach dla obciążeń intensywnie korzystających z operacji we/wy działających na platformie Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3979920cac8e3ef856b3211733f08c21afac3e26
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693692"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Czasami problem z wydajnością dysku to w rzeczywistości wąskie gardło sieci. W takich sytuacjach należy zoptymalizować [wydajność sieci](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z artykułem dotyczącym [tworzenia wzorców na dysku](disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszone sieci, należy upewnić się, że jest ona włączona. Jeśli nie jest włączona, można ją włączyć dla już wdrożonych maszyn wirtualnych w [systemach Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed rozpoczęciem, jeśli jesteś nowym do Premium Storage, najpierw Przeczytaj artykuł [Wybieranie typu dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i [skalowalności i wydajności usługi Azure Storage dla kont magazynu](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
