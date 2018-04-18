---
title: Menedżer ruchu Azure - metody routingu ruchu | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga zrozumieć metody routingu ruchu innego użyty przez Menedżera ruchu
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: c9bd9b4913e38ed5c1f7f4ec8ee7e3210fa3be8f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="traffic-manager-routing-methods"></a>Metody routingu w usłudze Traffic Manager

Usługi Azure Traffic Manager obsługuje cztery metody routingu ruchu, aby określić sposób kierowania ruchu sieciowego do różnych punktów końcowych usługi. Menedżer ruchu dotyczą każdego zapytania DNS, który odbierze metody routingu ruchu. Metody routingu ruchu określa, które punkt końcowy zwrócił w odpowiedzi DNS.

Dostępne są cztery metody routingu ruchu w usłudze Traffic Manager:

* **[Priorytet](#priority):** wybierz **priorytet** gdy chcesz używać podstawowego punktu końcowego dla całego ruchu i podaj kopii zapasowych w przypadku podstawowej lub kopii zapasowej punktów końcowych, które są niedostępne.
* **[Ważone](#weighted):** wybierz **ważone** rozłożenie ruch między zbiorem punktów końcowych, należy albo równomiernie według wagi, która będzie definiować lub.
* **[Wydajność](#performance):** wybierz **wydajności** gdy masz punktów końcowych w różnych lokalizacjach geograficznych i chcesz użytkownikom końcowym użycia "najbliższy" punktu końcowego pod względem najniższe opóźnienia sieci.
* **[Geograficzne](#geographic):** wybierz **geograficzne** tak, aby użytkownicy są przekierowywani do określonych punktów końcowych (Azure, zewnętrzne lub zagnieżdżone) na podstawie których lokalizacji geograficznej pochodzi ich zapytanie DNS. To umożliwia klientom usługi Traffic Manager na potrzeby scenariuszy, w których znajomość regionu geograficznego użytkownika oraz routing je w oparciu o który jest ważna. Przykładami zgodnych z danych suwerenności zleceń, lokalizacja środowiska użytkownika & zawartości i pomiar ruchu z różnych regionów.

Wszystkie profile Menedżera ruchu obejmują monitorowania kondycji punktu końcowego i punktu końcowego automatycznej pracy awaryjnej. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego Menedżera ruchu](traffic-manager-monitoring.md). Jeden profil usługi Traffic Manager można użyć tylko jedna metoda routingu ruchu. W dowolnym momencie można wybrać metodę routingu ruchu innego profilu. Zmiany zostaną zastosowane w ciągu jednej minuty, a poniesienia bez przestojów. Metody routingu ruchu można łączyć, używając zagnieżdżonych profilów usługi Traffic Manager. Zagnieżdżanie umożliwia zaawansowane i elastyczne routingu ruchu konfiguracje, które potrzeb większych, złożonych aplikacji. Aby uzyskać więcej informacji, zobacz [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Metody routingu ruchu o priorytecie

Często organizacja chce zapewnić niezawodność dla jego usług przez wdrożenie co najmniej jednej usługi tworzenia kopii zapasowych w przypadku, gdy ich podstawowa usługa przestanie działać. Metody routingu ruchu 'Priority' umożliwia Azure klientom łatwe Implementowanie tego wzorca trybu failover.

![Menedżer ruchu Azure 'Priority' routingu ruchu — metoda][1]

Profil usługi Traffic Manager zawiera priorytetową listą punktów końcowych usługi. Domyślnie usługi Traffic Manager wysyła cały ruch do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy nie jest dostępna, Menedżer ruchu kieruje ruch do drugiego punktu końcowego. Jeśli zarówno podstawowe i pomocnicze punkty końcowe nie są dostępne, ruch przechodzi do innej i tak dalej. Dostępność punktu końcowego jest na podstawie skonfigurowanego stanu (włączona lub wyłączona) i monitorowania bieżących punktów końcowych.

### <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Usługi Azure Resource Manager można skonfigurować priorytet punktu końcowego jawnie przy użyciu właściwości 'priority' dla każdego punktu końcowego. Ta właściwość jest wartość z zakresu od 1 do 1000. Niższe wartości reprezentują wyższy priorytet. Punkty końcowe nie mogą mieć wartości priorytetu. Ustawienie właściwości jest opcjonalne. W przypadku pominięcia będzie używana domyślna wartość priorytetu na podstawie kolejności punkt końcowy jest używany.

##<a name = "weighted"></a>Ważoną metody routingu ruchu
"Ważoną" metody routingu ruchu umożliwia równomierne rozłożenie ruchu lub użyć wstępnie zdefiniowanych wagi.

![Azure Traffic Manager "Ważoną" routingu ruchu — metoda][2]

W przypadku ważoną metody routingu ruchu wagi przypisane do każdego punktu końcowego w konfiguracji profilu Menedżera ruchu. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr jest opcjonalny. Pominięcie menedżerów ruchu korzysta z domyślną wagę "1". Wyższe wagi, tym wyższy priorytet.

Dla każdego zapytania DNS Odebrano Traffic Manager losowo wybiera dostępnego punktu końcowego. Prawdopodobieństwo wybrać punkt końcowy jest oparta na wag przypisane do wszystkich dostępnych punktów końcowych. Wszystkie punkty końcowe wyniki w Dystrybucja ruchu nawet przy użyciu tymi samymi wagami. Za pomocą wag wyższe lub niższe w określonych punktach końcowych sprawia, że te punkty końcowe zwracaną częściej lub rzadziej w odpowiedzi DNS.

Metoda ważoną umożliwia niektórych przydatne w scenariuszach:

* Uaktualnienie stopniowe aplikacji: przydzielić procent ruchu do kierowania do nowego punktu końcowego, a następnie stopniowo zwiększać ruchu w czasie do 100%.
* Migracja aplikacji na platformie Azure: Utwórz profil z zarówno dla platformy Azure, jak i zewnętrznych punktów końcowych. Dostosuj wagę punkty końcowe preferować nowe punkty końcowe.
* Chmura poszerzająca dodatkowej pojemności: szybko rozwiń lokalnego wdrożenia w chmurze umieszczając za profilu usługi Traffic Manager. Jeśli potrzebujesz dodatkowej pojemności w chmurze, można dodać lub włączyć więcej punktów końcowych i określić, jaka część ruchu jest przesyłany do każdego punktu końcowego.

Portal Azure Resource Manager obsługuje konfigurację routingu ruchu ważoną.  Można skonfigurować przy użyciu wersji Menedżera zasobów Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST wagi.

Należy zrozumieć, czy odpowiedzi DNS są buforowane przez klientów i serwery DNS cykliczne, używane przez klientów do rozpoznawania nazw DNS. To buforowanie może mieć wpływ na dystrybucje ważoną ruchu. Gdy jest duża liczba klientów i serwery DNS cykliczne, Dystrybucja ruchu działa zgodnie z oczekiwaniami. Jednak w przypadku małych liczbę klientów lub serwerów DNS cykliczne buforowanie może znacznie pochylanie Dystrybucja ruchu.

Typowe przypadki użycia obejmują:

* Projektowanie i środowisk testowych
* Komunikacja aplikacji do aplikacji
* Aplikacje celem wąskie użytkownika — podstawowy, które używają wspólnej infrastruktury DNS cykliczne (na przykład pracownicy firmy łączących się za pośrednictwem serwera proxy)

Efekty buforowania te DNS są wspólne dla wszystkich DNS na podstawie ruchu systemów routingu, nie tylko usługi Azure Traffic Manager. W niektórych przypadkach jawnie wyczyszczenie pamięci podręcznej DNS może udostępnić obejście tego problemu. W innych przypadkach może być bardziej odpowiednie alternatywną metodę routingu ruchu.

## <a name = "performance"></a>Metody routingu ruchu wydajności

Wdrażanie punktów końcowych w dwóch lub więcej lokalizacji na całym świecie można zwiększyć czas odpowiedzi z wielu aplikacji przez routingu ruchu do lokalizacji, która jest najbliższym, użytkownikowi. Metody routingu ruchu "Performance" obsługuje tę funkcję.

![Menedżer ruchu Azure "Performance" routingu ruchu — metoda][3]

"Najbliższy" punktu końcowego nie jest niekoniecznie najbliższego mierzony odległość geograficznej. Zamiast tego metody routingu ruchu "Performance" Określa najbliższego punktu końcowego, mierząc opóźnienia sieci. Menedżer ruchu zapisuje tabeli opóźnienia Internet na śledzenie czasu Rundy między zakresów adresów IP i każdego centrum danych Azure.

Menedżer ruchu wyszukuje źródłowy adres IP żądania przychodzącego DNS w tabeli opóźnienia Internet. Menedżer ruchu wybiera dostępnego punktu końcowego w centrum danych Azure, który ma uzyskać najmniejsze opóźnienia dla tego zakresu adresów IP, a następnie zwraca w odpowiedzi DNS tego punktu końcowego.

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](traffic-manager-overview.md#how-traffic-manager-works), Menedżera ruchu nie odbiera zapytania DNS bezpośrednio od klientów. Zamiast zapytania DNS pochodzą z usługi DNS cykliczne że klienci są skonfigurowane do używania. W związku z tym adres IP używany do określenia punktu końcowego "najbliższy" nie jest adres IP klienta, ale jest adres IP, usługi DNS cykliczne. W praktyce ten adres IP jest dobrym serwera proxy klienta.


Menedżer ruchu regularnie aktualizuje tabeli opóźnienia Internet, aby uwzględnić zmiany w globalnej Internet i nowe regiony platformy Azure. Jednakże wydajność aplikacji w zależności od w czasie rzeczywistym wahania obciążenia w Internecie. Routing ruchu wydajności nie monitoruje obciążenie danego punktu końcowego. Jednak jeśli punkt końcowy jest niedostępny, usługi Traffic Manager nie ma go w odpowiedzi na zapytania DNS.


Informacje, które należy zwrócić uwagę:

* Jeśli Twój profil zawiera wiele punktów końcowych w tym samym regionie Azure, następnie Traffic Manager dystrybuuje ruch równomiernie między dostępnych punktów końcowych w tym regionie. Jeśli wolisz dystrybucji różnych ruchu w obrębie regionu, możesz użyć [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Jeśli zdegradowanie wszystkich włączonych punktów końcowych w najbliższy region platformy Azure, Menedżera ruchu przenosi ruch do punktów końcowych w następnym najbliższy region platformy Azure. Jeśli chcesz zdefiniować sekwencji preferowany tryb failover, użyj [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Jeśli używasz metody routingu ruchu wydajności z zewnętrznych punktów końcowych lub zagnieżdżone punktów końcowych, musisz określić lokalizację tych punktów końcowych. Wybierz region platformy Azure najbardziej zbliżony do wdrożenia. Te lokalizacje są obsługiwane przez tabelę opóźnienia Internet wartości.
* Algorytm wybiera punkt końcowy jest deterministyczna. Powtórzony zapytania DNS z tego samego klienta są przekierowywane do tego samego punktu końcowego. Zazwyczaj klienci używają serwerów DNS innej cykliczne podczas podróży. Klienta mogą być kierowane do innego punktu końcowego. Routing można również zostaną objęte aktualizacje tabeli opóźnienia Internet. W związku z tym metody routingu ruchu wydajności nie gwarantuje, że klient zawsze są kierowane do tego samego punktu końcowego.
* Po zmianie tabeli opóźnienia Internet, można zauważyć, że niektóre klienci są kierowani do innego punktu końcowego. Ta zmiana routingu jest dokładniejsze na podstawie bieżących danych opóźnienia. Te aktualizacje są niezbędne do obsługi dokładność routingu ruchu wydajności w miarę Internet stale rozwoju środowisko.

## <a name = "geographic"></a>Geograficzne metody routingu ruchu

Profilów usługi Traffic Manager można skonfigurować tak, aby użytkownicy są kierowane do określonych punktów końcowych (Azure, zewnętrzne lub zagnieżdżone) oparte na które lokalizacji geograficznej ich zapytanie DNS pochodzi z przy użyciu metody routingu geograficznych. To umożliwia klientom usługi Traffic Manager na potrzeby scenariuszy, w których znajomość regionu geograficznego użytkownika oraz routing je w oparciu o który jest ważna. Przykładami zgodnych z danych suwerenności zleceń, lokalizacja środowiska użytkownika & zawartości i pomiar ruchu z różnych regionów.
Po skonfigurowaniu profilu geograficznego routingu każdego punktu końcowego skojarzone z profilu musi mieć zestaw regionów geograficznych przypisane do niej. W następujących poziomów szczegółowości może być regionu geograficznego 
- World — dowolny region
- Regionalne grupowania — na przykład Afryka Bliski Wschód, Australia/pacyficzny itp. 
- Kraj/Region — na przykład Irlandia, Peru, SRA Hongkong itp. 
- Województwo — na przykład Kalifornijskiej USA, Australia-Queensland, Kanada Alberta itp. (Uwaga: ten poziom szczegółowości jest obsługiwana tylko w przypadku stanów / prowincje w Australii, Kanady UK i USA).

Jeśli region lub zestawie regionów jest przypisana do punktu końcowego, wszystkie żądania z tych regionów kierowania tylko do określonego punktu końcowego. Menedżer ruchu używa źródłowy adres IP zapytanie DNS do określić region, w którym pyta użytkownika z — zazwyczaj jest to adres IP wykonywania zapytań w imieniu użytkownika lokalnego rozpoznawania nazw DNS.  

![Azure Traffic Manager "Geograficzne" routingu ruchu — metoda](./media/traffic-manager-routing-methods/geographic.png)

Menedżer ruchu odczytuje źródłowy adres IP zapytanie DNS i decyduje o tym, które regionu geograficznego jest z. Następnie wygląda na to czy jest punktu końcowego, który ma tego regionu geograficznego mapowane. To wyszukiwanie rozpoczyna się na najniższym poziomie szczegółowości (województwo gdzie jest obsługiwany, else na poziomie Kraj/Region) i przechodzi do najwyższego poziomu, który jest **World**. Pierwsze dopasowanie ustalił, że przy użyciu tego przechodzenie jest wyznaczony jako punkt końcowy, aby zwrócić w odpowiedzi na kwerendę. Dopasowywanie za punkt końcowy typu zagnieżdżone w tym profilu podrzędnego punktu końcowego jest zwracany, oparty na jego metody routingu. Dotyczy to zachowanie są następujące kwestie:

- Regionu geograficznego mogą być mapowane tylko na jeden punkt końcowy profilu Menedżera ruchu, gdy typ routingu jest geograficzne routingu. Dzięki temu routingu użytkowników jest deterministyczna, a mogą umożliwić scenariusze, które wymagają jednoznaczne granice geograficzne.
- Jeśli regionu użytkownika zawiera mapowania geograficznej w dwóch różnych punktów końcowych, Menedżer ruchu wybiera punkt końcowy z najniższego stopnia szczegółowości i nie należy wziąć pod uwagę routingu żądań z tego regionu innych punktów końcowych. Rozważmy na przykład typ profilu geograficznego routingu z dwoma punktami końcowymi - Punk końcowy 1 i Punk końcowy 2. Punk końcowy 1 jest skonfigurowana do odbierania ruchu z Irlandii i Punk końcowy 2 jest skonfigurowana do odbierania ruchu z Europy. Jeśli żądanie pochodzi z Irlandii, zawsze jest kierowany do Punk końcowy 1.
- Ponieważ region mogą być mapowane tylko na jeden punkt końcowy, Traffic Manager zwraca niezależnie od tego, czy punkt końcowy jest w dobrej kondycji lub też nie.

    >[!IMPORTANT]
    >Zdecydowanie zaleca się, że klienci używający geograficzne metody routingu skojarzyć go z punktów końcowych typu zagnieżdżone, które ma profile podrzędnych zawierające co najmniej dwa punkty końcowe w ramach każdej.
- Jeśli znaleziono punktu końcowego, a tego punktu końcowego w **zatrzymane** stanu odpowiedzi NODATA zwraca Menedżera ruchu. W takim przypadku nie dalsze wyszukiwań następuje się wyżej w hierarchii regionu geograficznego. To zachowanie mają też zastosowanie dla typów zagnieżdżonych punktu końcowego, gdy profil podrzędnej jest **zatrzymane** lub **wyłączone** stanu.
- Jeśli punkt końcowy Wyświetla **wyłączone** stanu, nie będą uwzględniane w regionie zgodny proces. To zachowanie mają też zastosowanie dla typów zagnieżdżonych punktu końcowego, gdy punkt końcowy **wyłączone** stanu.
- Jeśli zapytanie pochodzi od regionu geograficznego, który nie ma mapowania w tym profilu, Traffic Manager zwraca odpowiedź NODATA. W związku z tym zdecydowanie zaleca się klienci powinni korzystać geograficzne routingu o jeden punkt końcowy, najlepszym rozwiązaniem typu zagnieżdżone z co najmniej dwa punkty końcowe w profilu podrzędnych, o regionie **World** przypisane do niej. Dzięki temu, że wszystkie adresy IP, które nie są mapowane na region są obsługiwane.

Zgodnie z objaśnieniem w [sposób działania usługi Traffic Manager](traffic-manager-how-traffic-manager-works.md), Menedżera ruchu nie odbiera zapytania DNS bezpośrednio od klientów. Zamiast zapytania DNS pochodzą z usługi DNS cykliczne że klienci są skonfigurowane do używania. W związku z tym adres IP używany do określenia region nie jest adres IP klienta, ale jest to adres IP cykliczne usługi DNS. W praktyce ten adres IP jest dobrym serwera proxy klienta.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak tworzyć aplikacje wysokiej dostępności przy użyciu [monitorowania punktu końcowego Menedżera ruchu](traffic-manager-monitoring.md)

Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



