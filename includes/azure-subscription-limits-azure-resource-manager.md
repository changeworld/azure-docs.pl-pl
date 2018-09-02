---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/22/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 08cd67e86666cdc9c9fc1b0f4ef98cf3ea7a156c
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2018
ms.locfileid: "43435618"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Maszyny wirtualne na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |10 000 <sup>1</sup> na region |10 000 na region |
| Całkowita liczba rdzeni maszyn wirtualnych na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region | Kontakt z pomocą techniczną |
| Liczba rdzeni maszyn wirtualnych na serię (Dv2, F itp.) na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region | Kontakt z pomocą techniczną |
| [Współadministratorzy](../articles/billing-add-change-azure-subscription-administrator.md) na subskrypcję |Nieograniczona liczba |Nieograniczona liczba |
| [Konta magazynu](../articles/storage/common/storage-create-storage-account.md) na region na subskrypcję |200 |200<sup>2</sup> |
| [Grupy kontenerów](../articles/azure-resource-manager/resource-group-overview.md) na subskrypcję |980 |980 |
| [Zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na subskrypcję |2000 na region |2000 na region |
| Odczyty interfejsu API usługi Resource Manager |15 000 na godzinę |15 000 na godzinę |
| Zapisy interfejsu API usługi Resource Manager |1200 na godzinę |1200 na godzinę |
| Rozmiar żądania interfejsu API usługi Resource Manager |4 194 304 bajty |4 194 304 bajty |
| Tagi na subskrypcję<sup>3</sup> |bez ograniczeń |bez ograniczeń |
| Obliczenia unikatowych tagów na subskrypcję<sup>3</sup> | 10 000 | 10 000 |
| [Usługi w chmurze](../articles/cloud-services/cloud-services-choose-me.md) na subskrypcję |Nie dotyczy<sup>4</sup> |Nie dotyczy<sup>4</sup> |
| [Grupy koligacji](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) na subskrypcję |Nie dotyczy<sup>4</sup> |Nie dotyczy<sup>4</sup> |
| [Subskrypcja wdrożeń poziomu](../articles/azure-resource-manager/deploy-to-subscription.md) według lokalizacji | 800 | 800 |

<sup>1</sup>Domyślne limity zależą od typu kategorii oferty, na przykład „bezpłatna wersja próbna”, „płatność zgodnie z rzeczywistym użyciem” i serii, na przykład Dv2, F, G itd.

<sup>2</sup>Obejmuje konta magazynu warstwy Standardowa i Premium. Jeśli potrzebujesz więcej niż 200 kont magazynu, wyślij żądanie za pośrednictwem [Pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq/). Zespół usługi Azure Storage przejrzy Twój przypadek biznesowy i może zatwierdzić do 250 kont magazynu.

<sup>3</sup>Liczba tagów, które można zastosować na subskrypcję, nie jest ograniczona. Liczba tagów na zasób lub grupę zasobów jest ograniczona do 15. Usługa Resource Manager zwraca [listę unikatowych nazw i wartości tagów](/rest/api/resources/tags#Tags_List) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal jednak można odnaleźć zasób na podstawie tagu, gdy ich liczba przekracza 10 000.  

<sup>4</sup>Te funkcje nie są już wymagane w przypadku grup zasobów platformy Azure i usługi Azure Resource Manager.

> [!NOTE]
> Trzeba podkreślić, że rdzenie maszyny wirtualnej mają regionalny całkowity limit, a także regionalny limit na rozmiar dla serii (Dv2, F, itp.). Te limity są wymuszane oddzielnie.  Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30.  W przypadku tej subskrypcji dozwolone będzie wdrożenie 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 albo kombinacji obu rodzajów maszyn wirtualnych, pod warunkiem, że liczba rdzeni nie przekroczy 30 (na przykład 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1).  
> <!-- -->
> 
> 

