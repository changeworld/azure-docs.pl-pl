---
title: Usługa Azure Traffic Manager — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.openlocfilehash: b89e9a48ce20c7c00bd3282623919b8066e34b25
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717059"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Usługa Traffic Manager — często zadawane pytania (FAQ)

## <a name="traffic-manager-basics"></a>Podstawy usługi Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Jakiego adresu IP używa usługi Traffic Manager?

Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), usługa Traffic Manager działa na poziomie usługi DNS. Wysyła odpowiedzi DNS klientów bezpośrednich do endpoint odpowiednią usługę. Klienci następnie łączą się z punktu końcowego usługi bezpośrednio, nie za pomocą usługi Traffic Manager.

W związku z tym usługi Traffic Manager nie zapewnia punkt końcowy, czyli adres IP, aby klienci mogli nawiązać połączenie. Jeśli potrzebujesz statycznego adresu IP dla usługi, która musi być skonfigurowana w usłudze, a nie w usłudze Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jakie rodzaje ruchu może być kierowany za pomocą usługi Traffic Manager?
Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), punkt końcowy usługi Traffic Manager może być dowolnym połączenie z Internetem usługi hostowanej wewnątrz lub na zewnątrz systemu Azure. Dzięki temu usługi Traffic Manager może kierować ruch pochodzący z publicznego Internetu do zestawu punktów końcowych, są również nakierowany na internet. W przypadku punktów końcowych znajdujących się w sieci prywatnej (na przykład wewnętrzny wersję [usługi Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)) lub nie można użyć usługi Traffic Manager kierować ruchem to użytkownikom wysyłającym żądania DNS z takich sieci wewnętrznych.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Usługa Traffic Manager obsługuje "trwałych" sesji?

Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), usługa Traffic Manager działa na poziomie usługi DNS. Odpowiedzi DNS używa klientów bezpośrednich do endpoint odpowiednią usługę. Klienci łączą się z punktu końcowego usługi bezpośrednio, nie za pomocą usługi Traffic Manager. W związku z tym usługi Traffic Manager nie widzi ruch HTTP między klientem a serwerem.

Ponadto adres IP źródła zapytania DNS odebranych przez usługę Traffic Manager należy do cyklicznej usługi DNS, nie klienta. W związku z tym Traffic Manager nie ma możliwości śledzenia poszczególnych klientów i nie może implementować "trwałych" sesji. To ograniczenie jest wspólny dla wszystkich systemów zarządzania ruchu oparte na systemie DNS i nie jest specyficzne dla Menedżera ruchu.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Dlaczego widzę błąd HTTP podczas korzystania z usługi Traffic Manager?

Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), usługa Traffic Manager działa na poziomie usługi DNS. Odpowiedzi DNS używa klientów bezpośrednich do endpoint odpowiednią usługę. Klienci następnie łączą się z punktu końcowego usługi bezpośrednio, nie za pomocą usługi Traffic Manager. Usługa Traffic Manager nie Zobacz ruch HTTP między klientem i serwerem. W związku z tym wszelkie błędy HTTP, widocznej musi pochodzić z aplikacji. Dla klienta w celu połączenia się z aplikacją wszystkie kroki rozwiązania DNS są kompletne. Obejmuje to dowolna interakcja, które usługi Traffic Manager ma na przepływ ruchu aplikacji.

Bliższe zbadanie tej sprawy w związku z tym należy skoncentrować się na aplikacji.

Nagłówka hosta HTTP wysyłane z przeglądarki klienta jest najbardziej typowe źródła problemów z. Upewnij się, że aplikacja jest skonfigurowana do akceptowania nagłówek hosta poprawny dla nazwy domeny, którego używasz. Dla punktów końcowych przy użyciu usługi Azure App Service, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu usługi Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Co to jest wpływ wydajności przy użyciu usługi Traffic Manager?

Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), usługa Traffic Manager działa na poziomie usługi DNS. Ponieważ klienci łączą się z punktów końcowych usługi bezpośrednio, nie ma wpływu na wydajność ponoszony, gdy za pomocą usługi Traffic Manager, po nawiązaniu połączenia.

Ponieważ usługa Traffic Manager integruje się z aplikacjami na poziomie usługi DNS, jest wymagane dodatkowe wyszukiwania DNS, który ma zostać wstawiony do łańcucha rozpoznawania DNS. Usługi Traffic Manager negatywny wpływ na czas rozpoznawania nazw DNS jest minimalny. Usługa Traffic Manager korzysta z globalnej sieci serwerów nazw i używa [emisji dowolnej](https://en.wikipedia.org/wiki/Anycast) sieć w celu zapewnienia DNS zapytania są zawsze kierowane do najbliższego serwera dostępnych nazw. Ponadto buforowanie odpowiedzi DNS oznacza, że dodatkowe opóźnienie DNS naliczane przy użyciu usługi Traffic Manager ma zastosowanie tylko do końcowej sesji.

Metoda wydajności kieruje ruch do najbliższego dostępnego punktu końcowego. Wynikiem jest, że należy minimalny wpływ na ogólną wydajność, skojarzone z tą metodą. Każde zwiększenie liczby opóźnienia systemu DNS powinien przesunięcia, mniejsze opóźnienia sieciowe do punktu końcowego.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jakie protokoły aplikacji można używać z usługą Traffic Manager?

Jak wyjaśniono w [jak działa usługa Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), usługa Traffic Manager działa na poziomie usługi DNS. Po wykonaniu wyszukiwania DNS klienci łączą się z punktu końcowego aplikacji bezpośrednio, nie za pomocą usługi Traffic Manager. W związku z tym połączenia, można użyć dowolnego protokołu aplikacji. Jeśli wybierzesz TCP jako protokół monitorowania, usługa Traffic Manager firmy monitorowania kondycji punktu końcowego może odbywać się bez korzystania z protokołów aplikacji. Jeśli zdecydujesz się kondycja weryfikowane przy użyciu protokołu aplikacji, punkt końcowy musi być w stanie odpowiadać na żądania HTTP lub HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Nazwa domeny "naked" można używać usługi Traffic Manager?
Tak. Aby dowiedzieć się, jak utworzyć rekord aliasu dla Twojego wierzchołku nazwy domeny można odwoływać się do profilu usługi Azure Traffic Manager, zobacz [Konfigurowanie rekordu aliasu do obsługi języka apex nazw domen przy użyciu usługi Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Usługa Traffic Manager za adres podsieci klienta podczas przetwarzania zapytań DNS? 
Tak, oprócz adres IP źródła zapytania DNS otrzymuje (co zwykle jest adres IP programu rozpoznawania nazw DNS), podczas przeprowadzania wyszukiwania dla metody routingu Geographic, wydajności i podsieci, usługa traffic manager również uwzględnia adres podsieci klienta, jeśli uwzględnione w zapytaniu przez program rozpoznawania nazw, dzięki czemu żądania w imieniu użytkownika końcowego.  
W szczególności [7871 RFC — podsieci klienta DNS zapytań](https://tools.ietf.org/html/rfc7871) zapewniający [mechanizmu rozszerzenia DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) które można przekazać na adres podsieci klienta z rozpoznawania nazw, które go obsługują.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co to jest czas wygaśnięcia DNS oraz jak ma ona wpływu na moich użytkowników?

Gdy zapytanie DNS umieszczać swoje dokumenty w usłudze Traffic Manager, ustawia wartość w odpowiedzi o nazwie czas wygaśnięcia (TTL). Tę wartość, w której jednostka jest w ciągu kilku sekund, wskazuje do rozpoznawania nazw DNS podrzędne na jak długo w pamięci podręcznej tej odpowiedzi. Podczas rozpoznawania nazw DNS nie ma gwarancji, ten wynik w pamięci podręcznej, buforowanie ich pozwoli na odpowiadać na żadne kolejne zapytania wyłączanie pamięci podręcznej, zamiast przechodzić do serwerów DNS usługi Traffic Manager. Ma to wpływ na odpowiedzi w następujący sposób:
- wyższa wartość TTL zmniejsza liczbę zapytań, które przejście na serwerach DNS usługi Traffic Manager, które można zredukować koszty dla klientów, ponieważ liczba zapytań, obsługiwane jest użycie płatnych.
- wyższa wartość TTL potencjalnie skrócić czas potrzebny do czy wyszukiwania DNS.
- wyższa wartość TTL oznacza również danych nie będzie odzwierciedlał najnowsze informacje o kondycji usługi Traffic Manager ma uzyskane za pośrednictwem jego agenci badania.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak wysokie lub niskie można ustawić czasu wygaśnięcia dla usługi Traffic Manager odpowiedzi?

Można ustawić na na poziomie profilu, czasu wygaśnięcia DNS jako jedyne 0 sekund oraz możliwie jak 2 147 483 647 sekund (zgodne z maksymalną wielkość zakresu [standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). Wartością TTL równą 0 oznacza, że podrzędnego rozpoznawania nazw DNS nie buforują odpowiedzi na kwerendy i oczekują, że wszystkie zapytania dotrzeć do serwerów w celu rozpoznawania DNS usługi Traffic Manager.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak wyjaśnić woluminu kwerendy mieszczących się do swojego profilu? 
Jednej z metryk udostępniane przez usługę Traffic Manager jest liczba zapytań wypełniona przez profil. Możesz uzyskać te informacje na poziomie agregacji profilu lub można podzielić ją aby zobaczyć wolumin zapytań, gdzie punkty końcowe zostały zwrócone. Ponadto użytkownik może skonfigurować alerty Powiadomienie, jeśli wolumin odpowiedzi zapytania przecina warunki, które zostały ustawione. Aby uzyskać więcej informacji [alerty i metryki usługi Traffic Manager](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metody routingu ruchu Geographic usługi Traffic Manager

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jakie są niektóre przypadki użycia, w których jest użyteczny geograficznego routingu? 
Typ routingu geograficznego może służyć w każdym scenariuszu których klient korzystający z systemu Azure wymaga do rozróżniania użytkowników na podstawie regionów geograficznych. Na przykład przy użyciu metody geograficznego routingu ruchu, użytkownicy mogli z regionów określonych interfejs użytkownika innego niż te, które z innych regionów. Innym przykładem jest zgodne z oczekuje się niezależność danych lokalnych, które wymagają, że użytkownicy z określonym regionie obsługiwane tylko przez punkty końcowe, w tym regionie.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak zdecydować, jeśli należy użyć metody routingu opartego na wydajności lub metody routingu geograficznego 
Główną różnicą między te dwie metody routingu popularne jest to, że wydajność przez Ciebie głównej dowiesz się, jak przesyłać dane do punktu końcowego, który zapewnia najniższe opóźnienie do obiektu wywołującego, Geographic routingu podstawowym celem jest w celu wymuszenia georegionu metody routingu ogrodzenia dla wywoływania usługi, dzięki czemu może świadomie kierować do określonego punktu końcowego. Nakładanie się odbywa się, ponieważ istnieje korelacja między bliskość geograficzne i mniejsze opóźnienia, chociaż nie zawsze jest to wartość true. Może być punkt końcowy w innej lokalizacji geograficznej, która może zapewnić lepsze środowisko opóźnienie do obiektu wywołującego i w takiej sytuacji wydajności routingu będzie wysłać użytkownika do tego punktu końcowego, ale geograficznego routingu zawsze będzie wysyłać je do punktu końcowego, które mają być mapowane do ich region geograficzny. Dodatkowo charakteryzowanych wyczyścić, rozważmy następujący przykład — za pomocą Geographic routingu, możesz wprowadzać nietypowe mapowania, takie jak Wyślij cały ruch z Azji do punktów końcowych w Stanach Zjednoczonych i cały ruch Stanów Zjednoczonych do punktów końcowych w Azji. W takim przypadku geograficznego routingu celowo wykona dokładnie co skonfigurowano mu i optymalizacji wydajności nie jest brany pod uwagę. 
>[!NOTE]
>Może to być scenariuszach, gdzie możesz potrzebować zarówno wydajności i geograficzne możliwości routingu dla profilów zagnieżdżonych w tych scenariuszach może być doskonałym wyborem. Na przykład można skonfigurować profil nadrzędnej z geograficznego routingu, w którym Wyślij cały ruch z Ameryki Północnej zagnieżdżonych profilu, który ma punkty końcowe w Stanach Zjednoczonych i korzystać z wydajności routing wysyłać te ruch optymalny punkt końcowy w ramach tego zestawu. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Co to są regiony, które są obsługiwane przez usługę Traffic Manager w przypadku geograficznego routingu? 
Hierarchia krajów/regionów, które jest używane przez usługę Traffic Manager można znaleźć [tutaj](traffic-manager-geographic-regions.md). Gdy ta strona jest zawsze na bieżąco ze wszystkimi zmianami, także programowo można pobrać te same informacje za pomocą [interfejsu REST API usługi Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak usługa traffic manager ustalić, gdzie użytkownik jest badanie od? 
Usługa Traffic Manager analizuje adres IP źródła zapytania (najprawdopodobniej jest to lokalnego rozpoznawania nazw DNS wykonywania zapytań w imieniu użytkownika) i używa wewnętrznym adresem IP do regionu mapy do określenia lokalizacji. Ta mapa jest aktualizowany na bieżąco, aby uwzględnić zmiany w Internecie. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Jest on gwarantowane, usługa Traffic Manager prawidłowo określić dokładną lokalizację geograficzną użytkownika w każdym przypadku
Nie, usługa Traffic Manager nie może zagwarantować, że regionu geograficznego, które możemy wyciągnąć z adres IP źródła zapytania DNS będzie zawsze odpowiadają od lokalizacji użytkownika z następujących przyczyn: 

- Po pierwsze zgodnie z opisem w poprzedniej odpowiedzi na pytanie, źródłowy adres IP, którego firma Microsoft jest program rozpoznawania nazw DNS podczas wyszukiwania w imieniu użytkownika. Gdy program rozpoznawania nazw DNS lokalizacja geograficzna jest dobrym serwera proxy dla lokalizacji geograficznej użytkownika, również można różne w zależności od śladu usługi rozpoznawania nazw DNS i określonych usługa rozpoznawania nazw DNS, który klient ma wybranego do użycia. Na przykład klient znajduje się w Malezji można określić w użyciu ustawienia swoich urządzeń usługa rozpoznawania nazw DNS, których serwer DNS w Singapurze może uzyskać wybrany do obsługi rozwiązania zapytania dla tego użytkownika/urządzenia. W takim przypadku usługa Traffic Manager może zobaczyć tylko adresu IP programu rozpoznawania nazw, który odnosi się do lokalizacji Singapur. Zobacz też wcześniej — często zadawane pytania dotyczące pomocy technicznej adres podsieci klienta na tej stronie.

- Po drugie usługi Traffic Manager używa wewnętrznego mapy celu adres IP do tłumaczenia z regionów geograficznych. To mapowanie jest zweryfikowana i aktualizowane na bieżąco, aby zwiększyć jego dokładność oraz konto do stale rosnących potrzeb charakteru Internetu, nadal jest możliwość, że nasze informacje nie są dokładnie reprezentacja lokalizacja geograficzna wszystkich adresów IP adresy.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Czy punkt końcowy musi się znajdować w tym samym regionie, który jest skonfigurowany przy użyciu dla geograficznego routingu? 
Nie, lokalizacja punktu końcowego nakłada żadnych ograniczeń, w których regionach mogą być mapowane do niego. Na przykład punkt końcowy w regionie środkowe stany USA Azure może mieć wszystkich użytkowników z Indii skierowany do niego.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Regiony geograficzne można przypisać do punktów końcowych w profilu, który nie jest skonfigurowany w celu geograficznego routingu? 

Tak, jeśli metody routingu w profilu nie jest geograficznego, możesz użyć [interfejsu REST API usługi Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) można przypisać regionów geograficznych do punktów końcowych w tym profilu. W przypadku profilów typu routingu geograficznie rozproszona ta konfiguracja jest ignorowana. Jeśli zmienisz takiego profilu typowi geograficznego routingu w późniejszym czasie, Traffic Manager może użyć tych mapowania.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Dlaczego otrzymuję błąd przy próbie zmienić metodę routingu istniejący profil do Geographic?

Wszystkie punkty końcowe w ramach profilu geograficznego routingu, muszą mieć przynajmniej jednego regionu do niej przyporządkowany. Aby przekonwertować istniejącego profilu geograficznego typu routingu, najpierw musisz skojarzyć regionów geograficznych, aby wszystkie jego punktów końcowych przy użyciu [interfejsu REST API usługi Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/) przed zmianą typu routingu geograficznego. Jeśli przy użyciu portalu, najpierw usunąć punkty końcowe, zmień metodę routingu profilu na geograficznych, a następnie dodaj punktów końcowych wraz z ich mapowania z regionów geograficznych. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Dlaczego jest zalecane użytkownikom utworzenie profilów zagnieżdżonych zamiast punktów końcowych w ramach profilu z włączonym routingiem geograficzne? 

Jeśli używane są metody routingu geograficznego regionu można przypisać do tylko jeden punkt końcowy w profilu. Jeśli punkt końcowy nie jest typem zagnieżdżonym z profilem podrzędnych, dołączone do niego, jeśli ruch tego punktu końcowego, przechodząc w złej kondycji, Menedżer w dalszym ciągu przesyłać do niego od alternatywne nie wysyła cały ruch nie jest żadnym lepsze. Usługa Traffic Manager nie nie trybu failover do innego punktu końcowego, nawet w przypadku, gdy region przypisany jest "elementem nadrzędnym" regionu przypisana do punktu końcowego, który wystąpił w złej kondycji, (na przykład, jeśli punkt końcowy, który ma Hiszpania region przechodzi w złej kondycji nie trybu failover do innego punktu końcowego czynność tę możemy wykonać został wybrany region Europa przypisane do niego). Odbywa się, aby upewnić się, że usługa Traffic Manager szanuje granicami geograficznymi, że klient ma ustawienia w swoim profilu. Aby skorzystać z zalet przechodzenie w tryb failover do innego punktu końcowego, gdy punkt końcowy przechodzi w złej kondycji, zalecane jest, że regionów geograficznych można przypisać do profilów zagnieżdżonych z wieloma punktami końcowymi w nim zamiast poszczególne punkty końcowe. W ten sposób w przypadku awarii punktu końcowego w profilu zagnieżdżonych elementów podrzędnych ruch można trybu failover do innego punktu końcowego w ramach tego samego profilu zagnieżdżonych elementów podrzędnych.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API, która obsługuje ten typ routingu?

Tak, tylko interfejsu API w wersji 2017-03-01 i nowszych obsługuje geograficznego routingu typu. Starsze wersje interfejsu API nie może służyć do utworzone profile typu routingu geograficznego lub przypisać regionów geograficznych do punktów końcowych. Jeśli starszej wersji interfejsu API jest używany do pobierania profilów z subskrypcją platformy Azure, dowolny profil geograficzny typu routingu nie są zwracane. Ponadto przy użyciu starszych wersji interfejsu API żadnego profilu zwracane, ma punkty końcowe z przypisaniem region geograficzny, nie ma przypisania region geograficzny, wyświetlane.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metody routingu ruchu podsieci usługi Traffic Manager

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Jakie są niektóre przypadki użycia, w której podsieci routing jest przydatne?
Podsieć routingu pozwala odróżnić doświadczenie w zakresie dostarczania dla konkretnych zestawów użytkowników identyfikowanych na podstawie źródłowego adresu IP z ich adres IP żądania DNS. Przykładem może być przedstawiający różną zawartość, jeśli użytkownicy nawiązują połączenie z witryną sieci Web w swojej Centrali firmy. Inny będzie ograniczenie dostępu użytkowników z określonych usługodawców internetowych, aby uzyskać dostęp tylko do punktów końcowych, które obsługują tylko połączeń IPv4, jeśli te usługodawców internetowych mają podrzędnych par wydajność, gdy używany jest protokół IPv6.
Kolejny powód, aby użyć metody routingu dla podsieci w połączeniu z innych profilów zagnieżdżonych profilu ustawiono. Na przykład jeśli chcesz użyć metody routingu geograficznego dla grodzenia użytkowników, ale dla określonego usługodawcę internetowego chcesz wykonać inną metodę routingu, możesz mieć profil withy podsieci metody routingu jako profil nadrzędnego i zastąpienie tego usługodawcy internetowego, aby użyć element podrzędny określonego pro plik, a ma standardowy profil geograficzny wszystkim innym użytkownikom.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Jak usługa Traffic Manager znać adres IP użytkownika końcowego?
Urządzenia użytkowników końcowych zwykle używać programu rozpoznawania nazw DNS do wyszukiwania DNS w ich imieniu. Wychodzący adres IP takiego rozpoznawania nazw jest usługa Traffic Manager widzi jako źródłowy adres IP. Ponadto metody routingu dla podsieci również sprawdza, czy informacje EDNS0 rozszerzone klienta podsieci (ECS), który został przekazany z żądaniem. Jeśli informacje o ECS, to adres używany do określenia routingu. W przypadku braku informacji ECS adres IP źródła zapytania jest używana do celów routingu.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak można określić adresy IP, przy użyciu routingu podsieci?
Adresy IP można skojarzyć z punktem końcowym usługi można określić na dwa sposoby. Po pierwsze służy notacji poczwórnej kropkowana octet dziesiętną z początkowy i końcowy adres do określania zakresu (na przykład 1.2.3.4-5.6.7.8 lub 3.4.5.6-3.4.5.6). Po drugie umożliwia notacji CIDR Określ zakres (na przykład 1.2.3.0/24). Można określić wiele zakresów i można użyć obu typów notacji w zestawie zakresu. Kilka ograniczenia mają zastosowanie.
-   Nie może mieć nakładanie się zakresów adresów, ponieważ każdego adresu IP musi być mapowane na tylko jeden punkt końcowy
-   Adres początkowy nie może być większa niż adres końcowy
-   W przypadku notacji CIDR, adres IP przed '/' powinien być adres początkowy zakresu (na przykład 1.2.3.0/24 jest prawidłowy, ale 1.2.3.4.4/24 nie jest prawidłową)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak można określić rezerwowy punkt końcowy przy użyciu routingu podsieci?
W profilu z routingiem podsieci w przypadku punktu końcowego z podsieciami, nie mapowanego, każde żądanie, które nie jest zgodny z innych punktów końcowych nastąpi przekierowanie do tego miejsca. Zdecydowanie zaleca się mieć taki rezerwowego punktu końcowego w Twoim profilu, ponieważ usługi Traffic Manager zwróci odpowiedź NXDOMAIN, jeśli żądanie jest oferowana w i nie została zamapowana na żadnych punktów końcowych lub jeśli jest mapowany do punktu końcowego, ale ten punkt końcowy jest w złej kondycji.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co się stanie, jeśli punkt końcowy jest wyłączony w podsieci routingu typu profilu?
W profilu z routingiem podsieci w przypadku punktu końcowego, z którego jest wyłączona, usługi Traffic Manager będzie zachowywać się tak, jakby punkt końcowy i mapowania podsieci, których nie istnieje. Jeśli zostanie odebrana zapytania, które mogłyby zostały dopasowane do jego mapowania adresów IP, a punkt końcowy jest wyłączona, usługa Traffic Manager zwróci rezerwowego punktu końcowego, (jeden z Brak mapowań) lub jeśli punkt końcowy nie jest obecny, zwróci odpowiedź NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Metody routingu ruchu atrybut wielowartościowy elementu usługi Traffic Manager

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Co to są niektórych przypadków użycia, w których wielu wartości routing jest przydatne?
Routing wielowartościowe zwraca wiele punktów końcowych w dobrej kondycji w odpowiedzi na jedno zapytanie. Główną zaletą to to, że jeśli punkt końcowy jest w złej kondycji, klient ma więcej opcji, aby ponowić próbę bez wprowadzania inne wywołanie DNS (która może zwrócić tę samą wartość z nadrzędnego pamięci podręcznej). Ma to zastosowanie dla dostępności aplikacji zawierających poufne dane, których chcesz skrócić czas przestoju.
Innym zastosowaniem dla metody routingu opartego na wielu wartości jest, jeśli punkt końcowy jest "dwukierunkowy" do obu protokołu IPv4 i IPv6 adresów, a chcesz nadać obiekt wywołujący obie opcje do wyboru gdy inicjuje on połączenie z punktem końcowym.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Ile punktów końcowych są zwracane, gdy wielu wartości routing jest używany?
Można określić maksymalną liczbę punktów końcowych do zwrócenia, a atrybut wielowartościowy elementu zwróci nie częściej niż wiele punktów końcowych w dobrej kondycji po otrzymaniu kwerendy. Maksymalnej możliwej wartości dla tej konfiguracji wynosi 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Ten sam zestaw punktów końcowych będzie uzyskać po wielu wartości routing jest używany?
Nie możemy zagwarantować, że ten sam zestaw punktów końcowych zostanie zwrócone w każdym zapytaniu. Dotyczy to również fakt, że niektóre z punktów końcowych, które mogą zostać przekazane złej kondycji, w tym momencie nie zostaną uwzględnione w odpowiedzi

## <a name="real-user-measurements"></a>Pomiary rzeczywistego użytkownika

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jakie są korzyści z używania pomiarów rzeczywistego użytkownika?
Korzystając z metody routingu opartego na wydajności usługi Traffic Manager wybiera najlepsze region platformy Azure dla usługi użytkownika końcowego nawiązać połączenie, sprawdzając źródłowy adres IP i podsieci klienta mechanizmów EDNS (jeśli przekazany) i obsługuje sprawdzanie go przed analiza opóźnienia sieci usługi. Pomiary dotyczące prawdziwych użytkowników podnosi poziom to dla użytkownika końcowego podstawowego o ich środowisko pracy, które przyczyniają się do tej tabeli opóźnienie Oprócz zapewniania, które w tej tabeli adekwatnie obejmuje sieci użytkowników końcowych, z którym użytkownikom końcowym łączenie się z platformą Azure. Prowadzi to do zwiększenia dokładności w routingu użytkownikowi końcowemu.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Dzięki regionom platformy Azure można używać pomiarów rzeczywistego użytkownika?
Pomiary dotyczące prawdziwych użytkowników mierzy i raportuje tylko z opóźnieniem w nawiązać regiony platformy Azure. Jeśli używane są oparte na wydajności routing za pomocą punktów końcowych hostowanej w regionach spoza platformy Azure, możesz nadal korzystać z tej funkcji przez o zwiększenie opóźnienia informacje o reprezentatywny regionu platformy Azure został wybrany do skojarzenia z tym punktem końcowym.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Które metody routingu korzyści płynące z pomiarów rzeczywistego użytkownika?
Dodatkowe informacje uzyskane za pośrednictwem pomiarów rzeczywistego użytkownika mają zastosowanie tylko w przypadku profilów, które należy użyć metody routingu wydajności. Łącze pomiarów rzeczywistego użytkownika jest dostępny w wszystkich profilów, podczas wyświetlania w witrynie Azure portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Należy włączyć pomiarów rzeczywistego użytkownika osobno każdy profil?
Nie, musisz ją włączyć raz na subskrypcję, wszystkie informacje opóźnienie mierzona i zgłaszane są dostępne dla wszystkich profilów.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak wyłączyć pomiarów rzeczywistego użytkownika dla mojej subskrypcji?
Można Naliczanie opłat związanych z pomiarów rzeczywistego użytkownika, gdy możesz zatrzymać zbieranie i wysyłanie pomiarów opóźnienia wstecz od aplikacji klienckiej. Na przykład gdy pomiaru JavaScript osadzone w stronach sieci web, można zatrzymać za pomocą tej funkcji, usuwając JavaScript lub przez wyłączenie swojego wywołania, gdy strona jest wyświetlana.

Pomiary dotyczące prawdziwych użytkowników można także wyłączyć, usuwając klucz. Po usunięciu klucz wszelkie pomiary wysyłane do usługi Traffic Manager za pomocą tego klucza, zostaną odrzucone.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Przy użyciu aplikacji klienckich niż stron sieci web można używać pomiarów rzeczywistego użytkownika?
Tak, pomiarów rzeczywistego użytkownika zaprojektowano w celu pozyskiwania danych zbieranych za pośrednictwem innego typu klientów użytkownika końcowego. Często zadawane pytania zostanie zaktualizowany, jako nowe typy aplikacji klienckich Pobierz obsługiwane.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Ile pomiarów każdorazowo Moje pomiarów rzeczywistego użytkownika włączone strony sieci web jest renderowany?
Pomiary dotyczące prawdziwych użytkowników jest używana z pomiaru JavaScript, pod warunkiem, każdy renderowanie strony skutkuje sześć pomiarów. Następnie są one raportowane powrót do usługi Traffic Manager. Opłaty są naliczane dla tej funkcji, na podstawie liczby pomiarów zgłoszone do usługi Traffic Manager. Na przykład jeśli użytkownik przejdzie od strony sieci Web, gdy są wykonywane pomiary, ale przed jego został zgłoszony, pomiarów nie są uwzględniane na potrzeby rozliczeń.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Czy istnieje opóźnienie przed uruchomieniem skryptu pomiarów rzeczywistego użytkownika w mojej stronie sieci Web?
Nie, nie ma żadnego zaprogramowanych opóźnienia przed wywołaniem skryptu.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Za pomocą tylko wybrane do mierzenia regiony platformy Azure można używać pomiarów rzeczywistego użytkownika?
Nie, każdy razem, gdy jest wywoływana, skrypt pomiarów rzeczywistego użytkownika mierzy zbiór sześciu regionach platformy Azure zgodnie z ustaleniami usługi. To ustawienie zmiany między różnymi wywołaniami i gdy wystąpi dużą liczbę tych wywołań, pokrycia pomiaru rozciąga się w różnych regionach platformy Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Czy można ograniczyć liczbę pomiarów dokonanych na określony numer?
Miary, którą JavaScript jest osadzony na stronie sieci Web oraz mają pełną kontrolę nad podczas rozpoczęcie i Zakończenie korzystania z niego. Tak długo, jak usługa Traffic Manager odbiera żądanie listę regionów świadczenia usługi Azure, które ma być mierzony, zestawie regionów są zwracane.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Może wyświetlać pomiarów dokonanych przez aplikację klienta jako część pomiarów rzeczywistego użytkownika?
Ponieważ logiki miary jest uruchamiany z aplikacji klienckiej, masz pełną kontrolę określenie, co się stanie, obejmującymi pomiary opóźnienia między innymi. Usługa Traffic Manager nie raportuje zagregowany widok pomiarów odebrane w kluczu połączone z subskrypcją.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Można zmodyfikować skrypt pomiaru dostarczane przez usługę Traffic Manager?
Podczas, gdy masz kontrolę nad co to jest osadzony na stronie sieci web, firma Microsoft zdecydowanie odradzamy możesz z wprowadzeniem jakichkolwiek zmian do skryptu pomiaru zapewnienie mierzy i raportuje opóźnienia poprawnie.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Jej będzie możliwe dla innych użytkowników wyświetlić klucz, używane z pomiarów rzeczywistego użytkownika?
Podczas osadzania skryptu miary do strony sieci web będzie to możliwe, aby inne osoby zobaczyły skrypt, a klucz rzeczywistego użytkownika pomiarów (RUM). Ale ważne jest, aby wiedzieć, że ten klucz jest inny niż identyfikator subskrypcji i jest generowany przez usługę Traffic Manager ma być używany tylko do tego celu. Wiedząc, uruchamianie klucza nie wpłynie na bezpieczeństwo Twojego konta platformy Azure.

### <a name="can-others-abuse-my-rum-key"></a>Inne gróźb mój klucz uruchamianie?
Choć jest możliwe dla innych użytkowników do użycia tego klucza do wysyłania informacji problem na platformie Azure, kilka niewłaściwego pomiarów nie ulegnie zmianie routingu, ponieważ jest on brane pod uwagę wraz z innymi pomiarów, że firma Microsoft otrzyma. Jeśli potrzebujesz zmienić klucze, można ponownie wygenerować klucza w tym momencie stary klucz staje się odrzucone.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Należy umieścić miary języka JavaScript w wszystkich stron sieci web?
Pomiary dotyczące prawdziwych użytkowników zapewnia większą wartość jako liczba pomiarów wzrost. Tym niemniej jest decyzji odnośnie tego, czy należy umieścić go w wszystkich stron sieci web lub wybierz kilka. Nasze zalecenie, jest uruchomienie przez umieszczenie go na stronie najczęściej odwiedzanych gdzie użytkownik powinien pozostać na tej stronie pięć sekund lub więcej.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Informacje dla użytkowników końcowych można zidentyfikować przez usługę Traffic Manager użycie pomiarów rzeczywistego użytkownika?
Podana pomiaru JavaScript jest używany, usługi Traffic Manager ma widoczność adres IP klienta przez użytkownika końcowego i źródłowy adres IP lokalnego rozpoznawania nazw DNS, używane. Usługi Traffic Manager korzysta z adresu IP klienta dopiero po on obcięty, to nie można rozpoznać określonego użytkownika końcowego, która wysłała pomiarów. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Czy strony sieci Web pomiarowe dotyczące prawdziwych użytkowników musi używać routingu usługi Traffic Manager?
Nie, nie trzeba używać usługi Traffic Manager. Routing po stronie usługi Traffic Manager działa niezależnie od rzeczywistych użytkowników pomiaru części i świetny pomysł, aby obie były w tej samej właściwości sieci web, ale nie muszą być.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Należy do obsługi dowolnej usługi w regionach platformy Azure za pomocą pomiarów rzeczywistego użytkownika?
Nie, nie potrzebujesz do obsługi dowolny składnik po stronie serwera na platformie Azure pomiarów rzeczywistego użytkownika do pracy. Obraz piksela pobierany przez pomiar JavaScript i usługi, uruchamiając go w różnych regionach platformy Azure jest hostowaną i zarządzaną przez platformę Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Moje Azure przepustowości zwiększy podczas używania pomiarów rzeczywistego użytkownika?
Jak wspomniano w poprzedniej odpowiedzi, składniki po stronie serwera pomiarów rzeczywistego użytkownika są własnością i zarządzane przez platformę Azure. Oznacza to, że nie zwiększy wykorzystanie przepustowości platformy Azure, ponieważ używasz pomiarów rzeczywistego użytkownika. Nie ma żadnych przepustowości poza jakie opłaty usługi Azure. Firma Microsoft zminimalizować przepustowość wykorzystywaną przez pobieranie tylko obraz piksela do pomiaru czas oczekiwania na region platformy Azure. 

## <a name="traffic-view"></a>Widok ruchu

### <a name="what-does-traffic-view-do"></a>Do czego służy widok ruchu?
Widok ruchu jest funkcją usługi Traffic Manager, który pomoże Ci dowiedzieć się więcej o użytkowników i czym się ich środowisko pracy. Używa zapytania odebrane przez usługi Traffic Manager i tabel analizy opóźnienia sieci, które usługa zapewnia do przedstawienia następujące czynności:
- Regiony, z której użytkownicy nawiązywane jest połączenie z punktami końcowymi usługi platformy Azure.
- Wolumin użytkowników łączących się z tych regionów.
- Regiony platformy Azure, do których są wprowadzenie kierowane do.
- Środowiska opóźnienia w tych regionach platformy Azure.

Te informacje są dostępne dzięki mapie geograficznej nakładki i widoków tabelarycznych w portalu są dostępne jako nieprzetworzone dane do pobrania oraz.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mogę korzystać z widok ruchu

Widok ruchu pozwala ogólny widok ruchu, otrzymany profilów usługi Traffic Manager. W szczególności może służyć, aby zrozumieć gdzie podstawa użytkownik łączy się z poziomu, i równie ważne jest ich doświadczeń Średni czas oczekiwania. Te informacje można następnie umożliwia znajdowanie obszarów, w których należy skoncentrować się na przykład, rozwijając swoje zasięgiem platformy Azure w regionie, który może służyć tych użytkowników z mniejszymi opóźnieniami. Szczegółowe informacje o innym mogą pochodzić z przy użyciu widoku ruchu jest, aby zobaczyć wzorców ruchu do różnych regionów, które z kolei może ułatwić podjęcie decyzji na zwiększenie lub zmniejszenie wymyślić w tych regionach.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Czym różni się widok ruchu z dostępnych za pośrednictwem usługi Azure monitor metryk usługi Traffic Manager?

Usługa Azure Monitor może służyć do zrozumienia na poziomie agregacji ruchu odbieranego przez Twojego profilu i jego punkty końcowe. W tym obszarze pozwala również śledzić stan kondycji punktów końcowych, zapewniając wyniki sprawdzania kondycji. Jeśli potrzebujesz czegoś więcej niż te i zrozumienie środowisko użytkownika końcowego, łączenie z platformą Azure na poziomie regionalnym, widok ruchu można osiągnąć.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Widok ruchu używa informacji o podsieci klienta mechanizmów EDNS?

Zapytania DNS, obsługiwane przez usługę Azure Traffic Manager, należy wziąć pod uwagę ECS informacje, aby zwiększyć dokładność routingu. Jednak po utworzeniu zestawu danych, który pokazuje, gdzie użytkownicy łączą się na widok ruchu przy użyciu tylko adresu IP programu rozpoznawania nazw DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Ile dni dla danych używa widok ruchu?

Widok ruchu tworzy dane wyjściowe za przetwarzanie danych z siedmiu dni poprzedzających dzień przed widzianego przez użytkownika. To okno ruchome i najnowsze dane będą używane przy każdym razem, gdy użytkownik odwiedza.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak widok ruchu obsługuje zewnętrzne punkty końcowe?

Gdy używasz zewnętrzne punkty końcowe umieszczonego poza regionów platformy Azure w profilu usługi Traffic Manager można jest mapowany do regionu platformy Azure, która jest serwer proxy dla jego właściwości opóźnienia (jest to w rzeczywistości potrzebne, jeśli używasz metody routingu opartego na wydajności). Jeśli ma to mapowanie region platformy Azure, metryki opóźnienia tego regionu platformy Azure będą używane podczas tworzenia danych wyjściowych widok ruchu. Jeśli określono nie regionu platformy Azure, informacje opóźnienia jest pusta w danych dla tych zewnętrzne punkty końcowe.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Czy muszę włączyć funkcję Widok ruchu dla każdego profilu w mojej subskrypcji?

W okresie zapoznawczym widok ruchu została włączona na poziomie subskrypcji. Jako część ulepszeń, które wprowadziliśmy przed ogólnie dostępne można teraz włączyć funkcję Widok ruchu na poziomie profilu, dzięki czemu możesz mieć bardziej szczegółowego włączenie tej funkcji. Domyślnie widok ruchu zostanie wyłączony dla profilu.

>[!NOTE]
>Jeśli włączono funkcję Widok ruchu na poziomie subskrypcji w czasie (wersja zapoznawcza), teraz należy ją ponownie włączyć dla każdego profilu w ramach tej subskrypcji.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak można wyłączyć funkcję Widok ruchu? 
Możesz wyłączyć funkcję Widok ruchu dla dowolnego profilu za pomocą portalu lub interfejsu API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Jak działa rozliczanie widok ruchu

Ruch Wyświetl cennik opiera się na liczbę punktów danych, które umożliwia utworzenie danych wyjściowych. Obecnie typ jedyny obsługiwany jest zapytań, które otrzymuje swój profil. Ponadto możesz ponosić dodatkowe opłaty jedynie do przetwarzania, która została wykonana, jeśli masz włączoną funkcję Widok ruchu. Oznacza to, że jeśli Włącz widok ruchu przez pewien czas w danym miesiącu, a następnie ją wyłączyć w pozostałym czasie, tylko te punkty danych, które są przetwarzane, gdy trzeba było funkcji liczba z włączoną na rachunku.

## <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Za pomocą punktów końcowych z wieloma subskrypcjami można używać usługi Traffic Manager?

Za pomocą punktów końcowych z wieloma subskrypcjami nie jest możliwe dzięki usłudze Azure Web Apps. Usługa Azure Web Apps wymaga, że dowolną nazwę domeny niestandardowej, wykorzystywanych z aplikacjami sieci Web jest używany tylko w ramach jednej subskrypcji. Nie jest możliwe użycie aplikacji sieci Web z wieloma subskrypcjami, z tą samą nazwą domeny.

Dla innych typów punktu końcowego jest możliwość użycia usługi Traffic Manager za pomocą punktów końcowych z więcej niż jedną subskrypcję. W usłudze Resource Manager, punkty końcowe z każdej subskrypcji można dodać do usługi Traffic Manager, tak długo, jak osoby, Konfigurowanie profilu usługi Traffic Manager ma dostęp do odczytu do punktu końcowego. Te uprawnienia można przyznać za pomocą [kontroli dostępu opartej na rolach usługi Azure Resource Manager (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Gniazda "Staging" usługi w chmurze można używać usługi Traffic Manager?

Tak. "Staging" miejsca usługi w chmurze można skonfigurować w usłudze Traffic Manager jako zewnętrzne punkty końcowe. Kontrole kondycji są nadal naliczane opłaty punkty końcowe platformy Azure. Ponieważ typ zewnętrzny punkt końcowy jest używany, zmiany w usłudze podstawowej nie są pobierane automatycznie. Za pomocą zewnętrznych punktów końcowych usługa Traffic Manager nie może wykryć po zatrzymaniu lub usunięciu usługi w chmurze. W związku z tym usługi Traffic Manager nadal rozliczeń dla kontroli kondycji, dopóki punkt końcowy jest wyłączona lub usunięta.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Usługa Traffic Manager obsługuje punktów końcowych protokołu IPv6?

Usługa Traffic Manager nie zapewnia obecnie adresy IPv6 serwerów nazw. Jednak usługa Traffic Manager można jej użyć protokołu IPv6 klientom nawiązywanie połączenia z punktów końcowych protokołu IPv6. Klient nie powoduje żądania DNS bezpośrednio do usługi Traffic Manager. Zamiast tego klient korzysta z rekursywnych usług DNS. Tylko protokół IPv6 klienta wysyła żądania do rekursywnych usług DNS za pomocą protokołu IPv6. Następnie usługa cykliczne, powinno być możliwe do kontaktowania się z serwerami nazw usługi Traffic Manager przy użyciu protokołu IPv4.

Usługa Traffic Manager odpowiada za pomocą nazwy DNS lub adres IP punktu końcowego. Aby zapewnić obsługę punktu końcowego protokołu IPv6, dostępne są dwie opcje. Możesz dodać punkt końcowy jako nazwy DNS, który ma skojarzony rekord AAAA i Traffic Manager będzie sprawdzenie kondycji, ten punkt końcowy i zwracany typ go jako rekord CNAME w odpowiedzi na zapytanie. Można również dodać punkt końcowy bezpośrednio przy użyciu adresu IPv6 i usługi Traffic Manager zwróci rekord AAAA typu w odpowiedzi na zapytanie. 

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Czy można używać usługi Traffic Manager, za pomocą więcej niż jednej aplikacji sieci Web, w tym samym regionie?

Zazwyczaj usługa Traffic Manager jest używany do kierowania ruchu do aplikacji wdrożonych w różnych regionach. Jednak może również służyć, którym aplikacja ma więcej niż jedno wdrożenie w tym samym regionie. Punkt końcowy usługi Traffic Manager platformy Azure nie zezwalają na więcej niż jeden punkt końcowy aplikacji sieci Web z tego samego regionu systemu Azure, które mają zostać dodane do tego samego profilu usługi Traffic Manager.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Jak przenieść punkty końcowe platformy Azure na mój profil usługi Traffic Manager do innej grupy zasobów?

Punkty końcowe platformy Azure, które są skojarzone z profilem usługi Traffic Manager są śledzone, przy użyciu ich identyfikatorów zasobów. Gdy zasobów platformy Azure, który jest używany jako punkt końcowy (na przykład publiczny adres IP, klasyczne usługi w chmurze, aplikacji sieci Web lub innego profilu usługi Traffic Manager w sposób zagnieżdżony) jest przenoszony do innej grupy zasobów, jego zmiany Identyfikatora zasobu. W tym scenariuszu aktualnie, należy zaktualizować profilu usługi Traffic Manager wcześniejszym usunięciu, a następnie dodając ponownie punkty końcowe do profilu. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Jest odporna na awarie regionu świadczenia usługi Azure Traffic Manager?

Traffic Manager to kluczowy składnik dostarczania aplikacji o wysokiej dostępności na platformie Azure.
Aby zapewnić wysoką dostępność, usługa Traffic Manager jest posiadanie wyjątkowo wysoki poziom dostępności oraz była odporna na awarie regionalne.

Zgodnie z projektem składników usługi Traffic Manager są odporne na całkowitej awarii dowolnego regionu systemu Azure. Odporność to ma zastosowanie do wszystkich składników usługi Traffic Manager: nazwa DNS, serwery, interfejsu API, warstwa magazynu i punktu końcowego usługi monitorowania.

W przypadku mało prawdopodobnego wystąpienia awarii całego regionu świadczenia usługi Azure Traffic Manager oczekuje się, w dalszym ciągu działać normalnie. Aplikacje wdrożone w wielu regionach platformy Azure można polegać w usłudze Traffic Manager kierować ruch do dostępnego wystąpienia swojej aplikacji.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak wybór lokalizacji grupy zasobów wpłynie na usługi Traffic Manager?

Usługa Traffic Manager to usługa pojedynczego, globalnego. Nie jest regionalne. Wybór lokalizacji grupy zasobów nie ma wpływu na profile usługi Traffic Manager wdrożonych w danej grupie zasobów.

Usługa Azure Resource Manager wymaga wszystkich grup zasobów określić lokalizację, która określa domyślną lokalizację dla zasobów wdrożonych w danej grupie zasobów. Podczas tworzenia profilu usługi Traffic Manager zostanie utworzony w grupie zasobów. Użyj wszystkich profilów usługi Traffic Manager **globalnego** jako ich lokalizacji, zastąpienie domyślnej grupy zasobów.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak ustalić bieżącą kondycję każdego punktu końcowego?

Bieżący stan monitorowania każdego punktu końcowego, oprócz ogólny profil jest wyświetlany w witrynie Azure portal. Te informacje są również dostępne za pośrednictwem Monitora ruchu [interfejsu API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager), i [interfejsu wiersza polecenia dla wielu platform Azure](../cli-install-nodejs.md).

Usługa Azure Monitor umożliwia również śledzenie kondycji punktów końcowych i zobaczyć graficzną reprezentację z nich. Aby uzyskać więcej informacji o korzystaniu z usługi Azure Monitor, zobacz [dokumentacja usługi Azure Monitoring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Czy można monitorować punktów końcowych HTTPS?

Tak. Usługa Traffic Manager obsługuje sondowania przy użyciu protokołu HTTPS. Konfigurowanie **HTTPS** jako protokół monitorowania konfiguracji.

Usługa Traffic manager nie może dostarczyć wszelkie weryfikację certyfikatu, w tym:

* Certyfikaty po stronie serwera nie są weryfikowane.
* Certyfikaty po stronie serwera SNI nie są weryfikowane.
* Certyfikaty klienta nie są obsługiwane.

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Używać adresu IP lub nazwy DNS podczas dodawania punktu końcowego?
Usługa Traffic Manager obsługuje dodawanie punktów końcowych, za pomocą trzy sposoby, aby znaleźć je — jako nazwy DNS, jako adres IPv4, a jako adres IPv6. Jeśli punkt końcowy został dodany jako adres IPv4 lub IPv6 odpowiedzi na zapytanie będzie typ rekordu A lub AAAA, odpowiednio. Jeśli punkt końcowy został dodany jako nazwa DNS, to odpowiedź na zapytanie będą typu rekordu CNAME. Dodawanie punktów końcowych, ponieważ adres IPv4 lub IPv6 jest dozwolone tylko wtedy, gdy punkt końcowy jest typu **zewnętrznych**.
Wszystkie metody routingu oraz ustawień monitorowania są obsługiwane przez trzy typy adresowania punktów końcowych.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jakie typy adresów IP można użyć podczas dodawania punktu końcowego?
Traffic Manager umożliwia określanie punktów końcowych przy użyciu adresów IPv4 lub IPv6. Istnieje kilka ograniczeń, które są wymienione poniżej:
- Adresy, które odpowiadają zarezerwowanych prywatnych przestrzeni adresów IP nie są dozwolone. Te adresy obejmują przywołane w dokumencie RFC 1918, RFC 6890, RFC 5737, RFC 3068 RFC 2544 i RFC 5771
- Adres nie może zawierać żadnych numerów portów (można określić porty, które mają zostać użyte w ustawieniach profilu konfiguracji) 
- Nie dwa punkty końcowe, w tym samym profilu może mieć ten sam docelowy adres IP

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Można użyć innego punktu końcowego adresowania typów w jednym profilu?
Nie, usługi Traffic Manager umożliwia mieszanie adresowania typy punktów końcowych w profilu, z wyjątkiem w przypadku profilu z wieloma wartościami typu routingu, gdzie można łączyć IPv4 i typy adresów IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co się stanie, gdy typ rekordu przychodzące zapytania jest inny niż typ rekordu, skojarzone z typem adresowania punkty końcowe?
Po odebraniu zapytania względem profilu usługi Traffic Manager umożliwia znalezienie najpierw, punkt końcowy, który musi być zwracane zgodnie z określonej metody routingu i stan kondycji punktów końcowych. Następnie przeszukuje na żądany w zapytaniu przychodzących typ rekordu i typ rekordu, skojarzone z punktem końcowym przed zwróceniem odpowiedzi na podstawie poniższej tabeli.

Dla profilów przy użyciu dowolnej metody routingu w innych niż atrybut wielowartościowy elementu:

|Przychodzące żądania zapytania|    Typ punktu końcowego|  Podany odpowiedź|
|--|--|--|
|ANY |  A / AAAA / CNAME |  Docelowy punkt końcowy| 
|A |    A / CNAME | Docelowy punkt końcowy|
|A |    AAAA |  NODATA |
|AAAA | AAAA / CNAME |  Docelowy punkt końcowy|
|AAAA | A | NODATA |
|CNAME |    CNAME | Docelowy punkt końcowy|
|CNAME  |A / AAAA | NODATA |
|

Dla profilów za pomocą metody routingu opartego na ustawić atrybut wielowartościowy elementu:

|Przychodzące żądania zapytania|    Typ punktu końcowego | Podany odpowiedź|
|--|--|--|
|ANY |  Kombinacja A i AAAA | Miejsce docelowe punktów końcowych|
|A |    Kombinacja A i AAAA | Tylko docelowych punktów końcowych, wpisz|
|AAAA   |Kombinacja A i AAAA|     Tylko docelowych punktów końcowych typu AAAA|
|CNAME |    Kombinacja A i AAAA | NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Można używać profilu z połączeniami IPv4 / IPv6 rozwiązane punktów końcowych w profilu zagnieżdżone?
Tak, z wyjątkiem, że profil typu atrybut wielowartościowy elementu nie może być profil nadrzędnego w profilu zagnieżdżone skonfigurowaniem.


### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Zatrzymano usługi w chmurze platformy Azure / sieci web punktu końcowego aplikacji w moim profilu usługi Traffic Manager, ale nie otrzymuję cały ruch nawet w przypadku, gdy I ponownie uruchomiony. Jak można to naprawić?

Gdy platformy Azure, usługa w chmurze / sieci web punktu końcowego aplikacji jest zatrzymana zatrzymanie usługi Traffic Manager sprawdzenia jej kondycji, a następnie ponownie uruchamia kontrole kondycji, tylko wtedy, gdy wykryje, że uruchomieniu punktu końcowego. Aby uniknąć tego opóźnienia, wyłącz i ponownie włączyć punkt końcowy w profilu usługi Traffic Manager, po ponownym uruchomieniu punktu końcowego.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Usługa Traffic Manager można używać nawet, jeśli Moja aplikacja nie ma obsługę protokołu HTTP lub HTTPS?

Tak. Można określić TCP jako protokół monitorowania i zainicjować połączenie TCP oraz oczekiwania na odpowiedź z punktu końcowego usługi Traffic Manager. Jeśli punkt końcowy odpowiada na żądanie połączenia przy użyciu odpowiedzi, aby nawiązać połączenie przed upływem limitu czasu, punkt końcowy jest oznaczony jako w dobrej kondycji.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jakie określone odpowiedzi są wymagane z punktu końcowego w przypadku, gdy za pomocą funkcji monitorowania TCP?

W przypadku protokołu TCP monitorowania usługi Traffic Manager rozpoczyna się TCP trójstopniowego polega na wysyłaniu żądania SYN do punktu końcowego na określonym porcie. Następnie czeka na okres (jak określono w ustawieniach limitu czasu) na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowie na żądanie SYN przy użyciu SYN ACK odpowiedzi przed upływem limitu czasu określonego w ustawieniach monitorowania, punkt końcowy będzie traktowany dobrej kondycji. Jeśli zostanie odebrana odpowiedź SYN potwierdzenia, usługi Traffic Manager resetuje połączenie odpowiedzi z powrotem RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak szybko usługi Traffic Manager może przenieść Moi użytkownicy w złej kondycji punktu końcowego?

Usługa Traffic Manager udostępnia wiele ustawień, które mogą pomóc Ci kontrolować zachowanie trybu failover w profilu usługi Traffic Manager w następujący sposób:
- można określić, że usługi Traffic Manager sondy punktów końcowych częściej przez ustawienie interwału sondowania na 10 sekund. Dzięki temu, jak najszybciej wykryto dowolnego punktu końcowego, przechodząc w złej kondycji. 
- można określić, jak długo czekać przed kondycji Sprawdź czas żądania (wartość minimalna limit czasu to 5 s).
- można określić, jak wiele błędów może wystąpić, zanim punkt końcowy jest oznaczony jako w złej kondycji. Ta wartość może być niski jako 0, w którym przypadek punkt końcowy jest oznaczony złej kondycji, tak szybko, jak nie powiedzie się pierwsze sprawdzenie kondycji. Jednak przy użyciu minimalna wartość 0 dla tolerowana liczba niepowodzeń może prowadzić do punktów końcowych jest wykluczana z rotacji ze względu na problemy przejściowe, które mogą wystąpić w czasie badania.
- można określić czasu wygaśnięcia (TTL) to odpowiedź DNS do być możliwie jak 0. Robi oznacza, że rozpoznawania nazw DNS nie można buforować odpowiedzi i każde nowe zapytanie pobiera odpowiedź, która zawiera najbardziej aktualne informacje kondycji, który ma usługi Traffic Manager.

Za pomocą tych ustawień, usługa Traffic Manager może zapewnić przejścia w tryb failover w obszarze po 10 sekundach od punktu końcowego przechodzi w złej kondycji i zapytanie DNS jest wykonywane względem odpowiedni profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu

Usługa Traffic Manager są ustawienia monitorowania na poziomie profilu. Jeśli potrzebujesz użyć innego ustawienia monitorowania dla tylko jeden punkt końcowy, może to nastąpić przez punkt końcowy jako [zagnieżdżone profilu](traffic-manager-nested-profiles.md) którego ustawienia monitorowania różnią się od profilu nadrzędnej.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak można przypisać nagłówków HTTP do usługi Traffic Manager kontroli kondycji moje punkty końcowe?
Usługa Traffic Manager umożliwia określenie niestandardowych nagłówków w tym HTTP (S) kontroli kondycji, inicjuje go do punktów końcowych. Jeśli chcesz określić niestandardowy nagłówek, możesz to zrobić na poziomie profilu (ma zastosowanie do wszystkich punktów końcowych) lub je określić na poziomie punktu końcowego. Jeśli nagłówek jest zdefiniowany na obu poziomach, określona na poziomie punktu końcowego spowoduje przesłonięcie poziomu profile, jeden.
Jeden typowy przypadek użycia tego jest określenie nagłówki hosta tak, aby żądania usługi Traffic Manager może uzyskać prawidłowo kierowane do punktu końcowego usługi hostowanej w środowisku wielodostępnym. Innym przypadku użycia tego jest zidentyfikowanie żądania usługi Traffic Manager z dzienników żądania HTTP (S) punktu końcowego

## <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakie kondycja punktu końcowego nagłówka hosta sprawdza, czy użycie?
Jeśli nie podano żadnego ustawienia nagłówka niestandardowego hosta, nagłówek hosta, używane przez usługę Traffic Manager jest nazwą DNS skonfigurowanych w profilu, obiektu docelowego punktu końcowego, jeśli jest dostępny. 


### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jakie są adresy IP, z których pochodzą kontroli kondycji?

Kliknij przycisk [tutaj](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) do wyświetlenia pliku JSON, który zawiera listę adresów IP, z której usługi Traffic Manager mogą pochodzić kontrole kondycji. Przejrzyj wymienione w pliku JSON adresy IP na potrzeby upewnij się, że mogą przyjmować połączenia przychodzące z tych adresów IP w punktach końcowych sprawdzić jego stan kondycji.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Ile kontrole kondycji do mojego punktu końcowego można oczekiwać od usługi Traffic Manager?

Liczba kondycji usługi Traffic Manager sprawdza, czy docieranie do punktu końcowego usługi jest zależna od następujących czynników:
- wartość ustawioną dla interwału monitorowania (mniejszy przedział oznacza liczby żądań kierowanych na punkt końcowy usługi w dowolnym danym okresie czasu).
- Liczba lokalizacji, z którego pochodzą przez kontrole kondycji (adresy IP, z których można oczekiwać, że te sprawdzenia znajduje się w poprzednim często zadawane pytania).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak można otrzymywać powiadomienia jeśli ulegnie awarii jednej z moich punkty końcowe? 
Jednym z metryki udostępniane przez usługę Traffic Manager jest stan kondycji punktów końcowych w profilu. Można to zobaczyć jak zagregowanych, wszystkie punkty końcowe w ramach profilu (na przykład 75% punktów końcowych są w dobrej kondycji), lub, co na poziomie punktu końcowego. Metryki usługi Traffic Manager są udostępniane za pośrednictwem usługi Azure Monitor i używać jej [alertów](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) Aby otrzymywać powiadomienia, gdy nie nastąpiła zmiana stanu kondycji punktu końcowego usługi. Aby uzyskać więcej informacji, zobacz [alerty i metryki usługi Traffic Manager](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Usługa Traffic Manager zagnieżdżone profile

### <a name="how-do-i-configure-nested-profiles"></a>Jak skonfigurować profile zagnieżdżone

Zagnieżdżone profile usługi Traffic Manager można skonfigurować przy użyciu usługi Azure Resource Manager i klasycznym Azure interfejsów API REST, poleceń cmdlet programu Azure PowerShell i polecenia wiersza polecenia platformy Azure dla wielu platform. Są one również obsługiwane za pomocą nowej witryny Azure portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Obsługuje liczbę warstw zagnieżdżenia ma Traffic Manager?

Można zagnieżdżać profile maksymalnie 10 poziomów w głąb. "W pętli" są niedozwolone.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Czy można łączyć inne typy punktów końcowych, przy użyciu profilów zagnieżdżonych elementów podrzędnych, w tym samym profilu usługi Traffic Manager?

Tak. Nie ma żadnych ograniczeń, w jak połączyć punktów końcowych o różnych typach w profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak stosuje model rozliczeń dla profile zagnieżdżone

Istnieje nie ujemna, cennik wpływ przy użyciu profilów zagnieżdżonych.

Rozliczanie usługi Traffic Manager ma dwa składniki: kontrole kondycji punktów końcowych i miliony zapytań DNS

* Kontrole kondycji punktów końcowych: Nie ma opłat dla profilu podrzędnych, gdy usługa skonfigurowany jako punkt końcowy w profilu nadrzędnej. Monitorowanie punktów końcowych w profilu podrzędnej jest rozliczana w zwykły sposób.
* Zapytania DNS: Każde zapytanie są traktowane jako jeden raz. Zapytanie profil nadrzędnego, który zwraca punktu końcowego z profilu podrzędnej jest przeliczane względem nadrzędnego tylko w przypadku profilu.

Aby uzyskać szczegółowe informacje, zobacz [usługi Traffic Manager stronę z cennikiem](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Ma wpływ na wydajność dla profilów zagnieżdżonych?

Nie. Brak wpływu na wydajność ponoszony, gdy użycie profilów zagnieżdżonych.

Serwery nazw usługi Traffic Manager przechodzenie przez hierarchię profilu wewnętrznie podczas przetwarzania każdego zapytania DNS. Zapytanie DNS do profilu nadrzędnego może odbierać odpowiedzi DNS przy użyciu punktu końcowego z profilu podrzędnych. Pojedynczy rekord CNAME jest używany, czy używasz jeden profil lub profile zagnieżdżone. Nie ma potrzeby utworzyć rekord CNAME dla każdego profilu w hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak usługa Traffic Manager obliczenia kondycji zagnieżdżony punkt końcowy w profilu nadrzędnego?

Profil nadrzędnego nie sprawdzać kondycję w elemencie podrzędnym bezpośrednio. Zamiast tego kondycję profilu podrzędnych punktów końcowych są używane do obliczenia ogólnej kondycji profilu podrzędnych. Te informacje są propagowane w górę hierarchii zagnieżdżonych profilów, które można określić kondycji zagnieżdżony punkt końcowy. Profil nadrzędnego używa ten zagregowaną kondycję, aby ustalić, czy ruch może zostać skierowany do elementu podrzędnego.

W poniższej tabeli opisano zachowanie usługi Traffic Manager kontroli kondycji dla zagnieżdżonych punktu końcowego.

| Stan monitora profilowania podrzędne | Stan monitorowania punktu końcowego nadrzędnej | Uwagi |
| --- | --- | --- |
| Wyłączone. Wyłączono profil podrzędnych. |Zatrzymano |Stan punktu końcowego nadrzędnego jest zatrzymana, nie jest wyłączona. Stan wyłączenia jest zarezerwowana dla wskazujący wyłączono punktu końcowego w profilu nadrzędnej. |
| Negatywny wpływ na dostępność. Co najmniej jeden element podrzędny punktu końcowego z profilu jest w stanie obniżony. |Online: liczba Online punktów końcowych w profilu podrzędnej jest co najmniej wartość elementu MinChildEndpoints.<BR>CheckingEndpoint: liczba Online, a także CheckingEndpoint punktów końcowych w profilu podrzędnych jest co najmniej wartość elementu MinChildEndpoints.<BR>Negatywny wpływ na dostępność: w przeciwnym razie. |Ruch jest kierowany do punktu końcowego stanu CheckingEndpoint. Jeśli MinChildEndpoints zostanie ustawiona zbyt wysoka, punkt końcowy zawsze ma obniżoną wydajność. |
| Online. Punktu końcowego profilu co najmniej jeden element podrzędny jest w stanie Online. Nie ma punktu końcowego jest w stanie obniżony. |Zobacz powyżej. | |
| CheckingEndpoints. Punktu końcowego profilu co najmniej jeden element podrzędny jest "CheckingEndpoint". Brak punktów końcowych są "Online" lub "Negatywny wpływ na dostępność" |Wartość taka sama jak powyżej. | |
| Nieaktywne. Profil wszystkich podrzędnych punktów końcowych są wyłączone lub zatrzymana lub ten profil nie ma punktów końcowych. |Zatrzymano | |

## <a name="next-steps"></a>Następne kroki:
- Dowiedz się więcej o usłudze Traffic Manager [punktu końcowego monitorowaniu i automatycznemu trybu failover](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o usłudze Traffic Manager [metody routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md).
