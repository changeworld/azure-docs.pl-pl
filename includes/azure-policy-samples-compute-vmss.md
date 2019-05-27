---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 4e2412a98b9f292398b972099daa1642534f180d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155445"
---
### <a name="virtual-machine-scale-sets"></a>Zestawy skali maszyn wirtualnych

|  |  |
|---------|---------|
| [Audit when VM does not use Managed Disk](../articles/governance/policy/samples/create-vm-managed-disk.md) (Sprawdzanie, czy maszyna wirtualna nie używa dysku zarządzanego) | Sprawdza, czy utworzona maszyna wirtualna nie korzysta z dysków zarządzanych.|
| [Create VM using Managed Disk](../articles/governance/policy/samples/use-managed-disk-vm.md) (Tworzenie maszyny wirtualnej używającej dysku zarządzanego) | Wymaga używania dysków zarządzanych przez maszyny wirtualne.|
| [Deny hybrid use benefit](../articles/governance/policy/samples/deny-hybrid-use.md) (Uniemożliwianie użycia korzyści użycia hybrydowego) | Uniemożliwia użycie korzyści użycia hybrydowego platformy Azure (AHUB). Zastosuj tę zasadę, jeśli nie chcesz zezwolić na korzystanie z licencji lokalnych. |
| [Only allow a certain VM platform image](../articles/governance/policy/samples/allow-certain-vm-image.md) (Zezwalanie tylko na obraz określonej platformy maszyn wirtualnych) | Wymaga użycia określonej wersji systemu UbuntuServer na maszynach wirtualnych. |