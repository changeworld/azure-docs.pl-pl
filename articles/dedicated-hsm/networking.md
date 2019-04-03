---
title: Zagadnienia dotyczące sieci — sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Omówienie sieci zagadnienia, które dotyczą wdrożeń platformy Azure w wersji dedykowanej w module HSM
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: d6672827a87fbb949237d51310f1a9febc192ff2
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886351"
---
# <a name="azure-dedicated-hsm-networking"></a>Sieci platformy Azure w wersji dedykowanej przez moduł HSM

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń wymaga bardzo bezpiecznego środowiska sieciowego. Dotyczy to czy jest poziomu usługi Azure cloud do klienta środowiska informatycznego (lokalnie), za pomocą aplikacji rozproszonych lub scenariuszy wysokiej dostępności. Sieć Azure udostępnia to wiąże się z czterech różnych obszarów, które muszą być kierowane.

- Tworzenie urządzenia sprzętowego modułu zabezpieczeń w Twojej sieci wirtualnej (VNet) na platformie Azure
- Połączenie środowiska lokalnego do zasobów w chmurze w celu skonfigurowania i zarządzanie urządzeniami przez moduł HSM
- Tworzenie i łączenie sieci wirtualnych między łączące zasobów aplikacji i urządzeń przez moduł HSM
- Łączenie sieci wirtualnych między regionami komunikacji między, a także umożliwia realizację scenariuszy wysokiej dostępności

## <a name="virtual-network-for-your-dedicated-hsms"></a>Sieć wirtualna dla sprzętowych modułów zabezpieczeń użytkownika w wersji dedykowanej

Dedykowanych sprzętowych modułów zabezpieczeń są zintegrowane w sieci wirtualnej i umieścić we własnej sieci prywatnej klientów na platformie Azure. Dzięki temu dostęp do urządzeń z maszyn wirtualnych lub zasobów obliczeniowych w sieci wirtualnej.  
Aby uzyskać więcej informacji na temat integracji usług platformy Azure w sieci wirtualnej i możliwości zapewnia, zobacz [sieć wirtualna dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md) dokumentacji.

### <a name="virtual-networks"></a>Sieci wirtualne

Przed zainicjowaniem obsługi administracyjnej urządzeń w wersji dedykowanej przez moduł HSM, klienci będą najpierw musisz utworzyć sieć wirtualną na platformie Azure lub użyj jednego, który już istnieje w subskrypcji klientów. Sieć wirtualna definiuje obwodu zabezpieczeń dla urządzenia HSM w wersji dedykowanej. Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentacja usługi virtual network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Podsieci

Podsieci sieci wirtualnej na segmenty na oddzielne przestrzenie adresowe można używać przez zasoby platformy Azure, które można umieścić w nich. Dedykowanych sprzętowych modułów zabezpieczeń są wdrażane w podsieci sieci wirtualnej. Każdego urządzenia w wersji dedykowanej przez moduł HSM, które zostało wdrożone w podsieci klienta otrzyma prywatny adres IP z tej podsieci. Podsieci, w której jest wdrażany urządzenia HSM musi jawnie można delegować do usługi: Microsoft.HardwareSecurityModules/dedicatedHSMs. Spowoduje to przydzielenie określonych uprawnień do usługi przez sprzętowy moduł zabezpieczeń w przypadku wdrożenia w tej podsieci. Delegowanie do dedykowanych sprzętowych modułów zabezpieczeń nakłada pewne ograniczenia zasad, w tej podsieci. Sieciowe grupy zabezpieczeń (NSG), a trasy zdefiniowane przez użytkownika (Udr) aktualnie nie są obsługiwane w podsieciach delegowanego. W rezultacie po podsieci jest delegowane do dedykowanych sprzętowych modułów zabezpieczeń, może tylko służyć do wdrażania zasobów przez moduł HSM. Zakończy się niepowodzeniem wdrożenia innych zasobów klientów w tej podsieci.


### <a name="expressroute-gateway"></a>Brama usługi ExpressRoute

Wymaganie bieżącej architektury jest konfiguracji bramy usługi ER w podsieci klientów, gdzie urządzenia HSM musi być umieszczona umożliwia integrację urządzenia sprzętowego modułu zabezpieczeń na platformie Azure. Ta brama ER nie mogą być wykorzystywane do łączenia z lokalizacjami lokalnymi na urządzeniach przez moduł HSM klientów na platformie Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Łączenie z lokalnymi IT na platformie Azure

Podczas tworzenia zasobów w chmurze, jest typowy potrzebę prywatne połączenie lokalne zasoby IT. W przypadku dedykowanego sprzętowego modułu zabezpieczeń są głównie to oprogramowanie klienckie przez moduł HSM skonfigurować urządzenia sprzętowego modułu zabezpieczeń oraz działań, takich jak tworzenie kopii zapasowych i ściąganie dzienników z sprzętowych modułów zabezpieczeń dla analizy. Punkt kluczowe decyzje, w tym miejscu to rodzaj połączenia, jako dostępne są opcje.  Najbardziej elastyczna opcja to sieci VPN typu lokacja-lokacja, ponieważ prawdopodobnie będą wielu zasobów w środowisku lokalnym, które wymagają bezpiecznej komunikacji z zasobami (w tym sprzętowych modułów zabezpieczeń) w chmurze platformy Azure. Będzie to wymagało organizacji klienta, aby urządzenie sieci VPN, aby ułatwić połączenia. Połączenie sieci VPN typu punkt-lokacja może służyć w przypadku tylko pojedynczy punkt końcowy w środowisku lokalnym takich jak pojedynczy administracyjnej stacji roboczej.
Aby uzyskać więcej informacji na temat opcji łączności, zobacz [bramy sieci VPN, opcje planowania](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> W tej chwili usługi ExpressRoute nie jest opcją dla połączenia z zasobami lokalnymi. Należy również zauważyć, że brama usługi ExpressRoute używane zgodnie z powyższym opisem, nie dla połączeń z infrastrukturą lokalną.

### <a name="point-to-site-vpn"></a>Sieci VPN typu punkt lokacja

Punkt lokacja wirtualnej sieci prywatnej jest najprostsza forma bezpiecznego połączenia z jednym punktem końcowym w środowisku lokalnym. Może to być istotne, jeśli chcesz mieć tylko jednego administracyjną stację roboczą dla opartych na platformie Azure z dedykowanych sprzętowych modułów zabezpieczeń.

### <a name="site-to-site-vpn"></a>Sieci VPN typu lokacja lokacja

Lokacja lokacja wirtualnej sieci prywatnej zezwala na potrzeby bezpiecznej komunikacji między opartych na platformie Azure w wersji dedykowanej sprzętowych modułów zabezpieczeń oraz lokalnej usługi IT. Przyczyna, w tym celu występują kopii zapasowych funkcji w ramach sprzętowego modułu zabezpieczeń w środowisku lokalnym i konieczności połączenia między tymi dwoma uruchamiania kopii zapasowej.

## <a name="connecting-virtual-networks"></a>Łączenie sieci wirtualnych

Typowe wdrożenie architektury przez sprzętowy moduł zabezpieczeń w wersji dedykowanej rozpocznie się za pomocą jednej sieci wirtualnej i odpowiednich podsieci, w którym są tworzone i aprowizacji urządzenia sprzętowego modułu zabezpieczeń. W tym samym regionie może również występować dodatkowe sieci wirtualne i podsieci dla składników aplikacji, które czyniłyby, Użyj sprzętowego modułu zabezpieczeń w wersji dedykowanej. Aby umożliwić komunikację między tymi sieciami, używamy komunikacji równorzędnej sieci wirtualnej.

### <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

W przypadku wielu sieci wirtualnych w regionie, które wymagają dostępu do zasobów siebie nawzajem komunikacji równorzędnej sieci wirtualnej może służyć do tworzenia kanałów bezpiecznej komunikacji między nimi.  Wirtualne sieci równorzędne zapewnia nie tylko bezpiecznej komunikacji, ale zapewnia również o małych opóźnieniach i dużej przepustowości połączenia między zasobami na platformie Azure.

![Komunikacja równorzędna w sieci](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Łączenie różnych regionach platformy Azure

Urządzenia sprzętowego modułu zabezpieczeń mają możliwość, za pomocą biblioteki oprogramowania, aby przekierować ruch do alternatywnego modułu HSM. Przekierowywanie ruchu jest przydatne, jeśli urządzenia nie lub dostępu do urządzenia zostaną utracone. Scenariusze regionalnym poziomie awarii, można zminimalizować przez wdrożenie modułów HSM w innych regionach i włączenie komunikacji między sieciami wirtualnymi w różnych regionach.

### <a name="cross-region-ha-using-vpn-gateway"></a>Między regionami wysokiej dostępności przy użyciu bramy sieci VPN

Dla aplikacji dystrybuowanych globalnie lub scenariuszy rozwiązania regionalnej pracy awaryjnej wysokiej dostępności jest wymagany do łączenia sieci wirtualnych między regionami. Za pomocą platformy Azure w wersji dedykowanej przez moduł HSM wysoką dostępność można osiągnąć za pomocą bramy sieci VPN, która udostępnia bezpieczny tunel między dwiema sieciami wirtualnymi. Aby uzyskać więcej informacji na temat połączeń sieci wirtualnej między sieciami wirtualnymi przy użyciu bramy sieci VPN, zobacz artykuł [co to jest usługa VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Globalne wirtualne sieci równorzędne jest niedostępna w łączności między regionami, które scenariusze za pomocą dedykowanych sprzętowych modułów zabezpieczeń, w tym czasie i bramy sieci VPN należy użyć. 

![globalne wirtualne sieci](media/networking/global-vnet.png)

## <a name="next-steps"></a>Kolejne kroki

- [Często zadawane pytania](faq.md)
- [Możliwości obsługi](supportability.md)
- [Wysoka dostępność](high-availability.md)
- [Zabezpieczenia fizyczne](physical-security.md)
- [Monitorowanie](monitoring.md)
- [Architektura wdrożenia](deployment-architecture.md)