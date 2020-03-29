---
title: Migrowanie maszyn wirtualnych platformy Azure na dyski zarządzane
description: Migrowanie maszyn wirtualnych platformy Azure utworzonych przy użyciu dysków niezarządzanych na kontach magazynu w celu użycia dysków zarządzanych.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921355"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure na dyski zarządzane na platformie Azure

Dyski zarządzane platformy Azure upraszczają zarządzanie magazynem, usuwając konieczność oddzielnego zarządzania kontami magazynu.  Można również migrować istniejące maszyny wirtualne platformy Azure do dysków zarządzanych, aby uzyskać lepszą niezawodność maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie umieszcza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnaturach), co ogranicza wpływ awarii pojedynczych jednostek skalowania magazynu spowodowanych awariami sprzętu i oprogramowania.
W zależności od potrzeb możesz wybrać jeden z czterech typów opcji pamięci masowej. Aby dowiedzieć się więcej o dostępnych typach dysków, zobacz nasz artykuł [Wybieranie typu dysku](disks-types.md)

## <a name="migration-scenarios"></a> Scenariusze migracji

Dyski zarządzane można przeprowadzić do dysków zarządzanych w następujących scenariuszach:

|Scenariusz  |Artykuł  |
|---------|---------|
|Konwertowanie autonomicznych maszyn wirtualnych i maszyn wirtualnych w zestawie dostępności na dyski zarządzane     |[Konwertowanie maszyn wirtualnych na dyski zarządzane](convert-unmanaged-to-managed-disks.md)         |
|Konwertowanie pojedynczej maszyny Wirtualnej z klasycznego na Menedżera zasobów na dyskach zarządzanych     |[Tworzenie maszyny wirtualnej z klasycznego dysku VHD](create-vm-specialized-portal.md)         |
|Konwertowanie wszystkich maszyn wirtualnych w sieci wirtualnej z klasycznego na Menedżera zasobów na dyskach zarządzanych     |[Migrowanie zasobów usługi IaaS z klasycznego do Menedżera zasobów,](migration-classic-resource-manager-ps.md) a następnie [konwertowanie maszyny Wirtualnej z dysków niezarządzanych na dyski zarządzane](convert-unmanaged-to-managed-disks.md)         |
|Uaktualnianie maszyn wirtualnych ze standardowymi dyskami niezarządzanymi do maszyn wirtualnych z zarządzanymi dyskami premium     | Najpierw [przekonwertuj maszynę wirtualną systemu Windows z dysków niezarządzanych na dyski zarządzane](convert-unmanaged-to-managed-disks.md). Następnie [zaktualizuj typ magazynu dysku zarządzanego](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dyskach zarządzanych](managed-disks-overview.md)
- Przejrzyj [ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks/).
