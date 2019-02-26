---
title: Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks | Dokumentacja firmy Microsoft
description: Migrowanie maszyn wirtualnych platformy Azure utworzone przy użyciu dysków niezarządzanych na kontach magazynu, aby korzystać z dysków zarządzanych.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803587"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks na platformie Azure

Usługa Azure Managed Disks upraszcza zarządzanie magazynu, usuwając konieczność oddzielnie Zarządzanie kontami magazynu.  Możesz również migrować istniejące maszyny wirtualne platformy Azure do usługi Managed Disks, aby korzystać z większą niezawodność, maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie przełącza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) co ogranicza wpływ błędy jednostki skali magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Zgodnie z potrzebami, można wybierać spośród czterech typy opcji magazynu. Informacje na temat typów dostępnego miejsca na dysku znajdują się w artykule naszych [wybierz typ dysku](disks-types.md)

## <a name="migrate-scenarios"></a>Scenariusze migracji

Można migrować do usługi Managed Disks w następujących scenariuszach:

| **Migrowanie...**                                            | **Link do dokumentacji**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konwertuj autonomiczny maszyn wirtualnych i maszyn wirtualnych w zestawie do usługi managed disks dostępności   | [Konwertowanie maszyn wirtualnych w celu używania dysków zarządzanych](convert-unmanaged-to-managed-disks.md) |
| Pojedyncza maszyna wirtualna z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Tworzenie maszyny Wirtualnej z klasycznym wirtualnego dysku twardego](create-vm-specialized-portal.md)  | 
| Wszystkie maszyny wirtualne w sieci wirtualnej z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Migrację zasobów IaaS od modelu klasycznego do usługi Resource Manager](migration-classic-resource-manager-ps.md) i następnie [konwertowanie maszyny Wirtualnej z dysków niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Managed Disks](managed-disks-overview.md)
- Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
