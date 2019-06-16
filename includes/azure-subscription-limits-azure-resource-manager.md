---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 04/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 712b70960e09a9c2b0e7a998bc0bddbc28c1e112
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146251"
---
| Resource | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Maszyny wirtualne na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na region. |25 000 na region. |
| Całkowita liczba rdzeni maszyn wirtualnych na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. | Skontaktuj się z pomocą techniczną. |
| Maszyn wirtualnych na serię, takie jak Dv2 i F, rdzeni na [subskrypcji](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. | Skontaktuj się z pomocą techniczną. |
| [Współadministratorów](../articles/billing-add-change-azure-subscription-administrator.md) na subskrypcję |Bez ograniczeń. |Bez ograniczeń. |
| [Konta magazynu](../articles/storage/common/storage-quickstart-create-account.md) na region na subskrypcję |250 |250 |
| [Grupy zasobów](../articles/azure-resource-manager/resource-group-overview.md) na subskrypcję |980 |980 |
| [Zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na subskrypcję |2000 na region. |2000 na region. |
| Rozmiar żądania interfejsu API usługi Resource Manager platformy Azure |4,194,304 bajtów. |4,194,304 bajtów. |
| Tagi na subskrypcję<sup>2</sup> |Bez ograniczeń. |Bez ograniczeń. |
| Obliczenia unikatowych tagów na subskrypcję<sup>2</sup> | 10 000 | 10 000 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |N/D<sup>3</sup> |N/D<sup>3</sup> |
| [Grupy koligacji](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na subskrypcję |N/D<sup>3</sup> |N/D<sup>3</sup> |
| [Poziom subskrypcji wdrożeń](../articles/azure-resource-manager/deploy-to-subscription.md) według lokalizacji | 800<sup>4</sup> | 800 |

<sup>1</sup>domyślne limity zależą przez typ kategorii oferty, np. bezpłatnej wersji próbnej i płatność za rzeczywiste użycie i serii Dv2, F i G.

<sup>2</sup>można zastosować dowolną liczbę tagów na subskrypcję. Liczba tagów na zasób lub grupę zasobów jest ograniczona do 15. Zwraca usługi Resource Manager [listę nazw i wartości tagów unikatowy](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasobu według tagów gdy ich liczba przekracza 10 000.  

<sup>3</sup>te funkcje nie są już wymagane z grupami zasobów platformy Azure i usługi Resource Manager.

<sup>4</sup>w przypadku osiągnięcia limitu 800 wdrożeń usunięcie wdrożeń z historii, które nie są już potrzebne. Aby usunąć wdrożeń poziomu subskrypcji, użyj [AzDeployment Usuń](/powershell/module/az.resources/Remove-AzDeployment) lub [Usuń wdrożenie az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Rdzenie maszyny wirtualnej mają regionalny limit całkowitej. Mają one również limit serii na rozmiar regionalnych, takich jak Dv2 i F. Te limity są wymuszane oddzielnie. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja można wdrożyć 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu, aby nie przekroczyć 30 rdzeni. Przykładem kombinację jest 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1.  
> <!-- -->
> 
> 

