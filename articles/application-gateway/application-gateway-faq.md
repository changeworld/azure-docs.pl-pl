---
title: Często zadawane pytania dotyczące usługi Azure Application Gateway
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: c40f372d3574f940e475a6626f998adae37a6d61
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851153"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Często zadawane pytania dotyczące usługi Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Ogólne

### <a name="what-is-application-gateway"></a>Co to jest usługa Application Gateway?

Usługa Azure Application Gateway to kontroler dostarczania aplikacji (ADC) as a service, oferujących różne warstwy 7 możliwości równoważenia obciążenia dla aplikacji. Oferuje wysoko dostępne i skalowalne usługi, która jest w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-application-gateway-support"></a>Jakie funkcje obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje Skalowanie automatyczne, odciążanie protokołu SSL i kompleksowej usługi SSL, zapory aplikacji sieci Web, koligacja sesji na podstawie plików cookie, adres url opartego na ścieżkach routingu, wielu lokacjach hostingu i inne. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [wprowadzenie do usługi Application Gateway](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Jaka jest różnica między bramą aplikacji i modułu równoważenia obciążenia platformy Azure?

Application Gateway to moduł równoważenia obciążenia warstwy 7, co oznacza, że działa z tylko ruch w sieci web (HTTP/HTTPS/WebSocket/protokołu HTTP/2). Obsługuje funkcje takie jak kończenia żądań SSL, koligacja sesji na podstawie pliku cookie i działanie okrężne do Równoważenie obciążenia ruchu. Załaduj moduł równoważenia obciążenia równoważy ruch w warstwie 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Jakie protokoły obsługuje usługa Application Gateway?

Usługa Application Gateway obsługuje HTTP, HTTPS i HTTP/2 oraz protokołu WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Jak usługa Application Gateway obsługuje protokołu HTTP/2

Zobacz [Obsługa protokołu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) Aby dowiedzieć się, jak usługa Application gateway obsługuje protokół HTTP/2.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jakie zasoby są obecnie obsługiwane w ramach puli wewnętrznej bazy danych?

Zobacz [obsługiwanych zasobów zaplecza](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) Aby dowiedzieć się, jakie zasoby są obsługiwane przez usługę Application gateway.

### <a name="what-regions-is-the-service-available-in"></a>Jakie regiony usługa jest dostępna w?

Usługa Application Gateway jest dostępna we wszystkich regionach, globalnej platformy Azure. Jest również dostępna w [Azure China 21Vianet](https://www.azure.cn/) i [Azure dla instytucji rządowych](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Jest to dedykowane wdrożenia dla mojej subskrypcji lub jest udostępniana wielu odbiorców?

Usługa Application Gateway to dedykowane wdrożenie w sieci wirtualnej.

### <a name="is-http-https-redirection-supported"></a>HTTP jest -> przekierowania protokołu HTTPS, obsługiwane?

Przekierowywanie jest obsługiwane. Zobacz [Application Gateway redirect overview](application-gateway-redirect-overview.md) Aby dowiedzieć się więcej.

### <a name="in-what-order-are-listeners-processed"></a>W jakiej kolejności odbiorników przetwarzania?

Zobacz [kolejność przetwarzania odbiorników](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Gdzie znaleźć adres IP bramy aplikacji i DNS

Korzystając z publicznego adresu IP jako punkt końcowy, te informacje można znaleźć na zasób publicznego adresu IP lub na stronie Przegląd usługi application gateway w portalu. Dla wewnętrznych adresów IP to można znaleźć na stronie Przegląd.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Co to jest Keep-Alive limitu czasu i ustawienie limitu czasu bezczynności protokołu TCP dla usługi Application Gateway?

Keep-Alive limit czasu w ramach jednostki SKU v1 jest 120 s. Keep-Alive upłynął limit czasu dla jednostki SKU w wersji 2 jest 75 s. limitu czasu bezczynności protokołu TCP jest 4-minutowy domyślne we frontonie VIP bramy aplikacji.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Nazwa DNS lub adresu IP zmienia się w okresie istnienia usługi Application Gateway?

Wirtualne adresy IP można zmienić bramy application gateway jest zatrzymana i uruchomiona. Nazwa DNS skojarzone z usługą application gateway nie zmienia się z cyklem bramy. Z tego powodu zaleca się użycie aliasów CNAME i wskaż adresu DNS bramy aplikacji.

### <a name="does-application-gateway-support-static-ip"></a>Usługa Application Gateway obsługuje statyczny adres IP?

Tak, usługa Application Gateway jednostkę SKU v2 obsługuje statyczne publiczne adresy IP. Jednostka SKU v1 obsługuje statyczne wewnętrzne adresy IP.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Usługa Application Gateway obsługuje wiele publicznych adresów IP w ramach bramy?

Tylko jeden publiczny adres IP jest obsługiwany w bramie aplikacji.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Jak duże powinny utworzyć mojej podsieci dla bramy Application Gateway?

Zobacz [zagadnienia rozmiar podsieci bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) zrozumienie rozmiar podsieci, wymagane dla danego wdrożenia.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>PYTANIE: Do jednej podsieci można wdrażać więcej niż jednego zasobu usługi Application Gateway?

Tak, oprócz posiadanie wielu wystąpień danego wdrożenia bramy Application Gateway, można udostępnić innym unikatowy zasobu usługi Application Gateway do istniejącej podsieci, który zawiera różne zasobu usługi Application Gateway.

Mieszanie Standard_v2 i standardowa usługa Application Gateway w tej samej podsieci nie jest obsługiwane.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Usługa Application Gateway obsługuje nagłówków x-forwarded dla?

Tak. Zobacz [modyfikacje, aby zażądać](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) Aby dowiedzieć się więcej na temat nagłówków x-forwarded dla obsługiwanych przez usługę Application Gateway.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Jak długo trwa wdrażanie bramy aplikacji? Moje bramy Application Gateway nadal działa podczas aktualizowania?

Nowych wdrożeń jednostek SKU v1 Application Gateway może potrwać do 20 minut do aprowizowania. Zmiany rozmiaru/liczba wystąpień nie są naturalnymi, a brama pozostaje aktywna, w tym czasie.

Jednostki SKU v2 wdrożenia może potrwać około pięciu lub sześciu minut aprowizację.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Można użyć programu Exchange server jako wewnętrznej bazy danych z usługą Application Gateway?

Nie, usługa Application Gateway nie obsługuje protokołów poczty e-mail, np. SMTP, IMAP i POP3. 

## <a name="performance"></a>Wydajność

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Jak usługa Application Gateway obsługuje wysoką dostępność i skalowalność?

Usługa Application Gateway jednostkę SKU v1 obsługuje scenariuszy wysokiej dostępności, gdy masz co najmniej dwóch wystąpieniach wdrożonych. Platforma Azure dystrybuuje te wystąpienia między domenami aktualizacji i błędów, aby upewnić się, że wszystkie wystąpienia nie zakończy się niepowodzeniem w tym samym czasie. Jednostka SKU v1 obsługuje skalowalności, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

Jednostka SKU v2 automatycznie sprawdza, czy nowe wystąpienia są dystrybuowane w domenach błędów i domenach aktualizacji. Jeśli zostanie wybrana opcja nadmiarowości strefy, najnowszych wystąpień rozprzestrzeniają się również w strefach dostępności do zaoferowania odporności strefowej awarii.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Jak osiągnąć scenariusza odzyskiwania po awarii w centrach danych z usługą Application Gateway?

Klientów można użyć usługi Traffic Manager do rozdzielenia ruchu między wiele bram aplikacji w różnych centrach danych.

### <a name="is-autoscaling-supported"></a>Automatyczne skalowanie jest obsługiwana?

Tak, usługa Application Gateway jednostkę SKU v2 obsługuje funkcję skalowania automatycznego. Aby uzyskać więcej informacji, zobacz [Skalowanie automatyczne i strefowo nadmiarowe Application Gateway (publiczna wersja zapoznawcza)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Skalowanie ręczne odbywa się w górę/dół przestój Przyczyna?

Brak przestojów. Wystąpienia są rozproszone między domeny błędów i domenach uaktualniania.

### <a name="does-application-gateway-support-connection-draining"></a>Usługa Application Gateway obsługuje opróżniania połączenia?

Tak. Można skonfigurować połączenia opróżniania, aby zmienić członków w ramach puli zaplecza, bez przerw w działaniu. Dzięki temu istniejące połączenia przejść do wysłania do ich poprzedniego miejsca docelowego, dopóki nie upłynie limit czasu można skonfigurować albo to połączenie jest zamykane. Tylko opróżniania połączenia czeka, aż bieżące aktywne połączenia do ukończenia. Usługa Application Gateway nie ma informacji o stanie sesji w aplikacji.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Czy mogę zmienić rozmiar wystąpienia średnie dużych bez zakłóceń?

Tak, platforma Azure rozdzieli wystąpień w domenach aktualizacji i błędów, aby upewnić się, że wszystkie wystąpienia nie zakończy się niepowodzeniem w tym samym czasie. Usługa Application Gateway obsługuje skalowanie, dodając wiele wystąpień tej samej bramy, aby udostępnić obciążenia.

## <a name="configuration"></a>Konfigurowanie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Usługa Application Gateway zawsze wdrożono w sieci wirtualnej?

Tak, usługa Application Gateway jest zawsze wdrażane w podsieci sieci wirtualnej. Ta podsieć może zawierać tylko bramy Application Gateway. Zobacz [wirtualnej wymagania dotyczące sieci i podsieci](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) zrozumienie zagadnień podsieć dla bramy Application Gateway.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Może Application Gateway komunikować się z wystąpieniami poza siecią wirtualną, w którym się lub poza nim subskrypcję, w której znajduje się w?

Usługa Application Gateway mogą komunikować się z wystąpieniami poza siecią wirtualną, która znajduje się w lub na zewnątrz subskrypcji, do której się znajduje, tak długo, jak istnieje łączność IP. Jeśli planujesz używać wewnętrznych adresów IP jako składowych puli zaplecza, a następnie wymaga [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Czy mogę wdrożyć niczego więcej w podsieci bramy aplikacji?

Nie, ale można wdrożyć inne bramy aplikacji w podsieci.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Czy sieciowe grupy zabezpieczeń są obsługiwane w podsieci bramy aplikacji?

Zobacz [ograniczenia sieciowe grupy zabezpieczeń w podsieci bramy Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet) Dowiedz się więcej na temat sieciowych grup zabezpieczeń obsługiwane w podsieci bramy aplikacji.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Czy trasy zdefiniowane przez użytkownika są obsługiwane w podsieci bramy aplikacji?

Zobacz [ograniczenia trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, obsługiwane w podsieci bramy aplikacji.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Jakie są limity dla usługi Application Gateway? Czy można zwiększyć te limity?

Zobacz [limitów usługi Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits) wyświetlić limitów.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Do czego służy usługa Application Gateway dla wewnętrznych i zewnętrznych ruchu jednocześnie?

Tak, usługa Application Gateway obsługuje jeden wewnętrzny adres IP i jeden zewnętrzny adres IP dla bramy application gateway.

### <a name="is-vnet-peering-supported"></a>Jest komunikacja równorzędna sieci wirtualnych obsługiwane?

Tak, komunikacja równorzędna sieci wirtualnych jest obsługiwana i jest korzystne dla Równoważenie obciążenia ruchu w innych sieciach wirtualnych.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Może mi odpowiedzieć na lokalnych serwerach łączenia tuneli ExpressRoute lub sieci VPN?

Tak, tak długo, jak ruch jest dozwolony.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Może mieć jednej puli wewnętrznej bazy danych obsługująca wiele aplikacji na różnych portów?

Architektura Micro usługi jest obsługiwany. Będziesz potrzebować wiele ustawień protokołu http skonfigurowany do sondowania w różnych portów.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Niestandardowe sondy obsługują symbole wieloznaczne/wyrażeń regularnych na dane odpowiedzi?

Niestandardowe sondy nie obsługują symboli wieloznacznych lub wyrażenie regularne na dane odpowiedzi.

### <a name="how-are-rules-processed"></a>Jak są przetwarzane reguły

Zobacz [kolejność przetwarzania reguły](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) zrozumienie, jak reguły routingu są procesy w usłudze Application Gateway.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Co to oznaczającego pole Host dla sond niestandardowych?

Pole host Określa nazwę aby wysłać sondy. Dotyczy tylko wtedy, gdy połączenia obejmujące wiele lokacji jest skonfigurowany w usłudze Application Gateway, w przeciwnym razie użyj "127.0.0.1". Ta wartość jest inna niż nazwa hosta maszyny Wirtualnej i jest w formacie \<protokołu\>://\<hosta\>:\<portu\>\<ścieżki\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Czy można umieścić na liście dozwolonych bramy aplikacji dostęp do kilku źródłowych adresów IP?

Tak. Zobacz [ograniczyć dostęp do określonych źródłowych adresów IP](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) zrozumienie, jak upewnić się, że tylko na liście dozwolonych źródłowe adresy IP będą mogli Application Gateway.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Można użyć tego samego portu dla prywatnych i publicznych detektorów mające połączenie z Internetem?

Nie. Takie rozwiązanie nie jest obsługiwane.

## <a name="configuration---ssl"></a>Konfiguracja — SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Jakie certyfikaty są obsługiwane w usłudze Application Gateway?

Certyfikaty z podpisem własnym, certyfikaty urzędu certyfikacji i Weryfikacją certyfikatów oraz certyfikatów z symbolami wieloznacznymi są obsługiwane.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Jakie są bieżące mechanizmów szyfrowania obsługiwanych przez usługę Application Gateway?

Poniżej przedstawiono bieżący mechanizmów szyfrowania obsługiwanych przez usługę Application Gateway. Zobacz [Konfigurowanie protokołu SSL, wersje zasad i mechanizmów szyfrowania w usłudze Application Gateway](application-gateway-configure-ssl-policy-powershell.md) dowiesz się, jak dostosować opcje protokołu SSL.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Usługa Application Gateway również obsługuje ponownie szyfrować ruchu do wewnętrznej bazy danych?

Tak, usługa Application Gateway obsługuje odciążanie protokołu SSL i kompleksowej usługi SSL, który ponownie szyfruje ruch do wewnętrznej bazy danych.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL?

Tak, można skonfigurować bramę aplikacji do odmowy TLS1.0 TLS1.1 i TLS1.2. Protokół SSL 2.0 i 3.0 już są domyślnie wyłączone i nie można konfigurować.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Mechanizmy szyfrowania i kolejności zasad można skonfigurować?

Tak, [konfiguracji mechanizmów szyfrowania](application-gateway-ssl-policy-overview.md) jest obsługiwana. Podczas definiowania zasad niestandardowych, należy włączyć co najmniej jeden z następujących mechanizmów szyfrowania. Usługa Application gateway używa SHA256 do wewnętrznej bazy danych zarządzania.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Jak wiele certyfikatów SSL są obsługiwane?

Maksymalnie 100 SSL certyfikaty są obsługiwane.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Jak wiele certyfikatów uwierzytelniania dla wewnętrznej bazy danych ponownie szyfrować są obsługiwane?

Maksymalnie 10 uwierzytelniania certyfikatów są obsługiwane z domyślną 5.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Czy usługa Application Gateway można zintegrować z usługą Azure Key Vault natywnie?

Nie, nie jest on zintegrowany z usługą Azure Key Vault.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Jak skonfigurować odbiorników HTTPS dla witryny .com i .net? 

Dla wielu domen routingu opartego na (oparte na hoście), można utworzyć odbiorników obejmujących wiele lokacji, wybierz opcję HTTPS protokół w konfiguracji odbiornika i skojarzyć odbiorniki za pomocą reguł routingu. Aby uzyskać więcej informacji, zobacz [hostowanie wielu witryn z usługą Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Konfiguracja — Zapora aplikacji sieci Web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>Jednostka SKU zapory aplikacji sieci Web oferuje wszystkie funkcje dostępne w wersji Standard?

Tak, zapory aplikacji sieci Web obsługuje wszystkie funkcje standardowej jednostki SKU.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Co to jest wersja CRS Application Gateway obsługuje?

Usługa Application Gateway obsługuje CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Jak monitorować zapory aplikacji sieci Web?

Zapora aplikacji sieci Web jest monitorowany za pośrednictwem rejestrowania diagnostycznego, więcej informacji na temat rejestrowania diagnostycznego można znaleźć w folderze [rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>Tryb wykrywania blokować ruch?

Nie, Tryb wykrywania dzienniki ruchu, która wywołała regułę zapory aplikacji sieci Web.

### <a name="can-i-customize-waf-rules"></a>Czy można dostosować reguły zapory aplikacji sieci Web?

Tak, reguły zapory aplikacji sieci Web są możliwe do dostosowania. Aby uzyskać więcej informacji, zobacz [zapory aplikacji sieci Web dostosowywanie grup reguł i reguł](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Jakie zasady są obecnie dostępne

Zapora aplikacji sieci Web obsługuje obecnie CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) i [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), które zapewniają linii bazowej zabezpieczeń przed większością z 10 najważniejszych luk w zabezpieczeniach, zidentyfikowane przez Otwórz sieci Web aplikacji Security Project (OWASP) znaleźć tutaj [ Luk w zabezpieczeniach OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Ochrona przed atakami polegającymi na iniekcji SQL

* Ochrona przed atakami z użyciem skryptów wykorzystywanych w obrębie wielu witryn

* Częste ataki w ramach sieci Web polegające na iniekcji poleceń, przemycaniu żądań HTTP, rozdzielaniu odpowiedzi HTTP i zdalnym dołączaniu plików

* Ochrona przed naruszeniami protokołu HTTP

* Ochrona przed nieprawidłowościami protokołu HTTP, takimi jak brakujące powiązania agenta i użytkownika hosta oraz akceptowanie nagłówków

* Zapobieganie atakom z użyciem robotów, przeszukiwarek i skanerów

* Wykrywanie typowych błędów konfiguracji aplikacji (czyli Apache, usługi IIS itp.)

### <a name="does-waf-also-support-ddos-prevention"></a>Zapora aplikacji sieci Web również obsługuje zapobiegania przed atakami DDoS?

Tak. Można włączyć ochrony przed atakami DDos w sieci wirtualnej wdrożonym bramy aplikacji. Gwarantuje to, że brama aplikacji, który adres VIP jest również chroniony za pomocą usługi Azure DDos Protection.

## <a name="diagnostics-and-logging"></a>Rejestrowanie i Diagnostyka

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Jakie rodzaje dzienniki są dostępne z usługą Application Gateway?

Istnieją trzy dzienniki usługi Application Gateway. Aby uzyskać więcej informacji na temat tych dzienników i innych funkcji diagnostycznych, zobacz [zaplecza kondycji, dzienniki diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** — dziennik dostępu zawiera każde żądanie przesyłany do usługi frontonu bramy aplikacji. Dane obejmują IP obiektu wywołującego, adresu URL żądanego, opóźnienie odpowiedzi, pomniejszać i zwracają kod, w bajtach. Dziennik dostępu są gromadzone co 300 sekund. Ten dziennik zawiera jeden rekord dla każdego wystąpienia bramy aplikacji.
* **ApplicationGatewayPerformanceLog** — dziennik wydajności przechwytuje informacje dotyczące wydajności na na podstawie wystąpień w tym całkowity żądania obsłużonego, przepływność w bajtach, łączna liczba żądań one obsługiwane, a liczba nieudanych żądań zdrowe i niezdrowe zaplecza Liczba wystąpień.
* **ApplicationGatewayFirewallLog** — dziennik zapory zawiera żądań, które są rejestrowane w trybie wykrywania i zapobiegania usługi application gateway, który jest skonfigurowany przy użyciu zapory aplikacji sieci web.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Jak sprawdzić, jeśli Moja składowych puli zaplecza są w dobrej kondycji?

Można użyć polecenia cmdlet programu PowerShell `Get-AzApplicationGatewayBackendHealth` lub sprawdzać kondycję, za pośrednictwem portalu, odwiedzając [diagnostyki bramy aplikacji](application-gateway-diagnostics.md)

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jakie są zasady przechowywania dzienników diagnostycznych?

Dzienniki diagnostyczne usługi flow do konta magazynu klientów i klienci mogą ustawić zasady przechowywania na podstawie jego preferencji. Dzienniki diagnostyczne mogą być również wysyłane do Centrum zdarzeń lub usługi Azure Monitor dzienniki. Zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md) Aby uzyskać więcej informacji.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Jak uzyskać dzienniki inspekcji w usłudze Application Gateway?

Dzienniki inspekcji są dostępne w usłudze Application Gateway. W portalu, kliknij przycisk **dziennika aktywności** w bloku menu bramy aplikacji dostępu do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Można ustawić alerty z usługą Application Gateway?

Tak, usługa Application Gateway obsługuje alerty. Alerty są konfigurowane na metryki. Zobacz [metryki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) Aby dowiedzieć się więcej o metrykach Application Gateway. Aby dowiedzieć się więcej na temat alertów, zobacz [otrzymywanie powiadomień o alertach](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Jak analizować statystyki ruchu w usłudze Application Gateway?

Można wyświetlać i analizować Dzienniki dostępu przy użyciu kilku mechanizmów, takich jak dzienniki usługi Azure Monitor, Excel, Power BI itp.

Opublikowane dotychczas również szablon usługi Resource Manager, która jest instalowana i uruchamiana popularnej [GoAccess](https://goaccess.io/) dziennik analizatora, aby uzyskać dostęp do dzienników usługi Application Gateway. GoAccess udostępnia cenne statystyki ruch HTTP, takie jak unikatowych osób odwiedzających, żądane pliki, hosty, systemy operacyjne, przeglądarki, kody stanu HTTP i nie tylko. Aby uzyskać więcej informacji, zobacz [pliku Readme w folderu szablonu usługi Resource Manager w usłudze GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>Kondycja wewnętrznej bazy danych zwraca stan nieznany w poznaniu przyczyny tego stanu?

Najbardziej typową przyczyną jest dostęp do wewnętrznej bazy danych jest blokowany przez sieciową grupę zabezpieczeń, niestandardowe DNS lub masz trasy zdefiniowanej przez użytkownika w podsieci bramy aplikacji. Zobacz [zaplecza kondycji, rejestrowanie diagnostyczne i metryki dla usługi Application Gateway](application-gateway-diagnostics.md) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat bramy Application Gateway, zobacz [co to jest Azure Application Gateway?](overview.md)
