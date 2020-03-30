---
title: Rozwiązanie Azure VMware według CloudSimple — usługa
description: Zawiera omówienie usługi CloudSimple i pojęć.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024961"
---
# <a name="cloudsimple-service-overview"></a>Omówienie usługi CloudSimple

Usługa CloudSimple umożliwia korzystanie z rozwiązania Azure VMware przez CloudSimple.  Tworzenie usługi umożliwia zakup węzłów, zarezerwowanie węzłów i tworzenie chmur prywatnych.  Utwórz cloudsimple usługi w każdym regionie platformy Azure, gdzie usługa CloudSimple jest dostępna. Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple. Sieć brzegowa obsługuje usługi obejmujące sieć VPN, usługę ExpressRoute i łączność z Internetem z chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku CIDR /28.  Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z CloudSimple obejmują sieci lokalne i sieci wirtualnej platformy Azure.  Po utworzeniu podsieci bramy nie można usunąć.  Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure.](quickstart-create-cloudsimple-service.md)
