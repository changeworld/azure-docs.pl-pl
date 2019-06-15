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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jaredr80
ms.openlocfilehash: f7006a8162aab87bc36f3dae034f10daa3f37ac1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67115451"
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

| **Region geopolityczny** | **Strefa** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- | --- |
| **Australia — instytucje rządowe** | 1 | Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | 1 |Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |Amsterdam, Amsterdam2, Dublin, Frankfurt, Londyn, London2, Marsylii, Newport (Walia), Paryż, Zurych |
| **Ameryka Północna** | 1 |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nowy Jork, San Antonio, Seattle, Dolina Krzemowa, dolina Valley2, DC2 Waszyngton DC, Waszyngton, Montreal, Miasto Quebec, Toronto |
| **Azja** | 2 |Azja Wschodnia, Azja Południowo-Wschodnia |Hongkong SAR, Kuala Lumpur, Singapur, singapur2 Tajpej |
| **Australia** | 2 |Australia Południowo-Wschodnia, Australia Wschodnia |Melbourne, Perth, Sydney | 
| **Indie** | 2 |Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | 2 |Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio |
| **Korea Południowa** | 2 |Korea Środkowa, Korea Południowa |Busan, Seul|
| **ZJEDNOCZONE EMIRATY ARABSKIE** | 3 | Środkowe Zjednoczone Emiraty Arabskie, Zjednoczone Emiraty Arabskie Północna | Dubaj, Dubai2 |
| **Republika Południowej Afryki** | 3 |Republika Południowej Afryki (Zachód), Republika Południowej Afryki Północna |Kapsztad, Johannesburg |
| **Ameryka Południowa** | 3 |Brazylia Południowa |Sao Paulo |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony i granice geopolityczne chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="locations"></a>Lokalizacje dostawców połączenia

W poniższej tabeli przedstawiono lokalizacje połączeń i dostawców usług dla każdej lokalizacji. Jeśli chcesz wyświetlić dostawców usług i lokalizacje, w których świadczą usługi, zobacz [Lokalizacje według dostawcy usług](expressroute-locations.md#locations). 

**Regiony platformy Azure w lokalnych** są tymi, które [lokalnej usługi ExpressRoute](expressroute-faqs.md) w każdej lokalizacji komunikacji równorzędnej mogą uzyskiwać dostęp do. **n/d** wskazuje, że usługa ExpressRoute lokalne nie jest dostępna w tej lokalizacji komunikacji równorzędnej.


### <a name="production-azure"></a>Środowisko produkcyjne Azure
| **Lokalizacja** | **Właściciel lokalizacji komunikacji równorzędnej** | **Lokalne regiony platformy Azure** | **Dostawcy usług** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europa Zachodnia | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europa Zachodnia | DE-CIX, Interxion |
| **Atlanta** | Equinix | Nie dotyczy | Equinix, Megaport |
| **Pusan** |LG CNS | Korea Południowa | LG CNS |
| **Canberra** | CDC | Australia Środkowa | CDC |
| **Canberra2** | CDC | Australia Środkowa 2| CDC |
| **Kapsztad** | Teraco | Republika Południowej Afryki (zachód) | Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Indie Południowe | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Indie Południowe | Airtel |
| **Chicago** | Equinix | Środkowo-północne stany USA | Aryaka Networks, AT&T NetBond, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Dallas** | Equinix | Nie dotyczy | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | Środkowo-zachodnie stany USA | CoreSite, Megaport, Zayo |
| **Dubai** | Zjednoczone Emiraty Arabskie Etisalat | Północne Zjednoczone Emiraty Arabskie | Zjednoczone Emiraty Arabskie Etisalat |
| **Dubai2** | datamena jednostka bazy danych | Północne Zjednoczone Emiraty Arabskie | jednostka bazy danych datamena, Orixcom |
| **Dublin** | Equinix | Europa Północna | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | Nie dotyczy | DE-CIX, Interxion |
| **Hongkong SAR** | Equinix | Azja Wschodnia | Aryaka Networks, British Telecom, Chief Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Johannesburg** | Teraco | Republika Południowej Afryki (północ) | Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Kuala Lumpur** | TIME dotCom | Nie dotyczy | TIME dotCom |
| **Las Vegas** | Przełącznik | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Londyn** | Equinix | Południowe Zjednoczone Królestwo | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions — Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londyn2** | Telehouse | Południowe Zjednoczone Królestwo | IX Reach, Equinix |
| **Los Angeles** | CoreSite | Nie dotyczy | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marsylia** |Interxion | Francja Południowa | Interxion, Jaguar Network |
| **Melbourne** | NextDC | Australia Południowo-Wschodnia | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | Nie dotyczy | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | Nie dotyczy | Bell Canada, Cologix, Telus, Zayo |
| **Mumbaj** | Tata Communications | Indie Zachodnie | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbaj2** | Airtel | Indie Zachodnie | Airtel, Sify, Vodafone Idea |
| **Nowy Jork** | Equinix | Nie dotyczy | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Walia)** | Dane nowej generacji | Zachodnie Zjednoczone Królestwo | British Telecom, Colt, Level 3 Communications, dane nowej generacji |
| **Osaka** | Equinix | Japonia Zachodnia | Equinix, Internet Initiative Japan Inc. — IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paryż** | Interxion | Francja Środkowa | Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | Nie dotyczy | Megaport, NextDC |
| **Miasto Quebec** | 4Degrees | Kanada Wschodnia | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | Środkowo-południowe stany USA | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brazylia Południowa | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | Zachodnie stany USA 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seul** | KINX | Korea Środkowa | KINX, LG CNS, Sejong Telecom |
| **Dolina Krzemowa** | Equinix | Zachodnie stany USA | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, InterCloud, IX docierać do większej, pakietów, PacketFabric, Megaport, Verizon, Orange, Tata Communications, Level 3 Communications, Sprint, Zayo |
| **Silicon Valley2** | Coresite | Zachodnie stany USA | Coresite | 
| **Singapur** | Equinix | Azja Południowo-Wschodnia | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | Przełącznik globalny | Azja Południowo-Wschodnia | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Sydney** | Equinix | Australia Wschodnia | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Telstra Corporation, TPG Telecom, Verizon |
| **Tajpej** | Chief Telecom | Nie dotyczy | Chief Telecom, FarEasTone |
| **Tokio** | Equinix | Japonia Wschodnia | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. — IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada Środkowa | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Waszyngton** | Equinix | Wschodnie stany USA, wschodnie stany USA 2 | Aryaka Networks, AT & T NetBond, British Telecom, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona sieci, NTT Communications, Orange, PacketFabric, Sprint, Tata Communications, operatora Telia Verizon, Zayo |
| **Waszyngton 2** | Coresite | Wschodnie stany USA, wschodnie stany USA 2 |Coresite | 
| **Zurych** | Interxion | Nie dotyczy | Interxion |

 **+** oznacza wkrótce

### <a name="national-cloud-environments"></a>Krajowe środowiska chmury

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **Nowy Jork** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Dolina Krzemowa** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Waszyngton** |AT&T NetBond, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chiny
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Pekin** |China Telecom |
| **Pekin 2** | China Telecom, GDS |
| **Szanghaj** |China Telecom |
| **Szanghaj 2** | China Telecom, GDS |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach)

### <a name="germany"></a>Niemcy
| **Lokalizacja** | **Dostawcy usług** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Łączność za pośrednictwem dostawców usług Exchange
Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="c1partners"></a>Łączność za pośrednictwem dodatkowych dostawców usług
| **Lokalizacja** | **Exchange** | **Dostawcy połączeń** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Telecity | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, MainOne, Nianet, Post, Proximus, Telecom Italia Sparkle, Telia |
| **Atlanta** | Equinix| Firma Castle korony
| **Kapsztad** | Teraco | MTN |
| **Chicago** | Equinix | Windstream Castle korony, przedsiębiorstw o szerokim zakresie funkcji |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Telecity | BICS, Cinia, Nianet, QSC AG |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londyn** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Transtelco Castle korony, przedsiębiorstw o szerokim zakresie funkcji |
| **Madryt** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **Nowy Jork** |Equinix, Megaport | Altice, Castle korony o szerokim zakresie funkcji przedsiębiorstwa, Webair |
| **Paryż** | Equinix | Proximus |
| **Miasto Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Dolina Krzemowa** |Coresite, Equinix | Cox biznesowych o szerokim zakresie funkcji przedsiębiorstwa, Windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Cogeco element równorzędny 1, IVedha Inc, Rogers, Thinktel, Zirro|
| **Waszyngton** |Equinix | Windstream Limited, firmę Masergy, telekomunikacyjnych Epsilon Altice biznesowych, BICS, Cox biznesowych, korony Castle, Gtt komunikacji Inc, |

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Kontynent** | **Integratorzy systemów** |
| --- | --- |
| **Azja** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Ameryka Północna** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Ameryka Południowa** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
