---
title: 'Lokalizacje i dostawcy połączeń: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje są posortowane według lokalizacji.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2018
ms.author: pareshmu
ms.openlocfilehash: 8372cd82e9b62c4440a9e7d525b7135153e59dde
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378072"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


Tabele w tym artykule zawierają informacje dotyczące dostawców połączenia ExpressRoute, zasięgu geograficznego usługi ExpressRoute, usług w chmurze firmy Microsoft obsługiwanych za pośrednictwem usługi ExpressRoute oraz integratorów systemowych ExpressRoute (SI).

## <a name="partners"></a>Dostawcy połączenia usługi ExpressRoute
Usługa ExpressRoute jest obsługiwana we wszystkich regionach i lokalizacjach świadczenia usługi Azure. Poniższa mapa zawiera listę regionów świadczenia usługi Azure i lokalizacji usługi ExpressRoute. Lokalizacje usługi ExpressRoute to te, w których firma Microsoft prowadzi komunikację równorzędną z kilkoma dostawcami usług.

![Mapa lokalizacji][0]

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie. 

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiony świadczenia usługi Azure i lokalizacje usługi ExpressRoute w regionach geopolitycznych
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Ameryka Północna** |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Dolina Krzemowa, Las Vegas, Los Angeles, Miami, Miasto Quebec, Montreal, Nowy Jork, San Antonio, Seattle, Toronto, Waszyngton |
| **Ameryka Południowa** |Brazylia Południowa |Sao Paulo |
| **Europa** |Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |Amsterdam, Amsterdam2, Dublin, Londyn, Marsylia, Newport (Walia), Paryż |
| **Azja** |Azja Wschodnia, Azja Południowo-Wschodnia |Hongkong, Kuala Lumpur, Singapur, Singapur2 |
| **Japonia** |Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio |
| **Australia** |Australia Południowo-Wschodnia, Australia Wschodnia |Melbourne, Sydney |
| **Australia — instytucje rządowe** | Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 | 
| **Indie** |Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai (Madras), Mumbaj (Bombaj) |
| **Korea Południowa** |Korea Środkowa, Korea Południowa |Busan, Seul |
| **Republika Południowej Afryki** |[Zachodnia RPA+, Północna RPA+](https://blogs.microsoft.com/blog/2017/05/18/microsoft-deliver-microsoft-cloud-datacenters-africa/) |Kapsztad, Johannesburg |

 **+** oznacza wkrótce


### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony i granice geopolityczne chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="locations"></a>Lokalizacje dostawców połączenia

W poniższej tabeli przedstawiono lokalizacje połączeń i dostawców usług dla każdej lokalizacji. Jeśli chcesz wyświetlić dostawców usług i lokalizacje, w których świadczą usługi, zobacz [Lokalizacje według dostawcy usług](expressroute-locations.md#locations). 


### <a name="production-azure"></a>Środowisko produkcyjne Azure
| **Lokalizacja** | **Właściciel lokalizacji komunikacji równorzędnej** | **Dostawcy usług** |
| --- | --- | --- |
| **Amsterdam** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Verizon, Zayo |
| **Amsterdam2** | Interxion | Interxion |
| **Atlanta** | Equinix | Equinix, Megaport |
| **Pusan** |LG CNS | LG CNS |
| **Canberra** | CDC | CDC |
| **Canberra2** | CDC | CDC |
| **Kapsztad** | Teraco | Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Airtel |
| **Chicago** | Equinix | AT&T NetBond, Comcast, Coresite, Equinix, PacketFabric, PCCW Global Limited, Level 3 Communications, Megaport, Sprint, Verizon, Zayo |
| **Dallas** | Equinix | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet+, Verizon, Zayo|
| **Denver** | CoreSite | CoreSite, Megaport |
| **Dublin** | Equinix | Colt, eir, Equinix, Interxion, Megaport |
| **Hongkong** | Equinix | Aryaka Networks, British Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | CZAS | CZAS |
| **Las Vegas** | Przełącznik | CenturyLink Cloud Connect, Megaport |
| **Londyn** | Equinix | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions — Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Los Angeles** | CoreSite | CoreSite, Equinix, Megaport, NTT, Zayo |
| **Marsylia** |Interxion | Interxion |
| **Melbourne** | NextDC | AARNet, Equinix, Megaport, NEXTDC, Optus+, Telstra Corporation |
| **Miami** | Equinix | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | Bell Canada, Cologix, Telus, Zayo |
| **Mumbaj** | Tata Communications | Global CloudXchange (GCX), Sify, Tata Communications, Vodafone Idea |
| **Mumbaj2** | Airtel | Airtel, Sify |
| **Nowy Jork** | Equinix | CenturyLink Cloud Connect, Coresite, Equinix, Megaport, Zayo |
| **Newport (Walia)** | Dane nowej generacji | Level 3 Communications, Next Generation Data |
| **Osaka** | Equinix | Equinix, Internet Initiative Japan Inc. — IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paryż** | Interxion | Colt, Intercloud, Interxion, Equinix, Orange |
| **Miasto Quebec** | 4Degrees | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Ascenty Data Centers, Equinix, Level 3 Communications, Neutrona Networks, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | Equinix, Level 3 Communications, Megaport |
| **Seul** | KINX | KINX, LG CNS, Sejong Telecom |
| **Dolina Krzemowa** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, IXReach, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Verizon, Zayo Group |
| **Singapur** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Verizon |
| **Singapur2** | Przełącznik globalny | Megaport, SingTel |
| **Sydney** | Equinix | AARNet, AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, Verizon |
| **Tokio** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. — IIJ, NTT Communications, NTT EAST, Softbank, Verizon |
| **Toronto** | Cologix | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, Megaport, Telus, Zayo |
| **Waszyngton** | Equinix | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |

 **+** oznacza wkrótce

### <a name="national-cloud-environments"></a>Krajowe środowiska chmury

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **Nowy Jork** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | CenturyLink Cloud Connect |
| **San Antonio** | Megaport |
| **Dolina Krzemowa** | Equinix, Level 3 Communications |
| **Seattle** | Equinix |
| **Waszyngton** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |

### <a name="china"></a>Chiny
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Pekin** |China Telecom |
| **Szanghaj** |China Telecom |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach)

### <a name="germany"></a>Niemcy
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Łączność za pośrednictwem dostawców usług Exchange
Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [NextDC](http://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="c1partners"></a>Łączność za pośrednictwem dodatkowych dostawców usług
| **Lokalizacja** | **Exchange** | **Dostawcy połączeń** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, Eurofiber, Fastweb S.p.A, MainOne, Nianet, Post, Proximus, Telecom Italia Sparkle, Telia |
| **Chicago** | Equinix | Lightower, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Data Foundry, Transtelco |
| **Frankfurt** | Telecity | BICS, Nianet, QSC AG |
| **Hongkong** | Equinix | Macroview Telecom |
| **Londyn** | Equinix, euNetworks, Telecity | Bezeq International Ltd., Epsilon, Exponential E, HSO, NexGen Networks, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco |
| **Madryt** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies. Inc, Cogeco Peer 1, Rogers, Zirro |
| **Nowy Jork** |Equinix, Megaport | Altice Business, Lightower, Webair |
| **Seattle** |Equinix | Alaska Communications |
| **Dolina Krzemowa** |Equinix | Cox Business, Windstream |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix | Airgate Technologies. Inc, Cogeco Peer 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Waszyngton** |Equinix | Altice Business, BICS, Gtt Communications Inc, Epsilon, Lightower, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Kontynent** | **Integratorzy systemów** |
| --- | --- |
| **Azja** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Ameryka Północna** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Ameryka Południowa** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
