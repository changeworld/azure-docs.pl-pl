---
title: Usługa Azure Traffic Manager — metody routingu ruchu
description: W tym artykułach pomaga zrozumieć różne metody routingu ruchu używane przez Usługę Traffic Manager
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250909"
---
# <a name="traffic-manager-routing-methods"></a>Metody routingu w usłudze Traffic Manager

Usługa Azure Traffic Manager obsługuje sześć metod routingu ruchu, aby określić sposób kierowania ruchu sieciowego do różnych punktów końcowych usługi. W przypadku dowolnego profilu usługa Traffic Manager stosuje skojarzoną z nim metodę routingu ruchu do każdej odbieranej kwerendy DNS. Metoda routingu ruchu określa, który punkt końcowy jest zwracany w odpowiedzi DNS.

W usłudze Traffic Manager dostępne są następujące metody routingu ruchu:

* **[Priorytet:](#priority-traffic-routing-method)** Wybierz **priorytet,** jeśli chcesz użyć punktu końcowego usługi podstawowej dla całego ruchu i udostępnij kopie zapasowe w przypadku, gdy podstawowe lub zapasowe punkty końcowe są niedostępne.
* **[Ważony](#weighted):** Wybierz **ważony,** jeśli chcesz rozmieścić ruch w zestawie punktów końcowych, równomiernie lub według ważonych, które definiujesz.
* **[Wydajność:](#performance)** Wybierz **wydajność,** gdy masz punkty końcowe w różnych lokalizacjach geograficznych i chcesz, aby użytkownicy końcowi używali "najbliższego" punktu końcowego pod względem najniższego opóźnienia sieci.
* **[Geograficzne:](#geographic)** Wybierz **opcję Geograficzną,** aby użytkownicy kierowali się do określonych punktów końcowych (Azure, External lub Nested) na podstawie lokalizacji geograficznej, z której pochodzi ich zapytanie DNS. Umożliwia to klientom usługi Traffic Manager włączenie scenariuszy, w których znajomość regionu geograficznego użytkownika i ich routing na podstawie tego jest ważne. Przykłady obejmują przestrzeganie nakazów suwerenności danych, lokalizację zawartości & doświadczenia użytkownika i mierzenie ruchu z różnych regionów.
* **[Wielowartościowe:](#multivalue)** Wybierz **multiwartuł** dla profilów usługi Traffic Manager, które mogą mieć tylko adresy IPv4/IPv6 jako punkty końcowe. Po odebraniu kwerendy dla tego profilu zwracane są wszystkie punkty końcowe w dobrej kondycji.
* **[Podsieć](#subnet):** Wybierz metodę routingu ruchu **podsieci,** aby mapować zestawy zakresów adresów IP użytkownika końcowego do określonego punktu końcowego w profilu usługi Traffic Manager. Po odebraniu żądania zwracany punkt końcowy będzie punktem zamapowanym dla źródłowego adresu IP tego żądania. 


Wszystkie profile usługi Traffic Manager obejmują monitorowanie kondycji punktu końcowego i automatyczną przełączoną w tryb failover punktu końcowego. Aby uzyskać więcej informacji, zobacz [Monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md). Jeden profil usługi Traffic Manager może używać tylko jednej metody routingu ruchu. W każdej chwili można wybrać inną metodę routingu ruchu dla swojego profilu. Zmiany są stosowane w ciągu jednej minuty i nie występują żadne przestoje. Metody routingu ruchu można łączyć przy użyciu zagnieżdżonych profilów usługi Traffic Manager. Zagnieżdżanie umożliwia zaawansowane i elastyczne konfiguracje routingu ruchu, które spełniają potrzeby większych, złożonych aplikacji. Aby uzyskać więcej informacji, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Priorytetowa metoda routingu ruchu

Często organizacja chce zapewnić niezawodność swoich usług, wdrażając jedną lub więcej usług tworzenia kopii zapasowych w przypadku, gdy ich usługa podstawowa ulegnie ulegnie utracie wydajności. Metoda routingu ruchu "Priorytet" umożliwia klientom platformy Azure łatwe zaimplementowanie tego wzorca trybu failover.

![Metoda routingu ruchu usługi Azure Traffic Manager](media/traffic-manager-routing-methods/priority.png)

Profil usługi Traffic Manager zawiera listę punktów końcowych usług z różnymi priorytetami. Domyślnie usługa Traffic Manager kieruje cały ruch do podstawowego punktu końcowego (punktu końcowego o najwyższym priorytecie). Jeśli podstawowy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje ruch do drugiego punktu końcowego. Jeśli oba punkty końcowe — podstawowy i pomocniczy — nie są dostępne, ruch jest kierowany do trzeciego punktu końcowego itd. Dostępność punktu końcowego zależy od skonfigurowanego stanu (włączonego lub wyłączonego) oraz od bieżącego monitorowania.

### <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Za pomocą usługi Azure Resource Manager można skonfigurować priorytet punktu końcowego jawnie przy użyciu właściwości "priorytet" dla każdego punktu końcowego. Ta właściwość jest wartością od 1 do 1000. Niższe wartości reprezentują wyższy priorytet. Punkty końcowe nie mogą współużytkuj wartości priorytetu. Ustawienie właściwości jest opcjonalne. Po pominięciu używany jest domyślny priorytet oparty na kolejności punktu końcowego.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Ważona metoda routingu ruchu
Metoda "ważonego" routingu ruchu umożliwia równomierne rozłożenie ruchu lub użycie wstępnie zdefiniowanej wagi.

![Metoda routingu ruchu usługi Azure Traffic Manager](media/traffic-manager-routing-methods/weighted.png)

W metodzie ważonego routingu ruchu przypisywana jest waga do każdego punktu końcowego w konfiguracji profilu usługi Traffic Manager. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr jest opcjonalny. Jeśli pominięto, Menedżerowie ruchu używa domyślnej wagi "1". Im wyższa waga, tym wyższy priorytet.

Dla każdego odebranego zapytania DNS usługa Traffic Manager losowo wybiera dostępny punkt końcowy. Prawdopodobieństwo wybrania punktu końcowego zależy od wagi, jaka jest przypisana do tego punktu. Przy użyciu tej samej wagi we wszystkich punktach końcowych powoduje równomierny rozkład ruchu. Użycie wyższych lub niższych wag w określonych punktach końcowych powoduje, że te punkty końcowe mają być zwracane częściej lub rzadziej w odpowiedziach DNS.

Metoda ważona umożliwia kilka przydatnych scenariuszy:

* Stopniowe uaktualnianie aplikacji: Przydzielanie procentu ruchu do trasy do nowego punktu końcowego i stopniowe zwiększanie ruchu w czasie do 100%.
* Migracja aplikacji na platformę Azure: utwórz profil z platformą Azure i zewnętrznymi punktami końcowymi. Dostosuj wagę punktów końcowych, aby preferować nowe punkty końcowe.
* Chmura z dużą dobłędności zapewnia dodatkową pojemność: szybko rozszerzaj wdrożenie lokalne w chmurze, umieszczając go za profilem usługi Traffic Manager. Gdy potrzebujesz dodatkowej pojemności w chmurze, można dodać lub włączyć więcej punktów końcowych i określić, jaka część ruchu trafia do każdego punktu końcowego.

Oprócz korzystania z witryny Azure portal, można skonfigurować wagi przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST.

Ważne jest, aby zrozumieć, że odpowiedzi DNS są buforowane przez klientów i rekursywne serwery DNS używane przez klientów do rozpoznawania nazw DNS. To buforowanie może mieć wpływ na rozkłady ruchu ważonego. Gdy liczba klientów i cyklicznych serwerów DNS jest duża, dystrybucja ruchu działa zgodnie z oczekiwaniami. Jednak gdy liczba klientów lub cyklicznych serwerów DNS jest mała, buforowanie może znacznie wypaczyć dystrybucję ruchu.

Typowe przypadki użycia obejmują:

* Środowiska programistyczne i testowe
* Komunikacja między aplikacjami
* Aplikacje przeznaczone dla wąskiej bazy użytkowników, które współużytkują wspólną cykliczną infrastrukturę DNS (na przykład pracownicy firmy łączący się za pośrednictwem serwera proxy)

Te efekty buforowania DNS są wspólne dla wszystkich systemów routingu ruchu opartych na systemie DNS, a nie tylko usługi Azure Traffic Manager. W niektórych przypadkach jawne wyczyszczenie pamięci podręcznej DNS może zapewnić obejście problemu. W innych przypadkach alternatywna metoda routingu ruchu może być bardziej odpowiednia.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Metoda routingu ruchu wydajności

Wdrażanie punktów końcowych w dwóch lub więcej lokalizacjach na całym świecie może poprawić szybkość reakcji wielu aplikacji, kierując ruch do lokalizacji, która jest "najbliższa". Metoda routingu ruchu "Wydajność" zapewnia tę możliwość.

![Metoda routingu ruchu usługi Azure Traffic Manager](media/traffic-manager-routing-methods/performance.png)

"Najbliższy" punkt końcowy nie musi być najbliższy, mierzony odległością geograficzną. Zamiast tego metoda routingu ruchu "Wydajność" określa najbliższy punkt końcowy, mierząc opóźnienie sieci. Usługa Traffic Manager utrzymuje tabelę opóźnień w Internecie, aby śledzić czas podróży w obie strony między zakresami adresów IP a każdym centrum danych platformy Azure.

Usługa Traffic Manager wyszukuje źródłowy adres IP przychodzącego żądania DNS w tabeli opóźnienia internetowego. Menedżer ruchu następnie wybiera dostępny punkt końcowy w centrum danych platformy Azure, który ma najniższe opóźnienie dla tego zakresu adresów IP i zwraca ten punkt końcowy w odpowiedzi DNS.

Jak wyjaśniono w [how Traffic Manager Works](traffic-manager-how-it-works.md), Usługa Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zamiast tego kwerendy DNS pochodzą z cyklicznej usługi DNS, do której klienci są skonfigurowani do użycia. W związku z tym adres IP używany do określenia "najbliższego" punktu końcowego nie jest adresem IP klienta, ale jest to adres IP cyklicznej usługi DNS. W praktyce ten adres IP jest dobrym serwerem proxy dla klienta.


Usługa Traffic Manager regularnie aktualizuje tabelę opóźnienia w internecie, aby uwzględnić zmiany w globalnym Internecie i nowych regionach platformy Azure. Jednak wydajność aplikacji różni się w zależności od różnic w obciążeniu w czasie rzeczywistym w Internecie. Routing ruchu wydajności nie monitoruje obciążenia dla danego punktu końcowego usługi. Jeśli jednak punkt końcowy stanie się niedostępny, usługa Traffic Manager nie uwzględni go w odpowiedziach na zapytania DNS.


Uwagi do uwag:

* Jeśli twój profil zawiera wiele punktów końcowych w tym samym regionie platformy Azure, usługa Traffic Manager równomiernie rozdziela ruch między dostępne punkty końcowe w tym regionie. Jeśli wolisz inną dystrybucję ruchu w regionie, możesz użyć [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Jeśli wszystkie włączone punkty końcowe w najbliższym regionie platformy Azure są obniżone, usługa Traffic Manager przenosi ruch do punktów końcowych w następnym najbliższym regionie platformy Azure. Jeśli chcesz zdefiniować preferowaną sekwencję trybu failover, użyj [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Podczas korzystania z metody routingu ruchu wydajności z zewnętrznych punktów końcowych lub zagnieżdżonych punktów końcowych, należy określić lokalizację tych punktów końcowych. Wybierz region platformy Azure najbliżej wdrożenia. Te lokalizacje są wartościami obsługiwanymi przez tabelę opóźnienia w Internecie.
* Algorytm, który wybiera punkt końcowy jest deterministyczny. Powtarzające się zapytania DNS z tego samego klienta są kierowane do tego samego punktu końcowego. Zazwyczaj klienci używają różnych cyklicznych serwerów DNS podczas podróży. Klient może być kierowany do innego punktu końcowego. Na routing mogą mieć również wpływ aktualizacje tabeli opóźnienia internetu. W związku z tym wydajność ruchu routingu metoda nie gwarantuje, że klient jest zawsze kierowane do tego samego punktu końcowego.
* Po zmianie tabeli opóźnienia w Internecie można zauważyć, że niektórzy klienci są kierowani do innego punktu końcowego. Ta zmiana routingu jest dokładniejsza na podstawie bieżących danych opóźnienia. Te aktualizacje są niezbędne do utrzymania dokładności routingu ruchu wydajności w miarę ciągłego rozwoju Internetu.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Metoda routingu ruchu geograficznego

Profile usługi Traffic Manager można skonfigurować tak, aby używały metody routingu geograficznego, dzięki czemu użytkownicy są kierowani do określonych punktów końcowych (azure, zewnętrzny lub zagnieżdżony) na podstawie lokalizacji geograficznej, z której pochodzi ich zapytanie DNS. Umożliwia to klientom usługi Traffic Manager włączenie scenariuszy, w których znajomość regionu geograficznego użytkownika i ich routing na podstawie tego jest ważne. Przykłady obejmują przestrzeganie nakazów suwerenności danych, lokalizację zawartości & doświadczenia użytkownika i mierzenie ruchu z różnych regionów.
Gdy profil jest skonfigurowany do routingu geograficznego, każdy punkt końcowy skojarzony z tym profilem musi mieć przypisany zestaw regionów geograficznych. Region geograficzny może znajdować się na następujących poziomach 
- Świat – każdy region
- Ugrupowanie regionalne – na przykład Afryka, Bliski Wschód, Australia/Pacyfik itp. 
- Kraj/region – na przykład Irlandia, Peru, Hongkong SAR itp. 
- Stan/prowincja – na przykład USA-Kalifornia, Australia-Queensland, Kanada-Alberta itp.

Gdy region lub zestaw regionów jest przypisany do punktu końcowego, wszelkie żądania z tych regionów są kierowane tylko do tego punktu końcowego. Usługa Traffic Manager używa źródłowego adresu IP kwerendy DNS do określenia regionu, z którego użytkownik wykonuje kwerendę — zazwyczaj jest to adres IP lokalnego programu rozpoznawania nazw DNS wykonującego kwerendę w imieniu użytkownika.  

![Metoda routingu ruchu usługi Azure Traffic Manager](./media/traffic-manager-routing-methods/geographic.png)

Usługa Traffic Manager odczytuje źródłowy adres IP kwerendy DNS i decyduje, z którego regionu geograficznego pochodzi. Następnie sprawdza, czy istnieje punkt końcowy, który ma ten region geograficzny mapowane do niego. To wyszukiwanie rozpoczyna się od najniższego poziomu szczegółowości (stan/prowincja, w którym jest obsługiwane, w przeciwnym razie na poziomie kraju/regionu) i przechodzi aż do najwyższego poziomu, jakim jest **Świat**. Pierwsze dopasowanie znalezione przy użyciu tego przechodzenia jest wyznaczony jako punkt końcowy, aby powrócić w odpowiedzi na kwerendę. Podczas dopasowywania z zagnieżdżonego punktu końcowego, punkt końcowy w tym profilu podrzędnym jest zwracany, na podstawie jego metody routingu. Do tego zachowania mają zastosowanie następujące punkty:

- Region geograficzny można mapować tylko do jednego punktu końcowego w profilu usługi Traffic Manager, gdy typem routingu jest routing geograficzny. Gwarantuje to, że routing użytkowników jest deterministyczny, a klienci mogą włączyć scenariusze, które wymagają jednoznacznych granic geograficznych.
- Jeśli region użytkownika znajduje się w obszarze mapowania geograficznego dwóch różnych punktów końcowych, usługa Traffic Manager wybiera punkt końcowy o najniższej szczegółowości i nie bierze pod uwagę routingu żądań z tego regionu do drugiego punktu końcowego. Rozważmy na przykład profil typu routingu geograficznego z dwoma punktami końcowymi - Punkt końcowy1 i Punkt końcowy2. Punkt końcowy1 jest skonfigurowany do odbierania ruchu z Irlandii, a punkt końcowy 2 jest skonfigurowany do odbierania ruchu z Europy. Jeśli żądanie pochodzi z Irlandii, jest zawsze kierowane do punktu końcowego1.
- Ponieważ region może być mapowany tylko do jednego punktu końcowego, usługa Traffic Manager zwraca go niezależnie od tego, czy punkt końcowy jest w dobrej kondycji, czy nie.

    >[!IMPORTANT]
    >Zdecydowanie zaleca się, aby klienci korzystający z metody routingu geograficznego skojarzyli ją z punktami końcowymi typu zagnieżdżonego, które mają profile podrzędne zawierające co najmniej dwa punkty końcowe w ramach każdego z nich.
- Jeśli zostanie znalezione dopasowanie punktu końcowego i ten punkt końcowy jest w stanie **Zatrzymane,** usługa Traffic Manager zwraca odpowiedź NODATA. W takim przypadku żadne dalsze wyszukiwania nie są wykonane wyżej w hierarchii regionu geograficznego. To zachowanie ma również zastosowanie do zagnieżdżonych typów punktów końcowych, gdy profil podrzędny jest w stanie **Zatrzymane** lub **Wyłączone.**
- Jeśli punkt końcowy wyświetla **stan Wyłączone,** nie zostaną uwzględnione w procesie dopasowywania regionu. To zachowanie ma również zastosowanie do zagnieżdżonych typów punktów końcowych, gdy punkt końcowy jest w stanie **Wyłączone.**
- Jeśli kwerenda pochodzi z regionu geograficznego, który nie ma mapowania w tym profilu, usługa Traffic Manager zwraca odpowiedź NODATA. W związku z tym zdecydowanie zaleca się, aby klienci używali routingu geograficznego z jednym punktem końcowym, najlepiej typu Zagnieżdżone z co najmniej dwoma punktami końcowymi w profilu podrzędnym, z regionem **Świat** przypisany do niego. Gwarantuje to również, że wszystkie adresy IP, które nie są mapowane do regionu są obsługiwane.

Jak wyjaśniono w [how Traffic Manager Works](traffic-manager-how-it-works.md), Usługa Traffic Manager nie odbiera zapytań DNS bezpośrednio od klientów. Zamiast tego kwerendy DNS pochodzą z cyklicznej usługi DNS, do której klienci są skonfigurowani do użycia. W związku z tym adres IP używany do określenia regionu nie jest adresem IP klienta, ale jest to adres IP cyklicznej usługi DNS. W praktyce ten adres IP jest dobrym serwerem proxy dla klienta.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których routing geograficzny jest przydatny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Jak zdecydować, czy należy użyć metody routingu wydajności, czy metody routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Jakie regiony są obsługiwane przez usługę Traffic Manager dla routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [W jaki sposób menedżer ruchu określa, gdzie użytkownik wykonuje kwerendy?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Czy gwarantujemy, że Usługa Traffic Manager może poprawnie określić dokładną lokalizację geograficzną użytkownika w każdym przypadku?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Czy punkt końcowy musi być fizycznie zlokalizowany w tym samym regionie, w jakim jest skonfigurowany do routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Czy można przypisać regiony geograficzne do punktów końcowych w profilu, który nie jest skonfigurowany do routingu geograficznego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Dlaczego podczas próby zmiany metody routingu istniejącego profilu na geograficzny pojawia się błąd?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Dlaczego zdecydowanie zaleca się, aby klienci tworzyli profile zagnieżdżone zamiast punktów końcowych w profilu z włączonym routingiem geograficznym?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Czy istnieją jakieś ograniczenia dotyczące wersji interfejsu API obsługującej ten typ routingu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Metoda wielowartościowego wyznaczania ruchu
**Metoda wielowartościowego routingu** ruchu umożliwia uzyskanie wielu zdrowych punktów końcowych w jednej odpowiedzi na kwerendę DNS. Dzięki temu wywołujący do ponawiania po stronie klienta z innymi punktami końcowymi w przypadku zwracanego punktu końcowego nie odpowiada. Ten wzorzec pozwala zwiększyć dostępność usługi i ograniczyć opóźnienie związane z uzyskiwaniem punktu końcowego w dobrej kondycji przez nowe zapytanie DNS. Metoda routingu MultiValue działa tylko wtedy, gdy wszystkie punkty końcowe typu "Zewnętrzne" i są określone jako adresy IPv4 lub IPv6. Po odebraniu kwerendy dla tego profilu wszystkie punkty końcowe w dobrej kondycji są zwracane i podlegają konfigurowalnej maksymalnej liczbie zwracanych.

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których routing MultiValue jest przydatny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Ile punktów końcowych jest zwracanych, gdy używana jest routing Wielowartościowy?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Czy otrzymam ten sam zestaw punktów końcowych, gdy używana jest routing wielowartościowy?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Metoda routingu ruchu w podsieci
Metoda routingu ruchu **podsieci** umożliwia mapowanie zestawu zakresów adresów IP użytkownika końcowego do określonych punktów końcowych w profilu. Następnie, jeśli Usługa Traffic Manager otrzyma kwerendę DNS dla tego profilu, sprawdzi źródłowy adres IP tego żądania (w większości przypadków będzie to wychodzący adres IP programu rozpoznawania nazw DNS używanego przez wywołującego), określi, do którego punktu końcowego jest mapowany i zwróci tego punktu końcowego w odpowiedzi na kwerendę. 

Adres IP, który ma być mapowany do punktu końcowego, można określić jako zakresy CIDR (np. 1.2.3.0/24) lub jako zakres adresów (np. 1.2.3.4-5.6.7.8). Zakresy adresów IP skojarzone z punktem końcowym muszą być unikatowe w tym profilu i nie mogą nakładać się na zestaw adresów IP innego punktu końcowego w tym samym profilu.
Jeśli zdefiniujesz punkt końcowy bez zakresu adresów, który działa jako rezerwowy i przyjmuje ruch z pozostałych podsieci. Jeśli nie rezerwowy punkt końcowy jest uwzględniony, usługa Traffic Manager wysyła odpowiedź NODATA dla wszystkich niezdefiniowanych zakresów. W związku z tym zdecydowanie zaleca się zdefiniowanie rezerwowego punktu końcowego lub zapewnienie, że wszystkie możliwe zakresy adresów IP są określone w punktach końcowych.

Routing podsieci może służyć do dostarczania różnych środowiska dla użytkowników łączących się z określonej przestrzeni IP. Na przykład przy użyciu routingu dla podsieci wszystkie żądania wysyłane przez klienta z siedziby firmy mogą być kierowane do innego punktu końcowego, który może służyć do testowania wersji aplikacji przeznaczonej tylko na użytek wewnętrzny. Inny scenariusz polega na udostępnianiu określonego środowiska użytkownikom łączącym się za pośrednictwem danego usługodawcy internetowego (gdy na przykład chcesz blokować użytkowników tego usługodawcy).

### <a name="faqs"></a>Często zadawane pytania

* [Jakie są przypadki użycia, w których routing podsieci jest przydatny?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [W jaki sposób Traffic Manager zna adres IP użytkownika końcowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Jak określić adresy IP podczas korzystania z routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Jak określić rezerwowy punkt końcowy podczas korzystania z routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Co się stanie, jeśli punkt końcowy jest wyłączony w profilu typu routingu podsieci?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć aplikacje o wysokiej dostępności przy użyciu [monitorowania punktów końcowych usługi Traffic Manager](traffic-manager-monitoring.md)




