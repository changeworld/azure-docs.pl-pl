---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o wirtualnej skalowalnej gałęzi sieci WAN, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379393"
---
# <a name="about-azure-virtual-wan"></a>Informacje o wirtualnej sieci WAN platformy Azure

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która zapewnia zoptymalizowaną i zautomatyzowaną łączność z gałęzią z usługami i i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako centra, do których można połączyć gałęzie. Korzystając ze szkieletu platformy Azure, można również łączyć gałęzie i korzystać z połączeń między sieciami wirtualnymi. Mamy listę partnerów, którzy obsługują automatyzację łączności przy użyciu wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

Wirtualna sieć WAN platformy Azure oferuje wiele usług łączności w chmurze platformy Azure, takich jak sieć VPN typu lokacja-lokacja, Sieć VPN użytkownika (punkt-lokacja) i ExpressRoute w pojedynczy interfejs operacyjny. Połączenie z usługą Azure sieci wirtualnych jest nawiązywane przy użyciu połączeń sieci wirtualnej. Umożliwia ona [globalną architekturę sieci tranzytowej](virtual-wan-global-transit-network-architecture.md) opartą na klasycznym modelu łączności typu Hub i satelity, gdzie sieć hostowana w chmurze umożliwia komunikację przechodnią między punktami końcowymi, które mogą być dystrybuowane między różnymi typami "szprych".

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki podgląd łączności sieciowej w wirtualnej sieci WAN platformy Azure. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania łączności w centrum i szprychie:** Automatyzowanie konfiguracji lokacja-lokacja i łączności między lokacjami lokalnymi i centrum platformy Azure.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne Rozwiązywanie problemów:** Możesz zobaczyć kompleksowy przepływ na platformie Azure, a następnie użyć tych informacji do podjęcia wymaganych akcji.

## <a name="basicstandard"></a>Wirtualne sieci WAN w warstwach Podstawowa i Standardowa

Istnieją dwa typy wirtualnych sieci WAN: podstawowa i standardowa. W poniższej tabeli przedstawiono dostępne konfiguracje dla każdego typu.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Aby uzyskać instrukcje dotyczące uaktualniania wirtualnej sieci WAN, zobacz [uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa](upgrade-virtual-wan.md).

## <a name="architecture"></a>Będąc

Aby uzyskać informacje o architekturze wirtualnej sieci WAN i sposobach migracji do wirtualnej sieci WAN, zobacz następujące artykuły:

* [Architektura wirtualnej sieci WAN](migrate-from-hub-spoke-topology.md)
* [Globalna architektura sieci tranzytowej](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne między wirtualną siecią WAN nie komunikują się ze sobą.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usługi umożliwiające łączność. Z sieci lokalnej (vpnsite) można nawiązać połączenie z VPN Gateway wewnątrz koncentratora wirtualnego, podłączyć obwody usługi ExpressRoute do koncentratora wirtualnego, a nawet podłączyć użytkowników mobilnych do bramy typu punkt-lokacja w koncentratorze wirtualnym. Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku korzystania z wirtualnej sieci WAN nie można utworzyć połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego należy utworzyć połączenie lokacja-lokacja z centrum. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną.

* **(Wersja zapoznawcza) połączenia** między centrami — koncentratory są połączone ze sobą w wirtualnej sieci WAN. Oznacza to, że gałąź, użytkownik lub Sieć wirtualna połączona z koncentratorem lokalnym może komunikować się z inną gałęzią lub siecią wirtualną przy użyciu pełnej architektury sieci podłączonych centrów. Można także połączyć sieci wirtualnych w ramach przechodzenia przez centrum wirtualne, a także sieci wirtualnych między centrami przy użyciu platformy połączonej z centrum-centrum.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualnej sieci WAN**

  * **Lokacja:** Ten zasób jest używany tylko w przypadku połączeń lokacja-lokacja. Zasób lokacji to **vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="connectivity"></a>Typy łączności

Wirtualna sieć WAN umożliwia łączenie następujących typów: sieci VPN typu lokacja-lokacja, sieci VPN użytkownika (punkt-lokacja) i ExpressRoute.

### <a name="s2s"></a>Połączenia sieci VPN typu lokacja-lokacja

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Podczas tworzenia połączenia sieci VPN typu lokacja-lokacja można korzystać z dostępnego partnera. Jeśli nie chcesz korzystać z partnera, możesz skonfigurować połączenie ręcznie. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Przepływ pracy wirtualnego partnera sieci WAN

Podczas pracy z partnerem wirtualnego sieci WAN przepływ pracy to:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.

#### <a name="partners"></a>Partnerzy dla połączeń wirtualnych sieci WAN między lokacjami

Listę dostępnych partnerów i lokalizacji można znaleźć w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

### <a name="uservpn"></a>Połączenia sieci VPN użytkownika (punkt-lokacja)

Możesz połączyć się z zasobami na platformie Azure za pośrednictwem protokołu IPsec/IKE (IKEv2) lub połączenia OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN na komputerze klienckim. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Połączenia ExpressRoute
ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Miejsce

Informacje o lokalizacji znajdują się w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
