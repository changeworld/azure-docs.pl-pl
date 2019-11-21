---
title: 'Dostawcy połączeń i lokalizacje: Azure ExpressRoute | Microsoft Docs'
description: Ten artykuł zawiera szczegółowe omówienie lokalizacji, w których są oferowane usługi, i sposobu łączenia z regionami świadczenia usługi Azure. Informacje posortowano według dostawców połączeń.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 793d88ec67fbd839851894077e8f5c481b9ebda7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217096"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute

> [!div class="op_single_selector"]
> * [Lokalizacje według dostawcy](expressroute-locations.md)
> * [Dostawcy według lokalizacji](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers,and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Regiony systemu Azure
Azure regions are global datacenters where Azure compute, networking and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## <a name="expressroute-locations"></a>Lokalizacje usługi ExpressRoute
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise Edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft’s network – and are globally distributed, providing customers the opportunity to connect to Microsoft’s network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft’s network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

Będziesz mieć dostęp do usług Azure we wszystkich regionach regionu geopolitycznego, jeśli połączysz się przynajmniej z jedną lokalizacją usługi ExpressRoute w tym regionie.

## <a name="locations"></a>Azure regions to ExpressRoute locations within a geopolitical region.
Poniższa tabela zawiera mapę regionów świadczenia usługi Azure dla lokalizacji usługi ExpressRoute w regionie geopolitycznym.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Australia — instytucje rządowe** |Australia Środkowa, Australia Środkowa 2 |Canberra, Canberra2 |
| **Europa** | Europa Północna, Europa Zachodnia, Francja Południowa, Francja Środkowa, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, London, London2, Marseille, Milan, Munich, Newport(Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **Ameryka Północna** |Wschodnie stany USA, Zachodnie stany USA, Wschodnie stany USA 2, Zachodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Północno-środkowe stany USA, Środkowo-zachodnie stany USA, Kanada Środkowa, Kanada Wschodnia |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Azja** | Azja Wschodnia, Azja Południowo-Wschodnia |Hong Kong SAR, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **Indie** | Indie Zachodnie, Indie Środkowe, Indie Południowe |Chennai, Chennai2, Mumbaj, Mumbaj2 |
| **Japonia** | Japońska Zachodnia, Japonia Wschodnia |Osaka, Tokio |
| **Oceania** | Australia Południowo-Wschodnia, Australia Wschodnia |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Korea Południowa** | Korea Środkowa, Korea Południowa |Busan, Seul|
| **UAE** | UAE Central, UAE North | Dubai, Dubai2 |
| **Republika Południowej Afryki** | South Africa West, South Africa North |Kapsztad, Johannesburg |
| **Ameryka Południowa** | Brazylia Południowa |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiony i granice geopolityczne chmur krajowych
W poniższej tabeli zamieszczono informacje o regionach i granicach geopolitycznych chmur krajowych.

| **Region geopolityczny** | **Regiony platformy Azure** | **Lokalizacje usługi ExpressRoute** |
| --- | --- | --- |
| **Chmura administracji USA** |US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Wirginia, US DoD (region środkowy), US DoD (region wschodni)  |Chicago, Dallas, Nowy Jork, Phoenix, San Antonio, Seattle, Dolina Krzemowa, Waszyngton |
| **Chiny Wschodnie** |Chiny Wschodnie, Chiny Wschodnie 2 |Szanghaj, Szanghaj 2 |
| **Chiny Północne** |Chiny Północne, Chiny Północne 2 |Pekin, Pekin 2 |
| **Niemcy** |Niemcy Środkowe, Niemcy Wschodnie |Berlin, Frankfurt |

Łączność między regionami geopolitycznymi nie jest obsługiwana w standardowej jednostce SKU usługi ExpressRoute. Do obsługi połączeń globalnych trzeba włączyć dodatek Premium usługi ExpressRoute. Łączność z krajowymi środowiskami chmury nie jest obsługiwana. W razie potrzeby można współpracować z dostawcą połączenia.

## <a name="partners"></a>Dostawcy połączenia usługi ExpressRoute

W poniższej tabeli przedstawiono lokalizacje według dostawcy usług. Jeśli chcesz wyświetlić dostępnych dostawców według lokalizacji, zobacz [Dostawcy usług według lokalizacji](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Global commercial Azure

| **Dostawca usług** | **Microsoft Azure** | **Office 365**  | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Obsługiwane | Obsługiwane | Chennai2, Mumbaj2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Obsługiwane |Obsługiwane |Montreal, Toronto, miasto Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Obsługiwane |Obsługiwane |Amsterdam, Hong Kong SAR, Johannesburg, London, Newport(Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Obsługiwane |Obsługiwane |Miami |
| **CDC** | Obsługiwane | Obsługiwane | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Amsterdam2, Chicago, Hong Kong, Las Vegas, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Obsługiwane |Obsługiwane |Hong Kong, Taipei |
| **China Telecom Global** |Obsługiwane |Obsługiwane |Hong Kong SAR |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Montreal, Toronto, Waszyngton |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dublin, London, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Obsługiwane |Obsługiwane |Amsterdam2, Frankfurt, Marseille|
| **[Devoli](https://devoli.com/expressroute)** | Obsługiwane |Obsługiwane | Auckland, Melbourne, Sydney |
| **du datamena** |Obsługiwane |Obsługiwane | Dubai2 |
| **eir** |Obsługiwane |Obsługiwane |Dublin|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Chicago, Dallas, Dublin,  Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat UAE** |Obsługiwane |Obsługiwane |Dubaj|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dublin, London |
| **FarEasTone** |Obsługiwane |Obsługiwane |Tajpej|
| **GÉANT** |Obsługiwane |Obsługiwane |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Obsługiwane| Obsługiwane | Chennai (Madras), Mumbaj (Bombaj) |
| **[InterCloud](https://www.intercloud.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC, Zurich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. — IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Internet Solutions — Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg, Londyn |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Obsługiwane |Obsługiwane | Amsterdam, London2, Silicon Valley, Toronto |
| **Jaguar Network** |Obsługiwane |Obsługiwane |Marsylia|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Londyn |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Obsługiwane |Obsługiwane |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Obsługiwane | Obsługiwane | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Newport (Walia), Sao Paulo, Seattle, Dolina Krzemowa, Singapur, Waszyngton |
| **LG CNS** |Obsługiwane |Obsługiwane |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Hong Kong SAR, Las Vegas, London, Los Angeles, Melbourne, Miami, Montreal, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Obsługiwane |Obsługiwane |Londyn |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Obsługiwane |Obsługiwane |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Dane nowej generacji](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Obsługiwane |Obsługiwane |Newport (Walia) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Obsługiwane |Obsługiwane |Amsterdam, Hong Kong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Obsługiwane |Obsługiwane |Tokio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Obsługiwane |Obsługiwane |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Obsługiwane |Obsługiwane |Amsterdam, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Obsługiwane | Obsługiwane | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Obsługiwane |Obsługiwane |Chicago, Hong Kong SAR, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Obsługiwane |Obsługiwane |Seul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Obsługiwane |Obsługiwane | Waszyngton |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Obsługiwane |Obsługiwane |Chennai, Mumbaj2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[Przebieg](https://business.sprint.com/solutions/cloud-networking/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Obsługiwane | Obsługiwane | Zurich |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Obsługiwane |Obsługiwane |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Obsługiwane |Obsługiwane |Amsterdam, Sao Paulo |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Obsługiwane |Obsługiwane |London, London2 |
| **Telenor** |Obsługiwane |Obsługiwane |Amsterdam, London, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Obsługiwane | Obsługiwane |Amsterdam, Chicago, Dallas, Hong Kong, London, Paris, Silicon Valley, Stockholm, Washington DC |
| **Telmex Uninet**| Obsługiwane | Obsługiwane | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Obsługiwane |Obsługiwane |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Obsługiwane |Obsługiwane |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Obsługiwane | Obsługiwane | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Obsługiwane |Obsługiwane |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Obsługiwane | Obsługiwane | Waszyngton2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Obsługiwane | Obsługiwane | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Obsługiwane |Obsługiwane |Amsterdam2, London, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Obsługiwane | Obsługiwane | Mumbaj, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** oznacza wkrótce

### <a name="national-cloud-environment"></a>Krajowe środowisko chmury

Azure national clouds are isolated from each other and from global commerical Azure. ExpressRoute for one Azure cloud can't connect to the Azure regions in the others. 

### <a name="us-government-cloud"></a>Chmura administracji USA

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Seattle, Dolina Krzemowa, Waszyngton |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane | Obsługiwane | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Dolina Krzemowa, Waszyngton |

### <a name="china"></a>Chiny

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **China Telecom** |Obsługiwane |Nieobsługiwane |Beijing, Beijing2, Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Obsługiwane |Nieobsługiwane |Pekin 2, Szanghaj 2 |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach).

### <a name="germany"></a>Niemcy

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Obsługiwane |Nieobsługiwane |Berlin |
| **Interxion** |Obsługiwane |Nieobsługiwane |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane  | Nieobsługiwane | Berlin |
| **T-Systems** |Obsługiwane |Nieobsługiwane |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Connectivity through Exchange providers

Jeśli dostawca połączenia nie został wymieniony w poprzednich sekcjach, możesz i tak utworzyć połączenie.

* Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy jest on połączony z dowolną wymianą z tabeli powyżej. Użyj poniższych linków, aby zebrać więcej informacji o usługach oferowanych przez dostawców wymiany. Kilku dostawców połączenia jest już połączonych z wymianami sieci Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Poproś dostawcę połączenia o rozszerzenie sieci o wybraną lokalizację komunikacji równorzędnej.
  * Dopilnuj, by dostawca połączenia rozszerzył łączność w sposób wysoko dostępny, aby nie wystąpiły żadne punkty awarii.
* Zamów obwód usługi ExpressRoute z wymianą jako dostawcą połączenia, aby połączyć się z firmą Microsoft.
  * Wykonaj kroki opisane w artykule [Create an ExpressRoute circuit](expressroute-howto-circuit-classic.md) (Tworzenie obwodu usługi ExpressRoute), aby skonfigurować łączność.

## <a name="connectivity-through-satellite-operators"></a>Connectivity through satellite operators
If you are remote and don't have fiber connectivity or you want to explore other connectivity options you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivity through additional service providers

| **Dostawca połączenia** | **Exchange** | **Lokalizacje** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nowy Jork, Waszyngton |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londyn |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, Londyn, Singapur, Waszyngton |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | Hong Kong SAR |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | Londyn |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Dolina Krzemowa, Waszyngton |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londyn, Singapur, Waszyngton |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londyn |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Waszyngton |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londyn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong SAR 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Waszyngton |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Kapsztad, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londyn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londyn, Paryż |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | Londyn | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nowy Jork |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Dolina Krzemowa, Waszyngton |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londyn|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madryt |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Connectivity through datacenter providers

| **Dostawca** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivity through National Research and Education Networks (NREN)

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
| **[Avanade Inc.](https://www.avanade.com/)** | Azja, Europa, Ameryka Północna, Ameryka Południowa |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Azja
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Ameryka Północna |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Ameryka Północna |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Ameryka Północna |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australia |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Niemcy) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Azja |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Ameryka Północna |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Ameryka Północna |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Ameryka Południowa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
