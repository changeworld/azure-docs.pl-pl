---
title: Wirtualne sieci równorzędne platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej na temat wirtualnych sieci równorzędnych na platformie Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279548"
---
# <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna sieci wirtualnej umożliwia bezproblemowe łączenie sieci w [sieci wirtualnej platformy Azure.](virtual-networks-overview.md) Sieci wirtualne są wyświetlane jako jeden do celów łączności. Ruch między maszynami wirtualnymi korzysta z infrastruktury szkieletowej firmy Microsoft. Podobnie jak ruch między maszynami wirtualnymi w tej samej sieci, ruch jest kierowany tylko za pośrednictwem sieci *prywatnej* firmy Microsoft.

Platforma Azure obsługuje następujące typy komunikacji równorzędnej:

* Komunikacja równorzędna sieci wirtualnej: łączenie sieci wirtualnych w tym samym regionie platformy Azure.
* Globalna komunikacja równorzędna sieci wirtualnej: łączenie sieci wirtualnych w regionach platformy Azure.

Korzystanie z wirtualnych sieci równorzędnych, lokalnych lub globalnych, zapewnia m.in. następujące korzyści:

* Połączenie o małych opóźnieniach i dużej przepustowości między zasobami w różnych sieciach wirtualnych.
* Możliwość komunikowania się zasobów w jednej sieci wirtualnej z zasobami w innej sieci wirtualnej.
* Możliwość przesyłania danych między sieciami wirtualnymi w ramach subskrypcji platformy Azure, dzierżaw usługi Azure Active Directory, modeli wdrażania i regionów platformy Azure.
* Możliwość równowaki sieci wirtualnych utworzonych za pośrednictwem usługi Azure Resource Manager.
* Możliwość równania sieci wirtualnej utworzonej za pośrednictwem Menedżera zasobów do sieci utworzonej za pośrednictwem klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).
* Brak przestojów zasobów w sieci wirtualnej podczas tworzenia komunikacji równorzędnej albo po utworzeniu komunikacji równorzędnej.

Ruch sieciowy między wirtualnymi sieciami równorzędnymi jest prywatny. Ruch między sieciami wirtualnymi jest utrzymywany w sieci szkieletowej firmy Microsoft. Do komunikacji między sieciami wirtualnymi nie jest wymagany publiczny Internet, bramy ani szyfrowanie.

## <a name="connectivity"></a>Łączność

W przypadku sieci wirtualnych równorzędnych zasoby w każdej sieci wirtualnej mogą bezpośrednio łączyć się z zasobami w sieci wirtualnej równorzędnej.

Opóźnienie sieciowe między maszynami wirtualnymi w równorzędnych sieciach wirtualnych w tym samym regionie jest takie samo jak opóźnienie w pojedynczej sieci wirtualnej. Przepływność sieci zależy od przepustowości dozwolonej dla maszyny wirtualnej proporcjonalnie do jej rozmiaru. Nie ma żadnych dodatkowych ograniczeń przepustowości w obrębie komunikacji równorzędnej.

Ruch między maszynami wirtualnymi w równorzędnych sieciach wirtualnych odbywa się bezpośrednio za pomocą infrastruktury sieci szkieletowej firmy Microsoft, a nie przy użyciu bramy lub publicznego Internetu.

Sieciowe grupy zabezpieczeń można zastosować w sieci wirtualnej, aby zablokować dostęp do innych sieci wirtualnych lub podsieci.
Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej otwórz lub zamknij reguły sieciowej grupy zabezpieczeń między sieciami wirtualnymi. Jeśli otworzysz pełną łączność między sieciami wirtualnymi równorzędnym, możesz zastosować sieciowe grupy zabezpieczeń, aby zablokować lub odmówić określonego dostępu. Pełna łączność jest opcją domyślną. Aby dowiedzieć się więcej o sieciowych grupach zabezpieczeń, zobacz [Grupy zabezpieczeń](security-overview.md).

## <a name="service-chaining"></a>Tworzenie łańcuchów usług

Tworzenie łańcucha usług umożliwia kierowanie ruchu z jednej sieci wirtualnej do urządzenia wirtualnego lub bramy w sieci równorzędnej za pośrednictwem tras zdefiniowanych przez użytkownika.

Aby włączyć tworzenie łańcucha usług, należy skonfigurować trasy zdefiniowane przez użytkownika, które wskazują maszyny wirtualne w sieciach wirtualnych równorzędnych jako następny adres IP *przeskoku.* Trasy zdefiniowane przez użytkownika mogą również wskazywać bramy sieci wirtualnej, aby umożliwić tworzenie łańcucha usług.

Można wdrożyć sieci koncentratora i szprychy, w których sieć wirtualna koncentratora obsługuje składniki infrastruktury, takie jak wirtualne urządzenie sieciowe lub brama sieci VPN. Następnie wszystkie sieci wirtualne typu gwiazda można połączyć za pomocą komunikacji równorzędnej z centralną siecią wirtualną. Ruch przepływa przez wirtualne urządzenia sieciowe lub bramy sieci VPN w sieci wirtualnej koncentratora.

Wirtualne sieci równorzędne umożliwiają użycie kolejnego przeskoku w trasie zdefiniowanej przez użytkownika jako adresu IP maszyny wirtualnej w wirtualnej sieci równorzędnej lub bramie sieci VPN. Nie można kierować między sieciami wirtualnymi z trasą zdefiniowaną przez użytkownika, która określa bramę usługi Azure ExpressRoute jako następny typ przeskoku. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [User-defined routes overview](virtual-networks-udr-overview.md#user-defined) (Omówienie tras definiowanych przez użytkownika). Aby dowiedzieć się, jak utworzyć topologię sieci koncentratora i szprychy, zobacz [Topologia sieci szprychy koncentratora na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Bramy i łączność lokalna

Każda sieć wirtualna, w tym sieć wirtualna równorzędna, może mieć własną bramę. Sieć wirtualna może używać swojej bramy do łączenia się z siecią lokalną. Można również skonfigurować [połączenia sieci wirtualnej z siecią wirtualną](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przy użyciu bram, nawet dla sieci wirtualnych równorzędnych.

Podczas konfigurowania obu opcji dla sieci wirtualnej wzajemne połączenia, ruch między sieciami wirtualnymi przepływa przez konfigurację komunikacji równorzędnej. Ruch używa szkieletu platformy Azure.

Bramę w sieci wirtualnej równorzędnej można również skonfigurować jako punkt tranzytowy do sieci lokalnej. W takim przypadku sieć wirtualna korzystająca z bramy zdalnej nie może mieć własnej bramy. Sieć wirtualna ma tylko jedną bramę. Brama jest bramą lokalną lub zdalną w sieci wirtualnej równorzędnej, jak pokazano na poniższym diagramie:

![przesyłanie w równorzędnych sieciach wirtualnych](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Zarówno komunikacja równorzędna sieci wirtualnej, jak i globalna komunikacja równorzędna sieci wirtualnej obsługują przesyłanie bramy.

Obsługiwany jest tranzyt bramy między sieciami wirtualnymi utworzony za pośrednictwem różnych modeli wdrażania. Brama musi znajdować się w sieci wirtualnej w modelu Menedżera zasobów. Aby dowiedzieć się więcej na temat przesyłania danych za pomocą bramy, zobacz [Konfigurowanie bramy sieci VPN na potrzeby przesyłania danych w komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Gdy równorzędne sieci wirtualne, które współużytkują jedno połączenie usługi Azure ExpressRoute, ruch między nimi przechodzi przez relację komunikacji równorzędnej. Ten ruch korzysta z sieci szkieletowej platformy Azure. Użytkownicy mogą nadal korzystać z bram lokalnych w poszczególnych sieciach wirtualnych, aby łączyć się z obwodem lokalnym. W przeciwnym razie można użyć bramy udostępnionej i skonfigurować tranzyt dla łączności lokalnej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby potwierdzić, że sieci wirtualne są równorzędne, można sprawdzić efektywne trasy. Sprawdź trasy dla interfejsu sieciowego w dowolnej podsieci w sieci wirtualnej. Jeśli istnieje komunikacja równorzędna w sieci wirtualnej, wszystkie podsieci w tej sieci wirtualnej mają trasy z typem następnego przeskoku *Komunikacja równorzędna sieci wirtualnych* dla każdej przestrzeni adresowej w każdej równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Diagnozowanie problemu z routingiem maszyny wirtualnej](diagnose-network-routing-problem.md).

Można również rozwiązać problemy z łącznością z maszyną wirtualną w sieci wirtualnej równorzędnej przy użyciu usługi Azure Network Watcher. Sprawdzanie łączności pozwala zobaczyć, jak ruch jest kierowany z interfejsu sieciowego źródłowej maszyny wirtualnej do interfejsu sieciowego docelowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z połączeniami za pomocą usługi Azure Network Watcher przy użyciu witryny Azure portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Można również spróbować [rozwiązywania problemów z komunikacją równorzędową w sieci wirtualnej](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Ograniczenia dla sieci wirtualnych równorzędnych<a name="requirements-and-constraints"></a>

Następujące ograniczenia mają zastosowanie tylko wtedy, gdy sieci wirtualne są globalnie połączone za pomocą sieci równorzędnych:

* Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP front-end basicowego modułu równoważenia obciążenia wewnętrznego (ILB) w globalnej sieci wirtualnej równorzędnej.
* Niektóre usługi korzystające z modułu równoważenia obciążenia basic nie działają w globalnej komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Jakie są ograniczenia związane z globalną komunikacją równorzędnościową i modułami równoważenia obciążenia?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Aby uzyskać więcej informacji, zobacz [Wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints). Aby dowiedzieć się więcej o obsługiwanej liczbie komunikacji równorzędnej, zobacz [Limity sieci](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Uprawnienia

Aby dowiedzieć się więcej o uprawnieniach wymaganych do utworzenia komunikacji równorzędnej w sieci wirtualnej, zobacz [Uprawnienia](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Cennik

Istnieje opłata nominalna za ruch przychodzący i wychodzący, który używa połączenia równorzędnego sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Ceny sieci wirtualnej](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit to właściwość komunikacji równorzędnej, która umożliwia sieci wirtualnej korzystanie z bramy sieci VPN/ExpressRoute w sieci wirtualnej równorzędnej. Tranzyt bramy działa zarówno dla międzypomieszczeń, jak i łączności między siecią. Ruch do bramy (ruch przychodzący lub wychodzący) w sieci równorzędnej sieci wirtualnej wiąże się z opłatami równorzędnych w sieci wirtualnej szprychy (lub sieci wirtualnej niebędącej bramą). Aby uzyskać więcej informacji, zobacz [Ceny bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) dotyczące opłat za bramę sieci VPN i cennik bramy usługi ExpressRoute dla opłat za bramę usługi ExpressRoute.

>[!NOTE]
> Poprzednia wersja tego dokumentu stwierdzono, że opłaty równorzędne sieci wirtualnej nie będą miały zastosowania w sieci wirtualnej szprychy (lub sieci wirtualnej bez bramy) z gateway transit. Teraz odzwierciedla dokładne ceny na stronie cen.

## <a name="next-steps"></a>Następne kroki

* Można utworzyć komunikację równorzędną między dwiema sieciami wirtualnymi. Sieci mogą należeć do tej samej subskrypcji, różnych modeli wdrażania w tej samej subskrypcji lub różnych subskrypcji. Ukończ samouczek dla jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure             | Subskrypcja  |
    |---------                          |---------|
    |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Różne](create-peering-different-subscriptions.md)|
    |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
    |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

* Aby dowiedzieć się, jak utworzyć topologię sieci koncentratora i szprychy, zobacz [Topologia sieci szprychy koncentratora na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Aby dowiedzieć się więcej o wszystkich ustawieniach komunikacji równorzędnej w sieci [wirtualnej, zobacz Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej w sieci wirtualnej](virtual-network-manage-peering.md).
* Aby uzyskać odpowiedzi na typowe pytania dotyczące komunikacji równorzędnej sieci wirtualnej i globalnej sieci wirtualnej, zobacz [Komunikacja równorzędna sieci wirtualnej](virtual-networks-faq.md#vnet-peering).
