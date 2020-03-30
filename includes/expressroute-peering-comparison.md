---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75437018"
---
|  | **Prywatna komunikacja równorzędna** | **Komunikacja równorzędna firmy Microsoft** |  **Publiczne peering** (przestarzałe dla nowych obwodów) |
| --- | --- | --- | --- |
| **Max. # prefiksy obsługiwane na komunikację równorzędnej** |Domyślnie 4000, 10 000 z pakietem ExpressRoute Premium |200 |200 |
| **Obsługiwane zakresy adresów IP** |Każdy prawidłowy adres IP w sieci WAN. |Publiczne adresy IP należące do Ciebie lub Twojego dostawcy łączności. |Publiczne adresy IP należące do Ciebie lub Twojego dostawcy łączności. |
| **Wymagania dotyczące numeru AS** |Prywatne i publiczne numery AS. Musisz posiadać publiczny numer AS, jeśli zdecydujesz się go użyć. |Prywatne i publiczne numery AS. Należy jednak udowodnić własność publicznych adresów IP. |Prywatne i publiczne numery AS. Należy jednak udowodnić własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| Protokół IPv4 |  IPv4, IPv6 | Protokół IPv4 |
| **Adresy IP interfejsu routingu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP zarejestrowane w rejestrach routingu. |Publiczne adresy IP zarejestrowane w rejestrach routingu. |
| **Pomoc techniczna pomocy technicznej md5 hash** |Tak |Tak |Tak |