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
ms.date: 01/03/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12cd1caa4cb96dbd5862776589d4a34aeb294ca1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763762"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrowanie maszyn wirtualnych platformy Azure do usługi Managed Disks na platformie Azure

Usługa Azure Managed Disks upraszcza zarządzanie magazynu, usuwając konieczność oddzielnie Zarządzanie kontami magazynu.  Możesz również migrować istniejące maszyny wirtualne platformy Azure do usługi Managed Disks, aby korzystać z większą niezawodność, maszyn wirtualnych w zestawie dostępności. Zapewnia, że dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczego punktu awarii. Automatycznie przełącza dyski różnych maszyn wirtualnych w zestawie dostępności w różnych jednostkach skalowania magazynu (sygnatury) co ogranicza wpływ błędy jednostki skali magazynu w jednym spowodowany sprzętu i oprogramowania błędów.
Zgodnie z potrzebami, można wybierać spośród czterech typy opcji magazynu. Informacje na temat typów dostępnego miejsca na dysku znajdują się w artykule naszych [wybierz typ dysku](disks-types.md)

## <a name="migrate-scenarios"></a>Scenariusze migracji

Można migrować do usługi Managed Disks w następujących scenariuszach:

| **Migrowanie...**                                            | **Link do dokumentacji**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Konwertowanie autonomicznych maszyn wirtualnych i maszyn wirtualnych w zestawie dostępności na dyski zarządzane   | [Konwertowanie maszyn wirtualnych w celu używania dysków zarządzanych](convert-unmanaged-to-managed-disks.md) |
| Pojedyncza maszyna wirtualna z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Tworzenie maszyny Wirtualnej z klasycznym wirtualnego dysku twardego](create-vm-specialized-portal.md)  | 
| Wszystkie maszyny wirtualne w sieci wirtualnej z wersji klasycznej do usługi Resource Manager na dyskach zarządzanych     | [Migrację zasobów IaaS od modelu klasycznego do usługi Resource Manager](migration-classic-resource-manager-ps.md) i następnie [konwertowanie maszyny Wirtualnej z dysków niezarządzanych do usługi managed disks](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Managed Disks](managed-disks-overview.md)
- Przegląd [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
