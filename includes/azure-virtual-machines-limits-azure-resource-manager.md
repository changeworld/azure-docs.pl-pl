---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334734"
---
| Zasób | Limit |
| --- | --- |
| Maszyny wirtualne na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> na region. |
| Całkowita liczba rdzeni maszyn wirtualnych na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| Łączna liczba rdzeni maszyn wirtualnych platformy Azure w punktach na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| Maszyna wirtualna na serię, takich jak Dv2 i F, rdzenie na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> na region. Skontaktuj się z pomocą techniczną, aby zwiększyć limit. |
| [Zestawy dostępności](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) na subskrypcję |2000 na region. |
| Maszyny wirtualne na zestaw dostępności | 200 |
| Certyfikaty na subskrypcję |Nieograniczony<sup>2</sup> |

<sup>1.</sup> Domyślne limity różnią się w zależności od typu kategorii oferty, takiej jak Bezpłatna wersja próbna i Płatność zgodnie z rzeczywistym i pójdę, oraz według serii, takich jak Dv2, F i G. Na przykład wartość domyślna dla subskrypcji umowy Enterprise Agreement to 350.

<sup>2.</sup> W usłudze Azure Resource Manager certyfikaty są przechowywane w magazynie azure key vault. Liczba certyfikatów jest nieograniczona dla subskrypcji. Istnieje limit 1 MB certyfikatów na wdrożenie, który składa się z jednej maszyny Wirtualnej lub zestawu dostępności.

> [!NOTE]
> Rdzenie maszyny wirtualnej mają regionalny limit całkowity. Mają również limit dla regionalnych serii na rozmiar, takich jak Dv2 i F. Limity te są wymuszane oddzielnie. Rozważmy na przykład subskrypcję z całkowitym limitem rdzeni maszyn wirtualnych dla regionu Wschodnie stany USA wynoszącym 30, limitem rdzeni dla serii A wynoszącym 30 i limitem rdzeni dla serii D wynoszącym 30. Ta subskrypcja może wdrożyć 30 maszyn wirtualnych A1 lub 30 maszyn wirtualnych D1 lub kombinację tych dwóch, aby nie przekraczać łącznie 30 rdzeni. Przykładem kombinacji jest 10 maszyn wirtualnych A1 i 20 maszyn wirtualnych D1.  
> <!-- -->
> 
