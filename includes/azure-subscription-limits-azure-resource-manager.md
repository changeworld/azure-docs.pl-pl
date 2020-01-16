---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 2c5312c2e0a7121237c4d11197d90a2a5638557c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020863"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Maszyny wirtualne na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na region. |25 000 na region. |
| Całkowita liczba rdzeni maszyn wirtualnych na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. | Skontaktuj się z pomocą techniczną. |
| Łączna liczba rdzeni maszyn wirtualnych platformy Azure na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. | Skontaktuj się z pomocą techniczną. |
| Maszyny wirtualne na serię, takie jak Dv2 i F, rdzenie na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. | Skontaktuj się z pomocą techniczną. |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Nieograniczona liczba. |Nieograniczona liczba. |
| [Konta magazynu](../articles/storage/common/storage-account-create.md) na region na subskrypcję |250 |250 |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |980 |
| [Zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na subskrypcję |2 000 na region. |2 000 na region. |
| Rozmiar żądania interfejsu API Azure Resource Manager |4 194 304 bajtów. |4 194 304 bajtów. |
| Tagi na subskrypcję<sup>2</sup> |Nieograniczona liczba. |Nieograniczona liczba. |
| Obliczenia unikatowych tagów na subskrypcję<sup>2</sup> | 10 000 | 10 000 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |Nie dotyczy<sup>3</sup> |Nie dotyczy<sup>3</sup> |
| [Grupy koligacji](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na subskrypcję |Nie dotyczy<sup>3</sup> |Nie dotyczy<sup>3</sup> |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>4</sup> | 800 |

<sup>1</sup> Domyślne limity różnią się w zależności od typu kategorii oferty, na przykład bezpłatnej wersji próbnej i płatnej zgodnie z rzeczywistym użyciem oraz według serii, takich jak Dv2, F i G. Na przykład domyślna wartość dla subskrypcji Umowa Enterprise to 350.

<sup>2</sup> Możesz zastosować nieograniczoną liczbę tagów na subskrypcję. Liczba tagów dla zasobu lub grupy zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw tagów i wartości](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 10 000.  

<sup>3</sup> Te funkcje nie są już wymagane w przypadku grup zasobów platformy Azure i Menedżer zasobów.

<sup>4</sup> Jeśli osiągnięto limit 800 wdrożeń, Usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie subskrypcji, użyj polecenie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [AZ Deployment Delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> Rdzenie maszyn wirtualnych mają limit regionalny dla regionu. Mają również limit dla regionalnych serii dla poszczególnych rozmiarów, takich jak Dv2 i F. Limity te są wykonywane osobno. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja może wdrażać 30 maszyn wirtualnych a1 lub 30 D1 maszyn wirtualnych albo kombinację tych dwóch, aby nie przekroczyć całkowitej liczby 30 rdzeni. Przykładem kombinacji są 10 maszyn wirtualnych a1 i 20 maszyn wirtualnych.  
> <!-- -->
> 
> 

