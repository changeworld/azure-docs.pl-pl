---
title: Rozwiązanie VMware według usługi CloudSimple
description: Opisuje Omówienie usługi i koncepcji CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812513"
---
# <a name="cloudsimple-service-overview"></a>Przegląd usługi CloudSimple

Za pomocą usługi CloudSimple można użyć rozwiązania Azure VMware przez CloudSimple. Po utworzeniu usługi można zainicjować obsługę węzłów, zarezerwować węzły i utworzyć chmurę prywatną. Usługę CloudSimple można utworzyć w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa CloudSimple. Usługa definiuje sieć brzegową rozwiązania Azure VMware przez CloudSimple. Sieć brzegowa obsługuje usługi, takie jak sieci VPN, Azure ExpressRoute i łączność internetowa z chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla usługi CloudSimple i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i sieci wirtualne platformy Azure. Nie można usunąć podsieci bramy po jej utworzeniu. Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md).
