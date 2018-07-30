---
title: 'Dostawcy połączeń i lokalizacje: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje posortowano według dostawców połączeń.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: jaredro
ms.openlocfilehash: e3338e76f54516e384e5cfc3046b83f2e49476d9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215309"
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

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Regiony świadczenia usługi Azure i lokalizacje usługi ExpressRoute w regionach geopolitycznych.
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Ameryka Północna** |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Dolina Krzemowa, Las Vegas, Los Angeles, Miami, Miasto Quebec, Montreal, Nowy Jork, San Antonio, Seattle, Toronto, Waszyngton |
| **Ameryka Południowa** |Brazylia Południowa |Sao Paulo |
| **Europa** |Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |Amsterdam, Dublin, Londyn, Newport (Walia), Paryż |
| **Azja** |Azja Wschodnia, Azja Południowo-Wschodnia |Hongkong, Singapur, Singapur2 |
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
| **Chiny** |Chiny Północne, Chiny Wschodnie |Pekin, Szanghaj |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="locations"></a>Lokalizacje dostawców połączenia

W poniższej tabeli przedstawiono lokalizacje według dostawcy usług. Jeśli chcesz wyświetlić dostępnych dostawców według lokalizacji, zobacz [Dostawcy usług według lokalizacji](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Środowisko produkcyjne Azure
| **Dostawca usług** | **Microsoft Azure** | **Office 365 i Dynamics 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Airtel](http://www.airtel.in/creatingsmiles/)** | Obsługiwane | Obsługiwane | Chennai (Madras), Mumbaj (Bombaj) |
| **[Aryaka Networks](http://www.aryaka.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Dallas, Hongkong, Dolina Krzemowa, Singapur, Tokio, Waszyngton |
| **[Ascenty Data Centers](https://ascenty.com/servicos/cloud-connect/microsoft-expressroute/)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Obsługiwane |Obsługiwane |Montreal, Toronto, miasto Quebec |
| **[British Telecom](https://globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Obsługiwane |Obsługiwane |Amsterdam, Hongkong, Londyn, Dolina Krzemowa, Singapur, Sydney, Tokio, Waszyngton |
| **[C3ntro](https://c3ntro.com/)** |Wkrótce |Wkrótce |Miami |
| **CDC** | Obsługiwane | Obsługiwane | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Dolina Krzemowa, Las Vegas, Nowy Jork, San Antonio, Tokio, Toronto |
| **China Telecom Global** |Obsługiwane |Nieobsługiwane |Hongkong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Obsługiwane |Obsługiwane |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Dublin, Londyn, Paryż, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Chicago, Denver, Los Angeles, Nowy Jork, Dolina Krzemowa, Waszyngton |
| **eir** |Obsługiwane |Obsługiwane |Dublin|
| **Epsilon Global Communications** |Obsługiwane |Obsługiwane |Singapur |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Chicago, Dallas, Dolina Krzemowa, Dublin, Hongkong, Londyn, Los Angeles, Melbourne, Miami, Nowy Jork, Osaka, Paryż, Sao Paulo, Seattle, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **euNetworks** |Obsługiwane |Obsługiwane |Amsterdam, Londyn |
| **GÉANT** |Obsługiwane |Obsługiwane |Amsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Obsługiwane| Obsługiwane | Chennai (Madras), Mumbaj (Bombaj) |
| **[InterCloud](https://www.intercloud.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Londyn, Paryż, Singapur, Waszyngton |
| **Internet2** |Obsługiwane |Obsługiwane |Waszyngton |
| **[Internet Initiative Japan Inc. — IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Internet Solutions — Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg, Londyn |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Dublin, Londyn, Paryż |
| **[IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)**|Obsługiwane |Obsługiwane | Dolina Krzemowa, Toronto |
| **Jisc** |Obsługiwane |Obsługiwane |Londyn |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Obsługiwane |Obsługiwane |Seul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Obsługiwane | Obsługiwane | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Newport, Sao Paulo, Seattle, Silicon Valley, Singapur, Waszyngton |
| **LG CNS** |Obsługiwane |Obsługiwane |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Chicago, Dallas, Denver, Dublin, Dolina Krzemowa, Hongkong, Las Vegas, Londyn, Los Angeles, Melbourne, Miami, miasto Quebec, Nowy Jork, San Antonio, Seattle, Singapur, Singapur2, Sydney, Toronto, Waszyngton |
| **MTN** |Obsługiwane |Obsługiwane |Londyn |
| **[Neutrona Networks](http://www.neutrona.com/index.php/azure-expressroute/)** |Obsługiwane |Obsługiwane |Dallas, Miami, Sao Paulo |
| **[Dane nowej generacji](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Obsługiwane |Obsługiwane |Newport (Walia) |
| **NEXTDC** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Obsługiwane |Obsługiwane |Amsterdam, Hongkong, Londyn, Los Angeles, Osaka, Singapur, Sydney, Tokio, Waszyngton |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Obsługiwane |Obsługiwane |Tokio |
| **[NTT SmartConnect](http://cloud.nttsmc.com/cxc/azure.html)** |Obsługiwane |Obsługiwane |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Obsługiwane |Obsługiwane |Melbourne+, Sydney |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Obsługiwane |Obsługiwane |Amsterdam, Hongkong, Londyn, Paryż, Dolina Krzemowa, Singapur, Sydney, Waszyngton |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa |
| **PCCW Global Limited** |Obsługiwane |Obsługiwane |Hongkong |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Obsługiwane |Obsługiwane |Seul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Obsługiwane |Obsługiwane |Chennai (Madras), Mumbaj (Bombaj) |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Przebieg](https://business.sprint.com/solutions/cloud-networking/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Obsługiwane |Obsługiwane |Amsterdam, Chennai (Madras), Hongkong, Londyn, Mumbaj (Bombaj), Dolina Krzemowa, Singapur, Waszyngton |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Obsługiwane |Obsługiwane |Amsterdam, Sao Paulo |
| **[Telehouse — KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Obsługiwane |Obsługiwane |Londyn |
| **Telenor** |Obsługiwane |Obsługiwane |Amsterdam, Londyn |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Obsługiwane | Obsługiwane |Londyn, Waszyngton |
| **Telmex Uninet**| Wkrótce | Wkrótce | Dallas+ |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Telus](http://www.telus.com)** |Obsługiwane |Obsługiwane |Montreal, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Hongkong, Londyn, Dolina Krzemowa, Singapur, Sydney, Tokio, Waszyngton |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Obsługiwane |Nieobsługiwane |Londyn |
| **[Zayo](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Dolina Krzemowa, Londyn, Los Angeles, Montreal, Nowy Jork, Toronto, Waszyngton |

 **+** oznacza wkrótce

### <a name="national-cloud-environment"></a>Krajowe środowisko chmury

### <a name="us-government-cloud"></a>Chmura administracji USA
| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Chicago, Waszyngton |
| **[CenturyLink Cloud Connect](http://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Phoenix |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Seattle, Dolina Krzemowa, Waszyngton |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Nowy Jork+, Waszyngton |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane | Obsługiwane | Chicago, Dallas, San Antonio |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Waszyngton |

### <a name="china"></a>Chiny
| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **China Telecom** |Obsługiwane |Nieobsługiwane |Pekin, Szanghaj |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach).

### <a name="germany"></a>Niemcy
| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Nieobsługiwane |Berlin+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Obsługiwane |Nieobsługiwane |Berlin |
| **Interxion** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane  | Nieobsługiwane | Berlin |
| **T-Systems** |Obsługiwane |Nieobsługiwane |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Łączność za pośrednictwem dostawców usług Exchange

Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/services/cloud-connectivity/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="connectivity-through-additional-service-providers"></a>Łączność za pośrednictwem dodatkowych dostawców usług

| **Dostawca połączenia** | **Exchange** | **Lokalizacje** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nowy Jork, Waszyngton |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](http://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londyn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londyn, Singapur, Waszyngton |
| **[BroadBand Tower, Inc.](http://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Dolina Krzemowa, Waszyngton | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londyn, Singapur, Waszyngton |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londyn |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Waszyngton |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londyn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **LGA Telecom** |Equinix |Singapur|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, Nowy Jork, Waszyngton |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hongkong |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Waszyngton |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londyn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Post](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londyn, Paryż |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londyn | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nowy Jork |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Dolina Krzemowa, Waszyngton |
| **Zain** |Equinix |Londyn|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madryt |
| **[Zirro](https://zirro.com/services/)**| Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Łączność za pośrednictwem dostawców centrum danych
| **Dostawca** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach |
| **[T5 Datacenters](http://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Łączność za pośrednictwem sieci National Research and Education Networks (NREN)

| **Dostawca**|
| --- |
| **AARNET**| 
| **DeIC za pośrednictwem GÉANT**|
| **GARR za pośrednictwem GÉANT**|
| **GÉANT**|
| **HEAnet za pośrednictwem GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS za pośrednictwem GÉANT**|
| **SINET**|
| **Surfnet za pośrednictwem GÉANT**|

* Jeśli Twój dostawca połączenia nie został tutaj wymieniony, być może połączenia są nawiązywane za pośrednictwem partnerów wymiany z usługą ExpressRoute wymienionych powyżej.

## <a name="expressroute-system-integrators"></a>Integratorzy systemu ExpressRoute
Włączanie prywatnej łączności do własnych potrzeb może być wyzwaniem w zależności od skali sieci. Możesz pracować z dowolnymi integratorami systemu wymienionymi w poniższej tabeli, którzy ułatwiają dołączanie do usługi ExpressRoute.

| **Integrator systemu** | **Kontynent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Azja, Europa, Ameryka Północna, Ameryka Południowa |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](http://www.ensyst.com.au)** | Azja
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Ameryka Północna |
| **[FlexManage](http://www.flexmanage.com/cloud)** | Ameryka Północna |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Europa |
| **[Lightstream](https://www.ltstream.com/expressroute)** | Ameryka Północna |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Australia |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Niemcy) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/connectivity.html)** | Azja |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Ameryka Północna |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Ameryka Północna |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |


## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
