---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 5e251402a89f84a0aa67eee676c1794207dd24fb
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869890"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Zasoby na [grupę zasobów](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)dla każdego typu zasobu |800 |Różne dla każdego typu zasobu |
| Wdrożenia według grupy zasobów w historii wdrożenia |800<sup>1</sup> |800 |
| Zasoby na wdrożenie |800 |800 |
| Blokady zarządzania na unikatowy zakres |20 |20 |
| Liczba tagów dla zasobu lub grupy zasobów |15 |15 |
| Długość klucza tagu |512 |512 |
| Długość wartości tagu |256 |256 |

<sup>1</sup> Jeśli osiągnięto limit 800 wdrożeń dla każdej grupy zasobów, należy usunąć wdrożenia z historii, które nie są już potrzebne. Usunięcie wpisu z historii wdrożenia nie ma wpływu na wdrożone zasoby. Wpisy z historii można usuwać za pomocą polecenia [AZ Group Deployment Delete](/cli/azure/group/deployment) for Azure CLI lub [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) w programie PowerShell.  W przypadku skryptu programu PowerShell, który automatyzuje usuwanie wdrożeń w scenariuszu ciągłej integracji i ciągłego dostarczania (CI/CD), zobacz [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limity szablonów

| Wartość | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Parametry |256 |256 |
| Zmienne |256 |256 |
| Zasoby (w tym liczba kopii) |800 |800 |
| outputs |64 |64 |
| Wyrażenie szablonu |24 576 znaków |24 576 znaków |
| Zasoby w wyeksportowanych szablonach |200 |200 | 
| Rozmiar szablonu |4 MB |4 MB |
| Rozmiar pliku parametru |64 KB |64 KB |

Można przekroczyć niektóre limity szablonów przy użyciu zagnieżdżonego szablonu. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](../articles/azure-resource-manager/resource-group-linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub danych wyjściowych, można połączyć kilka wartości z obiektem. Aby uzyskać więcej informacji, zobacz [obiekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
