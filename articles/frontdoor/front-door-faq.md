---
title: Usługa frontonu platformy Azure — często zadawane pytania
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: dd315277e6e8f29a103760d605a7da4603190c20
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75908872"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Często zadawane pytania dotyczące usługi Azure Front drzwiczk

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące funkcji i funkcjonalności usługi Azure front-drzwi. Jeśli nie widzisz odpowiedzi na pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

1. Sekcja komentarzy w tym artykule.
2. [Usługa Azure front-drzwi UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Pomoc techniczna firmy Microsoft:** Aby utworzyć nowe żądanie obsługi, w Azure Portal na karcie **Pomoc** wybierz przycisk **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-front-door-service"></a>Co to jest usługa Azure Front Door Service?

Usługa frontonu platformy Azure to Application Delivery Network (wdrożenie) jako usługa oferująca różne możliwości równoważenia obciążenia warstwy 7 dla aplikacji. Zapewnia to przyspieszenie lokacji dynamicznej (DSA) wraz z globalnym równoważeniem obciążenia, które umożliwia przejście w tryb failover niemal w czasie rzeczywistym. Jest to wysoce dostępna i skalowalna usługa, która jest w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Jakie funkcje obsługuje usługa Azure Front drzwiczk?

Usługa frontonu platformy Azure obsługuje przyspieszenie witryn dynamicznych (DSA), odciążanie protokołu SSL i kompleksową obsługę protokołu SSL, zaporę aplikacji sieci Web, koligację sesji na podstawie plików cookie, routing oparty na ścieżkach URL, bezpłatne certyfikaty i zarządzanie wieloma domenami oraz inne osoby. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [Omówienie usługi Azure Front drzwiczk](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Jaka jest różnica między usługą Azure Front drzwiczk i platformą Azure Application Gateway?

Chociaż zarówno przód, jak i Application Gateway są modułami równoważenia obciążenia warstwy 7 (HTTP/HTTPS), podstawowa różnica polega na tym, że przód drzwi jest usługą globalną, a Application Gateway to usługa regionalna. Podczas gdy drzwi tylne mogą równoważyć obciążenie między różnymi jednostkami skalowania/klastrów/jednostkami sygnatur w różnych regionach, Application Gateway umożliwia Równoważenie obciążenia między maszynami wirtualnymi i kontenerami itp., które jest w jednostce skalowania.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kiedy należy wdrożyć Application Gateway za drzwiami przednimi?

Najważniejsze scenariusze, dla których należy używać Application Gateway za drzwiami przednimi:

- Z przodu drzwi można korzystać z równoważenia obciążenia opartego na ścieżkach tylko na poziomie globalnym, ale jeśli jedna z nich chce, aby równoważyć obciążenie ruchem jeszcze bardziej w ramach ich sieci wirtualnych (VNET), powinny używać Application Gateway.
- Ponieważ tylne drzwi nie działają na poziomie maszyny wirtualnej/kontenera, więc nie można ich opróżniać. Jednak Application Gateway umożliwia opróżnianie połączeń. 
- Za pomocą Application Gateway za AFD można odciążać 100% protokołu SSL i kierować tylko żądania HTTP w ramach ich sieci wirtualnych (VNET).
- Drzwiczki z przodu i Application Gateway obsługują koligacje sesji. Podczas gdy drzwi tylne mogą kierować ruch z sesji użytkownika do tego samego klastra lub zaplecza w danym regionie, Application Gateway może kierować ruch do tego samego serwera w klastrze.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Czy można wdrażać Azure Load Balancer za drzwiami przednimi?

Usługa frontonu platformy Azure potrzebuje publicznego adresu VIP lub publicznie dostępnej nazwy DNS, aby skierować ruch do programu. Wdrożenie Azure Load Balancer za drzwiami przednimi to typowy przypadek użycia.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Jakie protokoły obsługuje usługa Azure Front drzwiczk?

Usługa frontonu platformy Azure obsługuje protokół HTTP, HTTPS i HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Jak usługa Azure Front-drzwiczk obsługuje protokół HTTP/2?

Obsługa protokołu HTTP/2 jest dostępna dla klientów nawiązujących połączenie z usługą frontonu platformy Azure. Komunikacja do zaplecza w puli zaplecza jest za pośrednictwem protokołu HTTP/1.1. Obsługa protokołu HTTP/2 jest włączona domyślnie.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jakie zasoby są obecnie obsługiwane w ramach puli zaplecza?

Pule zaplecza mogą składać się z magazynu, aplikacji sieci Web, wystąpień Kubernetes lub dowolnej innej niestandardowej nazwy hosta z łącznością publiczną. Usługa frontonu platformy Azure wymaga, aby punkty końcowe były zdefiniowane za pośrednictwem publicznego adresu IP lub publicznie rozpoznawalnej nazwy hosta DNS. Elementy członkowskie pul zaplecza mogą znajdować się między strefami, regionami lub nawet poza platformą Azure, o ile mają łączność publiczną.

### <a name="what-regions-is-the-service-available-in"></a>Jakie regiony są dostępne w ramach usługi?

Usługa frontonu platformy Azure to usługa globalna i nie jest ona powiązana z żadnym określonym regionem świadczenia usługi Azure. Jedyną lokalizacją, którą należy określić podczas tworzenia drzwi czołowych, jest lokalizacja grupy zasobów, która zasadniczo określa, gdzie będą przechowywane metadane dla grupy zasobów. Sam zasób z drzwiami jest tworzony jako zasób globalny, a konfiguracja jest wdrażana globalnie we wszystkich punktach obecności. 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Jakie są lokalizacje POP usługi Azure front-drzwi?

Usługa frontonu platformy Azure ma tę samą listę lokalizacji POP (punkt obecności) jako Azure CDN od firmy Microsoft. Aby uzyskać pełną listę naszych punktów obecności, należy odnieść się [Azure CDN lokalizacji pop od firmy Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Czy usługa Azure front-drzwi ma dedykowane wdrożenie dla mojej aplikacji lub czy jest udostępniana przez klientów?

Usługa frontonu platformy Azure to globalnie dystrybuowana usługa wielodostępna. Dlatego infrastruktura dla drzwi przednich jest współdzielona przez wszystkich klientów. Jednak przez utworzenie profilu frontu drzwi należy zdefiniować konkretną konfigurację wymaganą dla aplikacji, a zmiany wprowadzone do przodu nie mają wpływu na inne konfiguracje drzwi z przodu.

### <a name="is-http-https-redirection-supported"></a>Czy obsługiwane jest przekierowanie HTTPS > HTTP?

Tak. W rzeczywistości usługa frontonu platformy Azure obsługuje przekierowania hosta, ścieżki i ciągu zapytania, jak również część przekierowania adresów URL. Dowiedz się więcej na temat [przekierowywania adresów URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>W jakiej kolejności są przetwarzane reguły routingu?

Trasy dla drzwi przednich nie są uporządkowane, a określona trasa jest wybierana na podstawie najlepszego dopasowania. Dowiedz się więcej o tym [, w jaki sposób przód jest zgodny z żądaniami do reguły routingu](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Jak mogę zablokować dostęp do mojego zaplecza tylko do drzwi platformy Azure z przodu?

Aby zablokować aplikację w celu akceptowania tylko ruchu pochodzącego z określonych drzwi, należy skonfigurować listy ACL protokołu IP dla zaplecza, a następnie ograniczyć zestaw akceptowanych wartości dla nagłówka "X-Forward-host" wysyłanego przez zewnętrzne drzwi platformy Azure. Poniższe kroki są szczegółowo opisane poniżej:

- Skonfiguruj ACLing IP dla zaplecza, aby akceptować ruch z przestrzeni adresów IP zaplecza platformy Azure i usług infrastruktury platformy Azure. Pracujemy nad integracją z [zakresem adresów IP platformy Azure i tagami usług](https://www.microsoft.com/download/details.aspx?id=56519) , ale teraz można odwoływać się do zakresów adresów IP w następujący sposób:
 
    - Przestrzeń adresów IP zaplecza **IPv4** z przodu: `147.243.0.0/16`
    - Przestrzeń adresów IP zaplecza **IPv6** z przodu: `2a01:111:2050::/44`
    - [Podstawowe usługi infrastruktury](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) platformy Azure za pomocą zwirtualizowanych adresów IP hosta: `168.63.129.16` i `169.254.169.254`

    > [!WARNING]
    > Przestrzeń adresów IP zaplecza z drzwiami może ulec zmianie później, jednak zapewnimy, że przed takim wdrożeniem będziemy zintegrowane z [zakresem adresów IP i tagami usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519). Zalecamy, aby zasubskrybować [zakresy adresów IP platformy Azure i Tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) dla wszelkich zmian lub aktualizacji.

-   Filtrowanie wartości dla przychodzącego nagłówka "**X-forwardd-Host**" wysyłanego przez przód drzwi. Jedyne dozwolone wartości nagłówka to wszystkie hosty frontonu, zgodnie z definicją w konfiguracji drzwi do przodu. W rzeczywistości jeszcze bardziej szczegółowo tylko nazwy hostów, dla których ma zostać zaakceptowany ruch, w tym konkretnym zaplecze.
    - Przykład: Załóżmy, że konfiguracja drzwi do przodu obejmuje następujące hosty frontonu _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _`api.contoso.com`_ (C) i _`notifications.contoso.com`_ (D). Załóżmy, że masz dwa punkty końcowe X i Y. 
    - Zaplecze X powinien przyjmować tylko ruch z nazw hostów a i B. zaplecze t może przyjmować ruch z, C i D.
    - Dlatego w zapleczu X należy akceptować tylko ruch, który ma nagłówek "**X-Forward-Host**" ustawiony na wartość _`contoso.azurefd.net`_ lub _`www.contoso.com`_ . W przypadku wszystkich innych elementów zaplecza X powinna odrzucać ruch.
    - Podobnie, w przypadku zaplecza t należy akceptować tylko ruch z nagłówkiem "**X-Forward-Host**" ustawionym na wartość _`contoso.azurefd.net`_ , _`api.contoso.com`_ lub _`notifications.contoso.com`_ . W przypadku wszystkich innych elementów zaplecza t powinna odrzucać ruch.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Czy adres IP emisji jest zmieniany w okresie istnienia moich zewnętrznych drzwi?

Adres IP usługi frontonu dla drzwi przednich nie powinien się zwykle zmieniać i może pozostawać statyczny w okresie istnienia drzwi z przodu. Nie ma jednak **żadnych gwarancji** dla tego samego. W adresie IP nie są wykonywane żadne bezpośrednie zależności.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Czy usługa frontonu platformy Azure obsługuje statyczne lub dedykowane adresy IP?

Nie, usługa frontonu platformy Azure obecnie nie obsługuje statycznych lub dedykowanych adresów IP dla frontonu. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Czy usługa frontonu platformy Azure obsługuje przekazanie x — w przypadku nagłówków?

Tak, usługa frontonu platformy Azure obsługuje nagłówki X-Forwarded-For, X-Forwarded-Host i X-Forwarded-proto. Dla elementu X-forwardd-for, jeśli nagłówek już istnieje, drzwi do przodu dołączają do niego adres IP gniazda klienta. W przeciwnym razie dodaje nagłówek z adresem IP gniazda klienta jako wartość. Dla X-forwardd-Host i X-Forwarded-proto wartość zostanie zastąpiona.

Dowiedz się więcej o [nagłówkach HTTP z obsługą przednią drzwi](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Jak długo trwa wdrażanie usługi Azure front-drzwi? Czy moje tylne drzwi nadal działają po aktualizacji?

Nowe tworzenie do przodu lub wszelkie aktualizacje istniejących przednich drzwi zajmują od 3 do 5 minut na wdrożenie globalne. Oznacza to, że od 3 do 5 minut konfiguracja drzwi do przodu zostanie wdrożona we wszystkich ogólnych punktach obecności.

Uwaga — Aktualizacje niestandardowego certyfikatu protokołu SSL trwają około 30 minut, aby można je było wdrożyć globalnie.

## <a name="configuration"></a>Konfigurowanie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Czy można zrównoważyć ruch z przodu platformy Azure lub kierowaniu ruchu w sieci wirtualnej?

Drzwi frontonu platformy Azure (AFD) wymagają publicznego adresu IP lub publicznie rozpoznawalnej nazwy DNS, aby kierować ruchem. Dlatego odpowiedź nie jest AFD bezpośrednio w ramach sieci wirtualnej, ale użycie Application Gateway lub Azure Load Balancer między nimi spowoduje rozwiązanie tego scenariusza.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Jakie są różne limity czasu i limity dla usługi Azure front-drzwi?

Dowiedz się więcej na temat wszystkich udokumentowanych [limitów czasu i limitów dla usługi Azure front-drzwi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Wydajność

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Jak usługa Azure front-drzwi obsługuje wysoką dostępność i skalowalność?

Usługa frontonu platformy Azure to globalnie dystrybuowana platforma z wieloma dzierżawcami z dużą ilością pojemności, która zaspokaja potrzeby związane z skalowalnością aplikacji. Od brzegu sieci globalnej firmy Microsoft drzwi do przodu udostępniają globalną funkcję równoważenia obciążenia, która umożliwia przechodzenie w tryb failover całej aplikacji lub nawet poszczególnych mikrousług między regionami lub różnymi chmurami.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Jakie wersje protokołu TLS są obsługiwane przez usługę Azure front-drzwi?

Wszystkie profile frontonu utworzone po wrzesień 2019 używają protokołu TLS 1,2 jako domyślnego minimum.

Drzwi z przodu obsługują protokół TLS w wersji 1,0, 1,1 i 1,2. Protokół TLS 1,3 nie jest jeszcze obsługiwany.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Jakie certyfikaty są obsługiwane w usłudze Azure front-drzwi?

Aby włączyć protokół HTTPS w celu bezpiecznego dostarczania zawartości w domenie niestandardowej z przednim Drzwiem, możesz wybrać użycie certyfikatu zarządzanego przez usługę frontonu platformy Azure lub użyć własnego certyfikatu.
Opcja zarządzana przed drzwiami udostępnia standardowy certyfikat SSL za pośrednictwem DigiCert i jest przechowywany w Key Vaultch przednich drzwi. Jeśli zdecydujesz się na użycie własnego certyfikatu, możesz dołączyć certyfikat z obsługiwanego urzędu certyfikacji, który może być standardowym protokołem SSL, rozszerzonym certyfikatem weryfikacji, a nawet certyfikatem wieloznacznym. Certyfikaty z podpisem własnym nie są obsługiwane. Dowiedz się [, jak włączyć protokół HTTPS dla domeny niestandardowej](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Czy przód drzwi obsługuje autorotację certyfikatów?

W przypadku opcji certyfikat zarządzany przed drzwiami certyfikaty są obracane na podstawie drzwi. Jeśli używasz certyfikatu zarządzanego z drzwiami i zobaczysz, że data wygaśnięcia certyfikatu jest mniejsza niż 60 dni, zapoznaj się z biletem pomocy technicznej.
</br>W przypadku własnego niestandardowego certyfikatu protokołu SSL autorotacja nie jest obsługiwana. Podobnie jak w przypadku, gdy został on skonfigurowany po raz pierwszy dla danej domeny niestandardowej, należy wskazać przód drzwi do odpowiedniej wersji certyfikatu w Key Vault i upewnić się, że jednostka usługi dla drzwi zewnętrznych nadal ma dostęp do Key Vault. Ta zaktualizowana operacja wdrażania certyfikatu przez drzwi tylne jest niepodzielna i nie powoduje żadnego wpływu na produkcję, gdy nazwa podmiotu lub sieć SAN dla certyfikatu nie ulegają zmianie.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Jakie są bieżące mechanizmy szyfrowania obsługiwane przez usługę frontonu platformy Azure?

Oto bieżące mechanizmy szyfrowania obsługiwane przez usługę frontonu platformy Azure:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Czy usługa frontonu platformy Azure obsługuje również ponowne szyfrowanie ruchu do zaplecza?

Tak, usługa frontonu platformy Azure obsługuje odciążanie protokołu SSL i kompleksową obsługę protokołu SSL, co spowoduje ponowne zaszyfrowanie ruchu do zaplecza. W rzeczywistości, ponieważ połączenia z zapleczem odbywają się za pośrednictwem publicznego adresu IP, zaleca się skonfigurowanie drzwi frontonu do korzystania z protokołu HTTPS jako protokół przekazywania dalej.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Czy można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL?

Minimalną wersję protokołu TLS można skonfigurować w ramach frontonu platformy Azure za pośrednictwem [interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Obecnie można wybrać między 1,0 a 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Czy można skonfigurować drzwiczki frontonu w celu obsługi tylko określonych mechanizmów szyfrowania?

Nie, Konfigurowanie drzwi zewnętrznych dla określonych mechanizmów szyfrowania nie jest obsługiwane. 

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Jakie typy metryk i dzienników są dostępne w usłudze Azure front-drzwi?

Aby uzyskać informacje na temat dzienników i innych funkcji diagnostycznych, zobacz [monitorowanie metryk i dzienników dla drzwi zewnętrznych](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Co to są zasady przechowywania w dziennikach diagnostycznych?

Dzienniki diagnostyczne przepływają do konta magazynu klientów i klienci mogą ustawić zasady przechowywania na podstawie ich preferencji. Dzienniki diagnostyczne można także wysyłać do centrum zdarzeń lub dzienników Azure Monitor. Aby uzyskać więcej informacji, zobacz [Diagnostyka usługi Azure front-drzwi](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Jak mogę pobrać dzienników inspekcji dla usługi frontonu platformy Azure?

Dzienniki inspekcji są dostępne dla usługi Azure front-drzwi. W portalu kliknij pozycję **Dziennik aktywności** w bloku menu swojego drzwi czołowych, aby uzyskać dostęp do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Czy mogę ustawić alerty za pomocą usługi Azure front-drzwi?

Tak, usługa frontonu platformy Azure obsługuje alerty. Alerty są konfigurowane na metrykach. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
