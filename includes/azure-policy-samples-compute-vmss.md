---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: e9f2f6eaab1b3504e549171ce6c60ecd9b3b5c7b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664519"
---
### <a name="virtual-machine-scale-sets"></a>Zestawy skali maszyn wirtualnych

|  |  |
|---------|---------|
| [Audit when VM does not use Managed Disk](../articles/azure-policy/scripts/create-vm-managed-disk.md) (Sprawdzanie, czy maszyna wirtualna nie używa dysku zarządzanego) | Sprawdza, czy utworzona maszyna wirtualna nie korzysta z dysków zarządzanych.|
| [Create VM using Managed Disk](../articles/azure-policy/scripts/use-managed-disk-vm.md) (Tworzenie maszyny wirtualnej używającej dysku zarządzanego) | Wymaga używania dysków zarządzanych przez maszyny wirtualne.|
| [Deny hybrid use benefit](../articles/azure-policy/scripts/deny-hybrid-use.md) (Uniemożliwianie użycia korzyści użycia hybrydowego) | Uniemożliwia użycie korzyści użycia hybrydowego platformy Azure (AHUB). Zastosuj tę zasadę, jeśli nie chcesz zezwolić na korzystanie z licencji lokalnych. |
| [Only allow a certain VM platform image](../articles/azure-policy/scripts/allow-certain-vm-image.md) (Zezwalanie tylko na obraz określonej platformy maszyn wirtualnych) | Wymaga użycia określonej wersji systemu UbuntuServer na maszynach wirtualnych. |