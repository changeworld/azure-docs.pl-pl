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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2019
ms.author: cherylmc
ms.openlocfilehash: 7df117dbb071081473a5651a953927cee8ddaa03
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801489"
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

## <a name="locations"></a>Regiony platformy Azure do ExpressRoute lokalizacji w regionie geopolitycznym.
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


### <a name="production-azure"></a>Środowisko produkcyjne Azure

| **Dostawca usług** | **Microsoft Azure** | **Office 365 i Dynamics 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Obsługiwane | Obsługiwane | Chennai2, Mumbaj2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Hongkong SAR, Wyspy świętegoł Paulo, Seattle, Dolina Krzemowa, Singapur, Tokio, Waszyngton |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Obsługiwane |Obsługiwane |Montreal, Toronto, miasto Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Obsługiwane |Obsługiwane |Amsterdam, Hongkong SAR, Johannesburg, Londyn, Newport (Walia), Wyspy Świętego Paulo, Dolina Krzemowa, Singapur, Sydney, Tokio, Waszyngton |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Obsługiwane |Obsługiwane |Miami |
| **CDC** | Obsługiwane | Obsługiwane | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Amsterdam2, Chicago, Hongkong, Las Vegas, Nowy Jork, Paryż, San Antonio, Dolina Krzemowa, Tokio, Toronto, Waszyngton |
| **Chief Telecom** |Obsługiwane |Obsługiwane |Hongkong, Tajpej |
| **China Telecom Global** |Obsługiwane |Obsługiwane |SRA Hongkong |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Montreal, Toronto, Waszyngton |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Dublin, Londyn, Newport, Osaka, Paryż, Singapur2, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Chicago, Denver, Los Angeles, Nowy Jork, Dolina Krzemowa, krzem Valley2, Waszyngton, Waszyngton DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Obsługiwane |Obsługiwane |Amsterdam2, Frankfurt, Marsylii|
| **[Devoli](https://devoli.com/expressroute)** | Obsługiwane |Obsługiwane | Auckland, Melbourne, Sydney |
| **du datamena** |Obsługiwane |Obsługiwane | Dubai2 |
| **eir** |Obsługiwane |Obsługiwane |Dublin|
| **[Epsilon — komunikacja globalna](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Chicago, Dallas, Dublin, Hongkong SAR, Londyn, London2y, Los Angeles, Melbourne, Miami, Nowy Jork, Osaka, Paryż, Wyspy świętegoł Paulo, Seattle, Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **Etisalat Zjednoczone Emiraty Arabskie** |Obsługiwane |Obsługiwane |Dubaj|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Dublin, Londyn |
| **FarEasTone** |Obsługiwane |Obsługiwane |Tajpej|
| **GÉANT** |Obsługiwane |Obsługiwane |Amsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Obsługiwane| Obsługiwane | Chennai (Madras), Mumbaj (Bombaj) |
| **[InterCloud](https://www.intercloud.com/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Londyn, Nowy Jork, Paryż, Dolina Krzemowa, Singapur, Waszyngton DC, Zurych |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Dolina Krzemowa, Waszyngton |
| **[Internet Initiative Japan Inc. — IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Internet Solutions — Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg, Londyn |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Obsługiwane |Obsługiwane |Amsterdam, Amsterdam2, Kopenhaga, Dublin, Frankfurt, Londyn, Marsylii, Paryż, Zurych |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Obsługiwane |Obsługiwane | Amsterdam, London2, Dolina Krzemowa, Toronto |
| **Sieć Jaguar** |Obsługiwane |Obsługiwane |Marsylia|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Obsługiwane |Obsługiwane |Londyn |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Obsługiwane |Obsługiwane |Seul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Obsługiwane | Obsługiwane | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Londyn, Newport (Walia), Sao Paulo, Seattle, Dolina Krzemowa, Singapur, Waszyngton |
| **LG CNS** |Obsługiwane |Obsługiwane |Busan, Seul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane |Obsługiwane |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dublin, Hongkong SAR, Las Vegas, Londyn, Los Angeles, Melbourne, Miami, Montrealu, Nowy Jork, Perth, Quebec City, San Antonio, Seattle, Dolina Krzemowa, Singapur, Singapur2, Sydney, Toronto, Waszyngton DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Obsługiwane |Obsługiwane |Londyn |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Obsługiwane |Obsługiwane |Dallas, Los Angeles, Miami, Wyspy świętegoł Paulo, Waszyngton |
| **[Dane nowej generacji](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Obsługiwane |Obsługiwane |Newport (Walia) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Melbourne, Perth, Sydney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Obsługiwane |Obsługiwane |Amsterdam, Hongkong SAR, Londyn, Los Angeles, Osaka, Singapur, Sydney, Tokio, Waszyngton |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Obsługiwane |Obsługiwane |Tokio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Obsługiwane |Obsługiwane |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Obsługiwane |Obsługiwane |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Obsługiwane |Obsługiwane |Amsterdam, Frankfurt, Hongkong SAR, Johannesburgy, Londyn, Paryż, Wyspy Świętego Paulo, Dolina Krzemowa, Singapur, Sydney, Tokio, Waszyngton |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Obsługiwane | Obsługiwane | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Obsługiwane |Obsługiwane |Chicago, Hongkong SAR, Londyn |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Obsługiwane |Obsługiwane |Seul |
| **SES** | Obsługiwane |Obsługiwane | Waszyngton |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Obsługiwane |Obsługiwane |Chennai, Mumbaj2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Obsługiwane |Obsługiwane |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Obsługiwane |Obsługiwane |Osaka, Tokio |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Obsługiwane |Obsługiwane |Auckland, Sydney |
| **[Przebieg](https://business.sprint.com/solutions/cloud-networking/)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Obsługiwane |Obsługiwane |Amsterdam, Chennai, Hongkong SAR, Londyn, Mumbaj, Wyspy świętegoł Paulo, Wyspa krzemowa, Singapur, Waszyngton |
| **Telecity Group** |Obsługiwane |Obsługiwane |Amsterdam |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Obsługiwane |Obsługiwane |Amsterdam, Sao Paulo |
| **[Telehouse — KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)**  |Obsługiwane |Obsługiwane |Londyn |
| **Telenor** |Obsługiwane |Obsługiwane |Amsterdam, Londyn |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Obsługiwane | Obsługiwane |Amsterdam, Chicago, Dallas, Hongkong, Londyn, Paryż, Dolina Krzemowa, Sztokholm, Waszyngton |
| **Telmex Uninet**| Obsługiwane | Obsługiwane | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Obsługiwane |Obsługiwane |Melbourne, Singapur, Sydney |
| **[Telus](https://www.telus.com)** |Obsługiwane |Obsługiwane |Montrealu, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Obsługiwane |Obsługiwane |Kapsztad, Johannesburg |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Obsługiwane | Obsługiwane | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Obsługiwane |Obsługiwane |Dallas, + Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Obsługiwane |Obsługiwane |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Hongkong SAR, Londyn, Mumbaj (Washington), Dolina Krzemowa, Singapur, Sydney, Tokio, Toronto, Waszyngton |
| **Viasat** | Obsługiwane | Obsługiwane | Waszyngton2 |
| **[Grupa Vocus NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Obsługiwane | Obsługiwane | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Obsługiwane |Obsługiwane |Amsterdam2, Londyn, Singapur |
| **Vodafone Idea** | Obsługiwane | Obsługiwane | Mumbaj, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Obsługiwane |Obsługiwane |Amsterdam, Chicago, Dallas, Denver, Londyn, Los Angeles, Montrealu, Nowy Jork, Paryż, Seattle, Dolina Krzemowa, Toronto, Waszyngton |

 **+** oznacza wkrótce

### <a name="national-cloud-environment"></a>Krajowe środowisko chmury

### <a name="us-government-cloud"></a>Chmura administracji USA

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Obsługiwane |Obsługiwane |Chicago, Phoenix, Waszyngton |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Obsługiwane |Obsługiwane |Nowy Jork, Phoenix, San Antonio, Waszyngton |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Seattle, Dolina Krzemowa, Waszyngton |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Obsługiwane |Obsługiwane |Chicago, Dolina Krzemowa, Waszyngton |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane | Obsługiwane | Chicago, Dallas, San Antonio, Seattle, Waszyngton |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Obsługiwane |Obsługiwane |Chicago, Dallas, Nowy Jork, Dolina Krzemowa, Waszyngton |

### <a name="china"></a>Chiny

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **China Telecom** |Obsługiwane |Brak obsługi |Pekin, Beijing2, Szanghaj, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Obsługiwane |Brak obsługi |Pekin 2, Szanghaj 2 |

Więcej informacji znajduje się w artykule [ExpressRoute in China](http://www.windowsazure.cn/home/features/expressroute/) (Usługa ExpressRoute w Chinach).

### <a name="germany"></a>Niemcy

| **Dostawca usług** | **Microsoft Azure** | **Office 365** | **Lokalizacje** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Obsługiwane |Brak obsługi |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Obsługiwane |Brak obsługi |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Obsługiwane |Brak obsługi |Berlin |
| **Interxion** |Obsługiwane |Brak obsługi |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Obsługiwane  | Brak obsługi | Berlin |
| **T-Systems** |Obsługiwane |Brak obsługi |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Łączność za pośrednictwem dostawców usług Exchange

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

## <a name="connectivity-through-additional-service-providers"></a>Łączność za pośrednictwem dodatkowych dostawców usług

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
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | SRA Hongkong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC Telecom]( https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Technologie Aptum](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | Londyn |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Dolina Krzemowa, Waszyngton |
| **[Castle korony](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, Nowy Jork, Waszyngton |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londyn, Singapur, Waszyngton |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londyn |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Miasto Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Waszyngton |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londyn, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrajn B. S. C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |SRA Hongkong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Waszyngton |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Kapsztad, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londyn |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |
| **[Opublikuj Telecom Luksemburg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, Londyn, Paryż |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, Nowy Jork, Dolina Krzemowa | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londyn | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nowy Jork |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Dolina Krzemowa, Waszyngton |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Dolina Krzemowa, Dolina Krzemowa 2|
| **Zain** |Equinix |Londyn|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madryt |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Łączność za pośrednictwem dostawców centrum danych

| **Dostawca** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX zasięg, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[Centra danych QTS](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Przesyłanie strumieniowe centrów danych]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX zasięg, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

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
| **[Avanade Inc.](https://www.avanade.com/)** | Azja, Europa, Ameryka Północna, Ameryka Południowa |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Azja
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Ameryka Północna |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Ameryka Północna |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Ameryka Północna |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australia |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Niemcy) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Azja |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Ameryka Północna |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Ameryka Północna |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Ameryka Południowa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |

## <a name="next-steps"></a>Następne kroki
* Więcej informacji na temat usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa lokalizacji"
