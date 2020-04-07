---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o wirtualnej sieci WAN zautomatyzowanej skalowalnej łączności między gałęziami, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743104"
---
# <a name="about-azure-virtual-wan"></a>Informacje o wirtualnej sieci WAN platformy Azure

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która łączy wiele funkcji sieciowych, zabezpieczeń i routingu w celu zapewnienia jednego interfejsu operacyjnego. Funkcje te obejmują łączność oddziałową (za pośrednictwem automatyzacji łączności z urządzeń wirtualnego partnera WAN, takich jak SD-WAN lub VPN CPE), łączność sieci VPN typu lokacja-lokacja, łączność vpn z użytkownikami zdalnymi (point-to-site), łączność prywatna (ExpressRoute), łączność wewnątrz chmury (łączność wewnątrz chmury dla sieci wirtualnych), sieć VPN, wzajemne połączenia expressroute, platforma Azure, zapora sieciowa, szyfrowanie łączności prywatnej itp. Nie trzeba mieć wszystkie te przypadki użycia, aby rozpocząć korzystanie z wirtualnej sieci WAN. Możesz po prostu rozpocząć pracę z jednym przypadkiem użycia i dostosować sieć w miarę jej rozwoju. Architektura Wirtualnej sieci WAN to architektura centrum i szprychy z wbudowaną skalą i wydajnością dla oddziałów (urządzeń VPN/SD-WAN), użytkowników (klienci Azure VPN/OpenVPN/IKEv2), obwodów usługi ExpressRoute i sieci wirtualnych. Umożliwia [architekturę globalnej sieci tranzytowej,](virtual-wan-global-transit-network-architecture.md) w której "koncentrator" sieci hostowanych w chmurze umożliwia przejściową łączność między punktami końcowymi, które mogą być rozproszone między różnymi typami "szprych".

Regiony platformy Azure służą jako koncentratory, z którymi można się połączyć. Wszystkie koncentratory są połączone w pełnej siatce w standardowej wirtualnej sieci WAN, co ułatwia użytkownikowi korzystanie z szkieletu firmy Microsoft dla dowolnej (dowolnej szprychy). W przypadku łączności szprychowej z urządzeniami SD-WAN/VPN użytkownicy mogą ręcznie skonfigurować ją w wirtualnej sieci WAN platformy Azure lub użyć rozwiązania partnerskiego Virtual WAN CPE (SD-WAN/VPN) w celu skonfigurowania łączności z platformą Azure. Mamy listę partnerów, którzy obsługują automatyzację łączności (możliwość eksportowania informacji o urządzeniu na platformę Azure, pobierania konfiguracji platformy Azure i ustanawiania łączności) za pomocą wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz [wirtualnych partnerów sieci WAN i lokalizacje](virtual-wan-locations-partners.md) artykułu. 

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki widok na łączność sieciową w wirtualnej sieci WAN platformy Azure. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania łączności w centrum i szprychy:** Automatyzacja konfiguracji lokacji i łączności między lokacjami lokalnymi a centrum platformy Azure.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne rozwiązywanie problemów:** Możesz zobaczyć przepływ end-to-end na platformie Azure, a następnie użyć tych informacji do podjęcia wymaganych akcji.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Podstawowe i standardowe wirtualne sieci WAN

Istnieją dwa typy wirtualnych sieci WAN: podstawowy i standardowy. W poniższej tabeli przedstawiono dostępne konfiguracje dla każdego typu.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Aby uzyskać kroki uaktualnienia wirtualnej sieci WAN, zobacz [Uaktualnianie wirtualnej sieci WAN z podstawowej do standardowej](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Aby uzyskać informacje na temat architektury wirtualnej sieci WAN i sposobu migracji do wirtualnej sieci WAN, zobacz następujące artykuły:

* [Wirtualna architektura sieci WAN](migrate-from-hub-spoke-topology.md)
* [Architektura globalnej sieci tranzytowej](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w wirtualnej sieci WAN nie komunikują się ze sobą.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usługi, aby włączyć łączność. Z sieci lokalnej (vpnsite) można połączyć się z bramą sieci VPN wewnątrz koncentratora wirtualnego, połączyć obwody usługi ExpressRoute z koncentratorem wirtualnym, a nawet połączyć użytkowników mobilnych z bramą typu punkt-lokacja w centrum wirtualnym. Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład podczas korzystania z wirtualnej sieci WAN nie tworzysz połączenia lokacja lokacja lokacja bezpośrednio do sieci wirtualnej. Zamiast tego należy utworzyć połączenie lokacja-lokacja z koncentratorem. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną.

* **(Wersja zapoznawcza) Połączenie hub-to-hub** — wszystkie koncentratory są połączone ze sobą w wirtualnej sieci WAN. Oznacza to, że gałąź, użytkownik lub sieć wirtualna połączona z koncentratorem lokalnym może komunikować się z inną gałęzią lub siecią wirtualną przy użyciu architektury pełnej siatki połączonych koncentratorów. Sieci wirtualne można również łączyć w obrębie koncentratora przechodzącego przez koncentrator wirtualny, a także sieci wirtualne w centrum, przy użyciu połączonej struktury koncentratora z koncentratorem.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualnej sieci WAN**

  * **Strona:** Ten zasób jest używany tylko dla połączeń lokacja-lokacja. Zasób **witryny jest vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Rodzaje łączności

Wirtualna sieć WAN umożliwia następujące typy połączeń: Sieć VPN typu lokacja lokacja, sieć VPN użytkownika (point-to-site) i usługa ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Połączenia sieci VPN typu lokacja-lokacja

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Podczas tworzenia wirtualnego połączenia lokacja-lokacja sieci WAN można pracować z dostępnym partnerem. Jeśli nie chcesz używać partnera, możesz skonfigurować połączenie ręcznie. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Przepływ pracy partnera wirtualnej sieci WAN

Podczas pracy z wirtualnym partnerem sieci WAN przepływ pracy jest:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partnerzy dla wirtualnych połączeń WAN lokacja-lokacja

Aby uzyskać listę dostępnych partnerów i lokalizacji, zobacz [wirtualnych partnerów sieci WAN i lokalizacje](virtual-wan-locations-partners.md) artykułu.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Połączenia sieci VPN użytkownika (point-to-site)

Możesz połączyć się z zasobami na platformie Azure za pomocą połączenia IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga skonfigurowania klienta sieci VPN na komputerze klienckim. Aby uzyskać więcej informacji, zobacz [Tworzenie połączenia typu punkt-lokacja](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Połączenia ExpressRoute
Usługa ExpressRoute umożliwia łączenie się z siecią lokalną z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Lokalizacje

Aby uzyskać informacje o lokalizacji, zobacz [wirtualnych partnerów sieci WAN i lokalizacje](virtual-wan-locations-partners.md) artykułu.

## <a name="faq"></a><a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
