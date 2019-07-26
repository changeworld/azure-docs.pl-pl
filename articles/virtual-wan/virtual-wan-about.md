---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o wirtualnej skalowalnej gałęzi sieci WAN, dostępnych regionach i partnerach.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406361"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która zapewnia zoptymalizowaną i zautomatyzowaną łączność z gałęzią z usługami i i za pośrednictwem platformy Azure. Regiony platformy Azure służą jako centra, do których można połączyć gałęzie. Korzystając ze szkieletu platformy Azure, można również łączyć gałęzie i korzystać z połączeń między sieciami wirtualnymi. Mamy listę partnerów, którzy obsługują automatyzację łączności przy użyciu wirtualnej sieci WAN platformy Azure. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

Wirtualna sieć WAN platformy Azure oferuje wiele usług łączności w chmurze platformy Azure, takich jak sieć VPN typu lokacja-lokacja, i ExpressRoute do jednego interfejsu operacyjnego. Połączenie z usługą Azure sieci wirtualnych jest nawiązywane przy użyciu połączeń sieci wirtualnej.

ExpressRoute dla wirtualnej sieci WAN jest obecnie w wersji zapoznawczej.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki podgląd łączności sieciowej w wirtualnej sieci WAN platformy Azure. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania łączności w centrum i szprychie:** Automatyzowanie konfiguracji lokacja-lokacja i łączności między lokacjami lokalnymi i centrum platformy Azure.
* **Instalacja i konfiguracja zautomatyzowanej szprychy:** Bezproblemowo łącz sieci wirtualne i obciążenia z centrum platformy Azure.
* **Intuicyjne Rozwiązywanie problemów:** Możesz zobaczyć kompleksowy przepływ na platformie Azure, a następnie użyć tych informacji do podjęcia wymaganych akcji.

## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** Zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i jest kolekcją wielu zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w usłudze Virtual WAN nie komunikują się ze sobą. Właściwość "Zezwalaj na ruch rozgałęzienia do oddziału" umożliwia ruch między lokacjami sieci VPN oraz sieciami VPN do ExpressRoute (obecnie w wersji zapoznawczej).

* **Centralny** Koncentratorem wirtualnym jest sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator. Utworzenie koncentratora w witrynie Azure Portal spowoduje utworzenie sieci wirtualnej i bramy vpngateway dla koncentratora wirtualnego.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku korzystania z wirtualnej sieci WAN nie można utworzyć połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego należy utworzyć połączenie lokacja-lokacja z centrum. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Połączenie sieci wirtualnej centrum:** Zasób połączenia sieci wirtualnej centrum służy do bezproblemowego łączenia koncentratora z siecią wirtualną. Obecnie można tworzyć połączenia tylko z sieciami wirtualnymi znajdującymi się w tym samym regionie co koncentrator.

* **Tabela tras centrum:**  Można utworzyć trasę koncentratora wirtualnego i zastosować trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualnej sieci WAN**

  * **Lokacji** Ten zasób jest używany tylko w przypadku połączeń lokacja-lokacja. Zasób lokacji to **vpnsite**. Reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="connectivity"></a>Łączność

Wirtualna sieć WAN zezwala na dwa typy łączności: Lokacja-lokacja i ExpressRoute (wersja zapoznawcza).

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


### <a name="er"></a>Połączenia usługi ExpressRoute (wersja zapoznawcza)

ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Miejsce

Informacje o lokalizacji znajdują się w artykule dotyczącym [partnerów i lokalizacji wirtualnych sieci WAN](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

[Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
