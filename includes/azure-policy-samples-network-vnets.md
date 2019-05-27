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
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155617"
---
### <a name="virtual-networks"></a>Sieci wirtualne

|  |  |
|---------|---------|
| [Allowed Application Gateway SKUs](../articles/governance/policy/samples/allowed-app-gateway-sku.md) (Dozwolone jednostki SKU bramy aplikacji) | Wymaga, aby bramy aplikacji używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Dozwolone jednostki SKU bramy sieci wirtualnej](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Wymaga, aby bramy sieci wirtualnych używały zatwierdzonych jednostek SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Allowed Load Balancer SKUs](../articles/governance/policy/samples/allowed-load-balancer-skus.md) (Dozwolone jednostki SKU modułu równoważenia obciążenia) | Wymaga, aby moduły równoważenia obciążenia sieci wirtualnych używały zatwierdzonych jednostek SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [Brak sieciowej komunikacji równorzędnej z siecią usługi Express Route](../articles/governance/policy/samples/no-peering-express-route-network.md) | Uniemożliwia skojarzenie sieci równorzędnej z siecią znajdującą się w określonej grupie zasobów. Pozwalają one zapobiegać nawiązywaniu połączeń z centralnie zarządzaną infrastrukturą sieci. Należy określić nazwę grupy zasobów, aby uniemożliwić skojarzenie. |
| [No User Defined Route Table](../articles/governance/policy/samples/no-user-defined-route-table.md) (Uniemożliwianie używania tabeli tras zdefiniowanej przez użytkownika)  | Uniemożliwia wdrażanie sieci wirtualnych przy użyciu tabeli tras zdefiniowanej przez użytkownika. |
| [NSG X on every subnet](../articles/governance/policy/samples/nsg-on-subnet.md) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Use approved subnet for VM network interfaces](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |