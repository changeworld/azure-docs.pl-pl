---
title: Komunikacja równorzędna usługi Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Informacje o komunikacji równorzędnej sieci wirtualnych na platformie Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2019
ms.author: anavin
ms.openlocfilehash: a53d5810b20aa8389c152889fed5d7f4e8cfc5b7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177672"
---
# <a name="virtual-network-peering"></a>Wirtualne sieci równorzędne

Komunikacja równorzędna sieci wirtualnych umożliwia bezproblemowe łączenie [sieci wirtualnych](virtual-networks-overview.md)platformy Azure. Po nawiązaniu połączenia równorzędnego sieci wirtualne są wyświetlane jako jedna w celach związanych z łącznością. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych odbywa się za pomocą infrastruktury sieci szkieletowej firmy Microsoft, podobnie jak ruch jest kierowany między maszynami wirtualnymi w tej samej sieci wirtualnej tylko za pomocą *prywatnych* adresów IP. Platforma Azure obsługuje:
* Wirtualne sieci równorzędne — łączenie sieci wirtualnych w tym samym regionie świadczenia usługi Azure
* Globalna komunikacja równorzędna sieci wirtualnych — łączenie sieci wirtualnych w regionach platformy Azure

Korzyści wynikające z używania komunikacji równorzędnej sieci wirtualnej, zarówno lokalnych, jak i globalnych, obejmują:

* Ruch sieciowy między równorzędnymi sieciami wirtualnymi jest prywatny. Ruch między sieciami wirtualnymi jest przechowywany w sieci szkieletowej firmy Microsoft. Komunikacja między sieciami wirtualnymi nie wymaga publicznego Internetu, bram ani szyfrowania.
* Połączenie o małym opóźnieniu i dużej przepustowości między zasobami w różnych sieciach wirtualnych.
* Możliwość komunikowania się zasobów w jednej sieci wirtualnej z zasobami w innej sieci wirtualnej, gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej.
* Możliwość transferu danych między subskrypcjami platformy Azure, modelami wdrażania i regionami platformy Azure.
* Możliwość komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pomocą Azure Resource Manager lub komunikacji równorzędnej jednej sieci wirtualnej utworzonej za pomocą Menedżer zasobów do sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zobacz [Omówienie modeli wdrażania platformy Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Brak przestojów w przypadku zasobów w sieci wirtualnej podczas tworzenia komunikacji równorzędnej lub po utworzeniu komunikacji równorzędnej.

## <a name="connectivity"></a>Łączność

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, zasoby w jednej sieci wirtualnej mogą łączyć się bezpośrednio z zasobami w równorzędnej sieci wirtualnej.

Opóźnienie sieci między maszynami wirtualnymi w równorzędnych sieciach wirtualnych w tym samym regionie jest takie samo jak opóźnienie w ramach jednej sieci wirtualnej. Przepływność sieci zależy od przepustowości dozwolonej dla maszyny wirtualnej proporcjonalnie do jej rozmiaru. Nie ma żadnych dodatkowych ograniczeń przepustowości w ramach komunikacji równorzędnej.

Ruch między maszynami wirtualnymi w równorzędnych sieciach wirtualnych odbywa się bezpośrednio za pomocą infrastruktury sieci szkieletowej firmy Microsoft, a nie za pośrednictwem bramy lub publicznego Internetu.

Sieciowe grupy zabezpieczeń można stosować w dowolnej sieci wirtualnej, aby blokować dostęp do innych sieci wirtualnych lub podsieci, w razie potrzeby.
Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej można otworzyć lub zamknąć reguły sieciowej grupy zabezpieczeń między sieciami wirtualnymi. W przypadku otwarcia pełnej łączności między równorzędnymi sieciami wirtualnymi (czyli opcją domyślną) można zastosować sieciowe grupy zabezpieczeń do określonych podsieci lub maszyn wirtualnych, aby zablokować lub odmówić określonego dostępu. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń](security-overview.md).

## <a name="service-chaining"></a>Tworzenie łańcucha usług

Można skonfigurować trasy zdefiniowane przez użytkownika, które wskazują na maszyny wirtualne w wirtualnych sieciach równorzędnych jako adres IP *następnego przeskoku* lub bramy sieci wirtualnej, aby umożliwić tworzenie łańcucha usług. Tworzenie łańcucha usług umożliwia kierowanie ruchu z jednej sieci wirtualnej do urządzenia wirtualnego lub bramy sieci wirtualnej w równorzędnej sieci wirtualnej za pośrednictwem tras zdefiniowanych przez użytkownika.

Można wdrożyć sieci gwiazdy, w których Centrum sieci wirtualnej może hostować składniki infrastruktury, takie jak sieciowe urządzenie wirtualne lub Brama sieci VPN. Wszystkie sieci wirtualne szprychy mogą następnie nawiązać komunikację równorzędną z centralną siecią wirtualną. Ruch może przepływać przez wirtualne urządzenia sieciowe lub bramy sieci VPN w sieci wirtualnej centrum. 

Komunikacja równorzędna sieci wirtualnej umożliwia następnym przeskoku w trasie zdefiniowanej przez użytkownika adres IP maszyny wirtualnej w równorzędnej sieci wirtualnej lub bramę sieci VPN. Nie można jednak kierować trasy między sieciami wirtualnymi przy użyciu trasy zdefiniowanej przez użytkownika, określającej bramę ExpressRoute jako typu następnego przeskoku. Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika — omówienie](virtual-networks-udr-overview.md#user-defined). Aby dowiedzieć się, jak utworzyć topologię sieci gwiazdy i gwiazdy, zobacz [topologia sieci gwiazdy i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Bramy i łączność lokalna

Każda sieć wirtualna, niezależnie od tego, czy jest połączona za pomocą komunikacji równorzędnej z inną siecią wirtualną, może mieć własną bramę i używać jej do łączenia się z siecią lokalną. Możesz również skonfigurować [połączenia sieci wirtualnej z](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) siecią wirtualną przy użyciu bram, nawet jeśli sieci wirtualne są połączone za pomocą komunikacji równorzędnej.

W przypadku skonfigurowania obu opcji łączności między sieciami wirtualnymi ruch między sieciami wirtualnymi odbywa się za pośrednictwem konfiguracji komunikacji równorzędnej (czyli za pośrednictwem szkieletu platformy Azure).

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, można również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do sieci lokalnej. W takim przypadku Sieć wirtualna korzystająca z bramy zdalnej nie może mieć własnej bramy. Sieć wirtualna może mieć tylko jedną bramę. Brama może być lokalna lub zdalna (w wirtualnej sieci równorzędnej), jak pokazano na poniższej ilustracji:

![tranzyt komunikacji równorzędnej sieci wirtualnej](./media/virtual-networks-peering-overview/figure04.png)

Tranzyt bramy jest obsługiwany zarówno w przypadku komunikacji równorzędnej sieci wirtualnej, jak i globalnej komunikacji równorzędnej sieci wirtualnej. Tranzyt bramy między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania (Menedżer zasobów i klasyczny) jest obsługiwany tylko wtedy, gdy Brama znajduje się w sieci wirtualnej (Menedżer zasobów). Aby dowiedzieć się więcej o używaniu bramy do celów tranzytowych, zobacz [Konfigurowanie bramy sieci VPN do tranzytu w wirtualnej sieci równorzędnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Gdy sieci wirtualne udostępniające pojedyncze połączenie usługi Azure ExpressRoute są połączone za pomocą komunikacji równorzędnej, ruch między nimi odbywa się przez relację komunikacji równorzędnej (czyli za pomocą sieci szkieletowej platformy Azure). Można nadal używać bram lokalnych w każdej sieci wirtualnej do nawiązywania połączenia z obwodem lokalnym. Alternatywnie możesz użyć udostępnionej bramy i skonfigurować tranzyt dla łączności lokalnej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby potwierdzić komunikację równorzędną sieci wirtualnej, można [sprawdzić efektywne trasy](diagnose-network-routing-problem.md) dla interfejsu sieciowego w dowolnej podsieci w sieci wirtualnej. W przypadku istnienia komunikacji równorzędnej sieci wirtualnej wszystkie podsieci w sieci wirtualnej mają trasy z typem następnego przeskoku *Komunikacja równorzędna*sieci wirtualnych dla każdej przestrzeni adresowej w każdej komunikacji równorzędnej.

Możesz również rozwiązywać problemy z łącznością z maszyną wirtualną w równorzędnej sieci wirtualnej przy użyciu [sprawdzenia łączności](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Network Watcher. Kontrola łączności pozwala zobaczyć, jak ruch jest kierowany z interfejsu sieciowego źródłowej maszyny wirtualnej do interfejsu sieciowego docelowej maszyny wirtualnej.

Możesz również wypróbować [Narzędzie do rozwiązywania problemów z siecią wirtualną za pomocą komunikacji równorzędnej](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues).

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia

Poniższe ograniczenia mają zastosowanie tylko wtedy, gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej:
- Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP frontonu podstawowego wewnętrznego modułu równoważenia obciążenia w wirtualnej sieci równorzędnej. Obsługa podstawowych Load Balancer istnieje tylko w tym samym regionie. Obsługa usługa Load Balancer w warstwie Standardowa istnieje zarówno dla sieci wirtualnej, jak i globalnej sieci równorzędnej. Usługi korzystające z podstawowego modułu równoważenia obciążenia, które nie będą działały w ramach globalnej komunikacji równorzędnej sieci wirtualnej, są udokumentowane w [tym miejscu.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Aby dowiedzieć się więcej o wymaganiach i ograniczeniach, zobacz temat [wymagania i ograniczenia dotyczące komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md#requirements-and-constraints). Aby dowiedzieć się więcej o limitach liczby elementów równorzędnych, które można utworzyć dla sieci wirtualnej, zobacz [limity dotyczące sieci platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

## <a name="permissions"></a>Uprawnienia

Aby dowiedzieć się więcej o uprawnieniach wymaganych do utworzenia komunikacji równorzędnej sieci wirtualnej, zobacz [uprawnienia komunikacji równorzędnej sieci wirtualnych](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Cennik

Dostępna jest nominalna opłata za ruch przychodzący i wyjściowy, który wykorzystuje połączenie komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji o wirtualnych sieciach równorzędnych i globalnych sieciach równorzędnych sieci wirtualnych, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/virtual-network).

Tranzyt bramy to właściwość komunikacji równorzędnej, która umożliwia sieci wirtualnej korzystanie z bramy sieci VPN/ExpressRoute w sieci wirtualnej w komunikacji równorzędnej na potrzeby łączności między różnymi lokalizacjami lub połączeniami typu sieć wirtualna-sieć wirtualna. Ruch do bramy (ruchu przychodzącego lub wychodzącego) w równorzędnej sieci wirtualnej będzie powodować naliczanie opłat za komunikację równorzędną w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz opłaty [za bramę sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) lub opłaty za bramę ExpressRoute oraz [opłaty za wirtualne sieci równorzędne.](https://azure.microsoft.com/pricing/details/virtual-network)

## <a name="next-steps"></a>Następne kroki

* Komunikacja równorzędna sieci wirtualnej jest tworzona między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania, które istnieją w tych samych lub różnych subskrypcjach. Ukończ samouczek dotyczący jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure             | Ramach  |
    |---------                          |---------|
    |Oba Menedżer zasobów              |[Ten](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Rodzaje](create-peering-different-subscriptions.md)|
    |Jeden Menedżer zasobów, jeden klasyczny  |[Ten](create-peering-different-deployment-models.md)|
    |                                   |[Rodzaje](create-peering-different-deployment-models-subscriptions.md)|

* Dowiedz się, jak utworzyć [topologię sieci gwiazdy i](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)gwiazdy.
* Dowiedz się więcej o wszystkich [ustawieniach komunikacji równorzędnej sieci wirtualnej i sposobach ich zmiany](virtual-network-manage-peering.md).
* Uzyskaj odpowiedzi na często [zadawane](virtual-networks-faq.md#vnet-peering) pytania dotyczące komunikacji równorzędnej sieci wirtualnej i globalnej komunikacji równorzędnej sieci wirtualnej za pomocą naszych pytań
