---
title: Usługa Azure Traffic Manager — często zadawane pytania
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Usługi Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254367"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące usługi Traffic Manager

## <a name="traffic-manager-basics"></a>Podstawowe informacje o menedżerze ruchu drogowego

### <a name="what-ip-address-does-traffic-manager-use"></a>Jaki adres IP używa Usługi Traffic Manager?

Jak wyjaśniono w [how Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie DNS. Wysyła odpowiedzi DNS do klientów bezpośrednich do odpowiedniego punktu końcowego usługi. Klienci następnie połączyć się z punktem końcowym usługi bezpośrednio, a nie za pośrednictwem usługi Traffic Manager.

W związku z tym usługa Traffic Manager nie udostępnia punktu końcowego ani adresu IP dla klientów, z którymi mają się łączyć. Jeśli chcesz statyczny adres IP dla usługi, które muszą być skonfigurowane w usłudze, a nie w usłudze Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Jakie typy ruchu mogą być kierowane za pomocą Usługi Traffic Manager?
Jak wyjaśniono w [how Traffic Manager Works,](../traffic-manager/traffic-manager-how-it-works.md)punktem końcowym usługi Usługi Usługi Usługi usługi usługi przeznaczonej dla Internetu, hostowanym wewnątrz platformy Azure lub poza nią. W związku z tym Usługa Traffic Manager może kierować ruch pochodzący z publicznego Internetu do zestawu punktów końcowych, które są również skierowane do Internetu. Jeśli masz punkty końcowe, które znajdują się wewnątrz sieci prywatnej (na przykład wewnętrzna wersja [modułu równoważenia obciążenia platformy Azure)](../load-balancer/concepts-limitations.md#internalloadbalancer)lub użytkownicy żądający DNS z takich sieci wewnętrznych, nie można użyć usługi Traffic Manager do kierowania tego ruchu.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Czy Traffic Manager obsługuje "lepkie" sesje?

Jak wyjaśniono w [how Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie DNS. Używa odpowiedzi DNS do kierowania klientów do punktu końcowego usługi odpowiednie. Klienci łączą się bezpośrednio z punktem końcowym usługi, a nie za pośrednictwem usługi Traffic Manager. W związku z tym Usługa Traffic Manager nie widzi ruchu HTTP między klientem a serwerem.

Ponadto źródłowy adres IP kwerendy DNS odebranej przez menedżera ruchu należy do cyklicznej usługi DNS, a nie do klienta. W związku z tym Usługa Traffic Manager nie ma możliwości śledzenia poszczególnych klientów i nie może zaimplementować sesji "przyklejonych". To ograniczenie jest wspólne dla wszystkich systemów zarządzania ruchem opartymi na systemie DNS i nie jest specyficzne dla usługi Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Dlaczego podczas korzystania z usługi Traffic Manager pojawia się błąd HTTP?

Jak wyjaśniono w [how Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie DNS. Używa odpowiedzi DNS do kierowania klientów do punktu końcowego usługi odpowiednie. Klienci następnie połączyć się z punktem końcowym usługi bezpośrednio, a nie za pośrednictwem usługi Traffic Manager. Usługa Traffic Manager nie widzi ruchu HTTP między klientem a serwerem. W związku z tym każdy błąd HTTP, który widzisz, musi pochodzić z aplikacji. Aby klient miał połączyć się z aplikacją, wszystkie kroki rozpoznawania dns zostały zakończone. Obejmuje to wszelkie interakcje, które usługa Traffic Manager ma na przepływ ruchu aplikacji.

Dalsze dochodzenie powinno zatem koncentrować się na wniosku.

Nagłówek hosta HTTP wysyłany z przeglądarki klienta jest najczęstszym źródłem problemów. Upewnij się, że aplikacja jest skonfigurowana do akceptowania prawidłowego nagłówka hosta dla używanej nazwy domeny. W przypadku punktów końcowych korzystających z usługi Azure App Service zobacz [konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web w usłudze Azure App Service przy użyciu usługi Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Jaki jest wpływ na wydajność korzystania z usługi Traffic Manager?

Jak wyjaśniono w [how Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie DNS. Ponieważ klienci łączą się bezpośrednio z punktami końcowymi usługi, nie ma wpływu na wydajność podczas korzystania z usługi Traffic Manager po nawiązaniu połączenia.

Ponieważ usługa Traffic Manager integruje się z aplikacjami na poziomie DNS, wymaga dodatkowego wyszukiwania DNS, które ma zostać wstawione do łańcucha rozpoznawania DNS. Wpływ usługi Traffic Manager na czas rozpoznawania DNS jest minimalny. Usługa Traffic Manager używa globalnej sieci serwerów nazw i używa sieci [emisji dowolnej,](https://en.wikipedia.org/wiki/Anycast) aby upewnić się, że zapytania DNS są zawsze kierowane do najbliższego dostępnego serwera nazw. Ponadto buforowanie odpowiedzi DNS oznacza, że dodatkowe opóźnienie DNS poniesione przy użyciu usługi Traffic Manager dotyczy tylko ułamka sesji.

Performance Metoda kieruje ruch do najbliższego dostępnego punktu końcowego. Wynik netto jest, że ogólny wpływ wydajności związane z tą metodą powinny być minimalne. Każdy wzrost opóźnienia DNS powinny być kompensowane przez mniejsze opóźnienie sieci do punktu końcowego.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Jakich protokołów aplikacji można używać z usługą Traffic Manager?

Jak wyjaśniono w [how Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager działa na poziomie DNS. Po zakończeniu wyszukiwania DNS klienci łączą się bezpośrednio z punktem końcowym aplikacji, a nie za pośrednictwem usługi Traffic Manager. W związku z tym połączenie można użyć dowolnego protokołu aplikacji. Jeśli jako protokół monitorowania zostanie wybrany protokół TCP, monitorowanie kondycji punktu końcowego usługi Traffic Manager można wykonać bez użycia protokołów aplikacji. Jeśli zdecydujesz się zweryfikować kondycję przy użyciu protokołu aplikacji, punkt końcowy musi mieć możliwość odpowiadania na żądania HTTP lub HTTPS GET.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Czy mogę używać Usługi Traffic Manager z "nagią" nazwą domeny?

Tak. Aby dowiedzieć się, jak utworzyć rekord aliasu dla wierzchołka nazwy domeny w celu odwoływania się do profilu usługi Azure Traffic Manager, zobacz [Konfigurowanie rekordu aliasu do obsługi nazw domen wierzchołka za pomocą usługi Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Czy Menedżer ruchu uwzględnia adres podsieci klienta podczas obsługi zapytań DNS? 

Tak, oprócz źródłowego adresu IP otrzymywanej kwerendy DNS (zwykle adres ip programu rozpoznawania nazw DNS), podczas wykonywania wyszukiwań metod routingu geograficznego, wydajności i podsieci, menedżer ruchu uwzględnia również adres podsieci klienta, jeśli jest on uwzględniony w kwerendzie przez program rozpoznawania nazw, który wysyła żądanie w imieniu użytkownika końcowego.  
W szczególności [RFC 7871 — podsieć klienta w kwerendach DNS,](https://tools.ietf.org/html/rfc7871) który udostępnia [mechanizm rozszerzenia dns (EDNS0),](https://tools.ietf.org/html/rfc2671) który może przekazać adres podsieci klienta z programów rozpoznawania nazw, które go obsługują.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Co to jest DNS TTL i jak wpływa na moich użytkowników?

Gdy kwerenda DNS ląduje w usłudze Traffic Manager, ustawia wartość w odpowiedzi o nazwie czas wygaśnięcia (TTL). Ta wartość, której jednostka znajduje się w sekundach, wskazuje programom rozpoznawania nazw DNS niższego rzędu, jak długo buforować tę odpowiedź. Programy rozpoznawania nazw DNS nie są gwarantowane do buforowania tego wyniku, buforowanie umożliwia im odpowiadanie na kolejne zapytania z pamięci podręcznej zamiast przechodzenia do serwerów DNS usługi Traffic Manager. Ma to wpływ na odpowiedzi w następujący sposób:

- wyższy czas wygaśnięcia zmniejsza liczbę zapytań, które lądują na serwerach DNS Usługi Traffic Manager, co może zmniejszyć koszty dla klienta, ponieważ liczba obsługiwanych zapytań jest użyciem podlegaalnym.
- wyższy czas wygaśnięcia może potencjalnie skrócić czas potrzebny do odnośniania DNS.
- wyższy czas wygaśnięcia oznacza również, że dane nie odzwierciedlają najnowszych informacji o kondycji, które Usługa Traffic Manager uzyskała za pośrednictwem swoich agentów sondujących.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Jak wysoki lub niski można ustawić czas wygaśnięcia odpowiedzi usługi Traffic Manager?

Na poziomie profilu można ustawić czas wygaśnięcia DNS na 0 sekund i 2 147 483 647 sekund (maksymalny zakres zgodny z [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Czas wygaśnięcia 0 oznacza, że podrzędne programy rozpoznawania nazw DNS nie buforują odpowiedzi na zapytania, a wszystkie kwerendy powinny dotrzeć do serwerów DNS Menedżera ruchu w celu rozwiązania.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Jak mogę zrozumieć ilość zapytań pojawia się w moim profilu? 

Jedną z metryk dostarczonych przez usługa Traffic Manager jest liczba zapytań, na które odpowiedział profil. Można uzyskać te informacje na poziomie profilu agregacji lub można podzielić go dalej, aby zobaczyć ilość zapytań, gdzie zostały zwrócone określone punkty końcowe. Ponadto można skonfigurować alerty, aby powiadamiać, jeśli wolumin odpowiedzi kwerendy przekracza ustawione warunki. Aby uzyskać więcej informacji, [metryki i alerty programu Traffic Manager](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metoda routingu ruchu geograficznego usługi Traffic Manager

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Jakie są przypadki użycia, w których routing geograficzny jest przydatny?

Typ routingu geograficznego może służyć w dowolnym scenariuszu, w którym klient platformy Azure musi odróżnić swoich użytkowników na podstawie regionów geograficznych. Na przykład za pomocą geograficznego ruchu routingu metody, można dać użytkownikom z określonych regionów inne środowisko użytkownika niż te z innych regionów. Innym przykładem jest zgodność z lokalnymi nakazami suwerenności danych, które wymagają, aby użytkownicy z określonego regionu były obsługiwane tylko przez punkty końcowe w tym regionie.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Jak zdecydować, czy należy użyć metody routingu wydajności, czy metody routingu geograficznego?

Kluczową różnicą między tymi dwiema popularnymi metodami routingu jest to, że w metodzie routingu wydajności głównym celem jest wysłanie ruchu do punktu końcowego, który może zapewnić najniższe opóźnienie wywołującemu, podczas gdy w geographic routingu głównym celem jest wymuszanie geo dla rozmówców, aby można było celowo przekierować ich do określonego punktu końcowego. Nakładanie się dzieje, ponieważ istnieje korelacja między bliskości geograficznej i mniejsze opóźnienie, chociaż nie zawsze jest to prawdą. Może istnieć punkt końcowy w innej lokalizacji geograficznej, który może zapewnić lepsze środowisko opóźnienia dla obiektu wywołującego i w takim przypadku routing wydajności wyśle użytkownika do tego punktu końcowego, ale routing geograficzny zawsze wyśle go do punktu końcowego, który został zamapowany dla ich regionu geograficznego. Aby jeszcze bardziej wyjaśnić, należy wziąć pod uwagę poniższy przykład — z routingiem geograficznym można wykonać nietypowe mapowania, takie jak wysyłanie całego ruchu z Azji do punktów końcowych w Stanach Zjednoczonych i cały ruch amerykański do punktów końcowych w Azji. W takim przypadku routing geograficzny celowo zrobi dokładnie to, do czego została skonfigurowana, a optymalizacja wydajności nie jest brana pod uwagę. 
>[!NOTE]
>Mogą istnieć scenariusze, w których może być potrzebne zarówno wydajności i możliwości routingu geograficznego, dla tych scenariuszy zagnieżdżone profile mogą być doskonałym wyborem. Na przykład można skonfigurować profil nadrzędny z routingiem geograficznym, w którym wysyłasz cały ruch z Ameryki Północnej do profilu zagnieżdżonego, który ma punkty końcowe w STANACH ZJEDNOCZONYCH, i użyć routingu wydajności, aby wysłać ten ruch do najlepszego punktu końcowego w tym zestawie. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Jakie regiony są obsługiwane przez usługę Traffic Manager dla routingu geograficznego?

Hierarchię kraju/regionu używaną przez Menedżera ruchu można znaleźć [tutaj](traffic-manager-geographic-regions.md). Ta strona jest aktualizowana na bieżąco z wszelkimi zmianami, ale można również programowo pobierać te same informacje za pomocą [interfejsu API REST usługi Azure Traffic Manager.](https://docs.microsoft.com/rest/api/trafficmanager/) 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>W jaki sposób menedżer ruchu określa, gdzie użytkownik wykonuje kwerendy?

Usługa Traffic Manager analizuje źródłowy adres IP kwerendy (najprawdopodobniej jest to lokalny program rozpoznawania nazw DNS wykonujący kwerendy w imieniu użytkownika) i używa wewnętrznej mapy adresu IP do regionu w celu określenia lokalizacji. Ta mapa jest aktualizowana na bieżąco w celu uwzględnienia zmian w Internecie. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Czy gwarantujemy, że Usługa Traffic Manager może poprawnie określić dokładną lokalizację geograficzną użytkownika w każdym przypadku?

Nie, usługa Traffic Manager nie może zagwarantować, że region geograficzny, który wywnioskować ze źródłowego adresu IP kwerendy DNS, będzie zawsze odpowiadał lokalizacji użytkownika z następujących powodów:

- Po pierwsze, jak opisano w poprzednim faq, źródłowy adres IP widzimy jest to, że rozpoznawania nazw DNS robi wyszukiwanie w imieniu użytkownika. Lokalizacja geograficzna programu rozpoznawania nazw DNS jest dobrym serwerem proxy dla lokalizacji geograficznej użytkownika, ale może się również różnić w zależności od śladu usługi rozpoznawania nazw DNS i określonej usługi rozpoznawania nazw DNS, której klient wybrał do użycia. Na przykład klient znajdujący się w Malezji może określić w ustawieniach urządzenia użyć usługi rozpoznawania nazw DNS, której serwer DNS w Singapurze może zostać wybrany do obsługi rozpoznawania zapytań dla tego użytkownika/urządzenia. W takim przypadku usługa Traffic Manager może zobaczyć tylko adres IP programu rozpoznawania nazw, który odpowiada lokalizacji w Singapurze. Ponadto zobacz wcześniejsze często zadawane pytania dotyczące obsługi adresów podsieci klienta na tej stronie.

- Po drugie Usługa Traffic Manager używa mapy wewnętrznej do wykonywania tłumaczenia adresu IP na region geograficzny. Chociaż mapa ta jest na bieżąco zatwierdzana i aktualizowana w celu zwiększenia jej dokładności i uwzględnienia ewoluującego charakteru Internetu, nadal istnieje możliwość, że nasze informacje nie są dokładną reprezentacją lokalizacji geograficznej całego ADRESU IP. Adresy.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Czy punkt końcowy musi być fizycznie zlokalizowany w tym samym regionie, w jakim jest skonfigurowany do routingu geograficznego?

Nie, lokalizacja punktu końcowego nie nakłada żadnych ograniczeń, na które regiony mogą być mapowane do niego. Na przykład punkt końcowy w regionie US-Central Azure może mieć wszystkich użytkowników z Indii skierowane do niego.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Czy można przypisać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?

Tak, jeśli metoda routingu profilu nie jest geograficzna, można użyć [interfejsu API REST usługi Azure Traffic Manager,](https://docs.microsoft.com/rest/api/trafficmanager/) aby przypisać regiony geograficzne do punktów końcowych w tym profilu. W przypadku niegeograficznych profili typu routingu ta konfiguracja jest ignorowana. Jeśli zmienisz taki profil na typ routingu geograficznego w późniejszym czasie, Usługa Traffic Manager może używać tych mapowań.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Dlaczego podczas próby zmiany metody routingu istniejącego profilu na geograficzny pojawia się błąd?

Wszystkie punkty końcowe w profilu z routingiem geograficznym muszą mieć co najmniej jeden region mapowany do niego. Aby przekonwertować istniejący profil na typ routingu geograficznego, przed zmianą typu routingu na geograficzny należy najpierw skojarzyć regiony geograficzne ze wszystkimi punktami końcowymi przy użyciu [interfejsu API REST usługi Azure Traffic Manager.](https://docs.microsoft.com/rest/api/trafficmanager/) Jeśli używasz portalu, najpierw usuń punkty końcowe, zmień metodę routingu profilu na geograficzną, a następnie dodaj punkty końcowe wraz z ich mapowaniem regionu geograficznego.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Dlaczego zdecydowanie zaleca się, aby klienci tworzyli profile zagnieżdżone zamiast punktów końcowych w profilu z włączonym routingiem geograficznym?

Region można przypisać tylko do jednego punktu końcowego w profilu, jeśli używa metody routingu geograficznego. Jeśli ten punkt końcowy nie jest typem zagnieżdżonym z dołączonym profilem podrzędnym, jeśli ten punkt końcowy będzie w złej kondycji, Usługa Traffic Manager kontynuuje wysyłanie ruchu do niego, ponieważ alternatywa nie wysyłania ruchu nie jest lepsza. Usługa Traffic Manager nie działa w sposób failover do innego punktu końcowego, nawet jeśli region przypisany jest "nadrzędny" regionu przypisanego do punktu końcowego, który przeszedł w złej kondycji (na przykład, jeśli punkt końcowy, który ma region Hiszpania idzie w złej kondycji, nie przechodzi awaryjnie do innego punktu końcowego, który ma region, który Europa do niego przypisana). Odbywa się to w celu zapewnienia, że usługa Traffic Manager respektuje granice geograficzne skonfigurowane przez klienta w ich profilu. Aby uzyskać korzyści z niepowodzenia w poprzek do innego punktu końcowego, gdy punkt końcowy idzie w złej kondycji, zaleca się, że regiony geograficzne mają być przypisane do profilów zagnieżdżonych z wielu punktów końcowych w nim zamiast poszczególnych punktów końcowych. W ten sposób, jeśli punkt końcowy w zagnieżdżonej profilu podrzędnego nie powiedzie się, ruch może zakończyć się awaryjnie do innego punktu końcowego wewnątrz tego samego zagnieżdżonego profilu podrzędnego.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API obsługującej ten typ routingu?

Tak, tylko wersja interfejsu API 2017-03-01 i nowsze obsługuje typ routingu geograficznego. Nie można używać starszych wersji interfejsu API do tworzenia profili typu routingu geograficznego ani przypisywania regionów geograficznych do punktów końcowych. Jeśli starsza wersja interfejsu API jest używana do pobierania profili z subskrypcji platformy Azure, każdy profil typu routingu geograficznego nie jest zwracany. Ponadto podczas korzystania ze starszych wersji interfejsu API każdy profil zwrócony, który ma punkty końcowe z przypisaniem regionu geograficznego, nie ma przypisania regionu geograficznego.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Metoda routingu ruchu w podsieci Usługi Menedżera ruchu

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Jakie są przypadki użycia, w których routing podsieci jest przydatny?

Routing podsieci umożliwia rozróżnienie środowiska dostarczanego dla określonych zestawów użytkowników zidentyfikowanych przez źródłowy adres IP ich adresów IP żądań DNS. Przykładem może być pokazanie różnych treści, jeśli użytkownicy łączą się z witryną sieci Web z głównej siedziby firmy. Innym byłoby ograniczenie użytkowników z niektórych usługodawców internetowych do dostępu tylko do punktów końcowych, które obsługują tylko połączenia IPv4, jeśli ci dostawcy usług internetowych mają wydajność poniżej par, gdy jest używany IPv6.
Innym powodem, dla którego należy użyć metody routingu podsieci, jest połączenie z innymi profilami w zagnieżdżonym zestawie profili. Na przykład, jeśli chcesz użyć geograficznej metody routingu dla geo-fencingu użytkowników, ale dla określonego usługodawcy internetowego chcesz wykonać inną metodę routingu, możesz mieć metodę routingu profilu withy Subnet jako profil nadrzędny i zastąpić ten usługodawca, aby użyć określonego obiektu podrzędnego profilu i mają standardowy profil geograficzny dla wszystkich innych osób.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>W jaki sposób Traffic Manager zna adres IP użytkownika końcowego?

Urządzenia użytkowników końcowych zazwyczaj używają programu rozpoznawania nazw DNS do wyszukiwania DNS w ich imieniu. Wychodzący adres IP takich programów rozpoznawania nazw jest tym, co usługa Traffic Manager widzi jako źródłowy adres IP. Ponadto metoda routingu podsieci sprawdza również, czy istnieją informacje o rozszerzonej podsieci klienta EDNS0 (ECS), które zostały przekazane wraz z żądaniem. Jeśli informacje ECS są obecne, jest to adres używany do określenia routingu. W przypadku braku informacji ECS źródłowy adres IP zapytania jest używany do celów routingu.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Jak określić adresy IP podczas korzystania z routingu podsieci?

Adresy IP do skojarzenia z punktem końcowym można określić na dwa sposoby. Najpierw można użyć kropkowanego kropkowanego notacji oktetu z adresami początkowymi i końcowymi, aby określić zakres (na przykład 1.2.3.4-5.6.7.8 lub 3.4.5.6-3.4.5.6). Po drugie, można użyć notacji CIDR, aby określić zakres (na przykład 1.2.3.0/24). Można określić wiele zakresów i można używać obu typów notacji w zestawie zakresów. Stosuje się kilka ograniczeń.

-    Nie można nakładać zakresów adresów, ponieważ każdy adres IP musi być mapowany tylko do jednego punktu końcowego
-    Adres początkowy nie może być większy niż adres końcowy
-    W przypadku notacji CIDR adres IP przed "/" powinien być adresem początkowym tego zakresu (na przykład 1.2.3.0/24 jest ważny, ale 1.2.3.4.4/24 nie jest ważny)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Jak określić rezerwowy punkt końcowy podczas korzystania z routingu podsieci?

W profilu z routingiem podsieci, jeśli masz punkt końcowy bez mapowanych podsieci, każde żądanie, które nie pasuje do innych punktów końcowych, zostanie przekierowane w tym miejscu. Zdecydowanie zaleca się, aby taki rezerwowy punkt końcowy w profilu, ponieważ usługa Traffic Manager zwróci odpowiedź NXDOMAIN, jeśli pojawi się żądanie i nie jest mapowane do żadnych punktów końcowych lub jeśli jest mapowane do punktu końcowego, ale ten punkt końcowy jest w złej kondycji.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Co się stanie, jeśli punkt końcowy jest wyłączony w profilu typu routingu podsieci?

W profilu z routingiem podsieci, jeśli masz punkt końcowy z tym jest wyłączony, Usługa Traffic Manager będzie zachowywać się tak, jakby ten punkt końcowy i mapowania podsieci, które ma, nie istnieją. Jeśli kwerenda, która byłaby dopasowana do mapowania adresów IP, zostanie odebrana, a punkt końcowy jest wyłączony, usługa Traffic Manager zwróci rezerwowy punkt końcowy (jeden bez mapowań) lub jeśli taki punkt końcowy nie jest obecny, zwróci odpowiedź NXDOMAIN.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Usługa Wielowartościowa usługi zmiany ruchu menedżera ruchu

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Jakie są przypadki użycia, w których routing MultiValue jest przydatny?

Routing Wielowartościowy zwraca wiele zdrowych punktów końcowych w odpowiedzi na pojedyncze zapytanie. Główną zaletą tego jest to, że jeśli punkt końcowy jest w złej kondycji, klient ma więcej opcji, aby ponowić próbę bez wykonywania innego wywołania DNS (które mogą zwracać tę samą wartość z nadrzędnej pamięci podręcznej). Dotyczy to aplikacji wrażliwych na dostępność, które chcą zminimalizować przestoje.
Innym zastosowaniem metody routingu MultiValue jest, jeśli punkt końcowy jest "dual-homed" do adresów IPv4 i IPv6 i chcesz dać wywołującemu obie opcje do wyboru, gdy inicjuje połączenie z punktem końcowym.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Ile punktów końcowych jest zwracanych, gdy używana jest routing Wielowartościowy?

Można określić maksymalną liczbę punktów końcowych do zwrócenia i MultiValue zwróci nie więcej niż wiele punktów końcowych w dobrej kondycji po odebraniu kwerendy. Maksymalna możliwa wartość dla tej konfiguracji wynosi 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Czy otrzymam ten sam zestaw punktów końcowych, gdy używana jest routing wielowartościowy?

Nie możemy zagwarantować, że ten sam zestaw punktów końcowych zostaną zwrócone w każdej kwerendzie. Wpływ na to ma również fakt, że niektóre punkty końcowe mogą być niezdrowe, w którym to momencie nie zostaną uwzględnione w odpowiedzi

## <a name="real-user-measurements"></a>Pomiary rzeczywistego użytkownika

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Jakie są korzyści z używania pomiarów rzeczywistych użytkowników?

Korzystając z metody routingu wydajności, Usługa Traffic Manager wybiera najlepszy region platformy Azure dla użytkownika końcowego, z którym można się połączyć, sprawdzając źródłowy adres IP i podsieć klienta EDNS (jeśli jest przekazywana) i sprawdzając ją względem analizy opóźnienia sieci obsługiwanej przez usługę. Pomiary rzeczywistych użytkowników zwiększa to dla bazy użytkowników końcowych, dzięki czemu ich środowisko przyczynia się do tej tabeli opóźnień, oprócz zapewnienia, że ta tabela odpowiednio obejmuje sieci użytkowników końcowych, z których użytkownicy końcowi łączą się z platformą Azure. Prowadzi to do zwiększenia dokładności routingu użytkownika końcowego.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Czy mogę używać pomiarów rzeczywistych użytkowników w regionach innych niż platformy Azure?

Pomiary rzeczywistych użytkowników mierzy i raportuje tylko opóźnienie, aby dotrzeć do regionów platformy Azure. Jeśli używasz routingu opartego na wydajności z punktami końcowymi hostowanymi w regionach innych niż platforma Azure, nadal możesz korzystać z tej funkcji, zwiększając informacje o opóźnieniu o reprezentatywnym regionie platformy Azure, który został wybrany do skojarzenia z tym punktem końcowym.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Która metoda routingu korzysta z pomiarów rzeczywistych użytkowników?

Dodatkowe informacje uzyskane za pomocą pomiarów rzeczywistego użytkownika mają zastosowanie tylko do profilów korzystających z metody routingu wydajności. Łącze Pomiary rzeczywistego użytkownika jest dostępne ze wszystkich profilów podczas wyświetlania go za pośrednictwem witryny Azure portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Czy muszę włączyć pomiary rzeczywistego użytkownika w każdym profilu oddzielnie?

Nie, wystarczy włączyć go tylko raz na subskrypcję, a wszystkie informacje o opóźnieniach mierzone i zgłaszane są dostępne dla wszystkich profili.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Jak wyłączyć pomiary rzeczywistego użytkownika dla mojej subskrypcji?

Możesz zatrzymać naliczanie opłat związanych z pomiarami rzeczywistego użytkownika, gdy przestaniesz zbierać i wysyłać z powrotem pomiary opóźnień z aplikacji klienckiej. Na przykład podczas pomiaru javascript osadzone na stronach internetowych, można przestać korzystać z tej funkcji, usuwając JavaScript lub wyłączając jego wywołania, gdy strona jest renderowana.

Możesz również wyłączyć pomiary rzeczywistego użytkownika, usuwając klucz. Po usunięciu klucza wszelkie pomiary wysyłane do menedżera ruchu z tym kluczem są odrzucane.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Czy mogę używać pomiarów rzeczywistych użytkowników w aplikacjach klienckich innych niż strony internetowe?

Tak, pomiary rzeczywistych użytkowników są przeznaczone do pozyskiwania danych zebranych za pośrednictwem różnych typów klientów użytkowników końcowych. To często zadawane pytania będą aktualizowane w miarę obsługi nowych typów aplikacji klienckich.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Ile pomiarów jest dokonywanych za każdym razem, gdy wyrenderowana jest strona internetowa z włączoną stroną realną pomiarów użytkownika?

Gdy pomiary rzeczywistego użytkownika są używane z dostarczonym pomiarem JavaScript, każde renderowanie strony powoduje wykonanie sześciu pomiarów. Są one następnie zgłaszane z powrotem do usługi Usługi Usługi Traffic Manager. Opłata za tę funkcję jest naliczana na podstawie liczby pomiarów zgłoszonych do usługi Traffic Manager. Jeśli na przykład użytkownik odsunie się od strony sieci Web podczas pomiarów, ale przed zgłoszeniem pomiarów pomiary te nie są uwzględniane do celów rozliczeniowych.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Czy na mojej stronie pojawi się opóźnienie, zanim skrypt Pomiary rzeczywistego użytkownika zostanie uruchomionym?

Nie, nie ma zaprogramowane opóźnienie przed wywoływanie skryptu.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Czy mogę używać pomiarów rzeczywistych użytkowników tylko w regionach platformy Azure, które chcę zmierzyć?

Nie, za każdym razem, gdy jest wywoływana, skrypt pomiarów rzeczywistego użytkownika mierzy zestaw sześciu regionów platformy Azure określonych przez usługę. Ten zestaw zmienia się między różnymi wywołaniami i gdy duża liczba takich wywołań się, zakres pomiaru obejmuje w różnych regionach platformy Azure.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Czy mogę ograniczyć liczbę pomiarów wykonanych do określonej liczby?

Pomiar JavaScript jest osadzony na stronie internetowej i masz pełną kontrolę nad tym, kiedy go uruchomić i przestać z niego korzystać. Tak długo, jak usługa Usługi Usługi Usługi Traffic Manager odbiera żądanie listy regionów platformy Azure, które mają być mierzone, zestaw regionów są zwracane.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Czy mogę zobaczyć pomiary wykonane przez aplikację kliencką w ramach pomiarów rzeczywistych użytkowników?

Ponieważ logika pomiaru jest uruchamiana z aplikacji klienckiej, masz pełną kontrolę nad tym, co się dzieje, w tym wyświetlanie pomiarów opóźnienia. Usługa Traffic Manager nie zgłasza zbiorczego widoku pomiarów otrzymanych przy kluczu połączonym z subskrypcją.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Czy mogę zmodyfikować skrypt pomiaru dostarczony przez Traffic Manager?

Gdy masz kontrolę nad tym, co jest osadzone na stronie internetowej, zdecydowanie odradzamy wprowadzanie jakichkolwiek zmian w skrypcie pomiaru, aby upewnić się, że mierzy i raportuje opóźnienia poprawnie.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Czy inni będą mogli zobaczyć klucz, którego używam w pomiarach rzeczywistego użytkownika?

Po umieszczeniu skryptu pomiarowego na stronie internetowej inni będą mogli zobaczyć skrypt i klucz pomiarów rzeczywistego użytkownika (RUM). Ale ważne jest, aby wiedzieć, że ten klucz różni się od identyfikatora subskrypcji i jest generowany przez usługa Traffic Manager do użycia tylko w tym celu. Znajomość klucza RUM nie naruszy bezpieczeństwa konta platformy Azure.

### <a name="can-others-abuse-my-rum-key"></a>Czy inni mogą nadużywać mojego klucza RUM?

Podczas gdy inne osoby mogą używać klucza do wysyłania nieprawidłowych informacji na platformę Azure, kilka błędnych pomiarów nie zmieni routingu, ponieważ jest brany pod uwagę wraz ze wszystkimi innymi pomiarami, które otrzymujemy. Jeśli trzeba zmienić klucze, można ponownie wygenerować klucz, w którym momencie stary klucz zostanie odrzucony.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Czy muszę umieścić pomiar JavaScript na wszystkich moich stronach internetowych?

Pomiary rzeczywistych użytkowników zapewniają większą wartość wraz ze wzrostem liczby pomiarów. Mimo to, to twoja decyzja, czy trzeba umieścić go na wszystkich stronach internetowych lub wybrać kilka. Naszą rekomendacją jest, aby rozpocząć od umieszczenia go na najczęściej odwiedzanych stronach, gdzie użytkownik ma pozostać na tej stronie pięć sekund lub więcej.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Czy informacje o moich użytkownikach końcowych mogą być identyfikowane przez Traffic Manager, jeśli używam pomiarów rzeczywistych użytkowników?

Gdy używany jest podany pomiar JavaScript, Usługa Traffic Manager będzie miała wgląd w adres IP klienta użytkownika końcowego i źródłowy adres IP używanego lokalnego programu rozpoznawania nazw DNS. Usługa Traffic Manager używa adresu IP klienta tylko po jego obcięciu, aby nie być w stanie zidentyfikować określonego użytkownika końcowego, który wysłał pomiary.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Czy strona internetowa mierząca pomiary rzeczywistego użytkownika musi używać Menedżera ruchu do routingu?

Nie, nie trzeba używać Menedżera ruchu. Strona routingu usługi Traffic Manager działa oddzielnie od części Pomiar rzeczywistego użytkownika i chociaż jest to świetny pomysł, aby mieć je zarówno w tej samej właściwości internetowej, nie muszą być.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Czy muszę obsługiwać dowolną usługę w regionach platformy Azure, aby używać z pomiarami rzeczywistego użytkownika?

Nie, nie trzeba hostować żadnego składnika po stronie serwera na platformie Azure, aby pomiary rzeczywistego użytkownika działały. Obraz pojedynczego piksela pobrany przez pomiar JavaScript i usługę uruchamianą w różnych regionach platformy Azure jest hostowany i zarządzany przez platformę Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Czy użycie przepustowości platformy Azure wzrośnie, gdy używam pomiarów rzeczywistego użytkownika?

Jak wspomniano w poprzedniej odpowiedzi, składniki po stronie serwera pomiarów rzeczywistego użytkownika są własnością platformy Azure i są zarządzane przez platformę Azure. Oznacza to, że użycie przepustowości platformy Azure nie wzrośnie, ponieważ używasz pomiarów rzeczywistego użytkownika. Nie obejmuje to użycia przepustowości poza opłatami platformy Azure. Minimalizujemy przepustowość używaną przez pobranie tylko obrazu pojedynczego piksela do pomiaru opóźnienia w regionie platformy Azure. 

## <a name="traffic-view"></a>Widok ruchu

### <a name="what-does-traffic-view-do"></a>Do czego służy widok ruchu?

Widok ruchu to funkcja Menedżera ruchu, która pomaga lepiej zrozumieć użytkowników i ich środowisko. Używa zapytań odebranych przez usługę Traffic Manager i tabel analizy opóźnienia sieci, które usługa utrzymuje, aby zapewnić Następujące informacje:

- Regiony, z których użytkownicy łączą się z punktami końcowymi na platformie Azure.
- Liczba użytkowników łączących się z tych regionów.
- Regiony platformy Azure, do których są one coraz kierowane do.
- Ich środowisko opóźnienia do tych regionów platformy Azure.

Te informacje są dostępne do wykorzystania za pośrednictwem nakładki mapy geograficznej i widoków tabelaryczne w portalu, oprócz tego, że są dostępne jako surowe dane do pobrania.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Jak mogę korzystać z usługi Traffic View?

Widok ruchu zapewnia ogólny widok ruchu, który otrzymują profile Usługi Traffic Manager. W szczególności może służyć do zrozumienia, gdzie baza użytkowników łączy się z i równie ważne, co ich średnie opóźnienie jest. Następnie można użyć tych informacji, aby znaleźć obszary, w których należy skupić się, na przykład, rozszerzając rozmiar platformy Azure do regionu, który może obsługiwać tych użytkowników z mniejszym opóźnieniem. Innym wglądem, który można uzyskać z korzystania z widoku ruchu, jest wyświetlanie wzorców ruchu do różnych regionów, co z kolei może pomóc w podejmowaniu decyzji dotyczących zwiększania lub zmniejszania wymyślania w tych regionach.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Czym różni się widok ruchu od metryk usługi Traffic Manager dostępnych za pośrednictwem monitora platformy Azure?

Usługa Azure Monitor może służyć do zrozumienia na poziomie zagregowanym ruchu odebranego przez profil i jego punkty końcowe. Umożliwia również śledzenie stanu kondycji punktów końcowych, ujawniając wyniki kontroli kondycji. Gdy trzeba wyjść poza te i zrozumieć środowisko użytkownika końcowego łączenia się z platformą Azure na poziomie regionalnym, traffic view może służyć do osiągnięcia tego.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Czy widok ruchu korzysta z informacji o podsieci klienta EDNS?

Kwerendy DNS obsługiwane przez usługę Azure Traffic Manager uwzględniają informacje ECS, aby zwiększyć dokładność routingu. Ale podczas tworzenia zestawu danych, który pokazuje, gdzie użytkownicy łączą się z, Traffic View używa tylko adres IP rozpoznawania nazw DNS.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Ile dni danych używa widok ruchu?

Widok ruchu tworzy swoje dane wyjściowe, przetwarzając dane z siedmiu dni poprzedzających dzień przed, gdy jest oglądany przez Ciebie. Jest to okno w ruchu, a najnowsze dane będą używane za każdym razem, gdy odwiedzasz.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Jak usługa Traffic View obsługuje zewnętrzne punkty końcowe?

Korzystając z zewnętrznych punktów końcowych hostowanych poza regionami platformy Azure w profilu usługi Traffic Manager, można wybrać opcję mapowania go na region platformy Azure, który jest serwerem proxy ze względu na jego właściwości opóźnienia (jest to w rzeczywistości potrzebne, jeśli używasz metody routingu wydajności). Jeśli ma to mapowanie regionu platformy Azure, metryki opóźnienia regionu platformy Azure będą używane podczas tworzenia danych wyjściowych widoku ruchu. Jeśli nie zostanie określony żaden region platformy Azure, informacje o opóźnieniu będą puste w danych dla tych zewnętrznych punktów końcowych.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Czy muszę włączyć widok ruchu dla każdego profilu w mojej subskrypcji?

W okresie podglądu widok ruchu został włączony na poziomie subskrypcji. W ramach ulepszeń wprowadzonych przed ogólną dostępnością można teraz włączyć widok ruchu na poziomie profilu, co pozwala na bardziej szczegółowe włączenie tej funkcji. Domyślnie widok ruchu zostanie wyłączony dla profilu.

>[!NOTE]
>Jeśli włączono widok ruchu na poziomie subskrypcji w czasie podglądu, teraz należy ponownie włączyć go dla każdego profilu w ramach tej subskrypcji.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Jak wyłączyć widok ruchu?

Widok ruchu dla dowolnego profilu można wyłączyć za pomocą interfejsu API portal lub REST. 

### <a name="how-does-traffic-view-billing-work"></a>Jak działa rozliczenia traffic view?

Ceny widoku ruchu są oparte na liczbie punktów danych użytych do utworzenia danych wyjściowych. Obecnie jedynym obsługiwanym typem danych są zapytania odbierane przez profil. Ponadto są tylko naliczane za przetwarzanie, które zostało wykonane, gdy masz włączony widok ruchu. Oznacza to, że jeśli włączysz widok ruchu przez pewien okres w miesiącu i wyłączysz go w innych okresach, tylko punkty danych przetworzone, gdy funkcja była włączona, są wliczane do rachunku.

## <a name="traffic-manager-endpoints"></a>Punkty końcowe usługi Traffic Manager

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Czy mogę używać usługi Traffic Manager z punktami końcowymi z wielu subskrypcji?

Korzystanie z punktów końcowych z wielu subskrypcji nie jest możliwe w usłudze Azure Web Apps. Usługa Azure Web Apps wymaga, aby każda niestandardowa nazwa domeny używana z aplikacjami sieci Web była używana tylko w ramach jednej subskrypcji. Nie można używać aplikacji sieci Web z wielu subskrypcji o tej samej nazwie domeny.

W przypadku innych typów punktów końcowych można używać usługi Traffic Manager z punktami końcowymi z więcej niż jednej subskrypcji. W Menedżerze zasobów punkty końcowe z dowolnej subskrypcji można dodać do usługi Traffic Manager, o ile osoba konfigurująca profil usługi Traffic Manager ma dostęp do odczytu do punktu końcowego. Uprawnienia te można przyznać za pomocą [kontroli dostępu opartej na rolach usługi Azure Resource Manager (RBAC).](../role-based-access-control/role-assignments-portal.md)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Czy mogę używać usługi Traffic Manager z miejscami przejściowymi usługi w chmurze?

Tak. Miejsca "przejściowe" usługi w chmurze można skonfigurować w usłudze Traffic Manager jako zewnętrzne punkty końcowe. Kontrole kondycji są nadal naliczane według stawki punktów końcowych platformy Azure.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Czy usługa Traffic Manager obsługuje punkty końcowe IPv6?

Usługa Traffic Manager nie udostępnia obecnie serwerów nazw adresowalnych w usłudze IPv6. Jednak usługa Traffic Manager może być nadal używana przez klientów IPv6 łączących się z punktami końcowymi IPv6. Klient nie wysunie żądań DNS bezpośrednio do Usługi Traffic Manager. Zamiast tego klient używa cyklicznej usługi DNS. Klient tylko IPv6 wysyła żądania do cyklicznej usługi DNS za pośrednictwem protokołu IPv6. Następnie usługa cykliczna powinna mieć możliwość skontaktowania się z serwerami nazw usługi Traffic Manager przy użyciu IPv4.

Usługa Traffic Manager odpowiada nazwą DNS lub adresem IP punktu końcowego. Aby obsługiwać punkt końcowy IPv6, istnieją dwie opcje. Można dodać punkt końcowy jako nazwę DNS, która ma skojarzony rekord AAAA i Menedżer ruchu będzie sprawdzić kondycję tego punktu końcowego i zwrócić go jako typ rekordu CNAME w odpowiedzi na kwerendę. Można również dodać ten punkt końcowy bezpośrednio przy użyciu adresu IPv6 i usługi Traffic Manager zwróci rekord typu AAAA w odpowiedzi na kwerendę.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Czy mogę używać usługi Traffic Manager z więcej niż jedną aplikacją sieci Web w tym samym regionie?

Zazwyczaj usługa Traffic Manager jest używana do kierowania ruchu do aplikacji wdrożonych w różnych regionach. Jednak może również służyć, gdy aplikacja ma więcej niż jedno wdrożenie w tym samym regionie. Punkty końcowe usługi Azure usługi Traffic Manager nie zezwalają na dodawać więcej niż jeden punkt końcowy aplikacji sieci Web z tego samego regionu platformy Azure do tego samego profilu usługi Traffic Manager.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Jak przenieść punkty końcowe usługi Azure profilu usługi Traffic Manager do innej grupy zasobów lub subskrypcji?

Punkty końcowe platformy Azure, które są skojarzone z profilem usługi Traffic Manager są śledzone przy użyciu ich identyfikatorów zasobów. Gdy zasób platformy Azure, który jest używany jako punkt końcowy (na przykład publiczny adres IP, klasyczna usługa w chmurze, webapp lub inny profil usługi Traffic Manager używany w sposób zagnieżdżony) jest przenoszony do innej grupy zasobów lub subskrypcji, zmienia się jego identyfikator zasobu. W tym scenariuszu obecnie należy zaktualizować profil usługi Traffic Manager, najpierw usuwając, a następnie dodając z powrotem punkty końcowe do profilu.

## <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Czy usługa Traffic Manager jest odporna na awarie regionu platformy Azure?

Usługa Traffic Manager jest kluczowym składnikiem dostarczania aplikacji o wysokiej dostępności na platformie Azure.
Aby zapewnić wysoką dostępność, usługa Traffic Manager musi mieć wyjątkowo wysoki poziom dostępności i być odporna na awarie regionalne.

Zgodnie z projektem składniki usługi Traffic Manager są odporne na całkowitą awarię dowolnego regionu platformy Azure. Ta odporność dotyczy wszystkich składników usługi Traffic Manager: serwerów nazw DNS, interfejsu API, warstwy magazynu i usługi monitorowania punktu końcowego.

W mało prawdopodobnym przypadku awarii całego regionu platformy Azure oczekuje się, że usługa Traffic Manager będzie nadal działać normalnie. Aplikacje wdrożone w wielu regionach platformy Azure mogą polegać na usłudze Traffic Manager, aby kierować ruch do dostępnego wystąpienia aplikacji.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Jak wybór lokalizacji grupy zasobów wpływa na Menedżera ruchu?

Traffic Manager to jedna, globalna usługa. Nie ma regionalnych. Wybór lokalizacji grupy zasobów nie ma znaczenia dla profilów usługi Traffic Manager wdrożonych w tej grupie zasobów.

Usługa Azure Resource Manager wymaga, aby wszystkie grupy zasobów określały lokalizację, która określa domyślną lokalizację zasobów wdrożonych w tej grupie zasobów. Podczas tworzenia profilu usługi Traffic Manager jest on tworzony w grupie zasobów. Wszystkie profile usługi Traffic Manager używają **globalnej** jako lokalizacji, zastępując domyślną grupę zasobów.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Jak określić bieżący stan każdego punktu końcowego?

Bieżący stan monitorowania każdego punktu końcowego, oprócz ogólnego profilu, jest wyświetlany w witrynie Azure portal. Te informacje są również dostępne za pośrednictwem [interfejsu API REST](https://msdn.microsoft.com/library/azure/mt163667.aspx)monitora ruchu, [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.trafficmanager)i [wieloplatformowego interfejsu wiersza polecenia platformy Azure.](../cli-install-nodejs.md)

Można również użyć usługi Azure Monitor do śledzenia kondycji punktów końcowych i zobacz wizualną reprezentację ich. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Monitor, zobacz [dokumentację monitorowania platformy Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

### <a name="can-i-monitor-https-endpoints"></a>Czy mogę monitorować punkty końcowe HTTPS?

Tak. Usługa Traffic Manager obsługuje sondowanie za pośrednictwem protokołu HTTPS. Skonfiguruj **protokół HTTPS** jako protokół w konfiguracji monitorowania.

Usługa Traffic Manager nie może zapewnić sprawdzania poprawności certyfikatu, w tym:

* Certyfikaty po stronie serwera nie są sprawdzane
* Certyfikaty po stronie serwera SNI nie są sprawdzane
* Certyfikaty klienta nie są obsługiwane

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Czy podczas dodawania punktu końcowego używam adresu IP lub nazwy DNS?

Usługa Traffic Manager obsługuje dodawanie punktów końcowych przy użyciu trzech sposobów odwoływania się do nich — jako nazwy DNS, jako adresu IPv4 i jako adresu IPv6. Jeśli punkt końcowy zostanie dodany jako adres IPv4 lub IPv6, odpowiedź na zapytanie będzie odpowiednio typu rekord A lub AAAA. Jeśli punkt końcowy został dodany jako nazwa DNS, odpowiedź kwerendy będzie typu CNAME rekordu. Dodawanie punktów końcowych jako adres IPv4 lub IPv6 jest dozwolone tylko wtedy, gdy punkt końcowy jest typu **Zewnętrzny**.
Wszystkie metody routingu i ustawienia monitorowania są obsługiwane przez trzy typy adresowania punktów końcowych.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Jakich typów adresów IP można używać podczas dodawania punktu końcowego?

Usługa Traffic Manager umożliwia używanie adresów IPv4 lub IPv6 do określania punktów końcowych. Istnieje kilka ograniczeń, które są wymienione poniżej:

- Adresy odpowiadające zarezerwowanym prywatnym przestrzeniom adresów IP są niedozwolone. Adresy te obejmują te wywołane w RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 i RFC 5771
- Adres nie może zawierać żadnych numerów portów (można określić porty, które mają być używane w ustawieniach konfiguracji profilu)
- Żadne dwa punkty końcowe w tym samym profilu nie mogą mieć tego samego docelowego adresu IP

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Czy mogę używać różnych typów adresowania punktów końcowych w ramach jednego profilu?

Nie, usługa Traffic Manager nie zezwala na mieszanie typów adresowania punktów końcowych w profilu, z wyjątkiem przypadku profilu z typem routingu MultiValue, w którym można mieszać typy adresowania IPv4 i IPv6

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Co się stanie, gdy typ rekordu kwerendy przychodzącej różni się od typu rekordu skojarzonego z typem adresowania punktów końcowych?

Po odebraniu kwerendy względem profilu usługa Traffic Manager najpierw znajduje punkt końcowy, który musi zostać zwrócony zgodnie z określoną metodą routingu i stanem kondycji punktów końcowych. Następnie analizuje typ rekordu żądany w kwerendzie przychodzącej i typ rekordu skojarzony z punktem końcowym przed zwróceniem odpowiedzi na podstawie poniższej tabeli.

W przypadku profilów z dowolną metodą routingu inną niż MultiValue:

|Przychodzące żądanie kwerendy|     Typ punktu końcowego|     Udzielona odpowiedź|
|--|--|--|
|WSZELKIE |    A / AAAA / CNAME |    Docelowy punkt końcowy| 
|A |    A / CNAME |    Docelowy punkt końcowy|
|A |    AAAA |    NODATA (NODATA) |
|AAAA |    AAAA / CNAME |    Docelowy punkt końcowy|
|AAAA |    A |    NODATA (NODATA) |
|CNAME |    CNAME |    Docelowy punkt końcowy|
|CNAME     |A / AAAA |    NODATA (NODATA) |
|

W przypadku profilów z ustawioną metodą routingu na MultiValue:

|Przychodzące żądanie kwerendy|     Typ punktu końcowego |    Udzielona odpowiedź|
|--|--|--|
|WSZELKIE |    Mieszanka A i AAAA |    Docelowe punkty końcowe|
|A |    Mieszanka A i AAAA |    Tylko docelowe punkty końcowe typu A|
|AAAA    |Mieszanka A i AAAA|     Tylko docelowe punkty końcowe typu AAAA|
|CNAME |    Mieszanka A i AAAA |    NODATA (NODATA) |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Czy mogę używać profilu z punktami końcowymi adresowanymi przez IPv4 / IPv6 w profilu zagnieżdżonego?

Tak, można z wyjątkiem, że profil typu MultiValue nie może być profilem nadrzędnym w zagnieżdżonej konfiguracji profilu.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Zatrzymałem punkt końcowy aplikacji sieci web w moim profilu usługi Traffic Manager, ale nie otrzymuję żadnego ruchu nawet po ponownym uruchomieniu. Jak mogę to naprawić?

Gdy punkt końcowy aplikacji sieci Web platformy Azure jest zatrzymany Usługa Traffic Manager przestaje sprawdzać jego kondycję i ponownie sprawdza kondycję dopiero po wykryciu ponownego uruchomienia punktu końcowego. Aby zapobiec temu opóźnieniu, należy wyłączyć, a następnie ponownie uruchomić ten punkt końcowy w profilu usługi Traffic Manager po ponownym uruchomieniu punktu końcowego.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Czy mogę korzystać z Usługi Traffic Manager, nawet jeśli moja aplikacja nie obsługuje protokołu HTTP lub HTTPS?

Tak. Można określić protokół TCP jako protokół monitorowania, a usługa Traffic Manager może zainicjować połączenie TCP i czekać na odpowiedź z punktu końcowego. Jeśli punkt końcowy odpowiada na żądanie połączenia z odpowiedzią na ustanowienie połączenia, w okresie limitu czasu, a następnie ten punkt końcowy jest oznaczony jako w dobrej kondycji.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Jakie konkretne odpowiedzi są wymagane od punktu końcowego podczas korzystania z monitorowania TCP?

Gdy używane jest monitorowanie TCP, usługa Traffic Manager uruchamia trójkierunkowe uzgadnianie TCP, wysyłając żądanie SYN do punktu końcowego w określonym porcie. Następnie czeka na odpowiedź SYN-ACK z punktu końcowego przez okres czasu (określony w ustawieniach limitu czasu).

- Jeśli odpowiedź SYN-ACK zostanie odebrana w okresie limitu czasu określonym w ustawieniach monitorowania, ten punkt końcowy jest uważany za zdrowy. FIN lub FIN-ACK jest oczekiwaną odpowiedzią menedżera ruchu, gdy regularnie kończy gniazdo.
- Jeśli odpowiedź SYN-ACK zostanie odebrana po upływie określonego limitu czasu, Usługa Traffic Manager odpowie za pomocą usługi RST w celu zresetowania połączenia.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Jak szybko usługa Traffic Manager odsunie użytkowników od punktu końcowego w złej kondycji?

Usługa Traffic Manager udostępnia wiele ustawień, które mogą pomóc w kontrolowaniu zachowania trybu failover profilu usługi Traffic Manager w następujący sposób:

- można określić, że Usługa Traffic Manager sonduje punkty końcowe częściej, ustawiając interwał sondowania na 10 sekund. Gwarantuje to, że każdy punkt końcowy będzie w złej kondycji można wykryć tak szybko, jak to możliwe. 
- można określić, jak długo czekać przed limitem czasu żądania sprawdzania kondycji (minimalna wartość przesunienia czasu wynosi 5 sekund).
- można określić, ile błędów może wystąpić, zanim punkt końcowy zostanie oznaczony jako w złej kondycji. Ta wartość może być niska jako 0, w którym to przypadku punkt końcowy jest oznaczony w złej kondycji, jak tylko zakończy się niepowodzeniem pierwszego sprawdzenia kondycji. Jednak przy użyciu minimalnej wartości 0 dla tolerowanej liczby awarii może prowadzić do punktów końcowych są wyjęte z rotacji z powodu wszelkich przejściowych problemów, które mogą wystąpić w czasie sondowania.
- można określić czas wygaśnięcia (TTL), aby odpowiedź DNS była tak niska, jak 0. Oznacza to, że program rozpoznawania nazw DNS nie może buforować odpowiedzi, a każde nowe zapytanie otrzymuje odpowiedź zawierającą najbardziej aktualne informacje o kondycji, które ma Usługa Traffic Manager.

Za pomocą tych ustawień, Usługa Traffic Manager może zapewnić pracy awaryjnej w obszarze 10 sekund po punkt końcowy idzie w złej kondycji i kwerendy DNS jest w stosunku do odpowiedniego profilu.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Jak określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu?

Ustawienia monitorowania usługi Traffic Manager są na poziomie profilu. Jeśli trzeba użyć innego ustawienia monitorowania tylko dla jednego punktu końcowego, można to zrobić, mając ten punkt końcowy jako [profil zagnieżdżony,](traffic-manager-nested-profiles.md) którego ustawienia monitorowania różnią się od profilu nadrzędnego.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Jak przypisać nagłówki HTTP do kontroli kondycji usługi Traffic Manager do moich punktów końcowych?

Usługa Traffic Manager umożliwia określenie niestandardowych nagłówków w kontroli kondycji HTTP(S), które inicjuje do punktów końcowych. Jeśli chcesz określić nagłówek niestandardowy, można to zrobić na poziomie profilu (dotyczy wszystkich punktów końcowych) lub określić go na poziomie punktu końcowego. Jeśli nagłówek jest zdefiniowany na obu poziomach, a następnie jeden określony na poziomie punktu końcowego zastąpi poziom profilu jeden.
Jednym z typowych przypadków użycia w tym celu jest określenie nagłówków hosta, dzięki czemu żądania usługi Traffic Manager mogą być kierowane poprawnie do punktu końcowego hostowanego w środowisku wielodostępnym. Innym przypadkiem użycia jest zidentyfikowanie żądań usługi Traffic Manager z dzienników żądań HTTP(S) punktu końcowego

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Jakiego nagłówka hosta używają kontrole kondycji punktu końcowego?

Jeśli nie podano niestandardowego ustawienia nagłówka hosta, nagłówek hosta używany przez menedżera ruchu jest nazwą DNS punktu końcowego skonfigurowanego w profilu, jeśli jest dostępny.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Jakie są adresy IP, z których pochodzą kontrole kondycji?

Kliknij [tutaj,](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) aby wyświetlić plik JSON zawierający listę adresów IP, z których mogą pochodzić kontrole kondycji usługi Traffic Manager. Przejrzyj adresy IP wymienione w pliku JSON, aby upewnić się, że połączenia przychodzące z tych adresów IP są dozwolone w punktach końcowych, aby sprawdzić jego stan kondycji.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Ile kontroli kondycji do punktu końcowego można oczekiwać od Traffic Manager?

Liczba kontroli kondycji programu Traffic Manager docierających do punktu końcowego zależy od następujących:

- ustawiona wartość dla interwału monitorowania (mniejszy interwał oznacza więcej żądań lądujących na punkcie końcowym w danym okresie).
- liczba lokalizacji, z których pochodzą kontrole kondycji (adresy IP, z których można oczekiwać, że te kontrole są wymienione w poprzednim FAQ).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Jak mogę otrzymywać powiadomienia, jeśli jeden z moich punktów końcowych ustępuje?

Jedną z metryk dostarczonych przez usługa Traffic Manager jest stan kondycji punktów końcowych w profilu. Można to zobaczyć jako zagregowane wszystkich punktów końcowych wewnątrz profilu (na przykład 75% punktów końcowych są w dobrej kondycji) lub na poziomie punktu końcowego. Metryki usługi Traffic Manager są udostępniane za pośrednictwem usługi Azure Monitor i można użyć jego [możliwości alertów,](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) aby otrzymywać powiadomienia, gdy nastąpi zmiana stanu kondycji punktu końcowego. Aby uzyskać więcej informacji, zobacz [metryki i alerty programu Traffic Manager](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Profile zagnieżdżone usługi Traffic Manager

### <a name="how-do-i-configure-nested-profiles"></a>Jak skonfigurować profile zagnieżdżone?

Zagnieżdżone profile usługi Traffic Manager można skonfigurować przy użyciu zarówno usługi Azure Resource Manager, jak i klasycznych interfejsów API usługi Azure REST, poleceń cmdlet platformy Azure PowerShell i wieloplatformowych poleceń interfejsu wiersza polecenia platformy Azure. Są one również obsługiwane za pośrednictwem nowego portalu Azure.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Ile warstw zagnieżdżania obsługuje usługa Traffic Manger?

Profile o głębokości do 10 poziomów można zagnieżdżać. "Pętle" nie są dozwolone.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Czy można mieszać inne typy punktów końcowych z zagnieżdżonych profilów podrzędnych, w tym samym profilu usługi Traffic Manager?

Tak. Nie ma żadnych ograniczeń dotyczących sposobu łączenia punktów końcowych różnych typów w profilu.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>W jaki sposób model rozliczeń ma zastosowanie do profilów zagnieżdżonych?

Korzystanie z profili zagnieżdżonych nie ma negatywnego wpływu na ceny.

Rozliczanie usługi Traffic Manager ma dwa składniki: sprawdzanie kondycji punktu końcowego i miliony zapytań DNS

* Sprawdzanie kondycji punktu końcowego: Nie ma żadnych opłat za profil podrzędny skonfigurowany jako punkt końcowy w profilu nadrzędnym. Monitorowanie punktów końcowych w profilu podrzędnym jest rozliczane w zwykły sposób.
* Kwerendy DNS: Każda kwerenda jest liczona tylko raz. Kwerenda względem profilu nadrzędnego, który zwraca punkt końcowy z profilu podrzędnego jest zliczany tylko do profilu nadrzędnego.

Aby uzyskać szczegółowe informacje, zobacz [stronę z cenami menedżera ruchu](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Czy wpływ na wydajność profilów zagnieżdżonych?

Nie. Nie ma wpływu na wydajność podczas korzystania z profili zagnieżdżonych.

Serwery nazw usługi Traffic Manager przechodzą wewnętrznie przez hierarchię profilów podczas przetwarzania każdej kwerendy DNS. Kwerenda DNS do profilu nadrzędnego może odbierać odpowiedź DNS z punktem końcowym z profilu podrzędnego. Pojedynczy rekord CNAME jest używany niezależnie od tego, czy używasz profilu pojedynczego, czy zagnieżdżonego. Nie ma potrzeby tworzenia rekordu CNAME dla każdego profilu w hierarchii.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>W jaki sposób usługa Traffic Manager oblicza kondycję zagnieżdżonego punktu końcowego w profilu nadrzędnym?

Profil nadrzędny nie wykonuje kontroli kondycji dziecka bezpośrednio. Zamiast tego kondycja punktów końcowych profilu podrzędnego są używane do obliczania ogólnej kondycji profilu podrzędnego. Te informacje są propagowane w hierarchii profilu zagnieżdżonego w celu określenia kondycji zagnieżdżonego punktu końcowego. Profil nadrzędny używa tego zagregowanego kondycji, aby określić, czy ruch może być kierowany do dziecka.

W poniższej tabeli opisano zachowanie kontroli kondycji programu Traffic Manager dla zagnieżdżonego punktu końcowego.

| Stan monitora profilu dziecka | Stan nadrzędnego monitora punktu końcowego | Uwagi |
| --- | --- | --- |
| Wyłączona. Profil dziecka został wyłączony. |Zatrzymano |Nadrzędny stan punktu końcowego jest zatrzymana, a nie wyłączona. Stan Disabled jest zarezerwowany dla wskazania, że punkt końcowy w profilu nadrzędnym został wyłączony. |
| Zdegradowanych. Co najmniej jeden punkt końcowy profilu podrzędnego jest w stanie zdegradowanym. |Online: liczba punktów końcowych online w profilu podrzędnym jest co najmniej wartość MinChildEndpoints.<BR>CheckingEndpoint: liczba punktów końcowych online plus CheckingEndpoint w profilu podrzędnym jest co najmniej wartością MinChildEndpoints.<BR>Zdegradowany: w przeciwnym razie. |Ruch jest kierowany do punktu końcowego stanu CheckingEndpoint. Jeśli MinChildEndpoints jest ustawiona zbyt wysoka, punkt końcowy jest zawsze zdegradowany. |
| Online. Co najmniej jeden punkt końcowy profilu podrzędnego jest stanem online. Żaden punkt końcowy nie jest w stanie obniżona. |Patrz wyżej. | |
| CheckingEndpoints. Co najmniej jeden punkt końcowy profilu podrzędnego to "CheckingEndpoint". Punkty końcowe nie są "online" lub "zdegradowane" |Tak samo jak powyżej. | |
| Nieaktywne. Wszystkie punkty końcowe profilu podrzędnego są wyłączone lub zatrzymane lub ten profil nie ma punktów końcowych. |Zatrzymano | |

## <a name="next-steps"></a>Następne kroki:

- Dowiedz się więcej o monitorowaniu punktu końcowego usługi Traffic Manager [i automatycznej pracy awaryjnej.](../traffic-manager/traffic-manager-monitoring.md)
- Dowiedz się więcej o [metodach routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md)usługi Traffic Manager .
