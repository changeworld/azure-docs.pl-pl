---
title: Menedżer ruchu Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kumud
ms.openlocfilehash: 18a0ca32f51e6c1be01e59c3899bc2e625868cad
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Często zadawane pytania (FAQ) Menedżera ruchu

## <a name="traffic-manager-basics"></a>Podstawowe informacje dotyczące Menedżera ruchu

### <a name="what-ip-address-does-traffic-manager-use"></a>Jakiego adresu IP używa Menedżera ruchu?

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), działanie Menedżera ruchu polega na poziomie DNS. Wysyła odpowiedzi DNS, aby kierować klientów do punktu końcowego odpowiednią usługę. Klienci następnie połączyć się z punktem końcowym usługi bezpośrednio, nie za pomocą Menedżera ruchu.

W związku z tym menedżera ruchu nie zawiera punktu końcowego lub adres IP dla klientów nawiązać połączenie. Jeśli ma statyczny adres IP dla usługi, które muszą być skonfigurowane na usługę, a nie w usłudze Traffic Manager.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager obsługuje "trwałe" sesje?

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), działanie Menedżera ruchu polega na poziomie DNS. Wykorzystuje odpowiedzi DNS, aby kierować klientów do punktu końcowego odpowiednią usługę. Klienci łączą się z punktem końcowym usługi bezpośrednio, nie za pomocą Menedżera ruchu. W związku z tym Menedżer ruchu nie widzi ruch HTTP między klientem a serwerem.

Ponadto źródłowy adres IP zapytanie DNS odebranych przez usługę Traffic Manager należy do usługi DNS cykliczne nie klienta. W związku z tym usługi Traffic Manager nie ma możliwości śledzenia poszczególnych klientów i nie może implementować "trwałe" sesji. To ograniczenie jest wspólny dla wszystkich systemów zarządzania systemem DNS ruch i nie jest specyficzne dla Menedżera ruchu.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Dlaczego widzę błąd HTTP podczas korzystania z Menedżera ruchu?

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), działanie Menedżera ruchu polega na poziomie DNS. Wykorzystuje odpowiedzi DNS, aby kierować klientów do punktu końcowego odpowiednią usługę. Klienci następnie połączyć się z punktem końcowym usługi bezpośrednio, nie za pomocą Menedżera ruchu. Menedżer ruchu nie można znaleźć ruchu HTTP między klientem i serwerem. W związku z tym błędu HTTP widocznej musi pochodzić z aplikacji. Dla klienta w celu połączenia się z aplikacją zakończeniu wszystkich kroków rozpoznawania DNS. Zawierającej interakcji z Menedżera ruchu na przepływie ruchu aplikacji.

Dalsza analiza w związku z tym należy skoncentrować się na aplikacji.

Nagłówek hosta HTTP wysyłane z przeglądarki klienta jest źródłem najbardziej typowych problemów. Upewnij się, że aplikacja jest skonfigurowana do akceptowania nagłówek hosta poprawny dla nazwy domeny, którego używasz. Dla punktów końcowych przy użyciu usługi Azure App Service, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu Menedżera ruchu](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Co to jest jego wpływ na wydajność przy użyciu Menedżera ruchu?

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), działanie Menedżera ruchu polega na poziomie DNS. Ponieważ klienci łączą się z punktami końcowymi usługi bezpośrednio, nie jest bez wpływu na wydajność poniesionych podczas korzystania z Menedżera ruchu, po nawiązaniu połączenia.

Ponieważ usługi Traffic Manager integruje się z aplikacjami na poziomie DNS, jest wymagane dodatkowe wyszukiwania DNS, który ma zostać wstawiony do łańcucha rozpoznawania DNS. Wpływ Traffic Manager na czas rozpoznawania nazw DNS jest minimalny. Menedżer ruchu korzysta globalnej sieci serwerów nazw, a [emisji](https://en.wikipedia.org/wiki/Anycast) sieci w celu zapewnienia DNS zapytania zawsze są kierowane do najbliższego serwera dostępnych nazw. Ponadto buforowanie odpowiedzi DNS oznacza, że dodatkowe DNS opóźnień przy użyciu Menedżera ruchu dotyczy tylko część sesji.

Metoda wydajności kieruje ruchem do najbliższego dostępnego punktu końcowego. Wynikiem jest, że wpływ ogólną wydajność skojarzonych z tą metodą powinien być minimalny. Wzrost opóźnienia DNS powinien być przesunięty mniejsze opóźnienia sieci do punktu końcowego.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jakie protokoły aplikacji można używać Menedżera ruchu?

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), działanie Menedżera ruchu polega na poziomie DNS. Po wykonaniu wyszukiwania DNS klienci łączą się z punktem końcowym aplikacji bezpośrednio, nie za pomocą Menedżera ruchu. W związku z tym połączenie można użyć dowolnego protokołu aplikacji. Jeśli wybierzesz TCP jako monitorowania protokołu, Menedżera ruchu monitorowania kondycji punktu końcowego mogą być przeprowadzane bez przy użyciu protokołów aplikacji. Jeśli wybierzesz opcję kondycji zweryfikowana przy użyciu protokołu aplikacji, punkt końcowy musi być mogą odpowiadać na żądania HTTP lub HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Nazwa domeny "naked" można używać Menedżera ruchu?

Nie. Standardy usługi DNS nie zezwalają na CNAME współistnieć z inne rekordy DNS o takiej samej nazwie. Szczytu (lub głównego) strefy DNS zawsze zawiera dwa rekordy DNS, istniejące; SOA i autorytatywne rekordy NS. Oznacza to, że rekord CNAME nie można utworzyć w wierzchołku strefy bez naruszania standardy usługi DNS.

Menedżer ruchu wymaga rekordu CNAME systemu DNS do mapowania nazwy DNS niestandardowych. Na przykład należy mapować www.contoso.com contoso.trafficmanager.net nazwy DNS profilu Menedżera ruchu. Ponadto profilu usługi Traffic Manager zwraca drugi rekordu CNAME systemu DNS w celu wskazania, który punkt końcowy klient ma nawiązać.

Aby obejść ten problem, firma Microsoft zaleca używanie przekierowania HTTP do bezpośredniego ruchu na podstawie nazwy domeny bez do innego adresu URL, którego można następnie użyć Menedżera ruchu. Na przykład naked domeny "contoso.com" może przekierować użytkowników do CNAME "www.contoso.com", który wskazuje nazwę DNS Menedżera ruchu.

Pełna obsługa naked domen w usłudze Traffic Manager są śledzone w naszym zaległości funkcji. Możesz zarejestrować programu obsługi dla tego żądania funkcji przez [głosowaniu w naszej witrynie opinii społeczności](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Menedżer ruchu za adres podsieci klienta podczas obsługi zapytań DNS? 
Tak, oprócz źródłowy adres IP zapytanie DNS odbiera (czyli zazwyczaj adresu IP program rozpoznawania nazw DNS), podczas przeprowadzania wyszukiwania dla metody routingu Geographic i wydajności, usługi traffic manager również uwzględnia adres podsieci klienta, jeśli jest uwzględnione w zapytaniu przez mechanizm rozpoznawania zgłoszenia żądania w imieniu użytkownika końcowego.  
W szczególności [RFC 7871 — podsieci klienta zapytań DNS](https://tools.ietf.org/html/rfc7871) zapewnia [mechanizmu rozszerzenie dla serwera DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) które można przekazać na adres podsieci klienta z rozpoznawania nazw, które obsługują.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co to jest czas wygaśnięcia DNS i jak ma ona wpływu na użytkowników?

Jeśli zapytanie DNS wyładowuje w usłudze Traffic Manager, ustawia wartość w odpowiedzi o nazwie czas wygaśnięcia (TTL). Tej wartości, którego jednostka jest w sekundach, wskazuje do rozpoznawania nazw DNS downstream na jak długo buforować odpowiedź. Podczas rozpoznawania nazw DNS nie ma gwarancji pamięci podręcznej tego wyniku, buforowanie ich umożliwia im odpowiadanie na wszystkie kolejne kwerendy poza pamięci podręcznej, zamiast do serwerów DNS Menedżera ruchu. Dotyczy to odpowiedzi w następujący sposób:
- wyższa wartość TTL zmniejsza liczbę zapytań, które na serwery DNS Menedżera ruchu, które można zmniejszyć koszt dla odbiorcy, ponieważ liczba zapytań, obsługiwane jest użycie rozliczeniowy.
- wyższa wartość TTL potencjalnie może skrócić czas potrzebny do wyszukiwania DNS.
- wyższe wartości TTL oznacza także, czy dane nie są widoczne najnowsze informacje o kondycji uzyskiwanej Menedżera ruchu za pośrednictwem jego sondowania agentów.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak wysokie lub niskie można ustawić czas TTL odpowiedzi Menedżera ruchu?

Można ustawić na na poziomie profilu, czas wygaśnięcia DNS być możliwie jak 0 sekund i wysokości 2 147 483 647 sekund (zgodne z maksymalną wielkość zakresu [ze standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). TTL 0 oznacza, że podrzędne rozpoznawania nazw DNS nie buforowanie odpowiedzi na kwerendy, wszystkie zapytania mogą uzyskać dostęp do usługi Traffic Manager w systemie DNS i serwerów rozpoznawania.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metody routingu ruchu Geographic Menedżera ruchu

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Co to są niektórych przypadków użycia, w którym geograficzne routing jest przydatne? 
Geograficzne typ routingu może służyć w jakimkolwiek scenariuszu których Azure klienta wymaga rozróżnienia użytkowników oparte na regionów geograficznych. Na przykład przy użyciu metody routingu ruchu geograficznych, użytkownicy mogli z określonych regionów różnych użytkowników z innych regionów niż. Innym przykładem jest zgodne z oczekuje suwerenności danych lokalnych, wymagających, że użytkownicy z określonego regionu obsługiwane tylko przez punkty końcowe w tym regionie.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Co to są regionów, które są obsługiwane przez usługę Traffic Manager geograficzne routingu? 
Hierarchii kraj/region, jaka jest używana przez usługę Traffic Manager można znaleźć [tutaj](traffic-manager-geographic-regions.md). Gdy ta strona jest aktualizowany o zmianach, można również programowane pobieranie tych samych informacji za pomocą [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Jak usługi traffic manager ustalić, gdzie pyta użytkownika z? 
Menedżer ruchu sprawdza źródłowy adres IP zapytania (prawdopodobnie jest to lokalnego rozpoznawania nazw DNS wykonywania zapytań w imieniu użytkownika) i używa wewnętrznego adresu IP do obszaru mapy w celu określenia lokalizacji. Ta mapa jest aktualizowane na bieżąco, aby uwzględnić zmiany w Internecie. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Jest on gwarantowane czy Menedżera ruchu można prawidłowo określić dokładnej lokalizacji geograficznej użytkownika w każdym przypadku
Nie, Traffic Manager nie może zagwarantować, że region geograficzny, które firma Microsoft wywnioskować z adresu IP źródłowego zapytania DNS zawsze odpowiada lokalizacji użytkownika z następujących powodów: 

- Po pierwsze zgodnie z opisem w poprzedniej odpowiedzi na pytanie, źródłowy adres IP, które przedstawiono jest który program rozpoznawania nazw DNS podczas wyszukiwania w imieniu użytkownika. Lokalizacja geograficzna program rozpoznawania nazw DNS jest dobrym serwera proxy dla lokalizacji geograficznej użytkownika, również można różne w zależności od rozmiaru usługi rozpoznawania nazw DNS i określonych usługa rozpoznawania nazw DNS, który klient ma wybranego do użycia. Na przykład klient znajduje się w Malezji można określić w ich urządzenia należy użyć ustawienia usługi rozpoznawania nazw DNS, których serwer DNS w Singapurze może pobrać wybierany do obsługi rozwiązania zapytania dla tego użytkownika/urządzenie. W takim przypadku Menedżera ruchu może zobaczyć tylko mechanizm rozpoznawania adresu IP, który odpowiada lokalizacji Singapuru. Zobacz też wcześniejszych — często zadawane pytania dotyczące pomocy technicznej adres podsieci klienta na tej stronie.

- Drugie usługi Traffic Manager używa wewnętrznego mapy do wykonywania adres IP do tłumaczenia regionu geograficznego. Ta mapa jest weryfikowane i aktualizowane na bieżąco, aby zwiększyć jego dokładność oraz konto dla charakter zmieniające się przez internet, nadal jest możliwość, że nasze informacje nie są dokładnie reprezentację lokalizację geograficzną wszystkich IP adresy.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Czy punkt końcowy musi się znajdować w tym samym regionie, który jest konfigurowana dla routingu geograficzne? 
Nie, lokalizacja punktu końcowego nakłada żadnych ograniczeń, na których regionach mogą być mapowane do niego. Na przykład punkt końcowy w regionie Azure środkowe stany USA mają wszyscy użytkownicy z Indie skierowane do niego.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Regionów geograficznych można przypisać do punktów końcowych w profil, który nie jest skonfigurowany do czy routingu geograficzne? 

Tak, jeśli metoda routingu profilu nie jest geograficznej, możesz użyć [interfejsu REST API usługi Traffic Manager Azure](https://docs.microsoft.com/rest/api/trafficmanager/) można przypisać regionów geograficznych do punktów końcowych w tym profilu. W przypadku profilów typu routingu-geograficznego ta konfiguracja jest ignorowana. Jeśli zmienisz takiego profilu geograficznego typ routingu w późniejszym czasie, Traffic Manager można użyć tych mapowania.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Dlaczego otrzymuję błąd przy próbie Zmień metodę routingu istniejącego profilu Geographic?

Wszystkie punkty końcowe przy użyciu profilu geograficznego routingu musi być co najmniej jeden region mapowane. Aby przekonwertować istniejącego profilu geograficznego typ routingu, najpierw należy skojarzyć regionach geograficznych, aby wszystkie jego punktów końcowych przy użyciu [interfejsu REST API usługi Traffic Manager Azure](https://docs.microsoft.com/rest/api/trafficmanager/) przed zmianą typu routingu geograficznych. Jeśli za pomocą portalu, należy najpierw usunąć punkty końcowe, zmień metodę routingu profil geograficzny, a następnie dodaj punkty końcowe wraz z ich mapowania regionu geograficznego. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Dlaczego jest zalecane użytkownikom utworzenie zagnieżdżonych profilów zamiast punktów końcowych przy użyciu profilu z włączonym routingiem geograficzne? 

Region można przypisać do tylko jeden punkt końcowy w profilu, jeśli jego przy użyciu geograficzne typu routingu. Jeśli ten punkt końcowy nie jest typem zagnieżdżonym z profilem podrzędnych dołączonych do niego, jeśli ruch tego punktu końcowego, przechodząc w złej kondycji, Menedżer nadal do wysyłania ruchu do niego od alternatywnej nie wysyłać cały ruch nie jest żadnym lepiej. Menedżer ruchu nie nie trybu failover do innego punktu końcowego, nawet w przypadku, gdy region przypisany jest elementem "nadrzędnym" region przypisany do punktu końcowego, który zakończył się zła, (na przykład, jeśli punktu końcowego, który ma Hiszpanii region przestanie zła przejdziemy nie trybu failover do innego punktu końcowego został wybrany region Europy przypisane do niej). Można to zrobić, aby upewnić się, że Menedżer ruchu szanuje granice geograficzne, że klient ma ustawienia w swoim profilu. Aby skorzystać z zalet awarii do innego punktu końcowego, gdy punkt końcowy złej kondycji, zalecane jest przypisania regionów geograficznych zagnieżdżonych profilów z wieloma punktami końcowymi w nim zamiast poszczególnych punktów końcowych. W przypadku niepowodzenia punktu końcowego w profilu zagnieżdżonych elementów podrzędnych ruchu w ten sposób można trybu failover do innego punktu końcowego w ramach tego samego profilu zagnieżdżonych elementów podrzędnych.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Czy istnieją ograniczeń z wersją interfejsu API, który obsługuje ten typ routingu?

Tak, tylko wersja interfejsu API 2017-03-01 i nowszej obsługuje routing geograficzne typu. Starsze wersje interfejsu API nie może służyć do utworzone profile geograficzne typ routingu lub przypisać regionów geograficznych do punktów końcowych. Jeśli starszej wersji interfejsu API jest używany do pobierania profile z subskrypcją platformy Azure, dowolny profil geograficzny typ routingu nie są zwracane. Ponadto przy użyciu starszej wersji interfejsu API dowolny profil zwrócił który ma punkty końcowe z przypisaniem region geograficzny, nie ma jej przypisanie regionu geograficznego pokazano.

## <a name="real-user-measurements"></a>Pomiary dotyczące prawdziwych użytkowników

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jakie są korzyści wynikające ze stosowania rzeczywiste pomiary użytkownika?
Gdy używasz metody routingu wydajności usługi Traffic Manager wybiera najlepsze region platformy Azure dla użytkownika końcowego nawiązać sprawdzając źródłowy adres IP i podsieci klienta mechanizmów EDNS (jeśli przekazano) i obsługuje sprawdzanie go przed analizy opóźnienia sieci usługi. Rzeczywiste pomiary użytkownika podnosi poziom to dla podstawowej użytkownikowi końcowemu, ich korzystania przyczyniają się do tej tabeli opóźnienia Oprócz zapewniania, obejmującej tej tabeli można ją było właściwie sieci użytkownika końcowego, z którym użytkownicy końcowi połączenia z platformą Azure. Prowadzi to do zwiększenia dokładności routingu użytkowników końcowych.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Rzeczywiste pomiary użytkownika można używać z regionami innych niż Azure?
Rzeczywiste pomiary użytkownika miar i raportów dotyczących tylko opóźnienie nawiązać regiony platformy Azure. Jeśli korzystasz z routingu opartego na wydajności z punktami końcowymi hostowanej w regionach innych niż Azure, można nadal korzystać z tej funkcji przez wzrosła opóźnienia informacje o reprezentatywny region platformy Azure został wybrany do skojarzenia z tym punktem końcowym.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Które metody routingu korzysta z rzeczywiste pomiary użytkownika?
Dodatkowe informacje uzyskane za pośrednictwem rzeczywiste pomiary użytkownika mają zastosowanie tylko w przypadku profilów, które należy użyć metody routingu wydajności. Należy pamiętać, że rzeczywiste pomiary użytkownika link jest dostępny ze wszystkich profilów, podczas wyświetlania za pośrednictwem portalu Azure.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Należy włączyć rzeczywiste pomiary użytkownika osobno każdy profil?
Nie, należy ją włączyć raz na subskrypcję, wszystkie informacje opóźnienia mierzony i zgłaszane są dostępne we wszystkich profilach.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak wyłączyć rzeczywiste pomiary użytkownika dla mojej subskrypcji?
Można wyłączyć Naliczanie opłat związanych z rzeczywiste pomiary użytkownika, gdy należy zatrzymać zbieranie i wysyłanie pomiarów opóźnienia wstecz od aplikacji klienta. Na przykład podczas pomiarów JavaScript osadzonych na stronach sieci web, można zatrzymać za pomocą tej funkcji, usuwając JavaScript lub wyłączając wywołania podczas renderowania strony.

Rzeczywiste pomiary użytkownika można również wyłączyć przez usunięcie klucza. Po usunięciu klucza wszelkie pomiary wysyłane do usługi Traffic Manager z tego klucza zostaną odrzucone.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Rzeczywiste pomiary użytkownika można używać z aplikacjami klienckimi niż stron sieci web?
Tak, rzeczywiste pomiary użytkownika zaprojektowano w celu pozyskiwania danych zbieranych za pośrednictwem innego typu klientów użytkownika końcowego. Często zadawane pytania zostaną zaktualizowane obsługiwana pobrać nowe typy aplikacji klienckich.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Ile pomiarów zawsze Moje rzeczywiste pomiary użytkownika włączone strony sieci web jest renderowany?
Rzeczywiste pomiary użytkownika jest używana z pomiaru JavaScript, pod warunkiem, każdy renderowania stron wynikiem sześć pomiarów. Te są następnie przekazywane do usługi Traffic Manager. Należy pamiętać, że naliczane są opłaty za tę funkcję na podstawie liczby pomiarów zgłosił do usługi Traffic Manager. Na przykład jeśli użytkownik nawiguje po swojej stronie sieci Web, gdy są wykonywane pomiary, ale przed jego został zgłoszony, pomiarów nie są uznawane za potrzeby rozliczeń.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Czy istnieje opóźnienie przed uruchomieniem skryptu rzeczywiste pomiary użytkownika w Moja strona sieci Web?
Nie, nie ma żadnego zaprogramowanych opóźnienia przed skrypt zostanie wywołany.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Można użyć tylko regiony platformy Azure do mierzenia skonfigurować rzeczywiste pomiary użytkownika?
Nie, każdy razem, gdy jest wywoływana, skrypt rzeczywiste pomiary użytkownika mierzy zestaw sześciu regiony platformy Azure, określone przez usługę. To ustawienie zmiany między różnymi wywołaniami i gdy wystąpi dużej liczby takich wywołań, pokrycie pomiaru obejmuje w różnych regionach platformy Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Można ograniczyć liczbę pomiarów do określonej liczby?
Odpowiednią wartość JavaScript jest osadzony w ramach swojej stronie sieci Web i są w pełną kontrolę nad do rozpoczęcie i Zakończenie korzystania z niego. Tak długo, jak usługa Menedżer ruchu odbiera żądanie mierzone listy regiony platformy Azure, są zwracane zestawie regionów.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Może wyświetlać pomiary przez aplikację klienta jako część rzeczywiste pomiary użytkownika?
Ponieważ logiki miary jest uruchamiany z aplikacji klienckiej, jesteś w pełnej kontroli z tego, co się stanie w tym wyświetlanie pomiarów opóźnienia. Menedżer ruchu nie raportuje zagregowany widok pomiarów odebrane w kluczu połączony z subskrypcją.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Można zmodyfikować skrypt miar udostępnianych przez Menedżera ruchu?
Podczas kontroli nad co to jest osadzony na stronie sieci web, firma Microsoft zdecydowanie zniechęcić możesz z wprowadzeniem jakichkolwiek zmian do skryptu pomiaru zapewnienie środków i poprawnie raporty opóźnienia.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Będzie można rejestrować dla innych osób klucza używanego z rzeczywiste pomiary użytkownika?
Po osadzeniu skryptu miary do strony sieci web będzie możliwe dla innych osób, skrypt i klucz rzeczywiste pomiary użytkownika (RUM). Jednak ważne jest, aby dowiedzieć się, że ten klucz jest inny niż identyfikator subskrypcji i jest generowany przez Menedżera ruchu ma być używany tylko w tym celu. Znajomość klucz rumu nie wpłynie na bezpieczeństwo Twojego konta platformy Azure.

### <a name="can-others-abuse-my-rum-key"></a>Inne nadużywanie mój klucz rumu?
Jest możliwe w dla innych użytkowników do korzystania z klucza do wysyłania nieprawdziwych informacji do usługi Azure należy pamiętać, że kilka miar niewłaściwy nie spowoduje zmiany routingu, ponieważ jej jest brana pod uwagę wraz z innymi pomiarów się, że Otrzymaliśmy. Jeśli musisz zmienić klucze, można ponownie wygenerować klucz w takim przypadku staje się odrzucone starego klucza.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Należy wprowadzić pomiaru JavaScript w wszystkich stron sieci web?
Rzeczywiste pomiary użytkownika zapewnia większą wartość jako liczbę wzrost pomiarów. Tym niemniej jest określające, czy należy umieścić go w stron sieci web lub select kilka decyzji. Nasze zalecenie jest na początek umieścić go na stronie najczęściej odwiedzanych, w którym użytkownik powinien pozostać na tej stronie pięciu sekund lub więcej.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Informacje o Moi użytkownicy końcowi identyfikację przez usługę Traffic Manager użycie rzeczywiste pomiary użytkownika?
Miary podana JavaScript jest używana, będą mieć wgląd w użytkownik końcowy adres IP klienta i źródłowy adres IP lokalnego rozpoznawania nazw DNS, używanych przez Menedżera ruchu. Menedżer ruchu używa adresu IP klienta tylko po jego obcięte do nie będą mogli zidentyfikować określonego użytkownika końcowego, który wysłał pomiarów. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Czy strona sieci Web pomiaru rzeczywiste pomiary użytkownik musi używać routingu menedżera ruchu?
Nie, nie trzeba za pomocą Menedżera ruchu. Routingu po stronie usługi Traffic Manager działa niezależnie z części prawdziwego pomiaru użytkownika i pomysł do nich obu w tej samej właściwości sieci web, ale nie muszą być.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Należy do obsługi dowolnej usługi na regiony platformy Azure do użycia z rzeczywiste pomiary użytkownika?
Nie, nie trzeba udostępniać wszystkie składniki po stronie serwera na platformie Azure, do rzeczywistych pomiarów użytkownika do pracy. Obraz piksela pobierany przez pomiar JavaScript i uruchomienie jej w różnych regionach platformy Azure jest hostowany i zarządzane przez usługę Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Moje Azure przepustowości zwiększy użycie rzeczywiste pomiary użytkownika?
Jak wspomniano w poprzedniej odpowiedzi, składniki po stronie serwera rzeczywiste pomiary użytkownika są własnością i są zarządzane przez usługę Azure. Oznacza to, że nie spowoduje zwiększenia użycie przepustowości Azure, ponieważ używasz rzeczywiste pomiary użytkownika. Należy pamiętać, że nie ma żadnych przepustowości poza jakiego Azure opłat. Firma Microsoft zminimalizować przepustowość wykorzystywaną przez pobieranie tylko jeden piksel obrazu do pomiaru opóźnienia do regionu platformy Azure. 

## <a name="traffic-view"></a>Widok ruchu

### <a name="what-does-traffic-view-do"></a>Do czego służy widok ruchu?
Widok ruchu jest funkcją Menedżera ruchu, który pomaga w zrozumieniu więcej o użytkowników i sposób ich środowisko jest. Używa kwerend odebranych przez Menedżera ruchu i tabel analizy opóźnienia sieci, które obsługuje usługę dostarczają poniżej:
- Regionach, gdzie użytkownicy są połączyć się z punktami końcowymi na platformie Azure.
- Wolumin użytkowników łączących się z tych regionów.
- Regiony platformy Azure, do których są pobierania kierowane do.
- Ich opóźnienia środowisko do tych regionów platformy Azure.

Te informacje są dostępne, można korzystać za pośrednictwem nakładki mapy geograficzne i widoków tabelarycznych w portalu, oprócz dostępne jako nieprzetworzone dane do pobrania.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mogą korzystać z widoku ruchu?

Widok ruchu daje ogólny widok ruchu otrzymywanych z profilów usługi Traffic Manager. W szczególności może służyć zrozumienie, gdzie bazy użytkowników nawiązuje połączenie z, i równie ważne co to jest środowisko jego średni czas oczekiwania. Te informacje można następnie użyć można znaleźć obszary, w których należy skoncentrować się na przykład, rozwijając użytkownika została zrozumiana. dzięki Azure w regionie, który może obsługiwać użytkowników z mniejsze opóźnienia. Inny wgląd w informacje o może pochodzić z widoku ruchu jest Zobacz wzorce ruchu do różnych regionach, które z kolei może ułatwić podjęcie decyzji na zwiększenie lub zmniejszenie magazynowych w regionach.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Czym różni się widok ruchu z dostępnych za pośrednictwem Monitora Azure metryk Menedżera ruchu?

Monitor usługi Azure może służyć do zrozumienia agregacji na poziomie ruchu odbieranego przez profilu i jej punkty końcowe. Umożliwia on również śledzić stan kondycji punktów końcowych w przypadku wystawianego wyniki sprawdzania kondycji. Gdy trzeba wykracza poza te i zrozumieć środowisko użytkownika końcowego nawiązywania Azure na poziomie regionalnym widoku ruchu można osiągnąć.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Widok ruchu używa informacji o podsieci klienta mechanizmów EDNS?

Zapytania DNS obsługiwanych przez usługę Azure Traffic Manager należy wziąć pod uwagę ECS informacje, aby zwiększyć jego dokładność routingu. Jednak podczas tworzenia pokazujący, gdy użytkownicy nawiązują połączenie z zestawu danych, widok ruchu przy użyciu tylko adresu IP program rozpoznawania nazw DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Ile dni danych używa widoku ruchu?

Widok ruchu tworzy dane wyjściowe przez przetwarzanie danych z siedmiu dni poprzedzających dzień przed widzianego przez użytkownika. Jest to okno, przenoszenie i najnowsze dane będą używane przy każdym razem, gdy użytkownik odwiedza witrynę.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak widok ruchu obsługuje zewnętrzne punkty końcowe?

Użyj zewnętrzne punkty końcowe spoza regiony platformy Azure w profilu usługi Traffic Manager można go mapować do regionu platformy Azure, która jest serwer proxy dla jego właściwości czas oczekiwania (jest to w rzeczywistości potrzebne użycie metody routingu wydajności). Jeśli jest to mapowanie region platformy Azure, metryki czas oczekiwania tego regionu Azure będzie używany podczas tworzenia widoku ruchu danych wyjściowych. Jeśli jest określony żaden region platformy Azure, informacje opóźnienia jest pusta w danych tych zewnętrzne punkty końcowe.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Należy włączyć widok ruchu dla każdego profilu w mojej subskrypcji?

W okresie podglądu widoku ruch został włączony na poziomie subskrypcji. W ramach ulepszeń, które wprowadziliśmy przed ogólnej dostępności umożliwiają teraz widoku ruchu na poziomie profilu, dzięki czemu może być bardziej szczegółowego włączenie tej funkcji. Domyślnie ruch widok zostanie wyłączony dla profilu.

>[!NOTE]
>Jeśli widok ruchu na poziomie subskrypcji jest włączone w czasie w wersji zapoznawczej, teraz należy ją ponownie włączyć dla każdego profilu w ramach tej subskrypcji.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak można wyłączyć widoku ruchu? 
Widok ruchu można wyłączyć dla dowolnego profilu za pomocą portalu lub interfejsu API REST. 

### <a name="how-does-traffic-view-billing-work"></a>Jak działa rozliczeń widoku ruchu?

Ruch Wyświetl cennik zależy od liczby punktów danych używany do tworzenia danych wyjściowych. Typ danych tylko obsługiwane jest obecnie, zapytania, który odbiera profilu. Ponadto tylko są rozliczane do przetwarzania, które już zostały wykonane, jeśli masz widoku ruchu włączone. Oznacza to, że po włączeniu widoku ruchu dla niektórych przedziale czasu, w ciągu miesiąca i wyłączyć innych okresach, tylko te punkty danych przetwarzane, kiedy ma funkcję włączone liczba na rachunku.

## <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Z punktami końcowymi z wieloma subskrypcjami można używać Menedżera ruchu?

Za pomocą punktów końcowych z wieloma subskrypcjami nie jest możliwe z aplikacjami sieci Web platformy Azure. Aplikacje sieci Web platformy Azure wymaga dowolną nazwę domeny niestandardowej, używany w aplikacjach sieci Web jest używana tylko w ramach jednej subskrypcji. Nie jest możliwe korzystanie z aplikacji sieci Web z wieloma subskrypcjami z tą samą nazwą domeny.

Dla innych typów punktu końcowego jest możliwe za pomocą Menedżera ruchu z punktami końcowymi z więcej niż jedną subskrypcję. W Menedżerze zasobów punktów końcowych z dowolnej subskrypcji można dodać do usługi Traffic Manager, tak długo, jak osoby konfigurowania profilu usługi Traffic Manager ma dostęp do odczytu do punktu końcowego. Te uprawnienia można otrzymać za pomocą [usługi Azure Resource Manager kontroli dostępu opartej na rolach (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Czy można używać Menedżera ruchu z miejsc "Przemieszczania" usługi w chmurze?

Tak. Usługi w chmurze przemieszczania gniazda można skonfigurować w usłudze Traffic Manager jako zewnętrzne punkty końcowe. Kontrole kondycji nadal są naliczane opłaty szybkością punkty końcowe platformy Azure. Ponieważ typu zewnętrznego punktu końcowego jest używana, zmiany w usłudze podstawowych nie są odczytywane automatycznie. Z zewnętrzne punkty końcowe Traffic Manager nie może wykryć po zatrzymaniu usługa w chmurze lub usunąć. W związku z tym Menedżer ruchu nadal rozliczenia dotyczące sprawdzania kondycji, aż do punktu końcowego zostało wyłączone lub usunięte.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager obsługuje punktów końcowych protokołu IPv6?

Menedżer ruchu nie ma obecnie IPv6 addressible serwerów nazw. Jednak Menedżera ruchu, mogą nadal używane przez klientów IPv6 łączących się z punktami końcowymi protokołu IPv6. Klient nie powoduje żądania DNS bezpośrednio do usługi Traffic Manager. Zamiast tego klient korzysta z usługi DNS cyklicznego. Tylko protokół IPv6 klienta wysyła żądania do usługi DNS cykliczne za pośrednictwem protokołu IPv6. Następnie usługa cykliczne powinno być możliwe do kontaktowania się z serwerów nazw usługi Traffic Manager przy użyciu protokołu IPv4.

Menedżer ruchu odpowiada o nazwie DNS punktu końcowego. Do obsługi punktu końcowego protokołu IPv6, musi istnieć rekord AAAA usługi DNS wskazuje nazwę DNS punkt końcowy adres IPv6. Kontrole kondycji usługi Traffic Manager obsługuje tylko adresy IPv4. Usługa musi ujawniać punkt końcowy IPv4 na tej samej nazwy DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Z więcej niż jednej aplikacji sieci Web w tym samym regionie można używać Menedżera ruchu?

Zazwyczaj Menedżera ruchu jest używany do kierowania ruchem danych do aplikacji wdrożonych w różnych regionach. Jednak może również służyć, gdzie ma więcej niż jedno wdrożenie aplikacji w tym samym regionie. Punkty końcowe usługi Azure Traffic Manager zezwala na więcej niż jeden punkt końcowy aplikacji sieci Web z tego samego regionu Azure, które mają zostać dodane do tego samego profilu Menedżera ruchu.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Jak przenieść mój profil Menedżera ruchu punkty końcowe systemu Azure do innej grupie zasobów?

Punkty końcowe systemu Azure, które są skojarzone z profilem Menedżera ruchu są śledzone przy użyciu ich identyfikatorów zasobów. Gdy zasobów platformy Azure, który jest używany jako punkt końcowy (na przykład publiczny adres IP, klasycznym usługi w chmurze, aplikacji sieci Web lub inny profil Menedżera ruchu w sposób zagnieżdżony) zostanie przeniesiony do innej grupie zasobów, jego zmiany Identyfikatora zasobu. W tym scenariuszu obecnie, należy zaktualizować profil Menedżera ruchu uprzedniego usunięcia, a następnie dodając ponownie punkty końcowe w profilu. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego Menedżera ruchu

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Menedżer ruchu jest odporność na awarie region platformy Azure?

Menedżer ruchu jest kluczowym składnikiem dostarczania wysoką dostępność aplikacji na platformie Azure.
Aby zapewnić wysoką dostępność, Traffic Manager musi mieć wyjątkowo wysoki poziom dostępności i zapewnienie odporności na awarie regionalne.

Zgodnie z projektem składniki usługi Traffic Manager są odporności na awarie pełną żadnych regionu Azure. Odporność to ma zastosowanie do wszystkich składników usługi Traffic Manager: nazw DNS, serwery, interfejsu API warstwy magazynu i monitorowanie usługi punktu końcowego.

Na wypadek wystąpienia awarii całego regionu Azure Traffic Manager powinien nadal działać normalnie. Aplikacje wdrożone w wielu regionach platformy Azure może polegać na Menedżera ruchu do kierowania ruchem danych do dowolnego dostępnego wystąpienia aplikacji.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak wybór lokalizacji grupy zasobów wpływa Menedżera ruchu?

Menedżer ruchu jest pojedyncza usługa globalnego. Nie ma charakteru regionalnego. Wybór lokalizacji grupy zasobów nie ma wpływu na profilów usługi Traffic Manager wdrożonych w tej grupie zasobów.

Usługa Azure Resource Manager wymaga wszystkich grup zasobów określić lokalizację, który określa domyślną lokalizację zasoby wdrożone w tej grupie zasobów. Po utworzeniu profilu Menedżera ruchu jest tworzony w grupie zasobów. Użyj wszystkich profilów usługi Traffic Manager **globalne** jako ich lokalizacji, Zastępowanie domyślnego grupy zasobów.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak określającymi bieżącą kondycję każdego punktu końcowego?

Bieżący stan monitorowania każdego punktu końcowego, oprócz ogólną profil jest wyświetlana w portalu Azure. Te informacje są również dostępne za pośrednictwem Monitora ruchu [interfejsu API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx), [poleceń cmdlet programu PowerShell](https://msdn.microsoft.com/library/mt125941.aspx), i [wiersza polecenia platformy Azure i platform](../cli-install-nodejs.md).

Azure Monitor umożliwia również śledzenia stanu punktów końcowych i wizualną reprezentację je w temacie. Aby uzyskać więcej informacji o Monitorze Azure, zobacz [monitorowania Azure dokumentacji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Można monitorować punktów końcowych HTTPS?

Tak. Traffic Manager obsługuje sondowanie za pośrednictwem protokołu HTTPS. Skonfiguruj **HTTPS** jako protokół w konfiguracji monitorowania.

Menedżer ruchu nie może dostarczyć żadnych weryfikacji certyfikatu w tym:

* Po stronie serwera, certyfikaty nie są weryfikowane.
* Funkcja SNI po stronie serwera certyfikatów nie są obsługiwane.
* Certyfikaty klienta nie są obsługiwane.

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Można zatrzymać usługi w chmurze Azure / punkt końcowy aplikacji w moim profilu Menedżera ruchu w sieci web, ale nie otrzymuję cały ruch nawet po I ponowne uruchomienie. Jak można to naprawić?

Gdy Azure usługa w chmurze / web punkt końcowy aplikacji jest zatrzymana zatrzymuje Menedżera ruchu sprawdzania kondycji i ponownie uruchamia kontrole kondycji tylko wtedy, gdy wykryje, że punkt końcowy zostanie uruchomiony ponownie. Aby uniknąć tego opóźnienia, należy wyłączyć i ponownie włączyć tego punktu końcowego profilu Menedżera ruchu, po ponownym uruchomieniu punktu końcowego.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Czy można użyć Menedżera ruchu, nawet jeśli Moja aplikacja nie ma obsługi protokołu HTTP lub HTTPS?

Tak. Można określić monitorowania protokołu TCP i usługi Traffic Manager można inicjować połączenie TCP i oczekiwania na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowiada na żądania połączenia za pomocą odpowiedzi do nawiązania połączenia, przed upływem limitu czasu, tego punktu końcowego jest oznaczona jako w dobrej kondycji.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jakie określone odpowiedzi są wymagane z punktu końcowego, korzystając z protokołu TCP monitorowania?

Podczas monitorowania TCP jest używany, usługi Traffic Manager uruchamia TCP trójstopniowego poprzez wysłanie żądania SYN punkt końcowy pod adresem określonego portu. Następnie czeka na pewien czas (jak określono w ustawieniach limitu czasu) na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowiada na żądania SYN za pomocą SYN ACK odpowiedzi przed upływem limitu czasu określonego w ustawieniach monitorowania, następnie tego punktu końcowego jest traktowany jako dobrej kondycji. Odebranie odpowiedzi potwierdzenia SYN Traffic Manager resetuje połączenie odpowiedzi z powrotem RST.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Szybkość Menedżera ruchu przenieść mój użytkowników zła punktu końcowego?

Menedżer ruchu udostępnia wiele ustawień, które ułatwiają kontrolowanie zachowania pracy awaryjnej w profilu usługi Traffic Manager w następujący sposób:
- można określić, że Menedżer ruchu sondy punkty końcowe częściej przez ustawienie interwału sondowania na 10 sekund. Dzięki temu, jak najszybciej wykryto dowolnego punktu końcowego, przechodząc w złej kondycji. 
- można określić, jak długo czekać przed kondycję wyewidencjonować razy żądanie (wartość minimalna limit czasu to 5 s).
- można określić, ile błędów mogą wystąpić, zanim punkt końcowy jest oznaczony jako w złej kondycji. Ta wartość może być niski jako 0, w których przypadku punkt końcowy jest oznaczony jako niepoprawny zaraz po awarii pierwszego sprawdzania kondycji. Jednak przy użyciu minimalnej wartości 0 dla tolerowaną liczba błędów może prowadzić do punktów końcowych przełączana poza obrotu z powodu przejściowych problemów, które mogą wystąpić podczas badania.
- czas wygaśnięcia (TTL) można określić dla odpowiedź DNS do brakować jako 0. Grozi to oznacza, że programy rozpoznawania nazw DNS nie można buforować odpowiedzi i każdego nowego zapytania pobiera odpowiedź, który zawiera najbardziej aktualne informacje kondycji z Menedżera ruchu.

Przy użyciu tych ustawień, Traffic Manager zapewnia tryb failover poniżej 10 sekund po punkt końcowy przechodzi złej kondycji i zapytanie DNS staje się przed odpowiedni profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu

Ustawienia monitorowania są Menedżera ruchu na poziomie profilu. Jeśli musisz użyć różnych ustawienia monitorowania dla tylko jeden punkt końcowy może odbywać dzięki użyciu tego punktu końcowego jako [zagnieżdżone profilu](traffic-manager-nested-profiles.md) którego ustawienia monitorowania różnią się od profilu nadrzędnej.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakie hosta nagłówka punktu końcowego kondycji sprawdza użycie?

Menedżer ruchu używa nagłówków hosta kontroli kondycji HTTP i HTTPS. Nagłówek hosta używane przez Menedżera ruchu jest nazwą elementu docelowego punktu końcowego skonfigurowane w profilu. Wartość nagłówka hosta nie można określić osobno z właściwością target.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Co to są adresy IP, z których pochodzą sprawdza kondycję?

Kliknij przycisk [tutaj](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) do wyświetlenia pliku JSON, który zawiera listę adresów IP, z których Menedżera ruchu może prowadzić kontroli kondycji. Przejrzyj plik JSON na liście adresów IP do upewnij się, czy połączenia przychodzące z tych adresów IP są dozwolone na punkty końcowe, aby sprawdzić jej stan kondycji.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Ile kontroli kondycji do mojego punktu końcowego można spodziewać się z Menedżera ruchu?

Liczba kondycji usługi Traffic Manager sprawdza, czy osiągnięciu punktu końcowego zależy od następujących czynników:
- wartość ustawioną dla interwał monitorowania (mniejszego oznacza więcej żądań kierowanych na punkt końcowy w dowolnym okresie w danym momencie).
- Liczba lokalizacji, z którego kontroli kondycji pochodzą (adresy IP, z których można oczekiwać, że kontrole jest wymieniony w poprzednim — często zadawane pytania).

## <a name="traffic-manager-nested-profiles"></a>Profile zagnieżdżone Menedżera ruchu

### <a name="how-do-i-configure-nested-profiles"></a>Jak skonfigurować zagnieżdżonych profilów?

Zagnieżdżonych profilów usługi Traffic Manager można skonfigurować przy użyciu zarówno usługi Azure Resource Manager i klasycznym Azure interfejsów API REST, poleceń cmdlet programu Azure PowerShell i polecenia wiersza polecenia platformy Azure i platform. Są one również obsługiwane za pośrednictwem portalu Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Obsługuje liczbę warstw zagnieżdżenia wykonuje Menedżera ruchu?

Można zagnieżdżać profile maksymalnie 10 poziomów w głąb. "Pętli" są niedozwolone.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Czy można mieszać inne typy punktów końcowych przy użyciu profilów zagnieżdżonych elementów podrzędnych, w tym samym profilu Menedżera ruchu

Tak. Nie ma żadnych ograniczeń na jak łączyć punktów końcowych różnych typów w profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Jak modelu rozliczeń stosowane profilów zagnieżdżone?

Nie ma nie ujemna cennik wpływ przy użyciu zagnieżdżonych profilów.

Karta usługi Traffic Manager zawiera dwa składniki: kontroli kondycji punktu końcowego i miliony zapytań DNS

* Kontroli kondycji punktu końcowego: bez dodatkowych opłat profilu podrzędnych skonfigurowane jako punktu końcowego w profilu nadrzędny nie istnieje. Monitorowania punktów końcowych w profilu podrzędnych jest on rozliczany w zwykły sposób.
* Zapytania DNS: każdego zapytania są traktowane jako jeden raz. Zapytanie profil nadrzędnego, który zwraca punkt końcowy z profilu podrzędnych jest uwzględniane w profilu nadrzędnej.

Aby uzyskać szczegółowe informacje, zobacz [cennikiem usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Czy istnieje negatywny wpływ na wydajność zagnieżdżonych profilów?

Nie. Nie ma żadnego wpływu wydajności, poniesionych przy użyciu zagnieżdżonych profilów.

Serwery nazw Menedżera ruchu przechodzenie przez hierarchię profilu wewnętrznie podczas przetwarzania każdego zapytania DNS. Zapytanie DNS do profilu nadrzędnej można otrzymywać odpowiedź DNS z punktem końcowym profilu podrzędnych. Pojedynczy rekord CNAME jest używany zarówno w przypadku korzystania w jednym profilu lub profilach zagnieżdżonych. Nie istnieje potrzeba aby utworzyć rekord CNAME dla każdego profilu w hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Jak usługi Traffic Manager obliczeniowe kondycji zagnieżdżonych punktu końcowego w profilu nadrzędnego?

Profil nadrzędnego nie sprawdzania kondycji w elemencie podrzędnym bezpośrednio. Zamiast tego kondycji profilu podrzędnych punktów końcowych są używane do obliczenia ogólnej kondycji profilu podrzędnych. Te informacje są propagowane w górę hierarchii profilu zagnieżdżone ustalenie kondycji zagnieżdżonych punktu końcowego. Profil nadrzędnego używa tego zagregowane kondycji do określenia, czy ruch może zostać skierowany do podrzędnych.

W poniższej tabeli opisano zachowanie z Menedżera ruchu kondycji sprawdza, czy punkt końcowy zagnieżdżonych.

| Stan monitora profilowania podrzędne | Stan punktu końcowego Monitor nadrzędny | Uwagi |
| --- | --- | --- |
| Wyłączone. Wyłączono profil podrzędnych. |Zatrzymano |Stan punktu końcowego nadrzędnego jest zatrzymana, nie jest wyłączone. Stanem wyłączono jest zarezerwowana dla wskazujący wyłączono punktu końcowego w profilu nadrzędnej. |
| Nieprawidłowe działanie. Co najmniej jeden element podrzędny punktu końcowego profilu jest w stanie obniżony. |Online: liczba Online punktów końcowych w profilu podrzędnych jest co najmniej wartość MinChildEndpoints.<BR>CheckingEndpoint: liczba Online oraz CheckingEndpoint punktów końcowych w profilu podrzędnych jest co najmniej wartość MinChildEndpoints.<BR>Ograniczone: inaczej. |Ruch jest kierowany do stanu CheckingEndpoint punktu końcowego. Jeśli zostanie ustawiona zbyt wysoka MinChildEndpoints, punkt końcowy jest zawsze znacznie mniej wydajna. |
| Online. Co najmniej jeden element podrzędny punktu końcowego profilu jest w stanie Online. Żaden punkt końcowy jest w stanie obniżony. |Zobacz powyżej. | |
| CheckingEndpoints. Co najmniej jeden element podrzędny punktu końcowego profilu jest "CheckingEndpoint". Brak punktów końcowych są "Online" lub "Ze spadkiem" |Taka sama jak powyżej. | |
| Nieaktywne. Wszystkie punkty końcowe profilu podrzędne są wyłączone lub zatrzymana lub ten profil nie ma punktów końcowych. |Zatrzymano | |

## <a name="next-steps"></a>Następne kroki:
- Dowiedz się więcej o Menedżerze ruchu [punkt końcowy monitorowania i automatycznej pracy awaryjnej](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o Menedżerze ruchu [metody routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md).
