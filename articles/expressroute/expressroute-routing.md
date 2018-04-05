---
title: Wymagania dotyczące routingu dla usługi Azure ExpressRoute | Microsoft Docs
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania routingu oraz zarządzania nim na potrzeby obwodów usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: ganesr
editor: ''
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2018
ms.author: ganesr
ms.openlocfilehash: 829646be6404f86d9f370b3a402cfc0c0c980699
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="expressroute-routing-requirements"></a>Wymagania dotyczące routingu w usłudze ExpressRoute
Aby połączyć się z usługami w chmurze firmy Microsoft przy użyciu usługi ExpressRoute, konieczne będzie skonfigurowanie routingu oraz zarządzanie nim. Niektórzy dostawcy połączenia oferują konfigurowanie routingu oraz zarządzanie nim jako usługą zarządzaną. Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy taka usługa jest oferowana. Jeśli nie, musisz spełnić wymagania opisane poniżej:

Opis sesji routingu, które należy skonfigurować w celu ułatwienia łączności, znajduje się w artykule [Circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu).

> [!NOTE]
> Firma Microsoft nie obsługuje protokołów nadmiarowości routerów (np. HSRP, VRRP) w konfiguracjach wysokiej dostępności. Polegamy na nadmiarowej parze sesji protokołu BGP na komunikację równorzędną w celu zapewnienia wysokiej dostępności.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Adresy IP używane do komunikacji równorzędnej
Należy zarezerwować kilka bloków adresów IP, aby skonfigurować routing między siecią i routerami MSEE. Ta sekcja zawiera listę wymagań i opis zasad dotyczących sposobu pozyskiwania i użycia tych adresów IP.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresy IP używane do prywatnej komunikacji równorzędnej Azure
Do konfigurowania komunikacji równorzędnej można użyć prywatnych lub publicznych adresów IP. Zakres adresów używany do konfigurowania tras nie może pokrywać się z zakresami adresów używanymi do tworzenia sieci wirtualnych na platformie Azure. 

* Należy zarezerwować podsieć /29 lub dwie podsieci /30 dla interfejsów routingu.
* Podsieci używane do routingu mogą być prywatnymi adresami IP lub publicznymi adresami IP.
* Podsieci nie mogą powodować konfliktu z zakresem zarezerwowanym przez klienta do użycia w chmurze firmy Microsoft.
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30. 
  * Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
  * Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
  * Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.  

#### <a name="example-for-private-peering"></a>Przykład prywatnej komunikacji równorzędnej
Jeśli do skonfigurowania komunikacji równorzędnej zdecydujesz się użyć podsieci a.b.c.d/29, zostanie ona podzielona na dwie podsieci /30. W poniższym przykładzie przedstawiono, jak używa się podsieci a.b.c.d/29. 

Podsieć a.b.c.d/29 zostanie podzielona na podsieci a.b.c.d/30 i a.b.c.d+4/30 i przekazana firmie Microsoft za pośrednictwem aprowizowanych interfejsów API. Użytkownik użyje podsieci a.b.c.d+1 jako adresu IP VRF usługi Primary PE, a firma Microsoft użyje podsieci a.b.c.d+2 jako adresu IP VRF podstawowego rozwiązania MSEE. Użytkownik użyje podsieci a.b.c.d+5 jako adresu IP VRF dodatkowej usługi PE, a firma Microsoft użyje podsieci a.b.c.d+6 jako adresu IP VRF dodatkowego rozwiązania MSEE.

Rozważ sytuację, w której w celu skonfigurowania prywatnej komunikacji równorzędnej wybierzesz podsieć 192.168.100.128/29. 192.168.100.128/29 obejmuje adresy od 192.168.100.128 do 192.168.100.135, z których:

* Adres 192.168.100.128/30 zostanie przypisany do linku link1, podczas gdy dostawca będzie używać 192.168.100.129, a firma Microsoft będzie używać 192.168.100.130.
* Adres 192.168.100.132/30 zostanie przypisany do linku link2, podczas gdy dostawca będzie używać adresu 192.168.100.133, a firma Microsoft będzie używać adresu 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Adresy IP używane do publicznej komunikacji równorzędnej na platformie Azure
Do konfigurowania sesji protokołu BGP należy użyć posiadanych publicznych adresów IP. Firma Microsoft musi mieć możliwość weryfikacji własności adresów IP za pomocą rejestrów internetowego routingu i internetowych rejestrów routingu. 

* Należy użyć unikatowej podsieci /29 lub dwóch podsieci /30 do skonfigurowania komunikacji równorzędnej protokołu BGP dla każdego wystąpienia komunikacji równorzędnej na obwód usługi ExpressRoute (jeśli używasz więcej niż jednego obwodu). 
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30. 
  * Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
  * Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
  * Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Adresy IP używane do komunikacji równorzędnej firmy Microsoft
Do konfigurowania sesji protokołu BGP należy użyć posiadanych publicznych adresów IP. Firma Microsoft musi mieć możliwość weryfikacji własności adresów IP za pomocą rejestrów internetowego routingu i internetowych rejestrów routingu.

* Należy użyć unikatowej podsieci /29 (IPv4) lub /125 (IPv6) albo dwóch podsieci /30 (IPv4) lub /126 (IPv6) do skonfigurowania komunikacji równorzędnej protokołu BGP dla każdego wystąpienia komunikacji równorzędnej na obwód usługi ExpressRoute (jeśli używasz więcej niż jednego obwodu).
* Jeśli zostanie użyta podsieć /29, zostanie ona podzielona na dwie podsieci /30.
* Pierwsza podsieć /30 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
* Dla każdej podsieci /30 należy użyć pierwszego adresu IP podsieci /30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /30 do skonfigurowania sesji protokołu BGP.
* Jeśli zostanie użyta podsieć /125, zostanie ona podzielona na dwie podsieci /126.
* Pierwsza podsieć /126 zostanie użyta na potrzeby linku podstawowego, a druga — dodatkowego.
* Dla każdej podsieci /126 należy użyć pierwszego adresu IP podsieci /126 na routerze. Firma Microsoft używa drugiego adresu IP podsieci /126 do skonfigurowania sesji protokołu BGP.
* Aby zapewnić ważność [umowy SLA dotyczącej dostępności](https://azure.microsoft.com/support/legal/sla/), musisz skonfigurować obie sesje protokołu BGP.

## <a name="public-ip-address-requirement"></a>Wymagania dotyczące publicznego adresu IP

### <a name="private-peering"></a>Prywatna komunikacja równorzędna
Do prywatnej komunikacji równorzędnej można używać publicznych lub prywatnych adresów IPv4. Zapewniamy kompleksową izolację ruchu, w związku z czym w warunkach prywatnej komunikacji równorzędnej nie ma możliwości, aby adresy się nakładały. Te adresy nie są anonsowane w Internecie. 


### <a name="public-peering"></a>Publiczna komunikacja równorzędna
Ścieżka publicznej komunikacji równorzędnej Azure umożliwia łączenie ze wszystkimi usługami obsługiwanymi na platformie Azure za pośrednictwem ich publicznych adresów IP. Dotyczy to usług wymienionych w temacie [ExpessRoute FAQ](expressroute-faqs.md) (ExpessRoute — często zadawane pytania) i wszystkich usług obsługiwanych przez niezależnych dostawców oprogramowania na platformie Microsoft Azure. Połączenie z usługami Microsoft Azure w publicznej komunikacji równorzędnej jest zawsze inicjowane z sieci użytkownika do sieci Microsoft. W odniesieniu do ruchu skierowanego do sieci firmy Microsoft należy użyć publicznych adresów IP.

> [!IMPORTANT]
> Wszystkie usługi PaaS platformy Azure są również dostępne za pośrednictwem komunikacji równorzędnej firmy Microsoft. Zalecamy utworzenie komunikacji równorzędnej firmy Microsoft i nawiązanie połączenia z usługami PaaS platformy Azure za pośrednictwem komunikacji równorzędnej firmy Microsoft.  
>   


Prywatny numer systemu autonomicznego jest dozwolony w publicznej komunikacji równorzędnej.

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft
Ścieżka komunikacji równorzędnej firmy Microsoft umożliwia nawiązanie połączenia z usługami w chmurze firmy Microsoft, które nie są obsługiwane przez ścieżkę publicznej komunikacji równorzędnej Azure. Lista usług obejmuje usługi Office 365 takie jak Exchange Online, SharePoint Online, Skype dla firm i Dynamics 365. Firma Microsoft zapewnia obsługę dwukierunkowej łączności w oparciu o komunikację równorzędną firmy Microsoft. Ruch skierowany do usług w chmurze firmy Microsoft musi uzyskać prawidłowe publiczne adresy IPv4, zanim wejdzie do sieci firmy Microsoft.

Zadbaj o to, aby adres IP i numer AS zostały zarejestrowane na Ciebie w jednym z następujących rejestrów:


* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

Jeśli Twoje prefiksy i numer systemu autonomicznego nie są przypisane do Ciebie w powyższych rejestrach, musisz otworzyć zgłoszenie do pomocy technicznej w celu ręcznej weryfikacji Twoich prefiksów i numeru systemu autonomicznego. Pomoc techniczna będzie wymagać dokumentacji, takiej jak upoważnienie potwierdzające zezwolenie na korzystanie z zasobów.

Prywatny numer systemu autonomicznego jest dozwolony dla komunikacji równorzędnej firmy Microsoft, ale będzie również wymagał ręcznej weryfikacji. Ponadto firma Microsoft usuwa prywatne numery AS w ścieżce AS dla odebranych prefiksów. W związku z tym nie można dołączyć prywatnych numerów AS do ścieżki AS, aby [wpłynąć na routing komunikacji równorzędnej firmy Microsoft](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Publiczne adresy IP anonsowane w sieci firmy Microsoft za pośrednictwem usługi ExpressRoute nie mogą być anonsowane w Internecie. Mogłoby to spowodować przerwanie łączności z innymi usługami firmy Microsoft. Używane przez serwery w sieci użytkownika publiczne adresy IP, które komunikują się z punktami końcowymi usługi O365 w środowisku firmy Microsoft, mogą być jednak anonsowane za pośrednictwem usługi ExpressRoute. 
> 
> 

## <a name="dynamic-route-exchange"></a>Wymiana tras dynamicznych
Wymiana routingu będzie odbywać się za pośrednictwem protokołu eBGP. Sesje eBGP są ustanawiane między rozwiązaniami MSEE a routerami użytkownika. Uwierzytelnianie sesji BGP nie jest wymagane. W razie potrzeby można skonfigurować skrót MD5. Informacje na temat konfigurowania sesji BGP znajdują się w artykułach [Configure routing](expressroute-howto-routing-classic.md) (Konfigurowanie routingu) i [Circuit provisioning workflows and circuit states](expressroute-workflows.md) (Przepływy pracy inicjowania obsługi obwodu i stany obwodu).

## <a name="autonomous-system-numbers"></a>Numery systemu autonomicznego
Firma Microsoft używa numeru AS 12076 do publicznej i prywatnej komunikacji równorzędnej Azure oraz komunikacji równorzędnej Microsoft. Zarezerwowaliśmy numery AS od 65515 do 65520 do użytku wewnętrznego. Obsługiwane są zarówno 16-, jak i 32-bitowe numery AS.

Nie ma żadnych wymagań związanych z symetrią transferu danych. Ścieżki przekazywania dalej i ścieżki zwracania mogą przechodzić różne pary routerów. Trasy identyczne muszą być anonsowane ze wszystkich stron w wielu parach obwodów należących do użytkownika. Metryki tras nie muszą być identyczne.

## <a name="route-aggregation-and-prefix-limits"></a>Agregacja tras i limity prefiksów
Firma Microsoft obsługuje do 4000 prefiksów anonsowanych nam za pośrednictwem prywatnej komunikacji równorzędnej Azure. Tę liczbę można zwiększyć do 10 000 prefiksów, jeśli zostanie włączony dodatek Premium usługi ExpressRoute. Akceptujemy do 200 prefiksów na sesję protokołu BGP dla publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft. 

Sesja protokołu BGP zostanie przerwana, jeśli liczba prefiksów przekroczy limit. Będziemy akceptować domyślne trasy tylko dla linku prywatnej komunikacji równorzędnej. Dostawca musi odfiltrować trasę domyślną i prywatne adresy IP (RFC 1918) ze ścieżek publicznej komunikacji równorzędnej Azure i komunikacji równorzędnej Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Routing tranzytowy i routing obejmujący wiele regionów
Usługi ExpressRoute nie można skonfigurować jako routera tranzytowego. W kwestii usług routingu tranzytowego trzeba polegać na dostawcy połączenia.

## <a name="advertising-default-routes"></a>Anonsowanie tras domyślnych
Trasy domyślne są dozwolone tylko w sesjach prywatnej komunikacji równorzędnej Azure. W takim przypadku firma Microsoft będzie kierować cały ruch ze skojarzonych sieci wirtualnych do sieci użytkownika. Anonsowanie tras domyślnych do prywatnej komunikacji równorzędnej spowoduje zablokowanie ścieżki internetowej z platformy Azure. Aby przekierować ruch do i z Internetu w przypadku usług hostowanych na platformie Azure, konieczne jest zastosowanie krawędzi sieci firmowej. 

 Aby można było włączyć łączność z innymi usługami i usługami infrastruktury Azure, musi być spełniony jeden z poniższych warunków:

* W publicznej komunikacji równorzędnej Azure jest włączone przekierowywanie ruchu do publicznych punktów końcowych.
* Używasz routingu zdefiniowanego przez użytkownika, aby zezwolić na połączenie z Internetem dla każdej podsieci wymagającej takiego połączenia.

> [!NOTE]
> Anonsowanie tras domyślnych spowoduje awarię aktywacji licencji maszyn wirtualnych systemu Windows i innych systemów. Aby obejść ten problem, postępuj zgodnie z instrukcjami zamieszczonymi [tutaj](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx).
> 
> 

## <a name="bgp"></a>Obsługa społeczności BGP
W tej sekcji przedstawiono sposób korzystania z protokołu BGP społeczności w usłudze ExpressRoute. Firma Microsoft będzie anonsować trasy w ścieżkach publicznej komunikacji równorzędnej oraz komunikacji równorzędnej firmy Microsoft za pomocą tras oznaczonych odpowiednimi wartościami społeczności. Uzasadnienie takiego postępowania oraz szczegółowe informacje dotyczące wartości społeczności zostały opisane poniżej. Firma Microsoft nie uzna jednak żadnych wartości społeczności oznaczonych do tras anonsowanych do firmy Microsoft.

Jeśli łączysz się z firmą Microsoft za pośrednictwem usługi ExpressRoute w dowolnej lokalizacji komunikacji równorzędnej w regionie geopolitycznym, będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft we wszystkich regionach w ramach granic geopolitycznych. 

Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute będziesz mieć dostęp do wszystkich usług w chmurze Microsoft obsługiwanych w Europie Północnej i Europie Zachodniej. 

Zapoznaj się ze stroną [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute), która zawiera szczegółową listę regionów geopolitycznych, powiązanych regionów świadczenia usługi Azure i odpowiadających im lokalizacji komunikacji równorzędnej usługi ExpressRoute.

Możesz kupić więcej niż jeden obwód usługi ExpressRoute na region geopolityczny. Wiele ofert połączeń daje znaczące korzyści w zakresie wysokiej dostępności z powodu nadmiarowości geograficznej. Jeśli masz wiele obwodów usługi ExpressRoute, otrzymasz ten sam zestaw prefiksów anonsowanych z firmy Microsoft w publicznej komunikacji równorzędnej i w ścieżkach komunikacji równorzędnej Microsoft. Oznacza to, że będziesz mieć wiele ścieżek ze swojej sieci do firmy Microsoft. Może to teoretycznie spowodować podjęcie nieoptymalnych decyzji w zakresie routingu w ramach sieci. W efekcie połączenia z różnymi usługami mogą nie być optymalne. Wartości społeczności gwarantują, że zostaną podjęte odpowiednie decyzje w kwestii routingu i klienci będą mieli zapewniony [optymalny routing do użytkowników](expressroute-optimize-routing.md).

| **Region platformy Microsoft Azure** | **Wartość społeczności BGP** |
| --- | --- |
| **Ameryka Północna** | |
| Wschodnie stany USA | 12076:51004 |
| Wschodnie stany USA 2 | 12076:51005 |
| Zachodnie stany USA | 12076:51006 |
| Zachodnie stany USA 2 | 12076:51026 |
| Środkowo-zachodnie stany USA | 12076:51027 |
| Środkowo-północne stany USA | 12076:51007 |
| Środkowo-południowe stany USA | 12076:51008 |
| Środkowe stany USA | 12076:51009 |
| Kanada Środkowa | 12076:51020 |
| Kanada Wschodnia | 12076:51021 |
| **Ameryka Południowa** | |
| Brazylia Południowa | 12076:51014 |
| **Europa** | |
| Europa Północna | 12076:51003 |
| Europa Zachodnia | 12076:51002 |
| Południowe Zjednoczone Królestwo | 12076:51024 |
| Zachodnie Zjednoczone Królestwo | 12076:51025 |
| Francja Środkowa | 12076:51030 |
| Francja Południowa | 12076:51031 |
| **Azja i Pacyfik** | |
| Azja Wschodnia | 12076:51010 |
| Azja Południowo-Wschodnia | 12076:51011 |
| **Japonia** | |
| Japonia Wschodnia | 12076:51012 |
| Japonia Zachodnia | 12076:51013 |
| **Australia** | |
| Australia Wschodnia | 12076:51015 |
| Australia Południowo-Wschodnia | 12076:51016 |
| **Indie** | |
| Indie Południowe | 12076:51019 |
| Indie Zachodnie | 12076:51018 |
| Indie Środkowe | 12076:51017 |
| **Korea** | |
| Korea Południowa | 12076:51028 |
| Korea Środkowa | 12076:51029 |


Wszystkie trasy anonsowane przez firmę Microsoft zostaną oznaczone odpowiednią wartością społeczności. 

> [!IMPORTANT]
> Globalne prefiksy są oznaczane odpowiednią wartością społeczności i będą anonsowane tylko po włączeniu dodatku Premium usługi ExpressRoute.
> 
> 

Oprócz tego firma Microsoft oznaczy również prefiksy w oparciu o usługę, do której się odnoszą. Dotyczy to tylko komunikacji równorzędnej firmy Microsoft. Poniższa tabela zawiera mapowanie usługi do wartości społeczności BGP.

| **Usługa** | **Wartość społeczności BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Skype dla firm Online | 12076:5030 |
| Dynamics 365 | 12076:5040 |
| Inne usługi online Office 365 | 12076:5100 |

> [!NOTE]
> Firma Microsoft nie uznaje żadnych wartości społeczności BGP ustawionych na trasach anonsowanych do firmy Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>Obsługa społeczności BGP w chmurach krajowych (wersja zapoznawcza)

| **Region chmur krajowych platformy Azure**| **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| Administracja USA — Arizona | 12076:51106 |
| Administracja USA — Iowa | 12076:51109 |
| Administracja USA — Wirginia | 12076:51105 |
| Administracja USA — Teksas | 12076:51108 |
| US DoD — środkowe stany | 12076:51209 |
| US DoD — wschodnie stany | 12076:51205 |


| **Usługa w chmurach krajowych** | **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype dla firm Online |12076:5130 |
| Dynamics 365 |12076:5140 |
| Inne usługi online Office 365 |12076:5200 |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
