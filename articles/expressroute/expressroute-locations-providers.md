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
ms.date: 08/14/2019
ms.author: cherylmc
ms.openlocfilehash: 2be56f5308fd16d0e20003b0038fdcb12503a574
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074453"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


Tabele w tym artykule zawierają informacje o ExpressRoute geograficznym i lokalizacjach, dostawcach połączeń ExpressRoute i integratorach systemu ExpressRoute (SIs).

> [!Note]
> Regiony platformy Azure i lokalizacje ExpressRoute są dwa odrębne i różne koncepcje, co oznacza, że różnica między tymi dwoma ma kluczowe znaczenie dla eksplorowania połączeń sieci hybrydowej platformy Azure. 
>
>

## <a name="azure-regions"></a>Regiony platformy Azure
Regiony platformy Azure to Globalne centra danych, w których znajdują się zasoby obliczeniowe, sieci i magazynu platformy Azure. Podczas tworzenia zasobu platformy Azure klient musi wybrać lokalizację zasobu. Lokalizacja zasobu określa, w którym centrum danych Azure (lub strefa dostępności) jest tworzony zasób.

## <a name="expressroute-locations"></a>Lokalizacje usługi ExpressRoute
Lokalizacje ExpressRoute (czasami określane jako lokalizacje komunikacji równorzędnej lub lokalizacje dopełnienia) to miejsca, w których znajdują się urządzenia Microsoft Enterprise Edge (MSEE). Lokalizacje ExpressRoute są punktami wejścia do sieci firmy Microsoft — i są dystrybuowane globalnie, dzięki czemu klienci mogą łączyć się z siecią firmy Microsoft na całym świecie. Te lokalizacje to miejsce, w którym partnerzy ExpressRoute i klienci z bezpośrednią ExpressRoute mogą emitować połączenia krzyżowe do sieci firmy Microsoft. Ogólnie rzecz biorąc, lokalizacja ExpressRoute nie musi być zgodna z regionem świadczenia usługi Azure. Klient może na przykład utworzyć obwód usługi ExpressRoute z lokalizacją zasobu *Wschodnie stany USA*w lokalizacji komunikacji równorzędnej w *Seattle* .

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie. 

## <a name="locations"></a>Regiony platformy Azure do ExpressRoute lokalizacji w regionie geopolitycznym
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Strefa** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- | --- |
| **Australia — instytucje rządowe** | 1 | Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | 1 |Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |Amsterdam, Amsterdam2, Kopenhaga, Dublin, Frankfurt, Londyn, London2, Marsylii, Newport (Walia), Paryż, Sztokholm, Zurych |
| **Ameryka Północna** | 1 |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, Nowy Jork, San Antonio, Seattle, Dolina Krzemowa, krzem Valley2, Waszyngton DC, Waszyngton DC2, Montrealu, Quebec City, Toronto |
| **Azja** | 2 |Azja Wschodnia, Azja Południowo-Wschodnia |Hongkong SAR, Kuala Lumpur, Singapur, Singapur2, Tajpej |
| **Indie** | 2 |Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | 2 |Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio |
| **Oceanii** | 2 |Australia Południowo-Wschodnia, Australia Wschodnia |Auckland, Melbourne, Perth, Sydney | 
| **Korea Południowa** | 2 |Korea Środkowa, Korea Południowa |Busan, Seul|
| **EMIRATY** | 3 | Europa Północna, Zjednoczone Emiraty Arabskie | Dubaj, Dubai2 |
| **Republika Południowej Afryki** | 3 |Zachodnia Republika Południowej Afryki, Północna Republika Południowej Afryki |Kapsztad, Johannesburg |
| **Ameryka Południowa** | 3 |Brazylia Południowa |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony platformy Azure i granice geopolityczne dla chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="partners"></a>Dostawcy połączenia usługi ExpressRoute

W poniższej tabeli przedstawiono lokalizacje połączeń i dostawców usług dla każdej lokalizacji. Jeśli chcesz wyświetlić dostawców usług i lokalizacje, w których świadczą usługi, zobacz [Lokalizacje według dostawcy usług](expressroute-locations.md). 

**Lokalne regiony platformy Azure** to te, które [ExpressRoute lokalnie](expressroute-faqs.md) w każdej lokalizacji komunikacji równorzędnej mogą uzyskać dostęp. **n/a** wskazuje, że ExpressRoute Local nie jest dostępny w tej lokalizacji komunikacji równorzędnej.


### <a name="production-azure"></a>Środowisko produkcyjne Azure
| **Location** | **Właściciel lokalizacji komunikacji równorzędnej** | **Lokalne regiony platformy Azure** | **Dostawcy usług** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europa Zachodnia | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europa Zachodnia | CenturyLink Cloud Connect, DE-CIX, Interxion, Vodafone |
| **Atlanta** | Equinix | Nie dotyczy | Equinix, Megaport |
| **Auckland** | Grupa Vocus NZ | Nie dotyczy | Devoli, kordia, Megaport, Spark NZ, Vocus Grupa NZ |
| **Pusan** |LG CNS | Korea Południowa | LG CNS |
| **Canberra** | CDC | Australia Środkowa | CDC |
| **Canberra2** | CDC | Australia Środkowa 2| CDC |
| **Kapsztad** | Teraco | Zachodnia Republika Południowej Afryki | Internet Solutions — Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | Indie Południowe | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | Indie Południowe | Airtel |
| **Chicago** | Equinix | Środkowo-północne stany USA | Sieci aryaka Networks, w & T teleobligacji, CenturyLink Cloud Connect, Cologix, Comcast, CoreSite, Equinix, międzychmurowe, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, przebieg, Telia, Verizon, Zayo |
| **Kopenhaga** | Interxion | Nie dotyczy | Interxion |
| **Dallas** | Equinix | Nie dotyczy | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | CoreSite | Środkowo-zachodnie stany USA | CoreSite, Megaport, Zayo |
| **Dubaj** | Etisalat Zjednoczone Emiraty Arabskie | Północne Zjednoczone Emiraty Arabskie | Etisalat Zjednoczone Emiraty Arabskie |
| **Dubai2** | du datamena | Północne Zjednoczone Emiraty Arabskie | du datamena, Orixcom |
| **Dublin** | Equinix | Europa Północna | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | Interxion | Nie dotyczy | DE-CIX, Interxion, pomarańczowy |
| **Hongkong SAR** | Equinix | Azja Wschodnia | Aryaka Networks Networks, Kolumbia Telecom, CenturyLink Cloud Connect, dyrektor telekomunikacyjny, Chiny Telecom, Equinix, Megaport, NTT Communications, pomarańcze, PCCW, Tata Communications, Telia, Verizon |
| **Johannesburg** | Teraco | Północna Republika Południowej Afryki | Kolumbia Telecom, Internet Solutions — Cloud Connect, ciecz Telecom, pomarańczowy, Teraco |
| **Kuala Lumpur** | TIME dotCom | Nie dotyczy | TIME dotCom |
| **Las Vegas** | Przełącznik | Nie dotyczy | CenturyLink Cloud Connect, Megaport |
| **Londyn** | Equinix | Południowe Zjednoczone Królestwo | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions — Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse — KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londyn2** | Telehouse | Południowe Zjednoczone Królestwo | IX Reach, Equinix |
| **Los Angeles** | CoreSite | Nie dotyczy | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marsylia** |Interxion | Francja Południowa | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | NextDC | Australia Południowo-Wschodnia | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | Nie dotyczy | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | Nie dotyczy | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbaj** | Tata Communications | Indie Zachodnie | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbaj2** | Airtel | Indie Zachodnie | Airtel, Sify, Vodafone Idea |
| **Nowy Jork** | Equinix | Nie dotyczy | CenturyLink Cloud Connect, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Walia)** | Dane nowej generacji | Zachodnie Zjednoczone Królestwo | Brytyjskie Telecom telekomunikacyjne, Colt, Level 3, dane nowej generacji |
| **Osaka** | Equinix | Japonia Zachodnia | Colt, Equinix, Internet Initiative — Japonia Inc. — IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paryż** | Interxion | Francja Środkowa | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | Nie dotyczy | Megaport, NextDC |
| **Miasto Quebec** | 4Degrees | Kanada Wschodnia | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | Środkowo-południowe stany USA | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brazylia Południowa | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | Zachodnie stany USA 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seul** | KINX | Korea Środkowa | KINX, LG CNS, Sejong Telecom |
| **Dolina Krzemowa** | Equinix | Zachodnie stany USA | Sieci aryaka Networks, w & T teleobligacje, Kolumbia Telecom, CenturyLink Cloud Connect, Comcast, CoreSite, Equinix, międzychmurowy, Internet2, IX zasięg, pakiet, PacketFabric, poziom 3 Communications, Megaport, pomarańczowy, przebieg, Tata Communications, Telia, Verizon, Zayo |
| **Valley2 krzemu** | Coresite | Zachodnie stany USA | Coresite | 
| **Singapur** | Equinix | Azja Południowo-Wschodnia | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapur2** | Przełącznik globalny | Azja Południowo-Wschodnia | Colt, Epsilon — komunikacja globalna, Megaport, SingTel |
| **Sztokholm** | Equinix | Nie dotyczy | Telia |
| **Sydney** | Equinix | Australia Wschodnia | AARNet, w & T teleobligacje, Brytyjskie Telecom, Devoli, Equinix, kordia, Megaport, NEXTDC, NTT Communications, Optus, pomarańczowy, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Tajpej** | Chief Telecom | Nie dotyczy | Dyrektor telekomunikacyjny, FarEasTone |
| **Tokio** | Equinix | Japonia Wschodnia | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. — IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Kanada Środkowa | W & T teleobligacji, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX zasięg Megaport, Telus, Verizon, Zayo |
| **Waszyngton** | Equinix | Wschodnie stany USA, Wschodnie stany USA 2 | Aryaka Networks Networks, w & T teleobligacje, Brytyjskie telekomunikacyjne, CenturyLink Cloud Connect, Cologix, Comcast, CoreSite, Equinix, Internet2, międzychmurowe, 3 Communications, Megaport, Neutrons, NTT Communications, pomarańczowy, PacketFabric, przebieg, Tata Komunikacja, Telia, Verizon, Zayo |
| **Waszyngton 2** | Coresite | Wschodnie stany USA, Wschodnie stany USA 2 |Coresite | 
| **Zurych** | Interxion | Nie dotyczy | Międzychmurowe, Interxion |

 **+** oznacza wkrótce

### <a name="national-cloud-environments"></a>Krajowe środowiska chmury

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Location** | **Dostawcy usług** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **Nowy Jork** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | W & T teleobligacji, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Dolina Krzemowa** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Waszyngton** |AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chiny
| **Location** | **Dostawcy usług** |
| --- | --- |
| **Pekin** |China Telecom |
| **Pekin 2** | Chiny Telecom, GDS |
| **Szanghaj** |China Telecom |
| **Szanghaj 2** | Chiny Telecom, GDS |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach)

### <a name="germany"></a>Niemcy
| **Location** | **Dostawcy usług** |
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
| **Location** | **Exchange** | **Dostawcy połączeń** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, poziom 3 komunikacja, miasto | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, perski Bridge International, Kalaam Telecom Bahrajn B. S. C, MainOne, Nianet, POST Telecom Luksemburg, Proximus, TDC Erhverv, Telecom Italia musujący, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castle korony
| **Kapsztad** | Teraco | MTN |
| **Chicago** | Equinix| Castle korony, spektrum korporacyjne, windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Korona Castle, Odlewia danych, całe przedsiębiorstwa, Transtelco |
| **Frankfurt** | Interxion, miasto | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Hongkong SAR** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londyn** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Castle korony, spektrum korporacyjne, Transtelco |
| **Madryt** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Technologie Aptum, Rogers, zirro |
| **Nowy Jork** |Equinix, Megaport | Altice Business, koroner Castle, spektrum korporacyjne, Webair |
| **Paryż** | Equinix | Proximus |
| **Miasto Quebec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Dolina Krzemowa** |Coresite, Equinix | COX Business, spektrum korporacyjne, windstream, X2nsat Inc. |
| **Singapur** |Equinix |1CLOUDSTAR, BICS, CMC telekomunikacyjne, Epsilon Telecom Limited, LGA Telecom, droga z informacjami Zjednoczonymi (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokio** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | -Gate Technologies Inc., Beanfield Metroconnect, technologie Aptum, IVedha Inc, Rogers, Thinktel, zirro|
| **Waszyngton** |Equinix | Altice Business, BICS, Cox Business, Korona Castle, GTT Communications Inc, Epsilon telekomunikacyjne Limited, Masergy, windstream |

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
