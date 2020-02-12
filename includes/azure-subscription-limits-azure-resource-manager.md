---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133714"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |980 |
| Rozmiar żądania interfejsu API Azure Resource Manager |4 194 304 bajtów. |4 194 304 bajtów. |
| Tagi na subskrypcję<sup>1</sup> |Nieograniczona liczba. |Nieograniczona liczba. |
| Obliczenia unikatowych tagów na subskrypcję<sup>1</sup> | 10 000 | 10 000 |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>2</sup> | 800 |
| Subskrypcje na Azure Active Directory dzierżawcę | Nieograniczona liczba. | Nieograniczona liczba. |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Nieograniczona liczba. |Nieograniczona liczba. |

<sup>1</sup> Możesz zastosować nieograniczoną liczbę tagów na subskrypcję. Liczba tagów dla zasobu lub grupy zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw tagów i wartości](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 10 000.  

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, Usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie subskrypcji, użyj polecenie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
