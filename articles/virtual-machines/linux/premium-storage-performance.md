---
title: 'Usługa Azure Premium Storage: Projektowanie pod kątem wydajności na maszynach wirtualnych z systemem Linux | Dokumenty firmy Microsoft'
description: Projektowanie aplikacji o wysokiej wydajności przy użyciu dysków zarządzanych ssd w wersji Premium platformy Azure. Magazyn w wersji Premium oferuje wysokowydajną obsługę dysku o niskim opóźnieniu dla obciążeń intensywnie korzystających z we/wy uruchomionych na maszynach wirtualnych platformy Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267146"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Usługa Azure premium storage: projektowanie dla wysokiej wydajności
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Czasami to, co wydaje się być problem z wydajnością dysku jest rzeczywiście wąskie gardło sieci. W takich sytuacjach należy zoptymalizować [wydajność sieci.](../../virtual-network/virtual-network-optimize-network-bandwidth.md)
>
> Jeśli szukasz porównania dysku, zobacz nasz artykuł na [benchmarking dysku](disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszoną sieć, upewnij się, że jest włączona. Jeśli nie jest włączona, można go włączyć na już wdrożonych maszynach wirtualnych zarówno w [systemie Windows,](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) jak i [linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed rozpoczęciem, jeśli jesteś nowy w magazynie w wersji Premium, najpierw przeczytaj [wybierz typ dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i cele [skalowalności dla kont magazynu obiektów blob strony premium.](../../storage/blobs/scalability-targets-premium-page-blobs.md)


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Jeśli szukasz porównania dysku, zobacz nasz artykuł na [benchmarking dysku](disks-benchmarks.md).

Dowiedz się więcej o dostępnych typach dysków: [wybierz typ dysku](disks-types.md)  

W przypadku użytkowników programu SQL Server przeczytaj artykuły na temat najlepszych rozwiązań dotyczących wydajności programu SQL Server:

* [Najważniejsze wskazówki dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Usługa Azure Premium Storage zapewnia najwyższą wydajność programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)