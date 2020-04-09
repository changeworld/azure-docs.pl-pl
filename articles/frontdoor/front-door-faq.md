---
title: Drzwi frontowe platformy Azure — często zadawane pytania
description: Ta strona zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Front Door
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
ms.openlocfilehash: 0fe5d245d629c731a47ca5441afd2a3388a22de4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878021"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Często zadawane pytania dotyczące usług Azure Front Door

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące funkcji i funkcji usługi Azure Front Door. Jeśli nie widzisz odpowiedzi na swoje pytanie, możesz skontaktować się z nami za pośrednictwem następujących kanałów (w kolejności eskalacji):

1. Komentarze w tym artykule.
2. [Głos użytkownika usługi Azure Front Door](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Pomoc techniczna firmy Microsoft:** Aby utworzyć nowe żądanie pomocy technicznej, w witrynie Azure portal na karcie **Pomoc** wybierz przycisk **Pomoc + pomoc,** a następnie wybierz pozycję Nowe żądanie pomocy **technicznej**.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-front-door"></a>Co to jest Azure Front Door?

Usługa Azure Front Door to usługa sieci dostarczania aplikacji (ADN) oferująca różne funkcje równoważenia obciążenia warstwy 7 dla aplikacji. Zapewnia dynamiczne przyspieszanie witryny (DSA) wraz z globalnym równoważeniem obciążenia z niemal trybem failover w czasie rzeczywistym. Jest to usługa o wysokiej dostępności i skalowalna, która jest w pełni zarządzana przez platformę Azure.

### <a name="what-features-does-azure-front-door-support"></a>Jakie funkcje obsługują usługi Azure Front Door?

Usługa Azure Front Door obsługuje dynamiczne przyspieszanie witryny (DSA), odciążanie TLS/SSL i odwrót TLS od końca do końca, zapora aplikacji sieci Web, koligację sesji opartą na plikach cookie, routing oparty na ścieżce adresów URL, bezpłatne certyfikaty i zarządzanie wieloma domenami i inne. Aby uzyskać pełną listę obsługiwanych funkcji, zobacz [Omówienie drzwi frontowych platformy Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Jaka jest różnica między drzwiami frontowymi platformy Azure a bramą aplikacji platformy Azure?

Podczas gdy zarówno drzwi frontowe, jak i brama aplikacji są modułami równoważenia obciążenia warstwy 7 (HTTP/HTTPS), podstawową różnicą jest to, że drzwiami frontowymi są usługą globalną, podczas gdy brama aplikacji jest usługą regionalną. Drzwi frontowe mogą równoważyć obciążenie między różnymi jednostkami skali/klastrami/jednostkami stempla w różnych regionach, ale brama aplikacji umożliwia równoważenie obciążenia między maszynami wirtualnymi/kontenerami itp., które znajduje się w jednostce skalowania.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Kiedy należy wdrożyć bramę aplikacji za drzwiami frontowymi?

Kluczowe scenariusze, dla których należy używać bramy aplikacji za drzwiami frontowymi, to:

- Drzwi frontowe mogą wykonywać równoważenie obciążenia oparte na ścieżce tylko na poziomie globalnym, ale jeśli chcesz jeszcze bardziej załadować ruch równoważenia w sieci wirtualnej (VNET), należy użyć bramy aplikacji.
- Ponieważ drzwi frontowe nie działa na poziomie maszyny Wirtualnej/kontenera, więc nie można wykonać opróżniania połączenia. Jednak brama aplikacji umożliwia opróżnianie połączenia. 
- Dzięki bramie aplikacji za afd, można osiągnąć 100% odciążanie TLS/SSL i trasy tylko żądania HTTP w ich sieci wirtualnej (VNET).
- Drzwi frontowe i brama aplikacji obsługują koligalności sesji. Drzwi frontowe mogą kierować kolejny ruch z sesji użytkownika do tego samego klastra lub wewnętrznej bazy danych w danym regionie, brama aplikacji może kierować affinitize ruchu do tego samego serwera w klastrze.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Czy możemy wdrożyć moduł równoważenia obciążenia platformy Azure za drzwiami frontowymi?

Usługa Azure Front Door potrzebuje publicznego adresu VIP lub publicznie dostępnej nazwy DNS, aby przekierować ruch. Wdrażanie modułu równoważenia obciążenia platformy Azure za drzwiami frontowymi jest typowym przypadkiem użycia.

### <a name="what-protocols-does-azure-front-door-support"></a>Jakie protokoły obsługują usługi Azure Front Door?

Drzwi frontowe platformy Azure obsługują protokół HTTP, HTTPS i HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>W jaki sposób usługi Azure Front Door obsługują protokół HTTP/2?

Obsługa protokołu HTTP/2 jest dostępna tylko dla klientów łączących się z usługą Azure Front Door. Komunikacja do zaplecza w puli wewnętrznej bazy danych jest za pośrednictwem PROTOKOŁU HTTP/1.1. Obsługa protokołu HTTP/2 jest domyślnie włączona.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Jakie zasoby są obecnie obsługiwane jako część puli zaplecza?

Pule zaplecza mogą składać się z magazynu, aplikacji sieci Web, wystąpień kubernetes lub innej niestandardowej nazwy hosta, która ma łączność publiczną. Usługa Azure Front Door wymaga, aby zaplecze były definiowane za pośrednictwem publicznego adresu IP lub publicznie rozpoznawanej nazwy hosta DNS. Członkowie pul zaplecza mogą znajdować się w różnych strefach, regionach lub nawet poza platformą Azure, o ile mają łączność publiczną.

### <a name="what-regions-is-the-service-available-in"></a>W jakich regionach jest dostępna usługa?

Usługa Azure Front Door jest usługą globalną i nie jest powiązana z żadnym określonym regionem platformy Azure. Jedyną lokalizacją, którą należy określić podczas tworzenia drzwi frontowych, jest lokalizacja grupy zasobów, która zasadniczo określa, gdzie będą przechowywane metadane dla grupy zasobów. Sam zasób drzwi frontowych jest tworzony jako zasób globalny, a konfiguracja jest wdrażana globalnie dla wszystkich punktów obecności (Punktu obecności). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Jakie są lokalizacje pop dla drzwi frontowych platformy Azure?

Usługa Azure Front Door ma taką samą listę lokalizacji POP (Point of Presence) jak usługa Azure CDN firmy Microsoft. Aby uzyskać pełną listę naszych punktów obecności, zapoznaj się [z lokalizacjami pop usługi Azure CDN firmy Microsoft.](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Czy usługa Azure Front Door jest dedykowanym wdrożeniem dla mojej aplikacji, czy jest współużytkowana przez klientów?

Usługa Azure Front Door to globalnie rozproszona usługa wielodostępna. Tak więc infrastruktura dla drzwi frontowych jest dzielona przez wszystkich swoich klientów. Jednak tworząc profil drzwi frontowych, można zdefiniować określoną konfigurację wymaganą dla aplikacji i żadne zmiany wprowadzone do drzwi frontowych wpływ innych konfiguracji drzwi frontowych.

### <a name="is-http-https-redirection-supported"></a>Czy przekierowanie HTTP->https jest obsługiwane?

Tak. W rzeczywistości usługi Azure Front Door obsługuje przekierowanie hosta, ścieżki i ciągu zapytania, a także część przekierowania adresu URL. Dowiedz się więcej o [przekierowaniu adresu URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>W jakiej kolejności są przetwarzane reguły routingu?

Trasy dla drzwi wejściowych nie są uporządkowane, a określona trasa jest wybierana na podstawie najlepszego dopasowania. Dowiedz się więcej o [tym, jak drzwiami frontowymi dopasowywać żądania do reguły routingu](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Jak zablokować dostęp do wewnętrznej bazy danych tylko do drzwi frontowych platformy Azure?

Aby zablokować aplikację, aby akceptować ruch tylko z określonych drzwiach frontowych, należy skonfigurować listy ACL adresów IP dla wewnętrznej bazy danych, a następnie ograniczyć ruch w wewnętrznej bazy danych do określonej wartości nagłówka "X-Azure-FDID" wysyłanego przez drzwiami frontowymi. Poniższe kroki są wyszczególnione w następujący sposób:

- Skonfiguruj usługę IP ACLing dla zaplecza, aby akceptować ruch z przestrzeni adresów IP zaplecza usługi Azure Front Door i tylko usług infrastruktury platformy Azure. Zapoznaj się z poniższymi informacjami IP, aby zapoznać się z informacjami o wewnętrznej wartości:
 
    - Zapoznaj się z sekcją *AzureFrontDoor.Backend* w [zakresach adresów IP platformy Azure i tagach usług](https://www.microsoft.com/download/details.aspx?id=56519) dla zakresu adresów IP zaplecza IPv4 drzwi frontowych lub możesz również użyć tagu usługi *AzureFrontDoor.Backend* w [sieciowych grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) lub z [zaporą azure.](https://docs.microsoft.com/azure/firewall/service-tags)
    - Miejsce ip zaplecza **IPv6** drzwi frontowych, gdy są objęte tagiem usługi, nie jest wymieniony w pliku JSON zakresów adresów IP platformy Azure. Jeśli szukasz wyraźnego zakresu adresów IPv6, jest on obecnie ograniczony do`2a01:111:2050::/44`
    - [podstawowe usługi infrastruktury](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) platformy Azure za pośrednictwem `168.63.129.16` zwirtualizowanych adresów IP hosta:`169.254.169.254`

    > [!WARNING]
    > Przestrzeń IP wewnętrznej bazy danych frontowych drzwi może ulec zmianie później, jednak zanim to nastąpi, zintegrowalibyśmy się z [zakresami adresów IP platformy Azure i tagami usług.](https://www.microsoft.com/download/details.aspx?id=56519) Firma Microsoft zaleca, aby subskrybować [zakresy adresów IP platformy Azure i tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) dla wszelkich zmian lub aktualizacji.

-    Wykonaj operację GET na drzwiach frontowych z wersją interfejsu API lub nowszą. `2020-01-01` W wywołaniu interfejsu `frontdoorID` API poszukaj pola. Filtruj nagłówek przychodzący '**X-Azure-FDID**' wysłany przez drzwi frontowe `frontdoorID`do wewnętrznej bazy danych z wartością jako wartość pola . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Czy adres IP dowolnej emisji może ulec zmianie w ciągu całego okresu istnienia moich drzwi frontowych?

Adres IP dowolnej emisji frontend dla drzwi frontowych zazwyczaj nie powinien się zmieniać i może pozostać statyczny przez cały okres istnienia drzwi wejściowych. Nie ma jednak **żadnych gwarancji** na to samo. Uprzejmie nie należy przyjmować żadnych bezpośrednich zależności od IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Czy usługi Azure Front Door obsługują statyczne lub dedykowane adresy IP?

Nie, usługi Azure Front Door obecnie nie obsługuje statycznych lub dedykowanych adresów IP emisji frontowej. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Czy usługi Azure Front Door obsługują nagłówki x-forwarded-for?

Tak, usługi Azure Front Door obsługuje nagłówki X-Forwarded-For, X-Forwarded-Host i X-Forwarded-Proto. Dla X-Forwarded-For, jeśli nagłówek był już obecny, następnie drzwiami frontowymi dołącza ip gniazda klienta do niego. W przeciwnym razie dodaje nagłówek z ip gniazda klienta jako wartość. W przypadku X-Forwarded-Host i X-Forwarded-Proto wartość jest zastępowana.

Dowiedz się więcej o [obsługiwanych przez drzwi frontowe nagłówkach HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Jak długo trwa wdrażanie drzwi trakcjowych platformy Azure? Czy drzwi wejściowe nadal działają po aktualizacji?

Nowe tworzenie drzwi frontowych lub wszelkie aktualizacje istniejących drzwi frontowych trwa około 3 do 5 minut dla wdrożenia globalnego. Oznacza to, że w ciągu około 3 do 5 minut konfiguracja drzwi frontowych zostanie wdrożona we wszystkich naszych klientach na całym świecie.

Uwaga — niestandardowe aktualizacje certyfikatów TLS/SSL zajmują około 30 minut do wdrożenia na całym świecie.

Wszelkie aktualizacje tras lub pul zaplecza itp są bezproblemowe i spowoduje zero przestojów (jeśli nowa konfiguracja jest poprawna). Aktualizacje certyfikatów są również niepodzielne i nie powodują żadnych awarii, chyba że przejście z "AfD Managed" na "Użyj własnego certyfikatu" lub odwrotnie.


## <a name="configuration"></a>Konfigurowanie

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Czy usługa Azure Front Door może równoważyć obciążenie lub kierować ruch w sieci wirtualnej?

Usługa Azure Front Door (AFD) wymaga publicznego adresu IP lub publicznie rozpoznawanej nazwy DNS do kierowania ruchu. Tak więc odpowiedź jest nie AFD bezpośrednio nie można routisku w sieci wirtualnej, ale przy użyciu bramy aplikacji lub azure load balancer pomiędzy rozwiąże ten scenariusz.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Jakie są różne limity czasu i limity dla usługi Azure Front Door?

Dowiedz się więcej o wszystkich [udokumentowanych limitach czasu i limitach dla drzwiach frontowych platformy Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Wydajność

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>W jaki sposób usługi Azure Front Door obsługują wysoką dostępność i skalowalność?

Usługa Azure Front Door to globalnie rozproszona platforma wielodostępna o dużej pojemności, która zaspokaja potrzeby aplikacji w zakresie skalowalności. Dostarczane z krawędzi globalnej sieci firmy Microsoft, drzwi frontowe zapewnia globalne możliwości równoważenia obciążenia, który pozwala na awaryjne nad całą aplikacją lub nawet poszczególnych mikrousług w regionach lub różnych chmurach.

## <a name="tls-configuration"></a>Konfiguracja protokołu TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Jakie wersje TLS są obsługiwane przez usługi Azure Front Door?

Wszystkie profile drzwi przednich utworzone po wrześniu 2019 r. używają protokołu TLS 1.2 jako domyślnego minimum.

Drzwi wejściowe obsługują TLS w wersjach 1.0, 1.1 i 1.2. TLS 1.3 nie jest jeszcze obsługiwany.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Jakie certyfikaty są obsługiwane w usłudze Azure Front Door?

Aby włączyć protokół HTTPS do bezpiecznego dostarczania zawartości w domenie niestandardowej drzwiami frontowymi, można użyć certyfikatu zarządzanego przez usługi Azure Front Door lub użyć własnego certyfikatu.
Opcja zarządzana drzwiami frontowymi udostępnia standardowy certyfikat TLS/SSL za pośrednictwem digicert i jest przechowywana w przechowalni kluczy do drzwi przednich. Jeśli zdecydujesz się użyć własnego certyfikatu, możesz dołączać certyfikat z obsługiwanego urzędu certyfikacji i może być standardowym certyfikatem TLS, certyfikatem rozszerzonej weryfikacji, a nawet certyfikatem wieloznacznym. Certyfikaty z podpisem własnym nie są obsługiwane. Dowiedz [się, jak włączyć protokół HTTPS dla domeny niestandardowej](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Czy drzwi frontowe obsługują autorotację certyfikatów?

W przypadku opcji certyfikatu zarządzanego drzwiami frontowymi certyfikaty są autorotated przez drzwiami frontowymi. Jeśli używasz certyfikatu zarządzanego drzwiami frontowymi i widzisz, że data wygaśnięcia certyfikatu jest mniejsza niż 60 dni, złóż bilet pomocy technicznej.
</br>W przypadku własnego niestandardowego certyfikatu TLS/SSL autorotacja nie jest obsługiwana. Podobnie jak po raz pierwszy skonfigurowano ją dla danej domeny niestandardowej, należy wskazać drzwiami frontowymi na właściwą wersję certyfikatu w magazynie kluczy i upewnić się, że podmiot usługi dla drzwi y frontowej nadal ma dostęp do magazynu kluczy. Ta zaktualizowana operacja wdrażania certyfikatów przez drzwiami frontowymi jest niepodzielna i nie powoduje żadnego wpływu na produkcję, pod warunkiem, że nazwa podmiotu lub sieci SAN dla certyfikatu nie ulegnie zmianie.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Jakie są bieżące mechanizmy szyfrowania obsługiwane przez usługi Azure Front Door?

Poniżej przedstawiono bieżące mechanizmy szyfrowania obsługiwane przez usługi Azure Front Door:

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Czy mogę skonfigurować zasady TLS do kontrolowania wersji protokołu TLS?

Minimalną wersję protokołu TLS można skonfigurować w drzwiach frontowych platformy Azure w ustawieniach HTTPS domeny niestandardowej za pośrednictwem witryny Azure portal lub [interfejsu API rest platformy Azure.](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) Obecnie można wybierać między 1.0 i 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Czy mogę skonfigurować drzwi frontowe tak, aby obsługiwać tylko określone mechanizmy szyfrowania?

Nie, konfigurowanie drzwi frontowych dla określonych zestawów szyfrowania nie jest obsługiwane. Można jednak uzyskać własny niestandardowy certyfikat TLS/SSL od urzędu certyfikacji (na przykład Verisign, Entrust lub Digicert) i mieć określone mechanizmy szyfrowania oznaczone na certyfikacie, gdy go wygenerowano. 

### <a name="does-front-door-support-ocsp-stapling"></a>Czy drzwi frontowe obsługują zszywanie OCSP?

Tak, zszywanie OCSP jest domyślnie obsługiwane przez drzwi frontowe i nie jest wymagana żadna konfiguracja.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Czy usługi Azure Front Door obsługują również ponowne szyfrowanie ruchu do wewnętrznej bazy danych?

Tak, usługi Azure Front Door obsługuje odciążanie protokołu TLS/SSL i end to end TLS, który ponownie szyfruje ruch do wewnętrznej bazy danych. W rzeczywistości, ponieważ połączenia z wewnętrznej bazy danych zdarzyć za pośrednictwem jego publicznych adresów IP, zaleca się skonfigurować drzwi frontowe do używania protokołu HTTPS jako protokołu przekazywania.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Czy drzwiami frontowymi obsługują certyfikaty z podpisem własnym w wewnętrznej bazy danych dla połączenia HTTPS?

Nie, certyfikaty z podpisem własnym nie są obsługiwane w drzwiach frontowych i ograniczenie dotyczy obu:

1. **Zaplecze:** Nie można używać certyfikatów z podpisem własnym podczas przekazywania ruchu jako sondy kondycji HTTPS lub HTTPS lub wypełniania pamięci podręcznej dla reguł od początku do routingu z włączoną buforowanie.
2. **Frontend:** Nie można używać certyfikatów z podpisem własnym podczas używania własnego niestandardowego certyfikatu TLS/SSL do włączania protokołu HTTPS w domenie niestandardowej.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Dlaczego ruch HTTPS do wewnętrznej bazy danych nie działa?

W przypadku pomyślnych połączeń HTTPS z zapleczem, czy dla sond kondycji lub przekazywania żądań, mogą istnieć dwa powody, dla których ruch HTTPS może zakończyć się niepowodzeniem:

1. **Niezgodność nazwy podmiotu certyfikatu:** W przypadku połączeń HTTPS drzwiami frontowymi oczekują, że wewnętrznej bazy danych jest prezentowany certyfikat z prawidłowego urzędu certyfikacji o nazwie podmiotu odpowiadającej nazwie hosta wewnętrznej bazy danych. Na przykład jeśli nazwa hosta wewnętrznej bazy `myapp-centralus.contosonews.net` danych jest ustawiona i certyfikat, który znajduje się `myapp-centralus.contosonews.net` w `*myapp-centralus*.contosonews.net` wewnętrznej bazy danych podczas uzgadniania TLS ani nie ma ani w nazwie podmiotu, drzwi frontowe odmówią połączenia i spowoduje błąd. 
    1. **Rozwiązanie:** Chociaż nie jest to zalecane z punktu widzenia zgodności, można obejść ten błąd, wyłączając sprawdzanie nazwy podmiotu certyfikatu dla drzwi frontowych. Jest to obecne w obszarze Ustawienia w witrynie Azure portal i w obszarze BackendPoolsSettings w interfejsie API.
2. **Certyfikat hosta zaplecza z nieprawidłowego urzędu certyfikacji:** Tylko certyfikaty z [prawidłowych urzędów certyfikacji](/azure/frontdoor/front-door-troubleshoot-allowed-ca) mogą być używane w wewnętrznej bazy danych z drzwiami frontowymi. Certyfikaty z wewnętrznych certyfikatów kasowych lub certyfikatów z podpisem własnym są niedozwolone.

## <a name="diagnostics-and-logging"></a>Diagnostyka i rejestrowanie

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Jakie typy metryk i dzienników są dostępne w usłudze Azure Front Door?

Aby uzyskać informacje na temat dzienników i innych funkcji diagnostycznych, zobacz [Monitorowanie metryk i dzienników dla drzwi frontowych](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Jakie są zasady przechowywania w dziennikach diagnostycznych?

Przepływ dzienników diagnostycznych do konta magazynu klientów, a klienci mogą ustawić zasady przechowywania na podstawie ich preferencji. Dzienniki diagnostyczne mogą być również wysyłane do dzienników usługi Event Hub lub Azure Monitor. Aby uzyskać więcej informacji, zobacz [Diagnostyka drzwi y frontowej platformy Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Jak uzyskać dzienniki inspekcji dla drzwi frontowych platformy Azure?

Dzienniki inspekcji są dostępne dla drzwi y frontowe platformy Azure. W portalu kliknij pozycję **Dziennik aktywności** w bloku menu drzwi frontowych, aby uzyskać dostęp do dziennika inspekcji. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Czy mogę ustawić alerty za pomocą drzwi frontowych platformy Azure?

Tak, usługi Azure Front Door obsługuje alerty. Alerty są konfigurowane na metryki. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
