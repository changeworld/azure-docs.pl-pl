---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437018"
---
|  | **Prywatna komunikacja równorzędna** | **Komunikacja równorzędna firmy Microsoft** |  **Publicznej komunikacji równorzędnej** (uznane za przestarzałe w obwodów) |
| --- | --- | --- | --- |
| **Maksymalna liczba. prefiksy # obsługiwane na połączenie komunikacji równorzędnej** |4000 domyślnie 10 000 operacji przy użyciu usługi ExpressRoute Premium |200 |200 |
| **Obsługiwane zakresów adresów IP** |Dowolny prawidłowy adres IP w sieci WAN. |Publiczne adresy IP posiadane przez Ciebie lub dostawcą łączności. |Publiczne adresy IP posiadane przez Ciebie lub dostawcą łączności. |
| **JAKO liczba wymagań** |Prywatne i publiczne numery AS. Musi być właścicielem publicznie jako liczbę, jeśli zdecydujesz się go użyć. |Prywatne i publiczne numery AS. Jednak musisz udowodnić, że własność publicznych adresów IP. |Prywatne i publiczne numery AS. Jednak musisz udowodnić, że własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| IPv4 |  IPv4, IPv6 | IPv4 |
| **Routing adresów IP interfejsu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP zarejestrowane na Ciebie w rejestrów routingu. |Publiczne adresy IP zarejestrowane na Ciebie w rejestrów routingu. |
| **Skrót MD5 pomocy technicznej** |Tak |Tak |Tak |