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
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664579"
---
### <a name="virtual-networks"></a>Sieci wirtualne

|  |  |
|---------|---------|
| [Allowed Application Gateway SKUs](../articles/azure-policy/scripts/allowed-app-gate-sku.md) (Dozwolone jednostki SKU bramy aplikacji) | Wymaga, aby bramy aplikacji używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [No network peering to ER network](../articles/azure-policy/scripts/no-peering-er-net.md) (Uniemożliwianie korzystania z sieci równorzędnych w sieci ER) | Uniemożliwia skojarzenie sieci równorzędnej z siecią znajdującą się w określonej grupie zasobów. Pozwalają one zapobiegać nawiązywaniu połączeń z centralnie zarządzaną infrastrukturą sieci. Należy określić nazwę grupy zasobów, aby uniemożliwić skojarzenie. |
| [No User Defined Route Table](../articles/azure-policy/scripts/no-user-def-route-table.md) (Uniemożliwianie używania tabeli tras zdefiniowanej przez użytkownika)  |Uniemożliwia wdrażanie sieci wirtualnych przy użyciu tabeli tras zdefiniowanej przez użytkownika. |
| [NSG X on every subnet](../articles/azure-policy/scripts/nsg-on-subnet.md) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Use approved subnet for VM network interfaces](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |