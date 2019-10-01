---
title: Architektura sieci tranzytu globalnego usługi Azure Virtual Network | Microsoft Docs
description: Dowiedz się więcej o globalnej architekturze sieci tranzytowej dla wirtualnej sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695270"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globalna architektura sieci tranzytowej i wirtualna sieć WAN

Globalna architektura sieci tranzytowej jest wdrażana przez przedsiębiorstwa do konsolidowania, łączenia i kontrolowania nowoczesnego przedsiębiorstwa opartego na chmurze. W nowoczesnej firmie zorientowanej na chmurę ruch sieciowy nie musi być CENTRALĄ. Globalna architektura sieci tranzytowej opiera się na obu znanych pojęciach dotyczących sieci i nowych koncepcjach, które są unikatowe dla architektur chmurowych i opartych na chmurze.

![architektura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Rysunek 1: globalna sieć tranzytowa z wirtualną siecią WAN**

Nowoczesne przedsiębiorstwa wymagają powszechnej łączności między aplikacjami rozproszonymi, danymi i użytkownikami w chmurze i lokalnymi. Wirtualna sieć WAN platformy Azure umożliwia globalną architekturę sieci tranzytowej przez umożliwienie powszechnej łączności między globalnie rozproszonymi zbiorami sieci wirtualnych, witryn, aplikacji i użytkowników. Wirtualna sieć WAN platformy Azure to usługa zarządzana przez firmę Microsoft. Wszystkie składniki sieci, które składają się na tę usługę, są hostowane i zarządzane przez firmę Microsoft. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz artykuł [Omówienie wirtualnych sieci WAN](virtual-wan-about.md) .

W architekturze sieci wirtualnej platformy Azure regiony platformy Azure służą jako centra, do których można połączyć gałęzie. Po nawiązaniu połączenia z gałęziami można wykorzystać szkielet platformy Azure do ustanowienia gałęzi do sieci wirtualnej, a opcjonalnie połączenia z gałęzią do gałęzi.

Można nawiązać wirtualną sieć WAN, tworząc pojedyncze wirtualne koncentratory sieci WAN w regionie, który ma największą liczbę szprych (gałęzie, sieci wirtualnych, użytkowników), a następnie łącząc szprychy, które znajdują się w innych regionach z koncentratorem. Alternatywnie, jeśli szprychy są rozproszone geograficznie, można również utworzyć wystąpienia centrów regionalnych i łączyć się z centrami. Koncentratory są częścią tej samej wirtualnej sieci WAN, ale mogą być skojarzone z różnymi zasadami regionalnymi.

## <a name="hub"></a>Tranzyt gwiazdy i gwiazdy

Globalna architektura sieci tranzytowej jest oparta na klasycznym modelu łączności typu Hub i satelity, w którym sieć hostowana w chmurze "Hub" umożliwia przechodnią łączność między punktami końcowymi, które mogą być dystrybuowane w różnych typach "szprych".
  
W tym modelu szprychą może być:

* Sieć wirtualna (sieci wirtualnych)
* Lokacja fizyczna
* Użytkownik zdalny
* Internet

![Diagram globalnego tranzytu Hub i szprych](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Rysunek 2: koncentrator i szprycha**

Rysunek 2 przedstawia widok logiczny sieci globalnej, w której użytkownicy rozproszoną geograficznie, lokacje fizyczne i sieci wirtualnych są współłączona za pośrednictwem Centrum sieciowego hostowanego w chmurze. Ta architektura umożliwia logiczne połączenie z jednym przeskokiem między punktami końcowymi sieci. Szprychy są połączone z koncentratorem za pomocą różnych usług sieciowych platformy Azure, takich jak ExpressRoute lub lokacja-VPN dla gałęzi fizycznych, połączeń sieci wirtualnej dla sieci wirtualnych, a także do połączeń VPN typu punkt-lokacja dla użytkowników zdalnych.

## <a name="crossregion"></a>Łączność między regionami

W przypadku przedsiębiorstwa zwykle odbywa się to w chmurze. Większość przedsiębiorstw uzyskuje dostęp do chmury z regionu znajdującego się najbliżej ich fizycznej witryny i użytkowników. Jednym z kluczowych zabezpieczeń architektury sieci globalnej jest włączenie łączności między obiektami sieciowymi i punktami końcowymi między różnymi regionami. Możliwości chmury mogą obejmować wiele regionów. Oznacza to, że ruch z gałęzi, która jest połączona z chmurą w jednym regionie, może należeć do innej gałęzi lub sieci wirtualnej w innym regionie przy użyciu połączenia typu Hub-do-Hub, która jest obecnie dostępna w naszym planie.

## <a name="any"></a>Łączność dowolna-do-dowolna

Globalna architektura sieci tranzytowej umożliwia korzystanie z *dowolnej łączności* za pośrednictwem centralnego centrum sieciowego. Ta architektura eliminuje lub zmniejsza potrzebę stosowania modeli łączności pełnej siatki lub częściowej siatki, które są bardziej skomplikowane do kompilowania i konserwowania. Ponadto w celu łatwiejszego konfigurowania i konserwowania kontroli routingu w sieciach typu Hub i szprych.

Dowolna z tych połączeń, w kontekście globalnej architektury, umożliwia przedsiębiorstwom globalnie rozproszonym użytkownikom, rozgałęzień, centrów danych, sieci wirtualnych i aplikacji łączenie się ze sobą za pośrednictwem centrum tranzytowego. Centrum tranzytowe pełni rolę globalnego systemu tranzytowego.

![ścieżki ruchu](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Rysunek 3. ścieżki ruchu wirtualnego sieci WAN**

Wirtualna sieć WAN platformy Azure obsługuje następujące globalne ścieżki połączeń tranzytowych. Litery w nawiasach są mapowane na rysunek 3.

* Odgałęzienie do sieci wirtualnej (a)  
* Rozgałęzienie (b)
* Zdalne użytkownika do sieci wirtualnej (c)
* Zdalny użytkownik do rozgałęzienia (d)
* Sieć wirtualna-sieć wirtualna z użyciem komunikacji równorzędnej sieci wirtualnej (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Rozgałęzienie do sieci wirtualnej

Gałąź do sieci wirtualnej jest ścieżką podstawową obsługiwaną przez wirtualną sieć WAN platformy Azure. Ta ścieżka umożliwia łączenie gałęzi z obciążeniami platformy Azure IAAS Enterprise, które są wdrożone w usłudze Azure sieci wirtualnych. Gałęzie mogą być połączone z wirtualną siecią WAN za pośrednictwem ExpressRoute lub sieci VPN typu lokacja-lokacja. Ruch do sieci wirtualnych podłączony do wirtualnych koncentratorów sieci WAN za pośrednictwem połączeń VNet. [Tranzyt bramy](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) nie jest wymagany w przypadku wirtualnej sieci WAN, ponieważ wirtualne sieci WAN automatycznie włączają przesyłanie bramy do lokacji gałęzi.

### <a name="branchbranch"></a>Rozgałęzienie do gałęzi

Gałęzie mogą być połączone z koncentratorem wirtualnej sieci WAN platformy Azure przy użyciu obwodów usługi ExpressRoute i/lub połączeń sieci VPN typu lokacja-lokacja. Gałęzie można połączyć z koncentratorem wirtualnej sieci WAN, który znajduje się w regionie najbliższym gałęzi.

Ta opcja umożliwia przedsiębiorstwom wykorzystanie szkieletu platformy Azure do łączenia gałęzi. Jednak mimo że ta funkcja jest dostępna, należy zastanowić się nad korzyściami z łączenia gałęzi za pośrednictwem wirtualnej sieci WAN platformy Azure a korzystanie z prywatnej sieci WAN.

### <a name="usertovnet"></a>Zdalny użytkownik-sieć wirtualna

Możesz włączyć bezpośredni i bezpieczny dostęp zdalny do platformy Azure przy użyciu połączeń punkt-lokacja z zdalnego klienta użytkownika z wirtualną siecią WAN. Użytkownicy zdalni przedsiębiorstwa nie muszą już hairpin do chmury przy użyciu firmowej sieci VPN.

### <a name="usertobranch"></a>Użytkownik zdalny do rozgałęzienia

Zdalna ścieżka użytkownika do gałęzi umożliwia użytkownikom zdalnym, którzy korzystają z połączenia typu punkt-lokacja z lokalnymi obciążeniami i aplikacjami dostępnymi na platformie Azure przez przeprowadzenie przesyłania danych przez chmurę. Ta ścieżka zapewnia użytkownikom zdalnym elastyczność dostępu do obciążeń, które są wdrażane na platformie Azure i lokalnie. Przedsiębiorstwa mogą włączyć centralną, opartą na chmurze usługę bezpiecznego dostępu zdalnego w wirtualnej sieci WAN platformy Azure.

### <a name="vnetvnet"></a>Tranzyt między sieciami wirtualnymi przy użyciu komunikacji równorzędnej VNet

Aby połączyć sieci wirtualnych ze sobą w celu obsługi aplikacji wielowarstwowych, które są implementowane przez wiele sieci wirtualnych, należy użyć komunikacji równorzędnej sieci wirtualnej. Scenariusz przesyłania między sieciami wirtualnymi za pośrednictwem wirtualnej sieci WAN platformy Azure nie jest obecnie obsługiwany, ale znajduje się w planie Azure. Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej jest zalecanym rozwiązaniem dla sieci wirtualnych, które muszą być ze sobą połączone. 

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute to prywatny i odporny na połączenie sieci lokalnych z Microsoft Cloud. ExpressRoute Global Reach to funkcja dodatku dla ExpressRoute. Za pomocą Global Reach można połączyć obwody usługi ExpressRoute w celu naprowadzenia prywatnej sieci między sieciami lokalnymi. Gałęzie, które są połączone z wirtualną siecią WAN platformy Azure za pomocą ExpressRoute, wymagają, aby Global Reach ExpressRoute się ze sobą.

W tym modelu każda gałąź, która jest połączona z wirtualnym koncentratorem sieci WAN przy użyciu programu ExpressRoute, może łączyć się z sieci wirtualnych przy użyciu ścieżki gałęzi do sieci wirtualnej. Ruch rozgałęzienia do gałęzi nie będzie przenoszony do centrum, ponieważ ExpressRoute Global Reach zapewnia bardziej optymalną ścieżkę w sieci WAN platformy Azure.

## <a name="security"></a>Zabezpieczenia i kontrola zasad

Koncentrator sieci wirtualnej umożliwia nawiązanie połączenia i potencjalnie widzi cały ruch tranzytowy. Może to być miejsce do hostowania centralnych funkcji sieciowych i usług, takich jak routing w chmurze, zasady sieci i zabezpieczenia oraz kontrola dostępu do Internetu.

## <a name="next-steps"></a>Następne kroki

Utwórz połączenie za pomocą wirtualnej sieci WAN.

* [Połączenia między lokacjami przy użyciu wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute połączeń przy użyciu wirtualnej sieci WAN](virtual-wan-expressroute-portal.md)
