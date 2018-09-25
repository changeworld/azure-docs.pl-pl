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
ms.openlocfilehash: df9c291136fc6a48effb08cd59eeb66486eb641c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003853"
---
### <a name="virtual-networks"></a>Sieci wirtualne

|  |  |
|---------|---------|
| [Allowed Application Gateway SKUs](../articles/governance/policy/samples/allowed-app-gate-sku.md) (Dozwolone jednostki SKU bramy aplikacji) | Wymaga, aby bramy aplikacji używały zatwierdzonej jednostki SKU. Należy określić tablicę zatwierdzonych jednostek SKU. |
| [No network peering to ER network](../articles/governance/policy/samples/no-peering-er-net.md) (Uniemożliwianie korzystania z sieci równorzędnych w sieci ER) | Uniemożliwia skojarzenie sieci równorzędnej z siecią znajdującą się w określonej grupie zasobów. Pozwalają one zapobiegać nawiązywaniu połączeń z centralnie zarządzaną infrastrukturą sieci. Należy określić nazwę grupy zasobów, aby uniemożliwić skojarzenie. |
| [No User Defined Route Table](../articles/governance/policy/samples/no-user-def-route-table.md) (Uniemożliwianie używania tabeli tras zdefiniowanej przez użytkownika)  |Uniemożliwia wdrażanie sieci wirtualnych przy użyciu tabeli tras zdefiniowanej przez użytkownika. |
| [NSG X on every subnet](../articles/governance/policy/samples/nsg-on-subnet.md) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Use approved subnet for VM network interfaces](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |