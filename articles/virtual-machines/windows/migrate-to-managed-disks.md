---
title: Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks | Dokumentacja firmy Microsoft
description: Migrowanie maszyn wirtualnych platformy Azure utworzone przy użyciu dysków niezarządzanych na kontach magazynu, aby korzystać z dysków zarządzanych.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418405"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks na platformie Azure

Usługa Azure Managed Disks upraszcza zarządzanie magazynu, usuwając konieczność oddzielnie Zarządzanie kontami magazynu.  Możesz również migrować istniejące maszyny wirtualne platformy Azure do usługi Managed Disks, aby korzystać z większą niezawodność, maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie przełącza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) co ogranicza wpływ błędy jednostki skali magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Zgodnie z potrzebami, można wybierać spośród czterech typy opcji magazynu. Informacje na temat typów dostępnego miejsca na dysku znajdują się w artykule naszych [wybierz typ dysku](disks-types.md)

## <a name="migration-scenarios"></a>Scenariusze migracji

Można migrować do usługi Managed Disks w następujących scenariuszach:

|Scenariusz  |Artykuł  |
|---------|---------|
|Konwertowanie autonomicznych maszyn wirtualnych i maszyn wirtualnych w zestawie dostępności na dyski zarządzane     |[Konwertowanie maszyn wirtualnych w celu używania dysków zarządzanych](convert-unmanaged-to-managed-disks.md)         |
|Konwertuj pojedynczej maszyny Wirtualnej z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     |[Tworzenie maszyny Wirtualnej z klasycznym wirtualnego dysku twardego](create-vm-specialized-portal.md)         |
|Konwertuj wszystkie maszyny wirtualne w sieci wirtualnej z klasycznej do usługi Resource Manager na dyskach zarządzanych     |[Migrację zasobów IaaS od modelu klasycznego do usługi Resource Manager](migration-classic-resource-manager-ps.md) i następnie [konwertowanie maszyny Wirtualnej z dysków niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md)         |
|Uaktualnienia maszyn wirtualnych przy użyciu standardowych dysków niezarządzanych do maszyn wirtualnych przy użyciu dysków zarządzanych w warstwie premium     | Po pierwsze, [konwertowania maszyny wirtualnej Windows z dysków niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md). Następnie [zaktualizować typ magazynu dysków zarządzanych](convert-disk-storage.md).         |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Managed Disks](managed-disks-overview.md)
- Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
