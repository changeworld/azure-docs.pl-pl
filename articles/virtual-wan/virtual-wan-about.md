---
title: Usługa Azure Virtual WAN — omówienie | Microsoft Docs
description: Dowiedz się więcej o automatycznej, skalowalnej łączności między oddziałami w usłudze Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: c2edb821eb8bd9a5da7a6cce81269e7d3f611722
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869895"
---
# <a name="what-is-azure-virtual-wan"></a>Co to jest usługa Azure Virtual WAN?

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie lub za pomocą urządzeń preferowanych partnerów za pośrednictwem partnera usługi Virtual WAN. Zobacz artykuł na temat [preferowanych partnerów](https://go.microsoft.com/fwlink/p/?linkid=2019615), aby uzyskać szczegółowe informacje. Użycie urządzeń preferowanych partnerów zapewnia łatwość obsługi i upraszcza łączność oraz zarządzanie konfiguracją. Wbudowany pulpit nawigacyjny sieci WAN na platformie Azure udostępnia na bieżąco wskazówki dotyczące rozwiązywania problemów, dzięki którym oszczędzisz czas, a także umożliwia łatwe monitorowanie łączności w dużej skali.

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Ten artykuł zawiera krótkie omówienie łączności sieciowej między obciążeniami na platformie Azure i poza nią. Usługa Virtual WAN oferuje następujące korzyści:

* **Zintegrowane rozwiązania w zakresie łączności w topologii piasty i szprych:** możliwość zautomatyzowania konfiguracji łączności między lokacjami lokalnymi a koncentratorem na platformie Azure.
* **Automatyczna instalacja i konfiguracja szprych:** bezproblemowe łączenie sieci wirtualnych i obciążeń z koncentratorem na platformie Azure.
* **Intuicyjne rozwiązywanie problemów:** możesz monitorować cały przepływ na platformie Azure i podejmować wymagane działania na podstawie tych informacji.

## <a name="s2s"></a>Połączenia typu lokacja-lokacja

Możesz utworzyć połączenie typu lokacja-lokacja w usłudze Virtual WAN za pośrednictwem [partnera usługi Virtual WAN](virtual-wan-locations-partners.md) lub ręcznie.

### <a name="s2spartner"></a>Współpraca z partnerem usługi Virtual WAN

Jeśli pracujesz z partnerem usługi Virtual WAN, proces wygląda następująco:

1. Kontroler urządzenia w oddziale (VPN/SDWAN) jest uwierzytelniany w celu wyeksportowania informacji z lokacji na platformę Azure za pomocą [jednostki usługi platformy Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. Kontroler urządzenia w oddziale (VPN/SDWAN) uzyskuje konfigurację łączności z platformy Azure i aktualizuje urządzenie lokalne. To umożliwia zautomatyzowanie pobierania, edytowania i aktualizowania konfiguracji lokalnego urządzenia sieci VPN.
3. Gdy urządzenie jest prawidłowo skonfigurowane pod kątem platformy Azure, tworzone jest połączenie typu lokacja-lokacja (dwa aktywne tunele) z siecią WAN platformy Azure. Platforma Azure obsługuje zarówno protokół IKEv1, jak i IKEv2. Protokół BGP jest opcjonalny.


Jeśli nie chcesz korzystać z preferowanego partnera, możesz skonfigurować połączenie ręcznie. Zobacz [Tworzenie połączenia typu lokacja-lokacja przy użyciu usługi Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Połączenia typu punkt-lokacja (wersja zapoznawcza)

Połączenie typu punkt-lokacja pozwala na bezpieczną komunikację między koncentratorem wirtualnym i komputerem klienckim. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja przydaje się również w przypadku niewielkiej liczby klientów, ponieważ pozwala zrezygnować z połączeń sieci VPN typu lokacja-lokacja.

Aby utworzyć połączenie ręcznie, zobacz [Tworzenie połączenia typu punkt-lokacja przy użyciu usługi Virtual WAN](virtual-wan-point-to-site-portal.md).

## <a name="er"></a>Połączenia usługi ExpressRoute (wersja zapoznawcza)

Aby utworzyć połączenie ręcznie, zobacz [Tworzenie połączenia usługi ExpressRoute przy użyciu usługi Virtual WAN](virtual-wan-expressroute-portal.md).


## <a name="resources"></a>Zasoby usługi Virtual WAN

Aby skonfigurować kompleksową wirtualną sieć WAN, należy utworzyć następujące zasoby:

* **virtualWAN:** zasób virtualWAN reprezentuje wirtualną nakładkę sieci platformy Azure i stanowi zbiór kilku zasobów. Zawiera linki do wszystkich koncentratorów wirtualnych, które mają się znaleźć w wirtualnej sieci WAN. Zasoby usługi Virtual WAN są wzajemnie izolowane i nie mogą zawierać wspólnego koncentratora. Koncentratory wirtualne w usłudze Virtual WAN nie komunikują się ze sobą. Właściwość „Zezwalaj na ruch między oddziałami” zezwala na ruch między lokacjami sieci VPN oraz z lokacji sieci VPN do lokacji z włączoną usługą ExpressRoute. Należy pamiętać, że usługa ExpressRoute w usłudze Azure Virtual WAN jest obecnie dostępna w wersji zapoznawczej.

* **Lokacja:** zasób lokacji, noszący nazwę vpnsite, reprezentuje lokalne urządzenie sieci VPN i jego ustawienia. Dzięki współpracy z partnerem usługi Virtual WAN otrzymasz wbudowane rozwiązanie do automatycznego eksportowania tych informacji na platformę Azure.

* **Koncentrator:** koncentrator wirtualny to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. W jednym regionie świadczenia usługi Azure może znajdować się tylko jeden koncentrator. Utworzenie koncentratora w witrynie Azure Portal spowoduje utworzenie sieci wirtualnej i bramy vpngateway dla koncentratora wirtualnego.

  Brama koncentratora różni się od bramy sieci wirtualnej używanej w usługach ExpressRoute i VPN Gateway. Na przykład w przypadku użycia usługi Virtual WAN nie tworzy się połączenia typu lokacja-lokacja z lokacji lokalnej bezpośrednio do sieci wirtualnej. Zamiast tego jest tworzone połączenie typu lokacja-lokacja z koncentratorem. Ruch zawsze jest kierowany przez bramę koncentratora. Oznacz to, że sieci wirtualne nie muszą mieć własnych bram sieci wirtualnej. Usługa Virtual WAN umożliwia łatwe skalowanie sieci wirtualnych za pośrednictwem koncentratora wirtualnego i bramy koncentratora wirtualnego. 

* **Połączenie sieci wirtualnej koncentratora:** zasób połączenia sieci wirtualnej koncentratora umożliwia bezproblemowe łączenie koncentratora z siecią wirtualną. Obecnie można tworzyć połączenia tylko z sieciami wirtualnymi znajdującymi się w tym samym regionie co koncentrator.

* **Tabela tras koncentratora:** możesz utworzyć trasę koncentratora wirtualnego i zastosować tę trasę do tabeli tras koncentratora wirtualnego. Możesz zastosować wiele tras do tabeli tras koncentratora wirtualnego.

## <a name="faq"></a>Często zadawane pytania

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze stroną [lokalizacji i partnerów usługi Virtual WAN](virtual-wan-locations-partners.md).
