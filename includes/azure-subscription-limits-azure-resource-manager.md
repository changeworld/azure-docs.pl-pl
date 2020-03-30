---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334797"
---
| Zasób | Limit |
| --- | --- |
| Subskrypcje na dzierżawę usługi Azure Active Directory | Nieograniczony. |
| [Coadministrators](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Nieograniczony. |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |
| Rozmiar żądania interfejsu API usługi Azure Resource Manager |4 194 304 bajtów. |
| Tagi na subskrypcję<sup>1</sup> |50 |
| Unikatowe obliczenia znaczników na subskrypcję<sup>1</sup> | 10 000 |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>2</sup> |

<sup>1.</sup> Do subskrypcji można zastosować maksymalnie 50 tagów. Jednak subskrypcja może zawierać nieograniczoną liczbę tagów, które są stosowane do grup zasobów i zasobów w ramach subskrypcji. Liczba znaczników na zasób lub grupę zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw i wartości tagów](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 10 000.  

<sup>2.</sup> Jeśli osiągniesz limit 800 wdrożeń, usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie subskrypcji, użyj [usuń-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [az deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
