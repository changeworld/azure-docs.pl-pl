---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 25928ef35da1ce4b3824303a5d46749c32aa701f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "69626378"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Zasoby na [grupę zasobów](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)dla każdego typu zasobu |800 |Niektóre typy zasobów mogą przekraczać limit 800. Zobacz [zasoby nieograniczone do 800 wystąpień na grupę zasobów](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Wdrożenia według grupy zasobów w historii wdrożenia |800<sup>1</sup> |800 |
| Zasoby na wdrożenie |800 |800 |
| Blokady zarządzania na unikatowy zakres |20 |20 |
| Liczba tagów dla zasobu lub grupy zasobów |50 |50 |
| Długość klucza tagu |512 |512 |
| Długość wartości tagu |256 |256 |

<sup>1</sup> Jeśli osiągnięto limit 800 wdrożeń dla każdej grupy zasobów, należy usunąć wdrożenia z historii, które nie są już potrzebne. Usunięcie wpisu z historii wdrożenia nie ma wpływu na wdrożone zasoby. Wpisy z historii można usuwać za pomocą polecenia [AZ Group Deployment Delete](/cli/azure/group/deployment) for Azure CLI lub [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) w programie PowerShell.  W przypadku skryptu programu PowerShell, który automatyzuje usuwanie wdrożeń w scenariuszu ciągłej integracji i ciągłego dostarczania (CI/CD), zobacz [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Limity szablonów

| Value | Limit domyślny | Limit maksymalny |
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
