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
ms.openlocfilehash: 388d6641fb77d09c1706623b84ec256573807747
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664566"
---
### <a name="network-interfaces"></a>Interfejsy sieciowe

|  |  |
|---------|---------|
| [NSG X on every NIC](../articles/azure-policy/scripts/nsg-on-nic.md) (Sieciowa grupa zabezpieczeń X na każdej karcie sieciowej) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdym wirtualnym interfejsem sieciowym. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [Use approved subnet for VM network interfaces](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |