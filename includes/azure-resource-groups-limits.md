---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334676"
---
| Zasób | Limit |
| --- | --- |
| Zasoby na [grupę zasobów](../articles/azure-resource-manager/management/overview.md#resource-groups) | Zasoby nie są ograniczone przez grupę zasobów. Zamiast tego są one ograniczone przez typ zasobu w grupie zasobów. Zobacz następny wiersz. |
| Zasoby na grupę zasobów według typu zasobu |800 - Niektóre typy zasobów może przekroczyć limit 800. Zobacz [Zasoby nie ograniczone do 800 wystąpień na grupę zasobów](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Wdrożenia na grupę zasobów w historii wdrażania |800<sup>1</sup> |
| Zasoby na wdrożenie |800 |
| Blokady zarządzania w unikatowym zakresie |20 |
| Liczba znaczników na zasób lub grupę zasobów |50 |
| Długość klawisza znacznika |512 |
| Długość wartości znacznika |256 |

<sup>1.</sup> Jeśli osiągniesz limit 800 wdrożeń na grupę zasobów, usuń wdrożenia z historii, które nie są już potrzebne. Usunięcie wpisu z historii wdrażania nie ma wpływu na wdrożone zasoby. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów, gdy liczba wdrożeń przekracza 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Limity szablonów

| Wartość | Limit |
| --- | --- |
| Parametry |256 |
| Zmienne |256 |
| Zasoby (w tym liczba kopii) |800 |
| Dane wyjściowe |64 |
| Wyrażenie szablonu |24 576 znaków |
| Zasoby w eksportowanych szablonach |200 |
| Rozmiar szablonu |4 MB |
| Rozmiar pliku parametru |64 KB |

Niektóre limity szablonów można przekroczyć przy użyciu szablonu zagnieżdżonego. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](../articles/azure-resource-manager/templates/linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub wyjść, można połączyć kilka wartości w obiekt. Aby uzyskać więcej informacji, zobacz [Obiekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
