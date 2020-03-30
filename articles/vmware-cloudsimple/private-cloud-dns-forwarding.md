---
title: Rozwiązanie Azure VMware — przekazywanie dns z chmury prywatnej do lokalnej
description: W tym artykule opisano, jak włączyć serwer DNS cloudsimple private cloud do przesyłania dalej wyszukiwania zasobów lokalnych
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961129"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Włączanie serwerów DNS chmury prywatnej CloudSimple do przekazywania wyszukiwania DNS zasobów lokalnych do serwerów DNS

Serwery DNS w chmurze prywatnej mogą przesyłać do serwera DNS wyszukiwanie dns dla dowolnych zasobów lokalnych.  Włączenie wyszukiwania umożliwia private cloud vSphere składników wyszukać wszystkie usługi uruchomione w środowisku lokalnym i komunikować się z nimi przy użyciu w pełni kwalifikowanych nazw domen (FQDN).

## <a name="scenarios"></a>Scenariusze 

Przekazywanie wyszukiwania DNS lokalnego serwera DNS umożliwia korzystanie z chmury prywatnej w następujących scenariuszach:

* Używanie chmury Private Cloud jako konfiguracji odzyskiwania po awarii dla lokalnego rozwiązania VMware
* Używanie lokalnej usługi Active Directory jako źródła tożsamości dla usługi Private Cloud vSphere
* Używanie hcx do migracji maszyn wirtualnych z lokalnego do prywatnego chmury

## <a name="before-you-begin"></a>Przed rozpoczęciem

Połączenie sieciowe musi być obecne z sieci private cloud do sieci lokalnej, aby przekierowanie DNS działało.  Połączenie sieciowe można skonfigurować za pomocą:

* [Łączenie się z lokalnego do CloudSimple przy użyciu usługi ExpressRoute](on-premises-connection.md)
* [Konfigurowanie bramy sieci VPN między lokacjami](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

Porty zapory muszą być otwarte w tym połączeniu, aby przekierowanie DNS działało.  Używane porty to port TCP 53 lub UDP 53.

> [!NOTE]
> Jeśli korzystasz z sieci VPN lokacja lokacja, lokalna podsieć serwera DNS musi zostać dodana jako część prefiksów lokalnych.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Żądanie przekierowania dns z chmury prywatnej do lokalnego

Aby włączyć przekazywanie dns z chmury prywatnej do lokalnej, prześlij [żądanie pomocy technicznej,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)podając następujące informacje.

* Typ problemu: **Techniczne**
* Subskrypcja: **subskrypcja, w której wdrożono usługę CloudSimple**
* Usługa: **Rozwiązanie VMware by CloudSimple**
* Typ problemu: **Poradnik lub Jak...**
* Podtyp problemu: **Potrzebujesz pomocy z NW**
* Podaj nazwę domeny lokalnej w okienku szczegółów.
* Podaj listę lokalnych serwerów DNS, do których wyszukiwanie będzie przekazywane z chmury prywatnej w okienku szczegółów.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o konfiguracji zapory lokalnej](on-premises-firewall-configuration.md)
* [Lokalna konfiguracja serwera DNS](on-premises-dns-setup.md)
