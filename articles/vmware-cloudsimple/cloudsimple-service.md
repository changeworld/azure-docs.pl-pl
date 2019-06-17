---
title: Rozwiązań VMware przez CloudSimple - usługi
description: W tym artykule opisano omówienie CloudSimple usługi i pojęcia.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358111"
---
# <a name="cloudsimple-service-overview"></a>Omówienie usługi CloudSimple

Usługa CloudSimple umożliwia korzystanie z platformy Azure VMware Solution by CloudSimple.  Tworzenie usługi pozwala kupić węzły rezerwa węzły oraz tworzenia chmur prywatnych.  Utworzysz usługę CloudSimple w każdym regionie platformy Azure, w których usługa CloudSimple jest dostępna.  Usługa zawiera definicję sieci krawędź Azure VMware Solution by CloudSimple.  Sieć usługi edge obsługuje usług, które obejmują sieć VPN, usługi ExpressRoute i łącznością internetową, aby z Chmurami prywatnymi.

## <a name="gateway-subnet"></a>Podsieć bramy

Podsieć bramy jest wymagany na usługę CloudSimple i jest unikatowa w regionie, w którym zostanie utworzona. Podsieć bramy jest używany podczas tworzenia sieci krawędź i wymaga o rozmiarze/28 blok CIDR.  Przestrzeń adresową podsieci bramy musi być unikatowa. Nie nakładać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z CloudSimple obejmują sieciami lokalnymi i siecią wirtualną platformy Azure.  Nie można usunąć podsieci bramy, po jego utworzeniu.  Podsieć bramy jest usuwany, gdy usługa zostanie usunięty.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [utworzyć usługę CloudSimple na platformie Azure](quickstart-create-cloudsimple-service.md)