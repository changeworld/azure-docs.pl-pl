---
title: Wirtualne sieci WAN przesyłania globalne sieci architektura na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o architektury sieci tranzytowej globalne wirtualne sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/06/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 8cda617ca60a17fceaaa818480ff9bbaef46c3fd
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414068"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Architektura sieci tranzytowej globalne oraz wirtualne sieci WAN

Architektura sieci tranzytowej globalnego przyjmowana jest firmom konsolidować, łączenie i kontrolowanie skoncentrowane na chmurze nowoczesnych przedsiębiorstw przestrzeni IT przedsiębiorstwa. W nowoczesnym przedsiębiorstwie skoncentrowane na chmurze ruch sieciowy nie trzeba backhauled do Centrali. Architektura sieci tranzytowej globalnego opiera się na dobrze znanych sieci pojęcia i koncepcje, które są unikatowe dla chmury i architektury oparte na chmurze.

![architektura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Rysunek 1: Sieć tranzytowa globalnego przy użyciu wirtualnej sieci WAN**

Nowoczesne przedsiębiorstwa wymagają wszechobecne łączność między hyper rozproszone aplikacje, dane i użytkownicy w chmurze i lokalnych. Azure wirtualne sieci WAN umożliwia architektury sieci tranzytowej globalnego, należy włączyć łączność wszechobecne, dowolna dowolna między globalnie rozproszone zestawy sieci wirtualnych, witryn, aplikacji i użytkowników. Usługa Azure wirtualne sieci WAN jest usługą zarządzanych przez firmę Microsoft. Wszystkie składniki sieci, które ta usługa składa się z są hostowaną i zarządzaną przez firmę Microsoft. Aby uzyskać więcej informacji na temat wirtualnych sieci WAN, zobacz [Omówienie wirtualnych sieci WAN](virtual-wan-about.md) artykułu.

W ramach architektury Azure wirtualnego WAN regionów świadczenia usługi Azure służy jako koncentratorów, do których użytkownik może połączyć się z gałęziami. Gdy gałęzie, które są połączone, można korzystać z sieci szkieletowej platformy Azure można ustanowić gałęzi między sieciami wirtualnymi i, opcjonalnie, łączność gałęzi do gałęzi.

Wirtualna sieć WAN można ustanowić, tworząc jedno centrum wirtualne sieci WAN w regionie, który ma największą liczbę szprychy (gałęzie, sieci wirtualnych i użytkowników) i następnie nawiąż połączenie szprych, które znajdują się w innych regionach do koncentratora. Alternatywnie Jeśli szprychy są rozproszone geograficznie, możesz również centra regionalne wystąpienia i połączenia koncentratorów. Koncentratory są częścią tej samej wirtualna sieć WAN, ale może on być powiązany z różnymi zasadami regionalne.

## <a name="hub"></a>Gwiazda przesyłania

Architektura sieci tranzytowej globalnego jest oparty na modelu klasycznym łączności Gwiazda — gdzie sieci hostowane w chmurze "Centrum" umożliwia przechodnie łączności między punktami końcowymi, rozproszonych w różnych rodzajów "szprychy".
  
W tym modelu szprychy mogą być:

* Sieć wirtualna (Vnet)
* Fizyczne oddziale.
* Zdalny użytkownik
* Internet

![diagram globalnego przesyłania gwiazdy](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Rysunek 2: Hub-and-spoke**

Rysunek 2 przedstawia widok logiczny globalnej sieci, gdzie użytkownicy geograficznie rozproszonych, lokacjach fizycznych i sieci wirtualne są połączone ze sobą za pośrednictwem sieci Centrum hostowane w chmurze. Ta architektura umożliwia połączenie logiczne przesyłane z jednym przeskokiem między sieci punktów końcowych. Szprychy są podłączone do koncentratora przez różne usługi platformy Azure networking takich jak usługi ExpressRoute lub site-to-sieci VPN typu lokacja fizycznych gałęzi, wirtualne sieci równorzędne sieci wirtualne i sieci VPN typu punkt lokacja, użytkownikom zdalnym.

## <a name="crossregion"></a>Łączność między regionami

Dla przedsiębiorstw zużycie chmury zwykle następuje fizycznych śladu. Większość przedsiębiorstw dostęp do chmury z regionu najbliższego lokacji fizycznej i użytkownikami. Jeden z kluczy jednostek architektury globalnej sieci jest łączność między regionami między jednostki sieci i punktów końcowych. Zużycie chmury może obejmować wiele regionów. Oznacza to, że ruch z gałęzi, która jest połączone z chmurą w jednym regionie może osiągnąć w innej gałęzi lub sieci wirtualnej w innym regionie.

## <a name="any"></a>Łączność dowolna dowolna

Architektura sieci tranzytowej globalnego umożliwia *łączność dowolna dowolna* za pośrednictwem Centrum sieci centralnej. Ta architektura pozwala wyeliminować lub zmniejsza potrzebę siatki pełnej lub częściowej siatki łączności modeli, które są bardziej złożone do tworzenia i obsługi. Ponadto formant routingu w Gwiazda a sieciami siatki jest łatwiejsze do konfigurowania i konserwacji.

Łączność dowolna dowolna w kontekście architektury globalnego umożliwia w przedsiębiorstwie za pomocą globalnie dystrybuowana, użytkownicy, gałęzie, centrów danych, sieci wirtualne i aplikacje do łączenia się ze sobą za pośrednictwem Centrum przesyłania. Centrum przesyłania działa jako system przesyłania globalnego.

![ścieżki ruchu](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Rysunek 3: Ścieżki wirtualnej ruchu w sieci WAN**

Azure wirtualne sieci WAN obsługuje następujące ścieżki globalnego przesyłania w łączności. Litery w nawiasy są mapowane na rysunku 3.

* Gałąź między sieciami wirtualnymi ()  
* Gałęzi do gałęzi (b)
* Zdalny użytkownik między sieciami wirtualnymi (c)
* Użytkownik do gałęzi zdalnej (d)
* Sieć wirtualna między sieciami wirtualnymi za pomocą komunikacji równorzędnej sieci wirtualnej (e)
* Globalny zasięg usługi ExpressRoute 

### <a name="branchvnet"></a>Gałąź między sieciami wirtualnymi

Gałąź między sieciami wirtualnymi jest obsługiwane przez Azure wirtualnego WAN ścieżki podstawowej. Ta ścieżka pozwala łączyć gałęzi do obciążeń przedsiębiorstwa IAAS platformy Azure, które są wdrożone w sieci wirtualnych platformy Azure. Gałęzie mogą być połączone z wirtualnych sieci WAN za pośrednictwem usługi ExpressRoute lub sieci VPN typu lokacja lokacja. Tranzytu ruchu w sieciach wirtualnych, które są podłączone do wirtualnej sieci WAN koncentratorów, za pośrednictwem połączenia sieci wirtualnej.

### <a name="branchbranch"></a>Gałęzi do gałęzi

Gałęzie można połączyć z koncentratora WAN wirtualnych platformy Azure przy użyciu obwodów usługi ExpressRoute i/lub połączeń sieci VPN typu lokacja lokacja. Gałęzie, które można połączyć wirtualne sieci WAN koncentratora, który znajduje się w regionie najbliżej gałęzi.

Ta opcja umożliwia przedsiębiorstwom korzystać z sieci szkieletowej platformy Azure do łączenia z gałęzi. Jednak mimo że ta funkcja jest dostępna, należy porównać zalety łączenia gałęzi za pośrednictwem sieci platformy Azure z wirtualnych WAN, a za pomocą prywatnej sieci WAN.

### <a name="usertovnet"></a>Zdalny użytkownik między sieciami wirtualnymi

Można włączyć bezpośredniego bezpiecznego dostępu zdalnego na platformie Azure za pomocą połączenia punkt lokacja z klienta użytkownika zdalnego wirtualna sieć WAN. Użytkownicy zdalni Enterprise już hairpin w chmurze za pomocą firmowej sieci VPN.

### <a name="usertobranch"></a>Zdalny użytkownik do gałęzi

Ścieżka zdalnego użytkownika do gałęzi umożliwia użytkowników zdalnych, którzy za pomocą połączenia punkt lokacja do dostępu na platformie Azure lokalnych obciążeń i aplikacji przez użyciem przejścia przez chmurę. Ta ścieżka daje użytkownikom zdalnym swobodę obciążeń dostępu, które są wdrożone w platformy Azure i lokalnie. Przedsiębiorstwa można włączyć usługę centralnej oparte na chmurze bezpiecznego dostępu zdalnego w sieci platformy Azure z wirtualnych WAN.

### <a name="vnetvnet"></a>Sieć wirtualna-sieć wirtualna przesyłane za pomocą komunikacji równorzędnej sieci wirtualnych

Łączenie sieci wirtualnych ze sobą w celu zapewnienia obsługi aplikacji wielowarstwowych, które są implementowane przez wiele sieci wirtualnych, użyj komunikacji równorzędnej sieci wirtualnych. Sieć wirtualna-sieć wirtualna scenariusz przesyłane za pośrednictwem sieci platformy Azure z wirtualnych WAN nie jest obecnie obsługiwane, ale znajduje się na plan usługi Azure. Łączenie sieci wirtualnych za pośrednictwem komunikacji równorzędnej sieci wirtualnej to rozwiązanie zaleca się dla sieci wirtualnych, które muszą być połączone ze sobą. Aby uzyskać więcej informacji na temat komunikacji równorzędnej sieci wirtualnych, zobacz [Omówienie wirtualnych sieci równorzędnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="globalreach"></a>Globalny zasięg usługi ExpressRoute

Usługa ExpressRoute jest prywatny i odporne na błędy sposób łączenia sieci lokalnej usługą Microsoft Cloud. Usługa ExpressRoute zasięgu globalnym to funkcja dodatkowa dla usługi ExpressRoute. O zasięgu globalnym możesz połączyć obwodów usługi ExpressRoute razem w celu zapewnienia sieci prywatnej od sieci w środowisku lokalnym. Gałęzie, które są podłączone do usługi Azure wirtualne sieci WAN przy użyciu usługi ExpressRoute wymaga usługi ExpressRoute zasięgu globalnym do komunikowania się ze sobą.

W tym modelu każdej gałęzi, która jest podłączony do koncentratora wirtualnego sieci WAN, przy użyciu usługi ExpressRoute można połączyć z sieciami wirtualnymi przy użyciu ścieżki gałęzi między sieciami wirtualnymi. Ruch gałęzi do gałęzi nie tranzytu koncentratora, ponieważ zasięgu globalnym ExpressRoute umożliwia bardziej optymalne ścieżki platformy Azure w sieci WAN.

## <a name="security"></a>Zabezpieczenia i zasady kontroli

Centrum sieci wirtualnej typu i potencjalnie widzi cały ruch przesyłania. Może być w tym miejscu centralnej funkcji sieciowych hosta i usług, takich jak takich chmury routingu, zasad sieciowych i zabezpieczenia i kontrola dostępu do Internetu.

## <a name="next-steps"></a>Kolejne kroki

Utwórz połączenie przy użyciu wirtualnej sieci WAN.

* [Połączenia lokacja lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
* [Połączenia punkt lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-point-to-site-portal.md)
* [Połączenia ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)
