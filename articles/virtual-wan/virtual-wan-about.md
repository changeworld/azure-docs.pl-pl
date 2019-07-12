---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Więcej informacji na temat wirtualnych łączność sieci WAN zautomatyzowane skalowalne gałęzi do gałęzi, dostępne regiony i partnerów.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 06/28/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 46f3f87fac5b65229e03ee91d8f2b93b1a1590e8
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795281"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Usługa Azure wirtualne sieci WAN jest sieci usługi, która zapewnia łączność zoptymalizowane i zautomatyzowanych oddziału, aby i za pośrednictwem platformy Azure. Koncentratorów, które można wybrać opcję połączenia gałęziach, aby pełnić rolę regionów świadczenia usługi Azure. Możesz korzystać z sieci szkieletowej platformy Azure również łączenie gałęzi i korzystaj ze łączności gałęzi między sieciami wirtualnymi. Mamy listę partnerów, które obsługują automatyzacji łączności z wirtualnej sieci WAN sieci VPN platformy Azure. Aby uzyskać więcej informacji, zobacz [wirtualne sieci WAN partnerzy i lokalizacje](virtual-wan-locations-partners.md) artykułu.

Azure wirtualne sieci WAN łączy wiele usług łączności chmury platformy Azure, takich jak sieci VPN typu lokacja lokacja i ExpressRoute, użytkownika point-to-site VPN w pojedynczy interfejs operacyjnej. Łączność z sieciami wirtualnymi platformy Azure została ustanowiona przy użyciu połączenia sieć wirtualna.

Usługi ExpressRoute i użytkownika point-to-site VPN dla wirtualnej sieci WAN są obecnie dostępne w wersji zapoznawczej.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan1.png)

Ten artykuł zawiera szybki wgląd w łączności sieciowej w sieci platformy Azure z wirtualnych WAN. Usługa Virtual WAN oferuje następujące korzyści:

* **Rozwiązania w zakresie łączności zintegrowane w gwiazdy:** Automatyzowanie konfiguracji lokacja lokacja i połączenia między lokacją lokalną i koncentrator platformy Azure.
* **Automatyczne szprychy instalacji i konfiguracji:** Łączenie sieci wirtualnych i obciążeń z platformy Azure Centrum bezproblemowo.
* **Intuicyjne rozwiązywania problemów:** Zobacz przepływ end-to-end w obrębie platformy Azure i następnie dzięki tym informacjom o podjęcie wymaganych akcji.

## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować end-to-end wirtualne sieci WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** Zasób virtualWAN reprezentuje wirtualne nakładki sieci platformy Azure i jest to zbiór wielu zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w usłudze Virtual WAN nie komunikują się ze sobą. Właściwość "Zezwalaj na ruch gałęzi do gałęzi" umożliwia ruch między witryn sieci VPN, a także sieci VPN usługi expressroute (obecnie w wersji zapoznawczej), które zostały włączone między lokacjami.

* **Centrum:** Koncentrator wirtualny jest zarządzany przez firmę Microsoft sieci wirtualnej. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator. Utworzenie koncentratora w witrynie Azure Portal spowoduje utworzenie sieci wirtualnej i bramy vpngateway dla koncentratora wirtualnego.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład używając wirtualnej sieci WAN, nie jest tworzona połączenia lokacja lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego tworzenia połączenia lokacja lokacja do Centrum. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego.

* **Centrum połączenia sieci wirtualnej:** Zasób połączenia sieci wirtualnej koncentratora służy do łączenia piasty bezproblemowo z siecią wirtualną. Obecnie można tworzyć połączenia tylko z sieciami wirtualnymi znajdującymi się w tym samym regionie co koncentrator.

* **Tabela tras Centrum:**  Można utworzyć trasę koncentratora wirtualnego i zastosować trasy w tabeli tras koncentrator wirtualny. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

**Dodatkowe zasoby wirtualne sieci WAN**

  * **Witryna:** Ten zasób jest używany tylko połączeń lokacja lokacja. Zasób witryny jest **vpnsite**. Reprezentuje urządzenie sieci VPN w środowisku lokalnym i jego ustawień. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

## <a name="connectivity"></a>Łączność

Wirtualna sieć WAN umożliwia trzech typów połączeń: lokacja lokacja, punkt lokacja (wersja zapoznawcza) i usługi ExpressRoute (wersja zapoznawcza).

### <a name="s2s"></a>Połączenia sieci VPN typu lokacja lokacja

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Podczas tworzenia wirtualnej sieci WAN połączenia lokacja lokacja można pracować z partnerem dostępne. Jeśli nie chcesz używać z partnerem, połączenie można skonfigurować ręcznie. Aby uzyskać więcej informacji, zobacz [utworzyć połączenie lokacja lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Wirtualne sieci WAN partnera przepływu pracy

Jeśli pracujesz z partnerem, wirtualne sieci WAN, przepływ pracy jest:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.

#### <a name="partners"></a>Partnerzy, wirtualne sieci WAN połączeń lokacja lokacja

Aby uzyskać listę dostępnych partnerzy i lokalizacje, zobacz [wirtualne sieci WAN partnerzy i lokalizacje](virtual-wan-locations-partners.md) artykułu.

### <a name="p2s"></a>Połączenia sieci VPN typu punkt lokacja (wersja zapoznawcza)

Połączenie punkt lokacja (P2S) pozwala utworzyć bezpieczne połączenie z wirtualnego Centrum z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja przydaje się również w przypadku niewielkiej liczby klientów, ponieważ pozwala zrezygnować z połączeń sieci VPN typu lokacja-lokacja.

Aby utworzyć połączenie, zobacz [utworzyć połączenie punkt lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Połączenia usługi ExpressRoute (wersja zapoznawcza)

Usługa ExpressRoute umożliwia łączenie sieci lokalnej z platformą Azure za pośrednictwem połączenia prywatnego. Aby utworzyć połączenie, zobacz [utworzyć połączenie ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>lokalizacje

Aby uzyskać informacje o lokalizacji, zobacz [wirtualne sieci WAN partnerzy i lokalizacje](virtual-wan-locations-partners.md) artykułu.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

[Utwórz połączenie lokacja lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
