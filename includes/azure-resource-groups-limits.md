---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183597"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Zasoby na [grupy zasobów](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), na typ zasobu |800 |Różni się dla typu zasobu |
| Wdrożenia dla każdej grupy zasobów w historii wdrożenia |800<sup>1</sup> |800 |
| Zasoby na wdrożenie |800 |800 |
| Blokady zarządzania na unikatowy zakres |20 |20 |
| Liczba tagów na zasób lub grupa zasobów |15 |15 |
| Długość klucza tagu |512 |512 |
| Długość wartości tagu |256 |256 |

<sup>1</sup>Jeśli przekroczysz limit 800 wdrożeń dla grupy zasobów, Usuń wdrożenia z historii, które nie są już potrzebne. Usuwanie wpisu z historii wdrożenia nie ma wpływu na wdrożonych zasobów. Można usunąć wpisów z historii z [Usuń wdrożenie grupy az](/cli/azure/group/deployment) wiersza polecenia platformy Azure lub [AzResourceGroupDeployment Usuń](/powershell/module/az.resources/remove-azresourcegroupdeployment) w programie PowerShell.  Dla programu PowerShell usuwanie wdrożeń w ciągłej integracji i ciągłego dostarczania (CI/CD) scenariusza, zobacz skrypt, który automatyzuje [deployments.ps1 Usuń](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limity szablonu

| Wartość | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Parametry |256 |256 |
| Zmienne |256 |256 |
| Zasoby (w tym liczba kopii) |800 |800 |
| Dane wyjściowe |64 |64 |
| Wyrażenie szablonu |24 576 znaki |24 576 znaki |
| Zasoby w wyeksportowanymi szablonami |200 |200 | 
| Rozmiar szablonu |1 MB |1 MB |
| Rozmiar pliku parametrów |64 KB |64 KB |

Pewne ograniczenia szablonu może przekroczyć przy użyciu zagnieżdżonych szablonów. Aby uzyskać więcej informacji, zobacz [używać połączone szablony do wdrażania zasobów platformy Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmienne ani danych wyjściowych, możesz połączyć kilka wartości do obiektu. Aby uzyskać więcej informacji, zobacz [obiektów jako parametrów](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
