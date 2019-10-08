---
title: 'Wymagania dotyczące routingu — ExpressRoute: Azure | Microsoft Docs'
description: Ta strona zawiera szczegółowe wymagania dotyczące konfigurowania routingu obwodów usługi ExpressRoute i zarządzania nimi.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 77c90cccbbfa5865878ae4dc47e5009697560ed3
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001313"
---
# <a name="expressroute-routing-requirements"></a>Wymagania dotyczące routingu ExpressRoute
Aby nawiązać połączenie z usługami w chmurze firmy Microsoft przy użyciu usługi ExpressRoute, musisz skonfigurować Routing i zarządzać nim. Niektórzy dostawcy połączenia oferują Konfigurowanie routingu i zarządzanie nim jako usługą zarządzaną. Skontaktuj się z dostawcą połączenia, aby sprawdzić, czy oferują tę usługę. Jeśli nie, musisz przestrzegać następujących wymagań:

Zapoznaj się z artykułem [obwody i domeny routingu](expressroute-circuit-peerings.md) , aby zapoznać się z opisem sesji routingu, które należy skonfigurować w programie w celu ułatwienia łączności.

> [!NOTE]
> Firma Microsoft nie obsługuje żadnych protokołów nadmiarowości routera (na przykład HSRP, VRRP) w przypadku konfiguracji o wysokiej dostępności. W celu zapewnienia wysokiej dostępności korzystamy z nadmiarowej pary sesji protokołu BGP na komunikację równorzędną.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Adresy IP używane do komunikacji równorzędnej
Musisz zarezerwować kilka bloków adresów IP, aby skonfigurować Routing między sieciami i routerami Microsoft Enterprise Edge (MSEE). Ta sekcja zawiera listę wymagań i Opis reguł dotyczących sposobu uzyskiwania i używania tych adresów IP.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresy IP używane do prywatnej komunikacji równorzędnej Azure
Aby skonfigurować komunikację równorzędną, można użyć prywatnych adresów IP lub publicznych adresów IP. Zakres adresów używany do konfigurowania tras nie może pokrywać się z zakresami adresów używanymi do tworzenia sieci wirtualnych na platformie Azure. 

* Należy zarezerwować podsieć/29 lub dwie podsieci/30 dla interfejsów routingu.
* Podsieci używane do routingu mogą być prywatnymi adresami IP lub publicznymi adresami IP.
* Podsieci nie mogą powodować konfliktu z zakresem zarezerwowanym przez klienta do użycia w chmurze firmy Microsoft.
* Jeśli zostanie użyta podsieć/29, zostanie ona podzielona na dwie podsieci/30. 
  * Pierwsza podsieć/30 jest używana na potrzeby linku podstawowego, a druga podsieć/30 jest używana na potrzeby linku pomocniczego.
  * Dla każdej podsieci/30 należy użyć pierwszego adresu IP podsieci/30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci/30 do skonfigurowania sesji protokołu BGP.
  * Aby [Umowa SLA dotycząca dostępności](https://azure.microsoft.com/support/legal/sla/) była ważna, musisz skonfigurować obie sesje protokołu BGP.  

#### <a name="example-for-private-peering"></a>Przykład prywatnej komunikacji równorzędnej
Jeśli zdecydujesz się na skonfigurowanie komunikacji równorzędnej za pomocą a. b. c. d/29, zostanie ona podzielona na dwie podsieci/30. W poniższym przykładzie Zwróć uwagę na to, jak jest używana podsieć a. b. c. d/29:

* a. b. c. d/29 jest podzielona na a. b. c. d/30 i a. b. c. d + 4/30 i przenoszona do firmy Microsoft za pomocą interfejsów API aprowizacji.
  * Używasz. b. c. d + 1 jako VRF IP dla podstawowego środowiska PE, a firma Microsoft będzie używać a. b. c. d + 2 jako adresu IP VRF podstawowego MSEE.
  * Należy użyć. b. c. d + 5 jako VRF IP dla pomocniczego środowiska PE, a firma Microsoft będzie używać a. b. c. d + 6 jako adresu IP MSEE.

Rozważ użycie w przypadku wybrania opcji 192.168.100.128/29 w celu skonfigurowania prywatnej komunikacji równorzędnej. 192.168.100.128/29 obejmuje adresy od 192.168.100.128 do 192.168.100.135, między innymi:

* 192.168.100.128/30 zostanie przypisany do link1, z dostawcą przy użyciu 192.168.100.129 i firmy Microsoft za pomocą 192.168.100.130.
* 192.168.100.132/30 zostanie przypisany do Link2, z dostawcą przy użyciu 192.168.100.133 i firmy Microsoft za pomocą 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>Adresy IP używane do komunikacji równorzędnej firmy Microsoft
Aby skonfigurować sesje protokołu BGP, należy użyć publicznych adresów IP. Firma Microsoft musi mieć możliwość zweryfikowania własności adresów IP za pomocą rejestrów routingu internetowego i rejestrów routingu internetowego.

* Adresy IP wymienione w portalu dla ogłaszanych publicznie prefiksów dla komunikacji równorzędnej firmy Microsoft zostaną utworzone listy ACL dla routerów Microsoft Core w celu zezwolenia na ruch przychodzący z tych adresów IP. 
* Aby skonfigurować komunikację równorzędną BGP dla każdej komunikacji równorzędnej dla obwodu usługi ExpressRoute, należy użyć unikatowej podsieci/29 (IPv4) lub/125 (IPv6) lub dwóch podsieci/30 (IPv4) lub/126 (IPv6).
* Jeśli zostanie użyta podsieć/29, zostanie ona podzielona na dwie podsieci/30.
* Pierwsza podsieć/30 jest używana na potrzeby linku podstawowego, a druga podsieć/30 zostanie użyta na potrzeby linku pomocniczego.
* Dla każdej podsieci/30 należy użyć pierwszego adresu IP podsieci/30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci/30 do skonfigurowania sesji protokołu BGP.
* Jeśli zostanie użyta podsieć/125, zostanie ona podzielona na dwie podsieci/126.
* Pierwsza podsieć/126 jest używana na potrzeby linku podstawowego, a druga podsieć/126 zostanie użyta dla linku pomocniczego.
* Dla każdej podsieci/126 należy użyć pierwszego adresu IP podsieci/126 na routerze. Firma Microsoft używa drugiego adresu IP podsieci/126 do skonfigurowania sesji protokołu BGP.
* Aby [Umowa SLA dotycząca dostępności](https://azure.microsoft.com/support/legal/sla/) była ważna, musisz skonfigurować obie sesje protokołu BGP.

### <a name="ip-addresses-used-for-azure-public-peering"></a>Adresy IP używane do publicznej komunikacji równorzędnej Azure

> [!NOTE]
> Publiczna Komunikacja równorzędna Azure nie jest dostępna dla nowych obwodów.
> 

Aby skonfigurować sesje protokołu BGP, należy użyć publicznych adresów IP. Firma Microsoft musi mieć możliwość zweryfikowania własności adresów IP za pomocą rejestrów routingu internetowego i rejestrów routingu internetowego. 

* Należy użyć unikatowej podsieci/29 lub dwóch podsieci/30 do skonfigurowania komunikacji równorzędnej protokołu BGP dla każdej komunikacji równorzędnej na obwód usługi ExpressRoute (Jeśli masz więcej niż jeden). 
* Jeśli zostanie użyta podsieć/29, zostanie ona podzielona na dwie podsieci/30. 
  * Pierwsza podsieć/30 jest używana na potrzeby linku podstawowego, a druga podsieć/30 jest używana na potrzeby linku pomocniczego.
  * Dla każdej podsieci/30 należy użyć pierwszego adresu IP podsieci/30 na routerze. Firma Microsoft używa drugiego adresu IP podsieci/30 do skonfigurowania sesji protokołu BGP.
  * Aby [Umowa SLA dotycząca dostępności](https://azure.microsoft.com/support/legal/sla/) była ważna, musisz skonfigurować obie sesje protokołu BGP.

## <a name="public-ip-address-requirement"></a>Wymagania dotyczące publicznego adresu IP

### <a name="private-peering"></a>Prywatna Komunikacja równorzędna
Możesz użyć publicznych lub prywatnych adresów IPv4 do prywatnej komunikacji równorzędnej. Zapewniamy kompleksową izolację ruchu, dlatego w przypadku prywatnych komunikacji równorzędnej nie jest możliwe nakładanie się adresów z innymi klientami. Te adresy nie są anonsowane w Internecie. 

### <a name="microsoft-peering"></a>Komunikacja równorzędna firmy Microsoft
Ścieżka komunikacji równorzędnej firmy Microsoft umożliwia nawiązanie połączenia z usługami w chmurze firmy Microsoft. Lista usług obejmuje usługi Office 365, takie jak Exchange Online, SharePoint Online, Skype dla firm i Microsoft Teams. Firma Microsoft obsługuje dwukierunkową łączność komunikacji równorzędnej firmy Microsoft. Ruch kierowany do usług w chmurze firmy Microsoft musi używać prawidłowych publicznych adresów IPv4 przed wprowadzeniem do sieci firmy Microsoft.

Upewnij się, że adres IP i numer AS są zarejestrowane w jednym z następujących rejestrów:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Jeśli prefiksy i numer AS nie są przypisane do Ciebie w powyższych rejestrach, musisz otworzyć przypadek pomocy technicznej w celu ręcznej weryfikacji Twoich prefiksów i numeru ASN. Obsługa wymaga dokumentacji, takiej jak autoryzacja, która potwierdza, że można korzystać z zasobów.

Prywatny numer AS jest dozwolony w komunikacji równorzędnej firmy Microsoft, ale będzie również wymagał ręcznej weryfikacji. Ponadto firma Microsoft usuwa prywatne numery AS w ścieżce AS dla odebranych prefiksów. W związku z tym nie można dołączyć prywatnych jako cyfry w ścieżce AS, aby [mieć wpływ na Routing komunikacji równorzędnej firmy Microsoft](expressroute-optimize-routing.md). 

> [!IMPORTANT]
> Nie należy anonsować tej samej publicznej trasy IP do publicznego Internetu i za pośrednictwem ExpressRoute. Aby zmniejszyć ryzyko niepoprawnej konfiguracji powodującej Routing asymetryczny, zdecydowanie zalecamy, aby [adresy IP translatora adresów sieciowych](expressroute-nat.md) anonsowane do firmy Microsoft przez ExpressRoute były z zakresu, który nie jest anonsowany w Internecie. Jeśli nie jest to możliwe, należy się upewnić, że anonsuje bardziej konkretny zakres od ExpressRoute niż ten, na którym nadano połączenie internetowe. Poza trasą publiczną dla translatora adresów sieciowych można także anonsować za pośrednictwem usługi ExpressRoute publiczne adresy IP używane przez serwery w sieci lokalnej, które komunikują się z punktami końcowymi pakietu Office 365 w firmie Microsoft. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Publiczna Komunikacja równorzędna (przestarzałe — niedostępne w przypadku nowych obwodów)
Ścieżka publicznej komunikacji równorzędnej platformy Azure umożliwia łączenie się ze wszystkimi usługami hostowanymi na platformie Azure za pośrednictwem ich publicznych adresów IP. Obejmują one usługi wymienione w [usługa ExpressRoute często zadawane pytania](expressroute-faqs.md) i wszystkie usługi hostowane przez niezależnych dostawców oprogramowania na Microsoft Azure. Łączność z usługami Microsoft Azureymi w publicznej komunikacji równorzędnej jest zawsze inicjowana z sieci do sieci firmy Microsoft. Należy używać publicznych adresów IP dla ruchu kierowanego do sieci firmy Microsoft.

> [!IMPORTANT]
> Wszystkie usługi Azure PaaS są dostępne za pomocą komunikacji równorzędnej firmy Microsoft.
>   

Prywatny numer AS jest dozwolony w publicznej komunikacji równorzędnej.

## <a name="dynamic-route-exchange"></a>Wymiana tras dynamicznych
Program Exchange Routing będzie za pośrednictwem protokołu eBGP. Sesje EBGP są ustanawiane między MSEE i routerami. Uwierzytelnianie sesji protokołu BGP nie jest wymagane. W razie potrzeby można skonfigurować skrót MD5. Aby uzyskać informacje o konfigurowaniu sesji protokołu BGP, zobacz sekcję Konfigurowanie [przepływów pracy i Stanów obwodów](expressroute-workflows.md) dotyczących [konfigurowania routingu](how-to-routefilter-portal.md) i obwodów.

## <a name="autonomous-system-numbers"></a>Numery systemu autonomicznego
Firma Microsoft stosuje jako 12076 dla publicznej platformy Azure, prywatnej i równorzędnej firmy Microsoft. Firma Microsoft zarezerwowa WPW od 65515 do 65520 do użytku wewnętrznego. Obsługiwane są zarówno 16, jak i 32 bitów jako liczby.

Nie ma wymagań dotyczących symetrii transferu danych. Ścieżki do przodu i powrotu mogą przechodzić przez różne pary routerów. Trasy identyczne muszą być anonsowane z obu stron w wielu parach obwodów należących do użytkownika. Metryki trasy nie muszą być identyczne.

## <a name="route-aggregation-and-prefix-limits"></a>Agregacja tras i limity prefiksów
Obsługujemy do 4000 prefiksy anonsowane nam za pomocą prywatnej komunikacji równorzędnej Azure. Można to zwiększyć do 10 000 prefiksów, jeśli włączono dodatek ExpressRoute Premium. Akceptujemy do 200 prefiksy na sesję protokołu BGP dla publicznej komunikacji równorzędnej platformy Azure i firmy Microsoft. 

Sesja protokołu BGP zostanie porzucona, jeśli liczba prefiksów przekroczy limit. Będziemy akceptować domyślne trasy tylko dla linku prywatnej komunikacji równorzędnej. Dostawca musi odfiltrować trasę domyślną i prywatne adresy IP (RFC 1918) ze ścieżek publicznych i prywatnych komunikacji równorzędnej platformy Azure. 

## <a name="transit-routing-and-cross-region-routing"></a>Routing tranzytowy i Routing między regionami
Nie można skonfigurować ExpressRoute jako routerów tranzytowych. Musisz polegać na dostawcy łączności dla usług routingu tranzytowego.

## <a name="advertising-default-routes"></a>Anonsowanie tras domyślnych
Trasy domyślne są dozwolone tylko w przypadku sesji prywatnych komunikacji równorzędnych platformy Azure. W takim przypadku będziemy kierować cały ruch ze skojarzonych sieci wirtualnych do sieci. Anonsowanie tras domyślnych w prywatnej komunikacji równorzędnej spowoduje zablokowanie ścieżki internetowej z platformy Azure. Aby kierować ruchem z i do Internetu usług hostowanych na platformie Azure, musisz polegać na firmowej granicy. 

 Aby włączyć łączność z innymi usługami platformy Azure i usługami infrastruktury, należy upewnić się, że jest jeden z następujących elementów:

* Publiczna Komunikacja równorzędna Azure jest włączona, aby kierować ruch do publicznych punktów końcowych.
* Używasz routingu zdefiniowanego przez użytkownika, aby zezwalać na łączność z Internetem dla każdej podsieci wymagającej łączności z Internetem.

> [!NOTE]
> Anonsowanie tras domyślnych spowoduje przerwanie aktywacji licencji na system Windows i inną maszynę wirtualną. Postępuj zgodnie z instrukcjami [tutaj](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) , aby obejść ten temat.
> 
> 

## <a name="bgp"></a>Obsługa społeczności BGP
Ta sekcja zawiera omówienie sposobu używania społeczności protokołu BGP z ExpressRoute. Firma Microsoft anonsuje trasy w publicznych i przesyłanych przez firmę Microsoft ścieżkach komunikacji równorzędnej przy użyciu tras oznakowanych odpowiednimi wartościami społecznościowymi. Uzasadnienie tego działania oraz szczegóły dotyczące wartości społeczności są opisane poniżej. Firma Microsoft nie będzie jednak przestrzegać żadnych wartości społeczności oznaczonych do tras anonsowanych firmie Microsoft.

Jeśli łączysz się z firmą Microsoft za pośrednictwem usługi ExpressRoute w jednej lokalizacji komunikacji równorzędnej w regionie geopolitycznym, będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft we wszystkich regionach w granicach geopolitycznych. 

Jeśli na przykład nawiązać połączenie z firmą Microsoft w Amsterdamie za poorednictwem ExpressRoute, będziesz mieć dostęp do wszystkich usług w chmurze firmy Microsoft obsługiwanych w Europie Północnej i Europie Zachodniej. 

Aby uzyskać szczegółową listę regionów geopolitycznych, skojarzonych regionów świadczenia usługi Azure i odpowiednich lokalizacji komunikacji równorzędnej ExpressRoute, zapoznaj się z informacjami na stronie [partnerzy ExpressRoute i lokalizacje komunikacji równorzędnej](expressroute-locations.md) .

Możesz kupić więcej niż jeden obwód ExpressRoute w regionie geopolitycznym. Posiadanie wielu połączeń oferuje znaczne korzyści z dużej dostępności z powodu nadmiarowości geograficznej. W przypadkach, gdy masz wiele obwodów usługi ExpressRoute, otrzymasz ten sam zestaw prefiksów anonsowanych od firmy Microsoft na ścieżce komunikacji równorzędnej firmy Microsoft i publicznych komunikacji równorzędnych. Oznacza to, że będziesz mieć wiele ścieżek z sieci do firmy Microsoft. Może to spowodować, że podoptymalne decyzje dotyczące routingu będą podejmowane w sieci. W związku z tym może wystąpić nieoptymalne środowisko łączności z różnymi usługami. Możesz polegać na wartościach społeczności, aby podejmować odpowiednie decyzje dotyczące routingu, aby zaoferować [użytkownikom optymalny Routing](expressroute-optimize-routing.md).

| **Region Microsoft Azure** | **Regionalna społeczność protokołu BGP** | **Społeczność protokołu BGP magazynu** | **Społeczność protokołu BGP SQL** | **Cosmos DB społeczność protokołu BGP** |
| --- | --- | --- | --- | --- |
| **Ameryka Północna** | |
| Wschodnie stany USA | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 |
| Wschodnie stany USA 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 |
| Zachodnie stany USA | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 |
| Zachodnie stany USA 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 |
| Zachodnio-środkowe stany USA | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 |
| Północno-środkowe stany USA | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 |
| Południowo-środkowe stany USA | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 |
| Środkowe stany USA | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 |
| Kanada środkowa | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 |
| Kanada Wschodnia | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 |
| **Ameryka Południowa** | |
| Brazylia Południowa | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 |
| **Terenie** | |
| Europa Północna | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 |
| Europa Zachodnia | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 |
| Południowe Zjednoczone Królestwo | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 |
| Zachodnie Zjednoczone Królestwo | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 |
| Francja środkowa | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 |
| Francja Południowa | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 |
| **Azja i Pacyfik** | |
| Azja Wschodnia | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 |
| Azja Południowo-Wschodnia | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 |
| **Japonia** | |
| Japonia Wschodnia | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 |
| Japonia Zachodnia | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 |
| **Australia** | |
| Australia Wschodnia | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 |
| Australia Południowo-Wschodnia | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 |
| **Australia rządowa** | |
| Australia Środkowa | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 |
| Australia Środkowa 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 |
| **Indie** | |
| Indie Południowe | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 |
| Indie Zachodnie | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 |
| Indie Środkowe | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 |
| **Korea** | |
| Korea Południowa | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 |
| Korea środkowa | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 |
| **Republika Południowej Afryki**| |
| Północna Republika Południowej Afryki | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 |
| Zachodnia Republika Południowej Afryki | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 |
| **Emiraty**| |
| Północne Zjednoczone Emiraty Arabskie | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 |
| Środkowy Zjednoczone Emiraty Arabskie | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 |


Wszystkie trasy anonsowane przez firmę Microsoft zostaną oznaczone odpowiednią wartością społeczności. 

> [!IMPORTANT]
> Globalne prefiksy są znakowane z odpowiednią wartością społeczności.
> 
> 

### <a name="service-to-bgp-community-value"></a>Wartość wspólnotowa usługi BGP
Oprócz powyższych, firma Microsoft będzie również oznakować prefiksy w zależności od usługi, do której należą. Dotyczy to tylko komunikacji równorzędnej firmy Microsoft. Poniższa tabela zawiera mapowanie usługi do wartości społeczności BGP. Można uruchomić polecenie cmdlet "Get-AzBgpServiceCommunity", aby zapoznać się z pełną listą najnowszych wartości.

| **Usługa** | **Wartość społeczności BGP** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Skype dla firm Online | 12076:5030 |
| Usługi globalne platformy Azure * | 12076:5050 |
| Inne usługi online pakietu Office 365 | 12076:5100 |

\* Usługi globalne platformy Azure obejmują tylko usługę Azure DevOps.


> [!NOTE]
> Firma Microsoft nie przestrzega żadnych wartości społeczności BGP ustawionych dla tras anonsowanych firmie Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Obsługa społeczności protokołu BGP w chmurach narodowych

| **Chmura krajowa — region platformy Azure**| **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| US Gov Arizona | 12076:51106 |
| US Gov Iowa | 12076:51109 |
| US Gov Wirginia | 12076:51105 |
| US Gov Teksas | 12076:51108 |
| US DoD (region środkowy) | 12076:51209 |
| US DoD (region wschodni) | 12076:51205 |


| **Usługa w chmurach narodowych** | **Wartość społeczności BGP** |
| --- | --- |
| **Administracja USA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype dla firm Online |12076:5130 |
| Inne usługi online pakietu Office 365 |12076:5200 |

## <a name="next-steps"></a>Następne kroki
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)
