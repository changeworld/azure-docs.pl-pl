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
ms.openlocfilehash: b6587a3928c2ddf0d00c90e07643525314690e42
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155543"
---
### <a name="virtual-machines"></a>Maszyny wirtualne

|  |  |
|---------|---------|
| [Allow custom VM image from a Resource Group](../articles/governance/policy/samples/allow-custom-vm-image.md) (Zezwalanie na korzystanie z niestandardowego obrazu maszyny wirtualnej pochodzącego z grupy zasobów) |  Wymaga, aby niestandardowe obrazy pochodziły z zatwierdzonej grupy zasobów. Należy określić nazwę zatwierdzonej grupy zasobów. |
| [Allowed SKUs for Storage Accounts and Virtual Machines](../articles/governance/policy/samples/allowed-skus-storage.md) (Dozwolone jednostki SKU dla kont magazynu i maszyn wirtualnych) | Wymaga, aby konta magazynu i maszyny wirtualne używały zatwierdzonych jednostek SKU. Używają wbudowanych zasad w celu zapewnienia zatwierdzonych jednostek SKU. Do Ciebie należy określenie tablicy z zatwierdzonymi jednostkami SKU maszyn wirtualnych i tablicy z zatwierdzonymi jednostkami SKU kont magazynu. |
| [Approved VM images](../articles/governance/policy/samples/allowed-custom-images.md) (Zatwierdzone obrazy maszyn wirtualnych) | Wymaga, aby w środowisku były wdrażane tylko zatwierdzone niestandardowe obrazy. Należy określić tablicę identyfikatorów zatwierdzonych obrazów. |
| [Audit if extension does not exist](../articles/governance/policy/samples/audit-extension-not-exist.md) (Sprawdzanie, czy rozszerzenie nie istnieje) | Sprawdza, czy rozszerzenie nie zostało wdrożone z maszyną wirtualną. Należy określić typ i wydawcę rozszerzenia, aby sprawdzić, czy zostało ono wdrożone. |
| [Not allowed VM Extensions](../articles/governance/policy/samples/not-allowed-vm-extension.md) (Niedozwolone rozszerzenia maszyny wirtualnej) | Uniemożliwia użycie określonych rozszerzeń. Należy określić tablicę zawierającą typy zabronionych rozszerzeń. |