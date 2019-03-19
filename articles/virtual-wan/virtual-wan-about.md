---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Więcej informacji na temat wirtualnych łączność sieci WAN zautomatyzowane skalowalne gałęzi do gałęzi, dostępne regiony i partnerów.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6a6fc9df2b102fd16bba03f26df4e24a1c946875
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409996"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie albo za pomocą urządzeń partnerów za pośrednictwem wirtualnej sieci WAN partnera. Za pomocą urządzeń partnerów pozwala ułatwić użytkowania uproszczenia łączności i zarządzanie konfiguracją. Aby uzyskać więcej informacji, zobacz [lokalizacje i partnerzy](virtual-wan-locations-partners.md) artykułu. Ponadto platformy Azure w sieci WAN wbudowany pulpit nawigacyjny zapewnia błyskawiczny wgląd rozwiązywania problemów, które może pomóc zaoszczędzić czas i zapewnia prosty sposób wyświetlenia łączności na dużą skalę.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Ten artykuł zawiera krótkie omówienie łączności sieciowej między obciążeniami na platformie Azure i poza nią. Usługa Virtual WAN oferuje następujące korzyści:

* **Rozwiązania w zakresie łączności zintegrowane w gwiazdy:** Automatyzowanie konfiguracji lokacja-lokacja i połączenia między lokacją lokalną i koncentrator platformy Azure.
* **Automatyczne szprychy instalacji i konfiguracji:** Łączenie sieci wirtualnych i obciążeń z platformy Azure Centrum bezproblemowo.
* **Intuicyjne rozwiązywania problemów:** Można zobaczyć przepływu end-to-end w obrębie platformy Azure, a dzięki tym informacjom o podjęcie wymaganych akcji.

## <a name="s2s"></a>Połączenia typu lokacja-lokacja

Możesz utworzyć połączenie typu lokacja-lokacja w usłudze Virtual WAN za pośrednictwem [partnera usługi Virtual WAN](virtual-wan-locations-partners.md) lub ręcznie.

### <a name="s2spartner"></a>Przepływ pracy partnera

Jeśli pracujesz z partnerem, wirtualne sieci WAN, przepływ pracy jest:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.

Jeśli nie chcesz używać z partnerem, można ręcznie skonfigurować połączenie, zobacz [utworzyć połączenie lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Połączenia typu punkt-lokacja (wersja zapoznawcza)

Połączenie typu punkt-lokacja pozwala na bezpieczną komunikację między koncentratorem wirtualnym i komputerem klienckim. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja przydaje się również w przypadku niewielkiej liczby klientów, ponieważ pozwala zrezygnować z połączeń sieci VPN typu lokacja-lokacja.

Aby utworzyć połączenie ręcznie, zobacz [Tworzenie połączenia typu punkt-lokacja przy użyciu usługi Virtual WAN](virtual-wan-point-to-site-portal.md).

## <a name="er"></a>Połączenia usługi ExpressRoute (wersja zapoznawcza)

Aby utworzyć połączenie ręcznie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu usługi Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** Zasób virtualWAN reprezentuje wirtualne nakładki sieci platformy Azure i jest to zbiór wielu zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w usłudze Virtual WAN nie komunikują się ze sobą. Właściwość „Zezwalaj na ruch między oddziałami” zezwala na ruch między lokacjami sieci VPN oraz z lokacji sieci VPN do lokacji z włączoną usługą ExpressRoute. Należy pamiętać, że usługa ExpressRoute w usłudze Azure Virtual WAN jest obecnie dostępna w wersji zapoznawczej.

* **Witryna:** Zasób witryn, znane jako vpnsite reprezentuje urządzenie sieci VPN w środowisku lokalnym i jego ustawień. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

* **Centrum:** Koncentrator wirtualny jest zarządzany przez firmę Microsoft sieci wirtualnej. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator. Utworzenie koncentratora w witrynie Azure Portal spowoduje utworzenie sieci wirtualnej i bramy vpngateway dla koncentratora wirtualnego.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku użycia usługi Virtual WAN nie tworzy się połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego jest tworzone połączenie typu lokacja-lokacja z koncentratorem. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego. 

* **Centrum połączenia sieci wirtualnej:** Zasób połączenia sieci wirtualnej koncentratora służy do łączenia piasty bezproblemowo z siecią wirtualną. Obecnie można tworzyć połączenia tylko z sieciami wirtualnymi znajdującymi się w tym samym regionie co koncentrator.

* **Tabela tras Centrum:**  Można utworzyć trasę koncentratora wirtualnego i zastosować trasy w tabeli tras koncentrator wirtualny. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

## <a name="partner-region"></a>Partnerzy i lokalizacje

### <a name="partner"></a>Partnerzy

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

### <a name="locations"></a>lokalizacje

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze stroną [lokalizacji i partnerów usługi Virtual WAN](virtual-wan-locations-partners.md).
