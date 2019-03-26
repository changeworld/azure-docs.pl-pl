---
title: Usługa Azure drzwiami frontowymi — często zadawane pytania dotyczące wejściu | Dokumentacja firmy Microsoft
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Service drzwi
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
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407755"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Często zadawane pytania dotyczące usługi Azure Service drzwi

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące funkcje i możliwości usługi drzwiami frontowymi platformy Azure. Jeśli nie widzisz odpowiedź na Twoje pytanie, możesz skontaktować się ze nam za pośrednictwem poniższych kanałów (w kolejności szybciej):

1. Komentarze części tego artykułu.
2. [Azure drzwiami frontowymi usługi UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Pomoc techniczna firmy Microsoft:** Aby utworzyć nowe żądanie obsługi w witrynie Azure portal na **pomocy** zaznacz **Pomoc i obsługa techniczna** przycisk, a następnie wybierz **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-front-door-service"></a>Co to jest usługa Azure Front Door Service?

Usługa drzwiami frontowymi jest sieć dostarczania aplikacji sieci (ADN) jako usługa, różne warstwy 7 możliwości równoważenia obciążenia dla aplikacji. Zapewnia przyspieszanie witryn dynamicznych (DSA) wraz z równoważenia obciążenia globalne przy użyciu niemal w czasie rzeczywistym trybu failover. Jest wysoko dostępna i skalowalna usługa, która jest w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Jakie funkcje obsługuje usługę drzwiami frontowymi Azure?

Usługa Azure drzwiami frontowymi obsługuje przyspieszanie witryn dynamicznych (DSA), odciążanie protokołu SSL i kompleksowej usługi SSL, zapory aplikacji sieci Web, koligacja sesji na podstawie pliku cookie, routing oparty na ścieżkach url, bezpłatne certyfikaty i zarządzanie wieloma domeny i inne. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [Omówienie usługi Azure Service drzwiami frontowymi](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Jaka jest różnica między usługą drzwiami frontowymi platformy Azure i usługi Azure Application Gateway?

Gdy zarówno drzwiami frontowymi, jak i bramy aplikacji są warstwy 7 (HTTP/HTTPS) usługi równoważenia obciążenia, główną różnicą jest drzwiami frontowymi globalną usługę Application Gateway jest to usługa regionalna. Chociaż drzwiami frontowymi będzie mogła równoważyć obciążenie między jednostek skalowania różne jednostki/klastrów/sygnatury w regionach, usługa Application Gateway umożliwia równoważenia obciążenia między swoje maszyny wirtualne/kontenery itp., które znajduje się w jednostce skalowania.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Gdy należy wdrożyć bramę aplikacji za wejściu?

Kluczowe scenariusze, dlaczego jeden należy używać bramy Application Gateway za wejściu są następujące:

- Drzwi wejściowe mogą wykonywać opartego na ścieżkach równoważenia tylko na poziomie globalnym, ale jeśli jeden chce, aby równoważyć obciążenie jeszcze bardziej w ramach ich sieci wirtualnej (VNET), a następnie powinny używać bramy Application Gateway.
- Ponieważ drzwiami frontowymi nie działa na poziomie maszyny Wirtualnej/kontenera, więc nie opróżniania połączenia. Jednak usługa Application Gateway umożliwia opróżniania połączenia. 
- Przy użyciu bramy aplikacji za zaporą AFD jeden osiągnięcia 100% odciążania protokołu SSL i trasy tylko żądania HTTP w ramach ich sieci wirtualnej (VNET).
- Drzwiami frontowymi i Application Gateway obsługuje koligację sesji. Gdy drzwiami frontowymi można kierować dalszy ruch z sesji użytkownika na tym samym klastrze lub wewnętrznej bazy danych w danym regionie, usługa Application Gateway można bezpośrednio affinitize ruch do tego samego serwera w klastrze.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Możemy wdrożyć usługi Azure Load Balancer za wejściu

Usługa drzwiami frontowymi musi kierować ruch do publicznych adresów VIP lub publicznej nazwy DNS. Wdrażanie usługi Azure Load Balancer za wejściu jest typowy przypadek użycia.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Jakie protokoły obsługuje usługę drzwiami frontowymi Azure?

Usługa Azure drzwiami frontowymi obsługuje HTTP, HTTPS i HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Jak usługa Azure Service drzwiami frontowymi obsługuje protokołu HTTP/2

Obsługa protokołu HTTP/2 jest dostępna dla klientów łączących się z usługi Azure Service drzwiami frontowymi tylko. Komunikację z usługą zaplecza w puli zaplecza jest za pośrednictwem protokołu HTTP/1.1. Obsługa protokołu HTTP/2 jest domyślnie włączona.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jakie zasoby są obecnie obsługiwane w ramach puli wewnętrznej bazy danych?

Pule zaplecza może składać się z magazynu, aplikacji sieci Web, wystąpień Kubernetes lub innych niestandardowych hostname, zawierającej łączności publicznej. Usługa drzwiami frontowymi wymaga zdefiniowania zaplecza za pośrednictwem publicznego adresu IP lub rozpoznania publicznie nazwy hosta DNS. Elementami członkowskimi pul zaplecza może być poszczególnych strefach, regionów, lub nawet spoza platformy Azure, tak długo, jak długo mają łączności publicznej.

### <a name="what-regions-is-the-service-available-in"></a>Jakie regiony usługa jest dostępna w?

Usługa Azure drzwiami frontowymi jest globalną usługę i nie jest związany z dowolnego regionu systemu Azure określone. Jedyną lokalizacją, w których należy określić podczas tworzenia drzwiami frontowymi jest lokalizacja grupy zasobów, która jest po prostu określenie, gdzie będą przechowywane metadane dla grupy zasobów. Samego zasobu drzwiami frontowymi jest tworzony jako globalny zasób i konfiguracji jest wdrażany globalnie do wszystkich lokalizacji POP (punktu obecność). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Co to są lokalizacje POP usługi Azure Service drzwiami frontowymi?

Usługa drzwiami frontowymi ma tę samą listę lokalizacji punktu obecności (punktu obecność) jako usługę Azure CDN, od firmy Microsoft. Pełna lista naszych punktów obecności uprzejmie można znaleźć [lokalizacje POP sieci CDN Azure firmy Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>To usługa drzwiami frontowymi platformy Azure dedykowanej wdrożenia dla mojej aplikacji lub jest udostępniana wielu odbiorców?

Usługa Azure Service drzwiami frontowymi to globalnie dystrybuowana usługa dla wielu dzierżawców. Tak infrastruktura drzwiami frontowymi jest udostępniony w swoim klientom. Jednakże, tworząc drzwiami frontowymi, należy zdefiniować określonej konfiguracji wymaganej dla aplikacji i 

### <a name="is-http-https-redirection-supported"></a>HTTP jest -> przekierowania protokołu HTTPS, obsługiwane?

Drzwi nie obsługuje obecnie adresu URL przekierowania.

### <a name="in-what-order-are-routing-rules-processed"></a>Kolejność przetwarzania reguły routingu są?

Trasy dla usługi drzwi wejściowe nie są uporządkowane i określoną trasę jest wybrane w oparciu o najlepsze dopasowanie. Dowiedz się więcej o [jak drzwiami frontowymi pasuje do żądania do reguły routingu](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Jak zablokować dostęp do mojej wewnętrznej bazy danych do tylko usługi Azure Service drzwi

Można skonfigurować ACLing adresów IP dla zaplecza do akceptowania ruchu z usługi drzwiami frontowymi platformy Azure. Można ograniczyć do akceptowania połączeń przychodzących, tylko z obszaru adresów IP zaplecza usługi Azure Service drzwiami frontowymi aplikacji. Pracujemy nad do integracji z usługą [zakresów adresów IP platformy Azure i tagi usługi](https://www.microsoft.com/download/details.aspx?id=56519) jednak może teraz odwoływać się zakresy adresów IP, tak jak pokazano poniżej:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Nasze przestrzeni adresów IP wewnętrznej bazy danych może zmienić później, jednak firma Microsoft zapewni, że przed tak się stanie, że firma Microsoft będzie zostały zintegrowane z usługą [zakresów adresów IP platformy Azure i tagami usługi](https://www.microsoft.com/download/details.aspx?id=56519). Zaleca się, że subskrybujesz [zakresów adresów IP platformy Azure i tagami usługi](https://www.microsoft.com/download/details.aspx?id=56519) dla zmiany lub aktualizacji. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Adres IP emisji dowolnej zmienić w okresie istnienia Moje drzwiami frontowymi?

Adresu IP frontonu emisji dowolnej usługi drzwi wejściowe powinny zazwyczaj nie zmienia się i mogą pozostawać statyczne okres istnienia wejściu. Istnieją jednak **nie gwarancje** dla tego samego. Pan nie przyjmują żadnych zależności bezpośrednich na adresie IP.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure Service drzwiami frontowymi obsługuje statyczne lub dedykowanych adresów IP?

Nie, usługa drzwiami frontowymi Azure obecnie nie obsługuje emisji dowolnej frontonu statyczne lub dedykowane adresy IP. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure Service drzwiami frontowymi obsługuje nagłówków x-forwarded uzyskać?

Tak, usługa drzwiami frontowymi Azure obsługuje nagłówków X-Forwarded-dla X-Forwarded-Host i X-Forwarded-Proto. IF-X-Forwarded-dla nagłówka zostało już istnieje, a następnie drzwiami frontowymi dołącza IP gniazda klienta do niego. W przeciwnym wypadku dodaje nagłówek IP gniazda klienta jako wartość. X-Forwarded-Host i X-Forwarded-Proto wartość zostanie zastąpiona.

Dowiedz się więcej o [drzwiami frontowymi obsługiwanych nagłówków HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Jak długo trwa wdrażanie usługi Azure drzwiami frontowymi? Moje drzwiami frontowymi nadal działa podczas aktualizowania?

Tworzenie nowego drzwiami frontowymi lub wszelkie aktualizacje istniejących drzwiami frontowymi zajmuje około 3 – 5 minut do wdrożenia globalnego. Oznacza to, w ciągu około 3 do 5 minut, konfigurację drzwi zostaną wdrożone we wszystkich naszych punktów obecności globalnie.

Uwaga — niestandardowe aktualizacje certyfikatów SSL zająć około 30 minut do wdrażany globalnie.

## <a name="configuration"></a>Konfigurowanie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Można trasy lub Równoważenie obciążenia Azure drzwiami frontowymi ruch w sieci wirtualnej?

Azure drzwiami frontowymi (ADF) wymaga publicznego adresu IP lub rozpoznania publicznie nazwy DNS na potrzeby kierowania ruchu. Tak, to odpowiedź na pytanie nie AFD bezpośrednio nie można skierować połączenia sieci wirtualnej, ale przy użyciu bramy aplikacji lub usługi Azure Load Balancer między rozwiąże ten scenariusz.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Jakie są różne limity czasu i limity dla usługi Azure Service drzwiami frontowymi?

Dowiedz się więcej o wszystkich zastosowaniu [przekroczeń limitu czasu i limity dla usługi Azure Service drzwiami frontowymi](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Wydajność

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Jak usługa Azure Service drzwiami frontowymi obsługuje wysoką dostępność i skalowalność?

Usługa drzwiami frontowymi jest globalnie dystrybuowane platformy wielodostępnej za pomocą olbrzymie ilości pojemności, które spełniają potrzeby w zakresie skalowalności Twojej aplikacji. Dostarczane z krawędzią globalnej sieci firmy Microsoft, drzwiami frontowymi zapewnia globalny możliwości równoważenia obciążenia umożliwiającą awaryjnie całej aplikacji lub nawet poszczególnych mikrousług w różnych regionach lub innej chmury.

## <a name="ssl-configuration"></a>Konfiguracja protokołu SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Jakie wersje protokołu TLS są obsługiwane przez usługę Azure drzwiami frontowymi Service?

Drzwiami frontowymi obsługuje protokół TLS w wersji 1.0, 1.1 i 1.2. TLS 1.3 nie jest jeszcze obsługiwana.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Jakie certyfikaty są obsługiwane w usłudze Azure Service drzwiami frontowymi?

Aby włączyć protokół HTTPS umożliwiającej bezpieczne dostarczanie zawartości na domenę niestandardową na wejściu, można użyć certyfikatu, który jest zarządzany przez usługę Azure Service drzwiami frontowymi lub użyć własnego certyfikatu.
Drzwiami frontowymi zarządzane przepisy opcja to standardowy certyfikat SSL za pośrednictwem firmy Digicert i przechowywane na wierzchu firmy drzwi Key Vault. Jeśli zdecydujesz się użyć własnego certyfikatu, a następnie można dodać certyfikatu z urzędu certyfikacji obsługiwana i może być standardowego protokołu SSL, rozszerzona Walidacja lub nawet certyfikatu wieloznacznego. Certyfikaty z podpisem własnym nie są obsługiwane. Dowiedz się, [jak włączyć protokół HTTPS dla domeny niestandardowej](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Jakie są bieżące mechanizmów szyfrowania obsługiwanych przez usługę Azure drzwiami frontowymi Service?

Bieżący mechanizmów szyfrowania obsługiwanych przez usługę Azure Service drzwiami frontowymi są następujące:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Service drzwiami frontowymi również obsługuje ponownie szyfrować ruchu do wewnętrznej bazy danych?

Tak, usługa drzwiami frontowymi Azure obsługuje odciążanie protokołu SSL i kompleksowej usługi SSL, który ponownie szyfruje ruch do wewnętrznej bazy danych. W rzeczywistości ponieważ połączenia z zapleczem usługi się zdarzyć, za pośrednictwem publicznego adresu IP, zalecane jest skonfigurowanie usługi wejściu do używania protokołu HTTPS jako protokół przekazywania.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Można skonfigurować zasady protokołu SSL w celu kontrolowania wersji protokołu SSL?

Nie, obecnie nie obsługuje drzwiami frontowymi w celu blokowania określonych wersji protokołu TLS ani nie można ustawić minimalne wersje protokołu TLS. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Można skonfigurować wejściu do obsługują tylko określonych mechanizmy szyfrowania?

Nie, konfigurowanie drzwi wejściowe dla określonych mechanizmy szyfrowania nie jest obsługiwana. 

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Jakie rodzaje metryk i dzienników są dostępne z usługą Azure Service drzwiami frontowymi?

Aby uzyskać informacje na temat dzienników i inne funkcje diagnostyczne, zobacz [monitorowania metryk i dzienników drzwiami frontowymi](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jakie są zasady przechowywania dzienników diagnostycznych?

Dzienniki diagnostyczne usługi flow do konta magazynu klientów i klienci mogą ustawić zasady przechowywania na podstawie jego preferencji. Dzienniki diagnostyczne mogą być również wysyłane do Centrum zdarzeń lub usługi Azure Monitor dzienniki. Aby uzyskać więcej informacji, zobacz [usługi Azure Diagnostics Service drzwiami frontowymi](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Jak uzyskać dzienniki inspekcji dla usługi Azure Service drzwiami frontowymi?

Dzienniki inspekcji są dostępne dla usługi Azure Service wejściu. W portalu, kliknij przycisk **dziennika aktywności** w bloku menu swojej drzwiami frontowymi dostępu do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Można ustawić alerty z usługą Azure Service drzwiami frontowymi?

Tak, usługa drzwiami frontowymi Azure obsługuje alerty. Alerty są konfigurowane na metryki. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).