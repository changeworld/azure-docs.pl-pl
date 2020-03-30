---
title: Łączenie platformy Azure z chmurami publicznymi | Dokumenty firmy Microsoft
description: Opis różnych sposobów łączenia platformy Azure z innymi chmurami publicznymi
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889625"
---
# <a name="connecting-azure-with-public-clouds"></a>Łączenie platformy Azure z chmurami publicznymi

Wiele przedsiębiorstw realizuje strategię wielochmurową ze względu na cele biznesowe i techniczne. Obejmują one koszty, elastyczność, dostępność funkcji, nadmiarowość, suwerenność danych itp. Ta strategia pomaga im wykorzystać najlepsze z obu chmur. 

Takie podejście stanowi również wyzwanie dla przedsiębiorstwa pod względem architektury sieci i aplikacji. Niektóre z tych wyzwań są opóźnienia i przepływności danych. Aby sprostać tym wyzwaniom, klienci chcą bezpośrednio połączyć się z wieloma chmurami. Niektórzy dostawcy usług zapewniają rozwiązanie do łączenia wielu dostawców chmury dla klientów. W innych przypadkach klient może wdrożyć własny router, aby połączyć wiele chmur publicznych.
## <a name="connectivity-via-expressroute"></a>Łączność za pośrednictwem usługi ExpressRoute
Usługa ExpressRoute umożliwia klientom rozszerzanie sieci lokalnych do chmury firmy Microsoft za pośrednictwem połączenia prywatnego ułatwione przez dostawcę łączności. Dzięki usłudze ExpressRoute klienci mogą nawiązywać połączenia z usługami w chmurze firmy Microsoft.

Istnieją trzy sposoby łączenia się za pośrednictwem usługi ExpressRoute.

1. Dostawca warstwy 3
2. Dostawca warstwy 2
3. Połączenie bezpośrednie

### <a name="layer3-provider"></a>Dostawca warstwy 3

Dostawcy warstwy 3 są powszechnie znani jako dostawcy sieci VPN IP lub MPLS. Klienci wykorzystują tych dostawców do łączności wielopunktowej między centrami danych, oddziałami i chmurą. Klienci łączą się z dostawcą L3 za pośrednictwem protokołu BGP lub statycznej trasy domyślnej. Usługodawca anonsuje trasy między lokacjami klientów, centrami danych i chmurą publiczną. 
 
Podczas łączenia się za pośrednictwem dostawcy warstwy 3 firma Microsoft anonsuje trasy sieci wirtualnej klienta do dostawcy usług za pośrednictwem protokołu BGP. Dostawca może mieć dwie różne implementacje.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Dostawca może być lądowania każdego dostawcy chmury w oddzielnym VRF, jeśli ruch ze wszystkich dostawców chmury dotrze do routera klienta. Jeśli klient jest uruchomiony BGP z dostawcą usług, trasy te zostaną ponownie anonsowane do innych dostawców chmury domyślnie. 

Jeśli dostawca usług jest lądowanie wszystkich dostawców chmury w tym samym VRF, a następnie trasy będą reklamowane do innych dostawców chmury od dostawcy usług bezpośrednio. Zakłada to standardową operację BGP, w której trasy eBGP są domyślnie anonsowane innym sąsiadom eBGP.

Każda chmura publiczna ma inny limit prefiksów, więc podczas dystrybucji usług usług tras dostawca powinien zachować ostrożność w dystrybucji tras.

### <a name="layer2-provider-and-direct-connection"></a>Dostawca warstwy2 i bezpośrednie połączenie

Chociaż łączność fizyczna w obu modelach jest inna, ale w warstwie 3 BGP jest ustanawiana bezpośrednio między MSEE a routerem klienta. W przypadku usługi ExpressRoute Direct klient łączy się bezpośrednio z MSEE. W przypadku warstwy 2 dostawca usług rozszerza sieć VLAN z pomieszczeń klienta do chmury. Klienci uruchamiają usługę BGP w sieci layer2, aby połączyć kontrolery domeny z chmurą.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
W obu przypadkach klient będzie miał połączenia typu punkt-punkt do każdej chmury publicznej. Klient nawiąza oddzielne połączenie BGP do każdej chmury publicznej. Trasy odebrane przez jednego dostawcę chmury będą domyślnie anonsowane innemu dostawcy chmury. Każdy dostawca chmury ma inny limit prefiksów, więc podczas reklamowania tras klient powinien zająć się tymi limitami. Klient może używać zwykłych pokręteł BGP z firmą Microsoft podczas reklamowania tras z innych chmur publicznych.

## <a name="direct-connection-with-expressroute"></a>Bezpośrednie połączenie z usługą ExpressRoute

Klienci mogą połączyć usługę ExpressRoute bezpośrednio z ofertą bezpośredniej łączności dostawcy chmury. Dwóch dostawców chmury zostanie połączonych z powrotem do tyłu, a BGP zostanie utworzony bezpośrednio między ich routerami. Ten typ połączenia jest już dostępny w oracle.

## <a name="site-to-site-vpn"></a>Sieć VPN typu lokacja-lokacja

Klienci mogą korzystać z Internetu, aby połączyć swoje wystąpienia na platformie Azure z innymi chmurami publicznymi. Prawie wszyscy dostawcy chmury oferują funkcje sieci VPN typu lokacja-lokacja. Jednak nie może być niezgodności z powodu braku niektórych wariantów. Na przykład niektórzy dostawcy chmury obsługują tylko IKEv1, więc w tej chmurze jest wymagany punkt końcowy zakończenia sieci VPN. Dla tych dostawców chmury obsługujących IKEv2 można ustanowić bezpośredni tunel między bramami sieci VPN u obu dostawców chmury.

Sieć VPN typu lokacja-lokacja nie jest uważana za rozwiązanie o wysokiej przepływności i małych opóźnieniach. Jednak może służyć jako kopia zapasowa do łączności fizycznej.

## <a name="next-steps"></a>Następne kroki
Zobacz [często zadawane pytania dotyczące usługi ExpressRoute, aby][ER-FAQ] uzyskać więcej pytań dotyczących usługi ExpressRoute i łączności z siecią wirtualną.

Zobacz [Konfigurowanie bezpośredniego połączenia między platformą Azure a oracle cloud][ER-OCI] w celu łączności między platformą Azure a Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



