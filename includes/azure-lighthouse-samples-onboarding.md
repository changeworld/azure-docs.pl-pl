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
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986811"
---
Udostępniamy różne szablony dla określonych scenariuszy dołączania. Wybierz najlepiej działającą opcję i pamiętaj, aby zmodyfikować plik parametrów w celu odzwierciedlenia środowiska. Aby uzyskać więcej informacji o sposobach korzystania z tych plików we wdrożeniu, zobacz [Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure](../articles/lighthouse/how-to/onboard-customer.md).

| **Szablon** | **Opis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Dołączanie subskrypcji klienta do zarządzania zasobami delegowanymi na platformie Azure. Dla każdej subskrypcji należy wykonać oddzielne wdrożenie. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Dołączanie jednej lub większej liczby grup zasobów klienta do zarządzania zasobami delegowanymi na platformie Azure. Użyj właściwości **rgDelegatedResourceManagement** dla pojedynczej grupy zasobów lub właściwości **multipleRgDelegatedResourceManagement**, aby dołączyć wiele grup zasobów w tej samej subskrypcji. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Jeśli [opublikowano ofertę usługi zarządzanej w witrynie Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), możesz opcjonalnie użyć tego szablonu do dołączenia zasobów dla klientów, którzy zaakceptowali ofertę. Wartości z witryny Marketplace w pliku parametrów muszą być zgodne z wartościami użytymi podczas publikowania oferty. |

Zazwyczaj potrzebne jest osobne wdrożenie dla każdej dołączanej subskrypcji, ale możesz także wdrożyć szablony w wielu subskrypcjach.

| **Szablon** | **Opis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Wdrażanie szablonów usługi Azure Resource Manager w wielu subskrypcjach. |
