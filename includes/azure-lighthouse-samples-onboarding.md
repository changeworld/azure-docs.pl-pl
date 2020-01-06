---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456756"
---
Udostępniamy różne szablony dla określonych scenariuszy dołączania. Wybierz najlepiej działającą opcję i pamiętaj, aby zmodyfikować plik parametrów w celu odzwierciedlenia środowiska. Aby uzyskać więcej informacji o sposobach korzystania z tych plików we wdrożeniu, zobacz [Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Szablon** | **Opis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Dołączanie subskrypcji klienta do zarządzania zasobami delegowanymi na platformie Azure. Dla każdej subskrypcji należy wykonać oddzielne wdrożenie. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Dołączanie jednej lub większej liczby grup zasobów klienta do zarządzania zasobami delegowanymi na platformie Azure. Użyj właściwości **rgDelegatedResourceManagement** dla pojedynczej grupy zasobów lub właściwości **multipleRgDelegatedResourceManagement**, aby dołączyć wiele grup zasobów w tej samej subskrypcji. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Jeśli [opublikowano ofertę usługi zarządzanej w witrynie Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), możesz opcjonalnie użyć tego szablonu do dołączenia zasobów dla klientów, którzy zaakceptowali ofertę. Wartości z witryny Marketplace w pliku parametrów muszą być zgodne z wartościami użytymi podczas publikowania oferty. |

Zazwyczaj potrzebne jest osobne wdrożenie dla każdej dołączanej subskrypcji, ale możesz także wdrożyć szablony w wielu subskrypcjach.

| **Szablon** | **Opis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Wdrażanie szablonów usługi Azure Resource Manager w wielu subskrypcjach. |
