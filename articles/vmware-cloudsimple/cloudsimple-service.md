---
title: Azure VMware Solutions (Automatyczna synchronizacja) — usługa
description: Zawiera omówienie usługi i koncepcji dotyczącej automatycznej synchronizacji.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024961"
---
# <a name="avs-service-overview"></a>Przegląd usługi automatycznej synchronizacji

Usługa automatycznej synchronizacji umożliwia korzystanie z rozwiązania VMware na platformie Azure przez automatyczna synchronizacja. Tworzenie usługi umożliwia kupowanie węzłów, rezerwowanie węzłów oraz tworzenie chmur prywatnych. Usługę automatycznej synchronizacji można utworzyć w każdym regionie świadczenia usługi Azure, w którym jest dostępna usługa automatycznej synchronizacji. Usługa definiuje sieć brzegową rozwiązania VMware firmy Azure przez automatyczna synchronizacja. Sieć brzegowa obsługuje usługi, które obejmują sieci VPN, ExpressRoute i łączność internetową z chmurami prywatnymi do automatycznej synchronizacji.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagana dla każdej usługi automatycznej synchronizacji i jest unikatowa dla regionu, w którym została utworzona. Podsieć bramy jest używana podczas tworzenia sieci brzegowej i wymaga bloku/28 CIDR. Przestrzeń adresowa podsieci bramy musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem automatycznej synchronizacji. Sieci, które komunikują się z funkcją automatycznej synchronizacji, obejmują sieci lokalne i usługę Azure Virtual Network. Nie można usunąć podsieci bramy po jej utworzeniu. Podsieć bramy jest usuwana po usunięciu usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć usługę automatycznej synchronizacji na platformie Azure](quickstart-create-cloudsimple-service.md).
