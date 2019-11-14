---
title: 'Azure Premium Storage: projektowanie pod kątem wydajności na maszynach wirtualnych z systemem Windows '
description: Projektuj aplikacje o wysokiej wydajności przy użyciu usługi Azure Premium Storage. Premium Storage oferuje wysoką wydajność, obsługę dysków o niskich opóźnieniach dla obciążeń intensywnie korzystających z operacji we/wy działających na platformie Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 56836b81ad63c3a2170606dc64988aab8dc7f47f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035283"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: projektowanie pod kątem wysokiej wydajności
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Czasami problem z wydajnością dysku to w rzeczywistości wąskie gardło sieci. W takich sytuacjach należy zoptymalizować [wydajność sieci](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z artykułem dotyczącym [tworzenia wzorców na dysku](disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszone sieci, należy upewnić się, że jest ona włączona. Jeśli nie jest włączona, można ją włączyć dla już wdrożonych maszyn wirtualnych w [systemach Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed rozpoczęciem, jeśli jesteś nowym do Premium Storage, najpierw Przeczytaj artykuł [Wybieranie typu dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i [skalowalności i wydajności usługi Azure Storage dla kont magazynu](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z artykułem dotyczącym [tworzenia wzorców na dysku](disks-benchmarks.md).

Dowiedz się więcej na temat dostępnych typów dysków: [Wybierz typ dysku](disks-types.md)  

Aby uzyskać SQL Server użytkowników, przeczytaj artykuły dotyczące najlepszych rozwiązań w zakresie wydajności dla SQL Server:

* [Najlepsze rozwiązania dotyczące wydajności SQL Server na platformie Azure Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Usługa Azure Premium Storage zapewnia najwyższą wydajność SQL Server na maszynie wirtualnej platformy Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)