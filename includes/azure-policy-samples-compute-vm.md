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
ms.openlocfilehash: eedce1ef3a4e7d56cfaf3142a72e370c96f9f575
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664530"
---
### <a name="virtual-machines"></a>Maszyny wirtualne

|  |  |
|---------|---------|
| [Allow custom VM image from a Resource Group](../articles/azure-policy/scripts/allow-custom-vm-image.md) (Zezwalanie na korzystanie z niestandardowego obrazu maszyny wirtualnej pochodzącego z grupy zasobów) |  Wymaga, aby niestandardowe obrazy pochodziły z zatwierdzonej grupy zasobów. Należy określić nazwę zatwierdzonej grupy zasobów. |
| [Allowed SKUs for Storage Accounts and Virtual Machines](../articles/azure-policy/scripts/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Approved VM images](../articles/azure-policy/scripts/allowed-custom-images.md) (Zatwierdzone obrazy maszyn wirtualnych) | Wymaga, aby w środowisku były wdrażane tylko zatwierdzone niestandardowe obrazy. Należy określić tablicę identyfikatorów zatwierdzonych obrazów. |
| [Audit if extension does not exist](../articles/azure-policy/scripts/audit-ext-not-exist.md) (Sprawdzanie, czy rozszerzenie nie istnieje) | Sprawdza, czy rozszerzenie nie zostało wdrożone z maszyną wirtualną. Należy określić typ i wydawcę rozszerzenia, aby sprawdzić, czy zostało ono wdrożone. |
| [Not allowed VM Extensions](../articles/azure-policy/scripts/not-allowed-vm-ext.md) (Niedozwolone rozszerzenia maszyny wirtualnej) | Uniemożliwia użycie określonych rozszerzeń. Należy określić tablicę zawierającą typy zabronionych rozszerzeń. |