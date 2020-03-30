---
title: Zagadnienia dotyczące sieci — dedykowany moduł HSM platformy Azure | Dokumenty firmy Microsoft
description: Omówienie zagadnień dotyczących sieci mających zastosowanie do wdrożeń dedykowanego modułu HSM platformy Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881290"
---
# <a name="azure-dedicated-hsm-networking"></a>Dedykowana sieć HSM platformy Azure

Dedykowany moduł HSM platformy Azure wymaga wysoce bezpiecznego środowiska sieciowego. Dotyczy to niezależnie od tego, czy jest z chmury platformy Azure z powrotem do środowiska IT klienta (lokalnie), przy użyciu aplikacji rozproszonych lub scenariuszy wysokiej dostępności. Usługa Azure Networking zapewnia to i istnieją cztery odrębne obszary, które należy rozwiązać.

- Tworzenie urządzeń HSM wewnątrz sieci wirtualnej (VNet) na platformie Azure
- Łączenie zasobów lokalnych z zasobami chmurowymi w celu konfiguracji i zarządzania urządzeniami HSM
- Tworzenie i łączenie sieci wirtualnych dla zasobów aplikacji łączących się i urządzeń HSM
- Łączenie sieci wirtualnych w różnych regionach w celu komunikacji wzajemnej, a także w celu umożliwienia scenariuszy wysokiej dostępności

## <a name="virtual-network-for-your-dedicated-hsms"></a>Sieć wirtualna dla dedykowanych modułów HSM

Dedykowane moduły HSM są zintegrowane z siecią wirtualną i umieszczane w sieci prywatnej klientów na platformie Azure. Umożliwia to dostęp do urządzeń z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.  
Aby uzyskać więcej informacji na temat integrowania usług platformy Azure z siecią wirtualną i możliwości, które zapewnia, zobacz dokumentację [sieci wirtualnej dla platformy Azure.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Sieci wirtualne

Przed inicjowania obsługi administracyjnej dedykowane urządzenie HSM, klienci będą najpierw trzeba utworzyć sieć wirtualną na platformie Azure lub użyć jednego, który już istnieje w subskrypcji klientów. Sieć wirtualna definiuje obwód zabezpieczeń dla dedykowanego urządzenia HSM. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentację sieci wirtualnej](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsieci

Podsieci segmentują sieć wirtualną na oddzielne przestrzenie adresowe, z których można dzielić zasoby platformy Azure, które w nich umieszczasz. Dedykowane moduły HSM są wdrażane w podsieci w sieci wirtualnej. Każde dedykowane urządzenie HSM wdrożone w podsieci klienta otrzyma prywatny adres IP z tej podsieci. Podsieć, w której jest wdrażane urządzenie HSM, musi zostać jawnie delegowana do usługi: Microsoft.HardwareSecurityModules/dedicatedHSMs. Daje to pewne uprawnienia do usługi HSM do wdrożenia w podsieci. Delegowanie do dedykowanych modułów HSM nakłada pewne ograniczenia zasad na podsieć. Sieciowe grupy zabezpieczeń (NSG) i trasy zdefiniowane przez użytkownika (UDR) nie są obecnie obsługiwane w podsieciach delegowanych. W rezultacie po delegowaniu podsieci do dedykowanych modułów HSM może służyć tylko do wdrażania zasobów modułu HSM. Wdrożenie innych zasobów klienta do podsieci zakończy się niepowodzeniem.


### <a name="expressroute-gateway"></a>Brama usługi ExpressRoute

Wymaganiem bieżącej architektury jest konfiguracja bramy ER w podsieci klientów, w której należy umieścić urządzenie HSM, aby umożliwić integrację urządzenia HSM z platformą Azure. Tej bramy ER nie można wykorzystać do łączenia lokalizacji lokalnych z urządzeniami HSM klientów na platformie Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Łączenie lokalnego rozwiązania IT z platformą Azure

Podczas tworzenia zasobów opartych na chmurze jest to typowe wymaganie dla połączenia prywatnego z powrotem do lokalnych zasobów IT. W przypadku dedykowanego modułu HSM będzie to głównie dla oprogramowania klienta modułu HSM do konfigurowania urządzeń HSM, a także dla działań, takich jak kopie zapasowe i ściąganie dzienników z modułów HSM do analizy. Kluczowym punktem decyzyjnym jest tutaj charakter połączenia, ponieważ istnieją opcje.  Najbardziej elastyczną opcją jest sieć VPN typu lokacja lokacja, ponieważ prawdopodobnie będzie wiele zasobów lokalnych, które wymagają bezpiecznej komunikacji z zasobami (w tym modułami HSM) w chmurze platformy Azure. Będzie to wymagało organizacji klienta, aby mieć urządzenie sieci VPN w celu ułatwienia połączenia. Połączenie sieci VPN typu punkt-lokacja może być używane, jeśli lokalnie istnieje tylko jeden punkt końcowy, taki jak pojedyncza stacja robocza administracyjna.
Aby uzyskać więcej informacji na temat opcji łączności, zobacz [Opcje planowania bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Obecnie usługa ExpressRoute nie jest opcją połączenia z zasobami lokalnymi. Należy również zauważyć, że brama usługi ExpressRoute używana w sposób opisany powyżej nie jest przeznaczony do połączeń z infrastrukturą lokalną.

### <a name="point-to-site-vpn"></a>Sieć VPN typu punkt-lokacja

Wirtualna sieć prywatna typu "punkt-lokacja" jest najprostszą formą bezpiecznego połączenia z jednym punktem końcowym lokalnie. Może to być istotne, jeśli zamierzasz mieć tylko jedną stację roboczą administracyjną dla dedykowanych modułów HSM opartych na platformie Azure.

### <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Wirtualna sieć prywatna lokacja-lokacja umożliwia bezpieczną komunikację między dedykowanymi modułami HSM opartymi na platformie Azure a lokalnymi rozwiązaniami IT. Powodem jest posiadanie funkcji tworzenia kopii zapasowych dla lokalnego modułu HSM i wymaga połączenia między nimi do uruchamiania kopii zapasowej.

## <a name="connecting-virtual-networks"></a>Łączenie sieci wirtualnych

Typowa architektura wdrażania dedykowanego modułu HSM rozpocznie się od jednej sieci wirtualnej i odpowiedniej podsieci, w której tworzone i aprowizowani są urządzenia HSM. W tym samym regionie mogą istnieć dodatkowe sieci wirtualne i podsieci dla składników aplikacji, które będą korzystać z dedykowanego modułu HSM. Aby umożliwić komunikację między tymi sieciami, używamy komunikacji równorzędnej sieci wirtualnej.

### <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Gdy istnieje wiele sieci wirtualnych w regionie, które muszą uzyskać dostęp do zasobów innych, virtual network peering może służyć do tworzenia bezpiecznych kanałów komunikacji między nimi.  Komunikacja równorzędna sieci wirtualnej zapewnia nie tylko bezpieczną komunikację, ale także zapewnia połączenia o małym opóźnieniu i dużej przepustowości między zasobami na platformie Azure.

![komunikacja równorzędna w sieci](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Łączenie się w regionach platformy Azure

Urządzenia HSM mają możliwość, za pośrednictwem bibliotek oprogramowania, aby przekierować ruch do alternatywnego modułu HSM. Przekierowanie ruchu jest przydatne w przypadku awarii urządzeń lub utraty dostępu do urządzenia. Scenariusze awarii na poziomie regionalnym można złagodzić, wdrażając moduły HSM w innych regionach i włączając komunikację między sieciami wirtualnymi w różnych regionach.

### <a name="cross-region-ha-using-vpn-gateway"></a>Wiele ha regionu przy użyciu bramy sieci VPN

W przypadku aplikacji rozproszonych globalnie lub dla regionalnych scenariuszy pracy awaryjnej o wysokiej dostępności jest wymagane połączenie sieci wirtualnych między regionami. Dzięki dedykowanemu modułowi HSM platformy Azure można osiągnąć wysoką dostępność przy użyciu bramy sieci VPN, która zapewnia bezpieczny tunel między dwiema sieciami wirtualnymi. Aby uzyskać więcej informacji na temat połączeń sieci wirtualnej do sieci wirtualnej przy użyciu bramy sieci VPN, zobacz artykuł ["Co to jest brama sieci VPN?For](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V) more information on Vnet-to-Vnet connections using VPN Gateway, see the article titled What is VPN Gateway?

> [!NOTE]
> Globalna komunikacja równorzędna sieci wirtualnej nie jest dostępna w scenariuszach łączności między regionami z dedykowanymi modułami HSM w tej chwili i zamiast tego należy użyć bramy sieci VPN. 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Następne kroki

- [Często zadawane pytania](faq.md)
- [Możliwości obsługi](supportability.md)
- [Wysoka dostępność](high-availability.md)
- [Zabezpieczenia fizyczne](physical-security.md)
- [Monitorowania](monitoring.md)
- [Architektura wdrażania](deployment-architecture.md)