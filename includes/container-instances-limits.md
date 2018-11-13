---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572503"
---
| Zasób | Limit domyślny |
| --- | :--- |
| Grupy kontenerów na [subskrypcję](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Liczba kontenerów na grupę kontenerów | 60 |
| Liczba woluminów na grupę kontenerów | 20 |
| Porty na adres IP | 5 |
| Utworzenia kontenera na godzinę |300<sup>1</sup> |
| Utworzenia kontenera na 5 minut | 100<sup>1</sup> |
| Usunięcia kontenera na godzinę | 300<sup>1</sup> |
| Usunięcia kontenera na 5 minut | 100<sup>1</sup> |
| Wiele kontenerów na grupę kontenerów | Tylko system Linux<sup>2</sup> |
| Woluminy usługi Azure Files | Tylko system Linux<sup>2</sup> |
| Woluminy GitRepo | Tylko system Linux<sup>2</sup> |
| Woluminy tajne | Tylko system Linux<sup>2</sup> |

<sup>1</sup> Aby zażądać zwiększenia limitu, utwórz [żądanie pomocy technicznej platformy Azure][azure-support].<br />
<sup>2</sup> Jest planowane wprowadzenie pomocy technicznej systemu Windows dla tej funkcji.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
