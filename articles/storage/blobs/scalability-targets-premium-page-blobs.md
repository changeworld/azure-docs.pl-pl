---
title: Cele skalowalności dla kont magazynu obiektów blob strony premium
titleSuffix: Azure Storage
description: Konto magazynu obiektów blob strony usługi premium jest zoptymalizowane pod kątem operacji odczytu/zapisu. Ten typ konta magazynu jest wycofywany z dysku niezarządzanego dla maszyny wirtualnej platformy Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756253"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Cele skalowalności i wydajności dla kont magazynu obiektów blob strony premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Skalowanie obiektów docelowych dla kont obiektów blob strony premium

Konto magazynu obiektów blob o wydajności premium jest zoptymalizowane pod kątem operacji odczytu/zapisu. Ten typ konta magazynu jest wycofywany z dysku niezarządzanego dla maszyny wirtualnej platformy Azure.

> [!NOTE]
> Firma Microsoft zaleca używanie dysków zarządzanych z maszynami wirtualnymi platformy Azure (VM), jeśli to możliwe. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz [Omówienie usługi Azure Disk Storage dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/managed-disks-overview.md).

Konta magazynu obiektów blob strony premium mają następujące cele skalowalności:

| Całkowita pojemność konta                            | Całkowita przepustowość dla lokalnie nadmiarowego konta magazynu                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Pojemność dysku: 4 TB (pojedynczy dysk)/ 35 TB (łączna suma wszystkich dysków) <br>Pojemność migawki: 10 TB | Do 50 gigabitów na sekundę dla przychodzących<sup>1</sup> + wychodzących<sup>2</sup> |

<sup>1</sup> Wszystkie dane (żądania) wysyłane do konta magazynu

<sup>2</sup> Wszystkie dane (odpowiedzi) otrzymane z konta magazynu

Konto obiektów blob strony premium jest kontem ogólnego przeznaczenia skonfigurowanym pod kątem wydajności premium. Zalecane są konta magazynu ogólnego przeznaczenia w wersji 2.

Jeśli używasz kont magazynu obiektów blob strony premium dla dysków niezarządzanych, a aplikacja przekracza cele skalowalności pojedynczego konta magazynu, firma Microsoft zaleca migrację na dyski zarządzane. Aby uzyskać więcej informacji na temat dysków zarządzanych, zobacz [Omówienie usługi Azure Disk Storage dla maszyn wirtualnych z systemem Windows](../../virtual-machines/windows/managed-disks-overview.md) lub [usługi Azure Disk Storage dla maszyn wirtualnych z systemem Linux](../../virtual-machines/linux/managed-disks-overview.md).

Jeśli nie można przeprowadzić migracji do dysków zarządzanych, należy utworzyć aplikację, aby używać wielu kont magazynu i partycjonować dane między tymi kontami magazynu. Na przykład jeśli chcesz dołączyć dyski o pojemności 51 TB na wielu maszynach wirtualnych, rozłóż je na dwóch kontach magazynu. 35 TB to limit dla jednego konta magazynu w wersji premium. Upewnij się, że jedno konto magazynu wydajności w wersji premium nigdy nie ma więcej niż 35 TB aprowizowanych dysków.

## <a name="see-also"></a>Zobacz też

- [Cele dotyczące skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md)
- [Cele skalowalności dla kont magazynu bloków obiektów blob w usłudze Premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Limity i przydziały subskrypcji platformy Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
