---
title: Rozwiązanie Azure VMware firmy CloudSimple — publiczny adres IP
description: Dowiedz się więcej o publicznych adresach IP i ich korzyściach z rozwiązania Azure VMware firmy CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024980"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Omówienie publicznego adresu IP cloudsimple

Publiczny adres IP umożliwia zasobom internetowym komunikowanie się przychodzące do zasobów chmury prywatnej przy prywatnym adresie IP. Prywatny adres IP to maszyna wirtualna lub moduł równoważenia obciążenia oprogramowania w centrum vCenter private cloud. Publiczny adres IP umożliwia udostępnienie usług uruchomionych w chmurze prywatnej w Internecie.

Publiczny adres IP jest dedykowany do prywatnego adresu IP, dopóki go nie zdejmiesz. Publiczny adres IP można przypisać tylko do jednego prywatnego adresu IP.

Zasób skojarzony z publicznym adresem IP zawsze używa publicznego adresu IP do uzyskiwania dostępu do Internetu. Domyślnie tylko wychodzący dostęp do Internetu jest dozwolony na publicznym adresie IP.  Ruch przychodzący w publicznym adresie IP jest odrzucany.  Aby zezwolić na ruch przychodzący, utwórz regułę zapory dla publicznego adresu IP do określonego portu.

## <a name="benefits"></a>Korzyści

Używanie publicznego adresu IP do komunikacji przychodzącej zapewnia:

* Zapobieganie atakom typu "rozproszona odmowa usługi" (DDoS). Ta ochrona jest automatycznie włączona dla publicznego adresu IP.
* Zawsze włączone monitorowanie ruchu i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. Te mechanizmy obronne są takie same mechanizmy obronne używane przez usługi online firmy Microsoft.
* Cała skala globalnej sieci platformy Azure. Sieć może służyć do dystrybucji i ograniczania ruchu ataków w różnych regionach.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przydzielić publiczny adres IP](public-ips.md)