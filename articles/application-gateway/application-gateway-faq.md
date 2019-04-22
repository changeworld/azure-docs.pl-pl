---
title: Często zadawane pytania dotyczące usługi Azure Application Gateway
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: 3c8a2fe9f4486fe4d33754b58f4e7ebec1b3252d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682955"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Często zadawane pytania na temat usługi Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Ogólne

### <a name="what-is-application-gateway"></a>Co to jest usługa Application Gateway?

Usługa Azure Application Gateway oferuje kontrolera dostarczania aplikacji (ADC) jako usługę. Oferuje ona różne warstwy 7 możliwości równoważenia obciążenia dla aplikacji. Ta usługa jest wysoce dostępne, skalowalne i w pełni zarządzane przez platformę Azure.

### <a name="what-features-does-application-gateway-support"></a>Jakie funkcje obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje Skalowanie automatyczne, odciążanie protokołu SSL i end-to-end SSL, zapory aplikacji sieci web (WAF), koligacja sesji na podstawie plików cookie, adresu URL routingu opartego na ścieżkach, hostingu w wielu lokacjach i inne funkcje. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [wprowadzenie do usługi Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Jak Application Gateway i Azure Load Balancer różni się?

Application Gateway to moduł równoważenia obciążenia warstwy 7, co oznacza, że działa tylko w przypadku ruchu w sieci web (HTTP, HTTPS, protokołu WebSocket i protokołu HTTP/2). Obsługuje funkcje, takie jak kończenia żądań SSL, koligacja sesji na podstawie pliku cookie i działanie okrężne Równoważenie obciążenia sieciowego ruchu. Obciążenia równoważenia Równoważenie obciążenia ruchu w warstwie 4 (TCP lub UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jakie protokoły obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje HTTP, HTTPS i HTTP/2 oraz protokołu WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak usługa Application Gateway obsługuje protokołu HTTP/2

Zobacz [Obsługa protokołu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Jakie zasoby są obsługiwane w ramach puli wewnętrznej bazy danych?

Zobacz [obsługiwanych zasobów zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>W jakich regionach jest dostępna usługa Application Gateway?

Usługa Application Gateway jest dostępna we wszystkich regionach, globalnej platformy Azure. Jest również dostępna w [Azure China 21Vianet](https://www.azure.cn/) i [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Jest to wdrożenie dedykowany dla mojej subskrypcji, czy jest udostępniana wielu odbiorców?

Usługa Application Gateway to dedykowane wdrożenie w sieci wirtualnej.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Usługa Application Gateway obsługuje przekierowania HTTP do HTTPS?

Przekierowywanie jest obsługiwane. Zobacz [Application Gateway redirect overview](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>W jakiej kolejności odbiorników przetwarzania?

Zobacz [kolejność przetwarzania odbiornika](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Gdzie znaleźć adres IP bramy aplikacji i DNS

Jeśli używasz publicznego adresu IP jako punkt końcowy, znajdziesz informacje dotyczące adresów IP i DNS na zasób publicznego adresu IP. Lub znajduje się w portalu na stronie przeglądu usługi application gateway. Jeśli używasz wewnętrzne adresy IP można znaleźć informacje na stronie Przegląd.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Co to są ustawienia limitu czasu podtrzymania i limitu czasu bezczynności protokołu TCP?

 W ramach jednostki SKU v1 Application Gateway Keep-Alive limitu czasu wynosi 120 sekund. Keep-Alive limitu czasu dla jednostki SKU w wersji 2 jest 75 sekund. Limit czasu bezczynności protokołu TCP jest 4-minutowej domyślny na frontonie wirtualny adres IP (VIP) usługi Application Gateway.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Nazwa DNS lub adresu IP zmienia się w okresie istnienia usługi application gateway?

Wirtualne adresy IP można zmienić, gdy zatrzymujesz i uruchamiasz bramy aplikacji. Jednak nazwy DNS skojarzone z usługą application gateway nie zmienia się w okresie istnienia bramy. Ponieważ nie zmienia nazwę DNS, należy Użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

### <a name="does-application-gateway-support-static-ip"></a>Usługa Application Gateway obsługuje statyczny adres IP?

Tak, usługa Application Gateway jednostkę SKU v2 obsługuje statyczne publiczne adresy IP. Jednostka SKU v1 obsługuje statyczne wewnętrzne adresy IP.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Usługa Application Gateway obsługuje wiele publicznych adresów IP w ramach bramy?

Usługa application gateway obsługuje tylko jeden publiczny adres IP.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak duże powinny utworzyć mojej podsieci dla bramy Application Gateway?

Zobacz [zagadnienia rozmiar podsieci bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Do jednej podsieci można wdrażać więcej niż jednego zasobu usługi Application Gateway?

Tak. Oprócz wielu wystąpień danego wdrożenia bramy Application Gateway można udostępnić innym unikatowy zasobu usługi Application Gateway do istniejącej podsieci, który zawiera różne zasobu usługi Application Gateway.

Jednej podsieci nie może obsługiwać razem Standard_v2 i standardową bramą aplikacji.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Usługa Application Gateway obsługuje nagłówków x-forwarded dla?

Tak. Zobacz [modyfikacji na żądanie](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Jak długo trwa wdrażanie bramy aplikacji? Moje bramy application gateway będzie działać, gdy jest aktualizowana?

Nowych wdrożeń jednostek SKU v1 Application Gateway może potrwać do 20 minut do aprowizowania. Zmiany rozmiaru wystąpienia lub liczby nie są naturalnymi, a brama pozostaje aktywna, w tym czasie.

Wdrożenia korzystające z jednostek SKU v2 może potrwać do 6 minut aprowizację.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Czy można użyć programu Exchange Server jako wewnętrznej bazy danych z usługą Application Gateway?

Nie. Usługa Application Gateway nie obsługuje protokołów poczty e-mail, takich jak POP3, SMTP i IMAP. 

## <a name="performance"></a>Wydajność

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak usługa Application Gateway obsługuje wysoką dostępność i skalowalność?

Usługa Application Gateway jednostkę SKU v1 obsługuje scenariusze wysokiej dostępności po wdrożeniu co najmniej dwóch wystąpień. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby upewnić się, że wystąpienia nie wszystkie kończyć się niepowodzeniem, w tym samym czasie. Jednostka SKU v1 obsługuje skalowalności, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

Jednostka SKU v2 automatycznie sprawdza, czy nowe wystąpienia są dystrybuowane w domenach błędów i domenach aktualizacji. Jeśli wybierzesz opcję nadmiarowości strefy, najnowszych wystąpień rozprzestrzeniają się również w strefach dostępności do zaoferowania odporności strefowej awarii.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Jak osiągnąć scenariusza odzyskiwania po awarii w centrach danych za pomocą bramy Application Gateway

Traffic Manager można używać do rozdzielenia ruchu między wiele bram aplikacji w różnych centrach danych.

### <a name="does-application-gateway-support-autoscaling"></a>Usługa Application Gateway obsługuje Skalowanie automatyczne?

Tak, usługa Application Gateway jednostkę SKU v2 obsługuje funkcję skalowania automatycznego. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway (publiczna wersja zapoznawcza)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Jest ręczne skalowanie w lub w dół przestój Przyczyna?

Nie. Wystąpienia są rozproszone między domeny błędów i domenach uaktualniania.

### <a name="does-application-gateway-support-connection-draining"></a>Usługa Application Gateway obsługuje opróżniania połączenia?

Tak. Można skonfigurować połączenia opróżniania, aby zmienić członków w ramach puli zaplecza, bez przerw w działaniu. Ta konfiguracja umożliwia możesz kontynuować wysyłanie istniejące połączenia do ich poprzedniego miejsca docelowego do momentu to połączenie zostanie zamknięte lub upłynie limit czasu można konfigurować. Opróżniania połączenia usługi czeka tylko bieżące aktywne połączenia na zakończenie. Usługa Application Gateway nie jest świadomy stanu sesji aplikacji.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Czy mogę zmienić rozmiar wystąpienia średnie dużych bez zakłóceń?

Tak. Platforma Azure rozdzieli wystąpień w domenach aktualizacji i błędów, aby upewnić się, że wystąpienia nie zakończy się niepowodzeniem w wszystko na tym samym czasie. Usługa Application Gateway obsługuje skalowanie, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

## <a name="configuration"></a>Konfigurowanie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Usługa Application Gateway zawsze wdrożono w sieci wirtualnej?

Tak. Usługa Application Gateway jest zawsze wdrażane w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy aplikacji. Aby uzyskać więcej informacji, zobacz [wirtualnej wymagania dotyczące sieci i podsieci](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Może Application Gateway komunikować się z wystąpieniami poza siecią wirtualną lub na zewnątrz jej subskrypcja?

Tak długo, jak możesz korzystać z łączności IP, usługa Application Gateway mogą komunikować się z wystąpieniami poza siecią wirtualną, która znajduje się w. Usługa Application Gateway mogą również komunikować się z wystąpień poza subskrypcję, w której znajduje się w. Jeśli planujesz użyć wewnętrznych adresów IP jako składowych puli zaplecza, użyj [wirtualne sieci równorzędne](../virtual-network/virtual-network-peering-overview.md) lub [bramy Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Czy mogę wdrożyć niczego więcej w podsieci bramy aplikacji?

Nie. Jednak można wdrożyć inne bramy aplikacji w podsieci.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Czy sieciowe grupy zabezpieczeń są obsługiwane w podsieci bramy aplikacji?

Zobacz [sieciowe grupy zabezpieczeń w podsieci bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Podsieci bramy aplikacji obsługuje trasy zdefiniowane przez użytkownika?

Zobacz [trasy zdefiniowane przez użytkownika, obsługiwane w podsieci usługi Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jakie są limity dla usługi Application Gateway? Czy można zwiększyć te limity?

Zobacz [ogranicza Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Można jednocześnie używać bramy Application Gateway dla wewnętrznych i zewnętrznych ruchu?

Tak. Usługa Application Gateway obsługuje jeden wewnętrzny adres IP i jeden zewnętrzny adres IP dla bramy application gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Usługa Application Gateway obsługuje komunikację równorzędną sieci wirtualnej?

Tak. Komunikacja równorzędna sieci wirtualnych pomaga równoważyć obciążenie ruchem w innych sieciach wirtualnych.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Może mi odpowiedzieć na lokalnych serwerach gdy są one połączone przez tunele ExpressRoute lub sieci VPN?

Tak, tak długo, jak ruch jest dozwolony.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Jedna pula zaplecza, może obsługiwać wiele aplikacji na różnych portów?

Architektura Mikrousług jest obsługiwane. Badanie na inne porty, należy skonfigurować wiele ustawień protokołu HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Niestandardowe sondy obsługują symboli wieloznacznych i wyrażeń regularnych na dane odpowiedzi?

Nie. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Jak są przetwarzane reguły routingu w usłudze Application Gateway?

Zobacz [kolejność przetwarzania reguły](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Dla sond niestandardowych co to pole Host oznaczającego?

Pole Host Określa nazwę aby wysłać sondy po skonfigurowaniu wielu lokacjach w usłudze Application Gateway. W przeciwnym razie użyj "127.0.0.1". Ta wartość jest inna niż nazwa hosta maszyny wirtualnej. Jego format jest \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\>.

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>Czy można umieścić na liście dozwolonych bramy aplikacji dostęp do tylko kilka źródłowych adresów IP?

Tak. Zobacz [ograniczyć dostęp do określonych źródłowych adresów IP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Dla publicznego i prywatnego skierowaną detektorów można używać tego samego portu?

Nie.

## <a name="configuration---ssl"></a>Konfiguracja — SSL

### <a name="what-certificates-does-application-gateway-support"></a>Certyfikaty, które obsługuje bramy Application Gateway?

Usługa Application Gateway obsługuje certyfikatów z podpisem własnym, certyfikatu urzędu certyfikacji certyfikatów, certyfikaty weryfikacji rozszerzonej i certyfikatów z symbolami wieloznacznymi.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Mechanizmach szyfrowania jest usługa Application Gateway obsługuje?

Usługa Application Gateway obsługuje następujące mechanizmów szyfrowania. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Aby uzyskać informacje na temat sposobu dostosowywania opcje protokołu SSL, zobacz [Konfigurowanie protokołu SSL, wersje zasad i mechanizmów szyfrowania w usłudze Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Usługa Application Gateway obsługuje reencryption ruchu do wewnętrznej bazy danych?

Tak. Usługa Application Gateway obsługuje odciążanie protokołu SSL i end-to-end SSL, które ponownie zaszyfrować ruch do wewnętrznej bazy danych.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Można skonfigurować zasad protokołu SSL do wersji protokołu SSL kontroli?

Tak. Można skonfigurować bramę aplikacji do odmowy TLS1.0 TLS1.1 i TLS1.2. Domyślnie protokół SSL 2.0 i 3.0 są już wyłączone i nie są konfigurowalne.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Mechanizmy szyfrowania i kolejności zasad można skonfigurować?

Tak. W usłudze Application Gateway możesz [Konfigurowanie mechanizmów szyfrowania](application-gateway-ssl-policy-overview.md). Aby zdefiniować zasady niestandardowe, należy włączyć co najmniej jeden z następujących mechanizmów szyfrowania. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Usługa Application Gateway używa SHA256 do wewnętrznej bazy danych zarządzania.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Jak wiele certyfikatów SSL obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje maksymalnie 100 certyfikaty SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Jak wiele certyfikatów uwierzytelniania dla reencryption wewnętrznej bazy danych obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje maksymalnie 10 certyfikaty uwierzytelniania. Wartość domyślna to 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Czy usługa Application Gateway można natywnie zintegrować z usługą Azure Key Vault?

Nie.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Jak skonfigurować odbiorników HTTPS dla witryny .com i .net? 

Dla wielu domen routingu opartego na (oparte na hoście) można utworzyć wielu lokacjach odbiorników, skonfiguruj odbiorników, korzystających z protokołu HTTPS i skojarzyć odbiorniki za pomocą reguł routingu. Aby uzyskać więcej informacji, zobacz [hostowanie wielu witryn przy użyciu bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguracja — Zapora aplikacji sieci web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Jednostka SKU zapory aplikacji sieci Web oferuje wszystkie funkcje dostępne w wersji Standard?

Tak. Zapora aplikacji sieci Web obsługuje wszystkie funkcje standardowej jednostki SKU.

### <a name="which-crs-versions-does-application-gateway-support"></a>Które wersje CRS obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Jak monitorować zapory aplikacji sieci Web?

Monitorowanie zapory aplikacji internetowych za pośrednictwem rejestrowania diagnostycznego. Aby uzyskać więcej informacji, zobacz [rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Tryb wykrywania blokować ruch?

Nie. Tryb wykrywania rejestruje tylko ruch, który wywołuje regułę zapory aplikacji sieci Web.

### <a name="can-i-customize-waf-rules"></a>Czy można dostosować reguły zapory aplikacji sieci Web?

Tak. Aby uzyskać więcej informacji, zobacz [zapory aplikacji sieci Web dostosowywanie grup reguł i reguł](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Jakie zasady są obecnie dostępne dla zapory aplikacji sieci Web?

Zapora aplikacji sieci Web obsługuje obecnie CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Reguły te udostępniają linii bazowej zabezpieczeń przed większością z 10 pierwszych luk w zabezpieczeniach, które identyfikuje Otwórz sieci Web aplikacji Security Project (OWASP): 

* Ochrona przed atakami polegającymi na iniekcji SQL
* Ochrona skryptów między witrynami
* Ochrona przed typowymi atakami w sieci web, takich jak polegające na iniekcji poleceń, przemycaniu odpowiedzi HTTP, rozdzielaniu żądań HTTP i ataku dołączenie pliku zdalnego
* Ochrona przed naruszeniami protokołu HTTP
* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków
* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów
* Wykrywanie typowych błędów konfiguracji aplikacji (czyli Apache, usługi IIS i tak dalej)

Aby uzyskać więcej informacji, zobacz [luk w zabezpieczeniach OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Zapora aplikacji sieci Web obsługuje usługę DDoS protection?

Tak. Można włączyć ochrony przed atakami DDoS w sieci wirtualnej wdrożonym bramy aplikacji. To ustawienie zapewni, że usługa Azure DDoS Protection chroni także application gateway wirtualny adres IP (VIP).

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Jakie typy dzienników oferuje usługa Application Gateway?

Usługa Application Gateway zapewnia trzech dzienników: 

* **ApplicationGatewayAccessLog**: Dziennik dostępu zawiera każde żądanie przesyłany do usługi frontonu bramy aplikacji. Dane obejmują wewnątrz i na zewnątrz IP obiektu wywołującego, żądany adres URL, opóźnienie odpowiedzi, kod powrotny i bajtów. Dziennik dostępu są gromadzone co 300 sekund. Zawiera jeden rekord w bramie aplikacji.
* **ApplicationGatewayPerformanceLog**: Dziennik wydajności przechwytuje informacje o wydajności dla każdej bramy aplikacji. Informacje obejmują przepływność w bajtach, łączna liczba żądań obsługiwana liczba żądań zakończonych niepowodzeniem, a liczba wystąpień zdrowe i niezdrowe wewnętrznej bazy danych.
* **ApplicationGatewayFirewallLog**: Dla bram aplikacji, które można skonfigurować przy użyciu zapory aplikacji sieci Web dziennik zapory zawiera żądań, które są rejestrowane za pośrednictwem Tryb wykrywania lub tryb zapobiegania.

Aby uzyskać więcej informacji, zobacz [zaplecza kondycji, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak sprawdzić, jeśli Moja składowych puli zaplecza są w dobrej kondycji?

Sprawdź kondycję przy użyciu polecenia cmdlet programu PowerShell `Get-AzApplicationGatewayBackendHealth` lub w portalu. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Jakie są zasady przechowywania dla dzienników diagnostycznych?

Dzienniki diagnostyczne przepływ do konta magazynu klienta. Klientów można ustawić zasady przechowywania na podstawie jego preferencji. Dzienniki diagnostyczne mogą być również wysyłane do Centrum zdarzeń i dzienników usługi Azure Monitor. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak uzyskać dzienniki inspekcji w usłudze Application Gateway?

W portalu, w bloku menu bramy aplikacji wybierz **dziennika aktywności** dostępu do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Można ustawić alerty z usługą Application Gateway?

Tak. W usłudze Application Gateway alerty są konfigurowane na metryki. Aby uzyskać więcej informacji, zobacz [metryki bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) i [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak analizować statystyki ruchu w usłudze Application Gateway?

Można wyświetlać i analizować Dzienniki dostępu na kilka sposobów. Użyj dzienników usługi Azure Monitor, Excel, Power BI i tak dalej.

Można również użyć szablonu usługi Resource Manager, która jest instalowana i uruchamiana popularnej [GoAccess](https://goaccess.io/) dziennika analizatora dla bramy aplikacji dostęp do dzienników. GoAccess udostępnia cenne statystyki ruch HTTP, takie jak unikatowych osób odwiedzających, żądane pliki, hosty, systemy operacyjne, przeglądarki i kodów stanu HTTP. Aby uzyskać więcej informacji, w usłudze GitHub, zobacz [pliku Readme w folderu szablonu usługi Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Co może spowodować, że kondycja wewnętrznej bazy danych do zwrócenia z nieznanym stanem?

Zazwyczaj zobaczysz nieznany stan podczas dostępu do wewnętrznej bazy danych jest blokowany przez sieciową grupę zabezpieczeń (NSG), niestandardowe DNS lub routingu zdefiniowanego przez użytkownika (UDR) w podsieci bramy aplikacji. Aby uzyskać więcej informacji, zobacz [zaplecza kondycji, rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Application Gateway, zobacz [co to jest Azure Application Gateway?](overview.md).
