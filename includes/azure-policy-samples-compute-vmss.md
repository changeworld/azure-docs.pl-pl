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
ms.openlocfilehash: 725d37a6521c874d14291ea7e3c53ea94907eb17
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138103"
---
### <a name="virtual-machine-scale-sets"></a>Zestawy skali maszyn wirtualnych

|  |  |
|---------|---------|
| [Audit when VM does not use Managed Disk](../articles/azure-policy/scripts/create-vm-managed-disk.md) (Sprawdzanie, czy maszyna wirtualna nie używa dysku zarządzanego) | Sprawdza, czy utworzona maszyna wirtualna nie korzysta z dysków zarządzanych.|
| [Create VM using Managed Disk](../articles/azure-policy/scripts/use-managed-disk-vm.md) (Tworzenie maszyny wirtualnej używającej dysku zarządzanego) | Wymaga używania dysków zarządzanych przez maszyny wirtualne.|
| [Deny hybrid use benefit](../articles/azure-policy/scripts/deny-hybrid-use.md) (Uniemożliwianie użycia korzyści użycia hybrydowego) | Uniemożliwia użycie korzyści użycia hybrydowego platformy Azure (AHUB). Zastosuj tę zasadę, jeśli nie chcesz zezwolić na korzystanie z licencji lokalnych. |
| [Only allow a certain VM platform image](../articles/azure-policy/scripts/allow-certain-vm-image.md) (Zezwalanie tylko na obraz określonej platformy maszyn wirtualnych) | Wymaga użycia określonej wersji systemu UbuntuServer na maszynach wirtualnych. |