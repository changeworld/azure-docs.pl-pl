---
title: 'Architektura: Architektura globalnej sieci tranzytowej'
titleSuffix: Azure Virtual WAN
description: Dowiedz się więcej o globalnej architekturze sieci tranzytowej dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064975"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globalna architektura sieci tranzytowej i wirtualna sieć WAN

Nowoczesne przedsiębiorstwa wymagają wszechobecnej łączności między hiper-rozproszonymi aplikacjami, danymi i użytkownikami w chmurze i lokalnie. Globalna architektura sieci tranzytowej jest wdrażana przez przedsiębiorstwa w celu konsolidacji, łączenia i kontrolowania nowoczesnego, globalnego śladu it dla przedsiębiorstw zorientowanego na chmurę.

Globalna architektura sieci tranzytowej jest oparta na klasycznym modelu łączności koncentratora i szprychy, w którym "koncentrator" sieci hostowany w chmurze umożliwia przechodnie łączność między punktami końcowymi, które mogą być rozproszone między różnymi typami "szprych".

W tym modelu szprycha może być:
* Sieć wirtualna (sieci wirtualne)
* Fizyczna lokacja oddziału
* Użytkownik zdalny
* Internet

![piasta i szprycha](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Wykres 1: Globalna sieć węzłów tranzytowych i szprych**

Rysunek 1 przedstawia logiczny widok globalnej sieci tranzytowej, w której geograficznie rozproszone użytkownicy, lokacje fizyczne i sieci wirtualne są ze sobą połączone za pośrednictwem koncentratora sieciowego hostowanego w chmurze. Ta architektura umożliwia logiczne połączenie tranzytowe z jednym przeskoku między punktami końcowymi sieci.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globalna sieć tranzytowa z wirtualną siecią WAN

Wirtualna sieć WAN platformy Azure to usługa sieci w chmurze zarządzana przez firmę Microsoft. Wszystkie składniki sieci, z których składa się ta usługa, są hostowane i zarządzane przez firmę Microsoft. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [omówienie wirtualnej sieci WAN.](virtual-wan-about.md)

Wirtualna sieć WAN platformy Azure umożliwia architekturę globalnej sieci tranzytowej, umożliwiając wszechobecną łączność między globalnie rozproszonymi zestawami obciążeń w chmurze w sieciach wirtualnych, lokacjach oddziałów, aplikacjach SaaS i PaaS oraz użytkownikach.

![Wirtualna sieć WAN platformy Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Rysunek 2: Globalna sieć tranzytowa i wirtualna sieć WAN**

W architekturze wirtualnej sieci WAN platformy Azure wirtualne koncentratory sieci WAN są aprowiedzone w regionach platformy Azure, z którymi można połączyć swoje gałęzie, sieci wirtualne i użytkowników zdalnych. Lokacje oddziałów fizycznych są połączone z koncentratorem przez usługi Premium ExpressRoute lub sieci VPN lokacji do lokacji, sieci wirtualne są połączone z koncentratorem za pomocą połączeń sieci wirtualnych, a użytkownicy zdalni mogą bezpośrednio łączyć się z koncentratorem za pomocą sieci VPN użytkownika (sieci VPN typu lokacja). Wirtualna sieć WAN obsługuje również międzyregionowe połączenie sieci wirtualnej, w którym sieć wirtualna w jednym regionie może być połączona z koncentratorem wirtualnej sieci WAN w innym regionie.

Można ustanowić wirtualną sieć WAN, tworząc pojedynczy wirtualny koncentrator sieci WAN w regionie, który ma największą liczbę szprych (oddziałów, sieci wirtualnych, użytkowników), a następnie łącząc szprychy, które znajdują się w innych regionach z koncentratorem. Jest to dobry opcja, gdy ślad przedsiębiorstwa jest głównie w jednym regionie z kilkoma zdalnymi szprychami.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Łączność między koncentratorem

Rozmiar chmury przedsiębiorstwa może obejmować wiele regionów chmury i jest optymalny (z opóźnieniem) dostęp do chmury z regionu najbliższego ich fizycznej witryny i użytkowników. Jedną z kluczowych zasad architektury globalnej sieci tranzytowej jest umożliwienie łączności między regionami między wszystkimi punktami końcowymi sieci w chmurze i lokalnie. Oznacza to, że ruch z gałęzi, która jest połączona z chmurą w jednym regionie, może dotrzeć do innej gałęzi lub sieci wirtualnej w innym regionie przy użyciu łączności hub-to-hub włączonej przez [usługę Azure Global Network.](https://azure.microsoft.com/global-infrastructure/global-network/)

![międzyregionów](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Rysunek 3: Łączność międzyregionami wirtualnej sieci WAN**

Gdy wiele koncentratorów są włączone w jednej wirtualnej sieci WAN, koncentratory są automatycznie połączone za pośrednictwem łączy hub-to-hub, umożliwiając w ten sposób globalną łączność między oddziałami i sieci wirtualnych, które są rozproszone w wielu regionach. 

Ponadto koncentratory, które są częścią tej samej wirtualnej sieci WAN, mogą być skojarzone z różnymi regionalnymi zasadami dostępu i zabezpieczeń. Aby uzyskać więcej informacji, zobacz [zabezpieczenia i kontrola zasad](#security) w dalszej części tego artykułu.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Łączność typu "dowolna"

Globalna architektura sieci tranzytowej umożliwia łączność dowolną za pośrednictwem wirtualnych koncentratorów sieci WAN. Ta architektura eliminuje lub zmniejsza potrzebę pełnej siatki lub częściowej łączności siatki między szprychami, które są bardziej złożone do tworzenia i konserwacji. Ponadto sterowanie routingu w sieciach hub-and-spoke vs. mesh jest łatwiejsze do skonfigurowania i obsługi.

Łączność typu "każdy dowolna" (w kontekście architektury globalnej) umożliwia przedsiębiorstwu z globalnie rozproszonymi użytkownikami, oddziałami, centrami danych, sieciami wirtualnymi i aplikacjami łączenie się ze sobą za pośrednictwem koncentratora "tranzytowego". Wirtualna sieć WAN platformy Azure działa jako globalny system tranzytowy.

![do jakichkolwiek](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Rysunek 4: Wirtualne ścieżki ruchu WAN**

Wirtualna sieć WAN platformy Azure obsługuje następujące ścieżki globalnej łączności tranzytowej. Litery w nawiasach są mapowane na rysunek 4.

* Sieć odgałęzienia do sieci wirtualnej (a)
* Odgałęzienie do gałęzi (b)
  * Globalny zasięg usługi ExpressRoute i wirtualna sieć WAN
* Zdalna usługa User-to-VNet (c)
* Zdalny użytkownik do gałęzi (d)
* Sieci wirtualnej do sieci wirtualnej (e)
* Odgałęzienie do koncentratora-do gałęzi (f)
* Sieć odgałęzienia do koncentratora do sieci wirtualnej (g)
* Sieci wirtualnej do koncentratora-do sieci wirtualnej (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Sieć międzygałęzienną a) i międzyregionem międzysieciową międzygałęźnikami (g)

Sieć lokacji do sieci wirtualnej jest podstawową ścieżką obsługiwaną przez wirtualną sieć WAN platformy Azure. Ta ścieżka umożliwia łączenie oddziałów z obciążeniami korporacyjnymi usługi Azure IAAS, które są wdrażane w sieciach wirtualnych platformy Azure. Gałęzie mogą być połączone z wirtualną siecią WAN za pośrednictwem usługi ExpressRoute lub sieci VPN typu lokacja lokacja. Ruch przechodzi do sieci wirtualnych, które są połączone z wirtualnych koncentratorów sieci WAN za pośrednictwem połączeń sieci wirtualnej. Jawne [przesyłanie bramy](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) nie jest wymagane dla wirtualnej sieci WAN, ponieważ wirtualna sieć WAN automatycznie umożliwia przesyłanie bramy do lokacji oddziału. Zobacz artykuł [Virtual WAN Partners](virtual-wan-configure-automation-providers.md) na temat podłączania cpe SD-WAN do wirtualnej sieci WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>Globalny zasięg usługi ExpressRoute i wirtualna sieć WAN

Usługa ExpressRoute to prywatny i odporny sposób łączenia sieci lokalnych z usługą Microsoft Cloud. Wirtualna sieć WAN obsługuje połączenia obwodów trasy ekspresowej. Połączenie lokacji oddziału z wirtualną siecią WAN z trasą ekspresową wymaga 1) obwodu Premium Circuit 2) w lokalizacji obsługującej globalny zasięg.

Globalny zasięg usługi ExpressRoute to funkcja dodatkowa dla usługi ExpressRoute. Dzięki globalnemu zasięgowi możesz połączyć obwody usługi ExpressRoute, aby utworzyć sieć prywatną między sieciami lokalnymi. Gałęzie, które są połączone z usługą Azure Virtual WAN przy użyciu usługi ExpressRoute wymagają globalnego zasięgu usługi ExpressRoute do komunikowania się ze sobą.

W tym modelu każda gałąź, która jest połączona z koncentratorem wirtualnej sieci WAN przy użyciu usługi ExpressRoute, może łączyć się z sieciami wirtualnymi przy użyciu ścieżki odgałęzienia do sieci wirtualnej. Ruch między gałęziami nie będzie przesyłał koncentratora, ponieważ globalny zasięg usługi ExpressRoute umożliwia bardziej optymalną ścieżkę za pomocą usługi Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Międzyregionem do gałęzi (b) i międzyregionami międzygałęźniami

Gałęzie mogą być połączone z wirtualnym koncentratorem sieci WAN platformy Azure przy użyciu obwodów usługi ExpressRoute i/lub połączeń sieci VPN między lokacjami. Gałęzie można połączyć z wirtualnym koncentratorem sieci WAN, który znajduje się w regionie najbliższym gałęzi.

Ta opcja umożliwia przedsiębiorstwom korzystanie z szkieletu platformy Azure w celu łączenia gałęzi. Jednak mimo że ta funkcja jest dostępna, należy rozważyć korzyści wynikające z łączenia gałęzi za pośrednictwem wirtualnej sieci WAN platformy Azure w porównaniu z prywatną siecią WAN.  

### <a name="remote-user-to-vnet-c"></a>Zdalna usługa User-to-VNet (c)

Można włączyć bezpośredni, bezpieczny zdalny dostęp do platformy Azure przy użyciu połączenia typu punkt-lokacja z klienta zdalnego użytkownika do wirtualnej sieci WAN. Użytkownicy zdalni w przedsiębiorstwie nie muszą już korzystać z sieci ową do chmury za pomocą firmowej sieci VPN.

### <a name="remote-user-to-branch-d"></a>Zdalny użytkownik do gałęzi (d)

Ścieżka zdalnego użytkownika do gałęzi umożliwia użytkownikom zdalnym korzystającym z połączenia typu "punkt-lokacja" lokalne obciążenia i aplikacje dostępu do lokalnych obciążeń i aplikacji przez przesyłanie przez chmurę. Ta ścieżka zapewnia użytkownikom zdalnym elastyczność dostępu do obciążeń, które są wdrażane zarówno na platformie Azure, jak i lokalnie. Przedsiębiorstwa mogą włączyć centralną chmurową usługę bezpiecznego dostępu zdalnego w wirtualnej sieci WAN platformy Azure.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Tranzyt sieci wirtualnej do sieci wirtualnej (e) i międzyregionem sieci wirtualnej do sieci wirtualnej (h)

Tranzyt sieci wirtualnej do sieci wirtualnej umożliwia sieci wirtualne połączyć się ze sobą w celu połączenia aplikacji wielowarstwowych, które są implementowane w wielu sieciach wirtualnych. Opcjonalnie można połączyć sieci wirtualne ze sobą za pośrednictwem komunikacji równorzędnej sieci wirtualnej i może to być odpowiednie dla niektórych scenariuszy, w których tranzyt przez koncentrator VWAN nie jest konieczne.

## <a name="security-and-policy-control"></a><a name="security"></a>Bezpieczeństwo i kontrola zasad

Centra wirtualnej sieci WAN platformy Azure łączą wszystkie punkty końcowe sieci w sieci hybrydowej i potencjalnie widzą cały ruch sieciowy przesyłania. Wirtualne koncentratory sieci WAN można przekonwertować na zabezpieczone koncentratory wirtualne, wdrażając zaporę platformy Azure w centrach wirtualnych sieci VWAN, aby umożliwić bezpieczeństwo oparte na chmurze, dostęp i kontrolę zasad. Aranżacja zapór platformy Azure w wirtualnych centrach sieci WAN może być wykonywana przez usługę Azure Firewall Manager.

[Usługa Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) udostępnia funkcje zarządzania i skalowania zabezpieczeń dla globalnych sieci tranzytowych. Usługa Azure Firewall Manager umożliwia centralne zarządzanie routingiem, globalnym zarządzaniem zasadami, zaawansowanymi usługami zabezpieczeń internetowych za pośrednictwem innych firm wraz z Zaporą platformy Azure.

![zabezpieczony koncentrator wirtualny z zaporą platformy Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Rysunek 5: Zabezpieczone centrum wirtualne z zaporą platformy Azure**

Zapora azure do wirtualnej sieci WAN obsługuje następujące ścieżki połączeń zabezpieczonych transportu globalnego. Litery w nawiasach są mapowane na rysunek 5.

* Bezpieczny tranzyt sieci wirtualnej do sieci wirtualnej (e)
* Wirtualna do Internetu lub usługi zabezpieczeń innych firm (i)
* Usługa zabezpieczeń między gałęziami a Internetem lub usługami zabezpieczeń innych firm (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Tranzyt zabezpieczony sieci wirtualnej do sieci wirtualnej (e)

Tranzyt zabezpieczony sieci wirtualnej do sieci wirtualnej umożliwia sieciom wirtualnym łączenie się ze sobą za pośrednictwem zapory platformy Azure w wirtualnym centrum sieci WAN.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Wirtualna do Internetu lub usługi zabezpieczeń innych firm (i)

Sieć wirtualna do Internetu lub tranzyt zabezpieczony innej firmy umożliwia sieciom wirtualnym łączenie się z Internetem lub obsługiwanymi usługami zabezpieczeń innych firm za pośrednictwem zapory azure w wirtualnym centrum sieci WAN.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Usługa zabezpieczeń między gałęziami a Internetem lub usługami zabezpieczeń innych firm (j)
Usługa przesyłania danych typu branch-to-Internet lub innej firmy secure umożliwia oddziałom łączenie się z Internetem lub obsługiwanymi usługami zabezpieczeń innych firm za pośrednictwem zapory azure w wirtualnym centrum sieci WAN.

## <a name="next-steps"></a>Następne kroki

Tworzenie połączenia przy użyciu wirtualnej sieci WAN i wdrażanie zapory platformy Azure w centrum wirtualnej sieci Wirtualnej sieci..

* [Połączenia lokacja-lokacja przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
* [Połączenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)
* [Usługa Azure Firewall Manager do wdrażania usługi Azure FW w usłudze VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
