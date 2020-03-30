---
title: Sondy kondycji do skalowania i zapewnienia wysokiej wartości dla twojej usługi
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak używać sond kondycji do monitorowania wystąpień za pomocą modułu Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ec1507e09a183f8d466a456b70151861f5f0e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159442"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji usługi Load Balancer

Podczas korzystania z reguł równoważenia obciążenia z modułem równoważenia obciążenia platformy Azure, należy określić sondy kondycji, aby umożliwić modułowi równoważenia obciążenia wykrywanie stanu punktu końcowego wewnętrznej bazy danych.  Konfiguracja odpowiedzi sondy kondycji i sondy określa, które wystąpienia puli wewnętrznej bazy danych będą odbierać nowe przepływy. Sondy kondycji można użyć do wykrywania awarii aplikacji w punkcie końcowym wewnętrznej bazy danych. Można również wygenerować niestandardową odpowiedź na sondę kondycji i użyć sondy kondycji do sterowania przepływem do zarządzania obciążeniem lub planowanym przestojem. Gdy sonda kondycji nie powiedzie się, moduł równoważenia obciążenia przestanie wysyłać nowe przepływy do odpowiedniego wystąpienia w złej kondycji. Nie ma to wpływu na łączność wychodzącą, dotyczy to tylko połączeń przychodzących.

Sondy kondycji obsługują wiele protokołów. Dostępność określonego protokołu sondy kondycji zależy od jednostki SKU modułu równoważenia obciążenia.  Ponadto zachowanie usługi różni się w zależności od jednostki SKU modułu równoważenia obciążenia, jak pokazano w tej tabeli:

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| [Typy sond](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sondowanie zachowania w dół](#probedown) | Wszystkie sondy w dół, wszystkie przepływy TCP kontynuować. | Wszystkie sondy w dół, wszystkie przepływy TCP wygasa. | 


>[!IMPORTANT]
>Przejrzyj ten dokument w całości, w tym ważne [wskazówki dotyczące projektu](#design) poniżej, aby utworzyć niezawodną usługę.

>[!IMPORTANT]
>Sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 i nie mogą być blokowane dla sond w celu oznaczenia wystąpienia.  Przejrzyj [źródłowy adres IP sondy,](#probesource) aby uzyskać szczegółowe informacje.

>[!IMPORTANT]
>Niezależnie od skonfigurowanego progu limitu czasu sondy kondycji modułu równoważenia obciążenia HTTP(S) automatycznie sondują wystąpienie, jeśli serwer zwróci dowolny kod stanu, który nie jest HTTP 200 OK lub jeśli połączenie zostanie zakończone za pomocą resetowania TCP.

## <a name="probe-configuration"></a><a name="probes"></a>Konfiguracja sondy

Konfiguracja sondy kondycji składa się z następujących elementów:

- Czas trwania interwału między poszczególnymi sondami
- Liczba reakcji sondy, które muszą być obserwowane przed przejściem sondy do innego stanu
- Protokół sondy
- Port sondy
- Ścieżka HTTP do użycia dla http get podczas korzystania z sond HTTP(S)

>[!NOTE]
>Definicja sondy nie jest obowiązkowa lub zaznaczona podczas korzystania z usługi Azure PowerShell, interfejsu wiersza polecenia platformy Azure, szablonów lub interfejsu API. Testy sprawdzania poprawności sondy są wykonywane tylko podczas korzystania z witryny Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Opis sygnału aplikacji, wykrywania sygnału i reakcji platformy

Liczba odpowiedzi sondy ma zastosowanie zarówno do

- liczbę udanych sond, które umożliwiają oznaczenie instancji jako
- liczba sond z przesuwem czasu, które powodują, że wystąpienie ma być oznaczone jako w dół.

Określone wartości limitu czasu i interwału określają, czy wystąpienie zostanie oznaczone jako w górę czy w dół.  Czas trwania interwału pomnożonego przez liczbę odpowiedzi sondy określa czas trwania, w którym należy wykryć odpowiedzi sondy.  A usługa zareaguje po osiągnięciu wymaganych sond.

Możemy jeszcze bardziej zilustrować to zachowanie za pomocą przykładu. Jeśli ustawiono liczbę odpowiedzi sondy na 2, a interwał na 5 sekund, oznacza to, że 2 błędy przesiewu sondy muszą być przestrzegane w odstępie 10 sekund.  Ponieważ czas, w którym wysyłana jest sonda nie jest synchronizowana, gdy aplikacja może zmienić stan, możemy powiązanie czasu do wykrycia przez dwa scenariusze:

1. Jeśli aplikacja rozpocznie produkcję odpowiedzi sondy przesuwu chwilę przed przybyciem pierwszej sondy, wykrycie tych zdarzeń zajmie 10 sekund (2 x 5 sekund) plus czas trwania aplikacji, która zaczyna sygnalizować limit czasu do momentu pierwszego sonda przybywa.  Można założyć, że to wykrycie zajmie nieco ponad 10 sekund.
2. Jeśli aplikacja rozpocznie produkcję odpowiedzi sondy limit czasu tuż po przybyciu pierwszej sondy, wykrywanie tych zdarzeń nie rozpocznie się, dopóki następna sonda nadejdzie (i upośnie limit czasu) plus kolejne 10 sekund (2 x 5 sekund interwałów).  Można założyć, że to wykrycie zajmie mniej niż 15 sekund.

W tym przykładzie po wykryciu platforma zajmie niewielką ilość czasu, aby zareagować na tę zmianę.  Oznacza to, że w zależności od 

1. gdy aplikacja zacznie zmieniać stan i
2. po wykryciu tej zmiany i spełnieniu wymaganych kryteriów (liczba sond wysłanych w określonym przedziale czasu) oraz
3. gdy wykrywanie zostało przekazane na całej platformie 

można założyć, że reakcja na odpowiedź sondy przekroju czasu zajmie od co najmniej nieco ponad 10 sekund do maksymalnie ponad 15 sekund, aby zareagować na zmianę sygnału z aplikacji.  Ten przykład jest przedstawiony w celu zilustrowania tego, co się dzieje, jednak nie jest możliwe prognozowanie dokładnego czasu trwania poza powyższymi wytycznymi przedstawionymi w tym przykładzie.
 
## <a name="probe-types"></a><a name="types"></a>Typy sond

Protokół używany przez sondę kondycji można skonfigurować w jedną z następujących opcji:

- [Słuchacze TCP](#tcpprobe)
- [Punkty końcowe HTTP](#httpprobe)
- [Punkty końcowe HTTPS](#httpsprobe)

Dostępne protokoły zależą od używanej jednostki SKU modułu równoważenia obciążenia:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardowy SKU |    &#9989; |   &#9989; |   &#9989; |
| Podstawowy SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>Sonda TCP

Sondy TCP inicjują połączenie, wykonując trójdrożny otwarty uzgadnianie TCP ze zdefiniowanym portem.  Sondy TCP kończą połączenie z czterokierunkowym zamknięciem uzgadniania TCP.

Minimalny interwał sondy wynosi 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Całkowity czas trwania wszystkich interwałów nie może przekraczać 120 sekund.

Sonda TCP kończy się niepowodzeniem, gdy:
* Odbiornik TCP w wystąpieniu nie odpowiada w ogóle w okresie limitu czasu.  Sonda jest oznaczona na podstawie liczby żądań sondy z przesuniętym w czasie, które zostały skonfigurowane tak, aby pozostawały bez odpowiedzi przed oznaczeniem w dół sondy.
* Sonda odbiera reset TCP z wystąpienia.

Poniżej przedstawiono, jak można wyrazić tego rodzaju konfiguracji sondy w szablonie Menedżera zasobów:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> Sonda HTTP / HTTPS

>[!NOTE]
>Sonda HTTPS jest dostępna tylko dla [standardowego modułu równoważenia obciążenia](load-balancer-standard-overview.md).

Sondy HTTP i HTTPS opierają się na sondzie TCP i wystawiają http get z określoną ścieżką. Obie te sondy obsługują ścieżki względne dla HTTP GET. Sondy HTTPS są takie same jak sondy HTTP z dodatkiem otoki TLS (Transport Layer Security), wcześniej znanej jako SSL). Sonda kondycji jest oznaczony, gdy wystąpienie odpowiada ze stanem HTTP 200 w okresie limitu czasu.  Sonda kondycji próbuje sprawdzić port sondy skonfigurowane kondycje co 15 sekund domyślnie. Minimalny interwał sondy wynosi 5 sekund. Całkowity czas trwania wszystkich interwałów nie może przekraczać 120 sekund.

Sondy HTTP / HTTPS mogą być również przydatne do implementacji własnej logiki, aby usunąć wystąpienia z rotacji modułu równoważenia obciążenia, jeśli port sondy jest również odbiornikiem dla samej usługi. Na przykład można podjąć decyzję o usunięciu wystąpienia, jeśli jest powyżej 90% procesora CPU i zwrócić stan HTTP innych niż 200. 

> [!NOTE] 
> Sonda HTTPS wymaga użycia certyfikatów opartych na minimalnym mieszaniu podpisu SHA256 w całym łańcuchu.

Jeśli korzystasz z usług w chmurze i masz role sieci web, które używają w3wp.exe, można również osiągnąć automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny zwracają stan nie-200 do sondy modułu równoważenia obciążenia.

Sonda HTTP / HTTPS kończy się niepowodzeniem, gdy:
* Punkt końcowy sondy zwraca kod odpowiedzi HTTP inny niż 200 (na przykład 403, 404 lub 500). Spowoduje to natychmiast oznaczyć sondę kondycji. 
* Punkt końcowy sondy nie odpowiada w ogóle podczas minimalnego interwału sondy i 30-sekundowego limitu czasu. Wiele żądań sondy może pozostać bez odpowiedzi, zanim sonda zostanie oznaczona jako nie uruchomiona i dopóki nie zostanie osiągnięta suma wszystkich interwałów limitu czasu.
* Punkt końcowy sondy zamyka połączenie za pomocą resetu TCP.

Poniżej przedstawiono, jak można wyrazić tego rodzaju konfiguracji sondy w szablonie Menedżera zasobów:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Sonda agenta gościa (tylko classic)

Role usługi w chmurze (role procesu roboczego i role sieci web) domyślnie używają agenta gościa do monitorowania sondy.  Sonda agenta gościa jest konfiguracją ostatniej szansy.  Zawsze używaj sondy kondycji jawnie za pomocą sondy TCP lub HTTP. Sonda agenta gościa nie jest tak skuteczna, jak jawnie zdefiniowane sondy dla większości scenariuszy aplikacji.

Sonda agenta gościa jest sprawdzenie agenta gościa wewnątrz maszyny Wirtualnej. Następnie nasłuchuje i odpowiada odpowiedzią HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie Gotowy. (Inne stany to zajęty, recykling lub zatrzymanie).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sond kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [Wprowadzenie przez utworzenie publicznego modułu równoważenia obciążenia dla usług w chmurze](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Jeśli agent gościa nie odpowiada http 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nieodpowiadające. Następnie zatrzymuje wysyłanie przepływów do tego wystąpienia. Moduł równoważenia obciążenia kontynuuje sprawdzanie wystąpienia. 

Jeśli agent gościa odpowiada http 200, moduł równoważenia obciążenia wysyła nowe przepływy do tego wystąpienia ponownie.

Podczas korzystania z roli sieci web, kod witryny sieci Web zazwyczaj działa w w3wp.exe, który nie jest monitorowany przez sieci szkieletowej platformy Azure lub agenta gościa. Błędy w pliku w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie bierze tego wystąpienia z obrotu.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sondowanie zachowania

Sondy kondycji TCP, HTTP i HTTPS są uważane za w dobrej kondycji i oznaczają punkt końcowy wewnętrznej bazy danych jako w dobrej kondycji, gdy:

* Sonda kondycji zakończy się pomyślnie po uruchomieniu maszyny Wirtualnej.
* Osiągnięto określoną liczbę sond wymaganych do oznaczenia punktu końcowego wewnętrznej bazy danych jako dobrej kondycji.

Każdy punkt końcowy wewnętrznej bazy danych, który osiągnął stan dobrej kondycji, kwalifikuje się do odbierania nowych przepływów.  

> [!NOTE]
> Jeśli sonda kondycji zmienia się, moduł równoważenia obciążenia czeka dłużej, zanim oddaje punkt końcowy wewnętrznej bazy danych z powrotem w stanie dobrej kondycji. Ten dodatkowy czas oczekiwania chroni użytkownika i infrastruktury i jest zamierzone zasady.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Sondowanie zachowania w dół

### <a name="tcp-connections"></a>Połączenia TCP

Nowe połączenia TCP uda się pozostałe punktu końcowego wewnętrznej bazy danych w dobrej kondycji.

Jeśli sonda kondycji punktu końcowego wewnętrznej bazy danych nie powiedzie się, ustanowione połączenia TCP z tym punktem końcowym wewnętrznej bazy danych będą kontynuowane.

Jeśli wszystkie sondy dla wszystkich wystąpień w puli wewnętrznej bazy danych nie powiedzie się, żadne nowe przepływy nie zostaną wysłane do puli wewnętrznej bazy danych. Standardowy moduł równoważenia obciążenia umożliwia kontynuowanie ustalonych przepływów TCP.  Podstawowy moduł równoważenia obciążenia zakończy wszystkie istniejące przepływy TCP do puli wewnętrznej bazy danych.
 
Moduł równoważenia obciążenia jest usługą przekazywalną (nie kończy połączeń TCP), a przepływ jest zawsze między klientem a serwerem operacyjnym gościa maszyny Wirtualnej i aplikacją. Pula ze wszystkimi sondami w dół spowoduje, że frontend nie odpowiada na próby otwarcia połączenia TCP (SYN), ponieważ nie ma punktu końcowego w dobrej kondycji, aby odbierać przepływ i odpowiadać za pomocą SYN-ACK.

### <a name="udp-datagrams"></a>Datagramy UDP

Datagramy UDP zostaną dostarczone do punktów końcowych wewnętrznej bazy danych w dobrej kondycji.

UDP jest bezłączny i nie ma stanu przepływu śledzone dla UDP. Jeśli sonda kondycji dowolnego punktu końcowego wewnętrznej bazy danych zakończy się nie powiedzie, istniejące przepływy UDP zostaną przeniesione do innego wystąpienia w dobrej kondycji w puli wewnętrznej bazy danych.

Jeśli wszystkie sondy dla wszystkich wystąpień w puli wewnętrznej bazy danych nie powiedzie się, istniejące przepływy UDP zakończy się dla podstawowych i standardowych modułów równoważenia obciążenia.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>Źródłowy adres IP sondy

Moduł równoważenia obciążenia używa rozproszonej usługi sondowania dla swojego wewnętrznego modelu kondycji. Usługa sondowania znajduje się na każdym hoście, gdzie maszyny wirtualne i mogą być programowe na żądanie do generowania sond kondycji na konfiguracji klienta. Ruch sondy kondycji jest bezpośrednio między usługą sondowania, która generuje sondę kondycji i maszynę wirtualną klienta. Wszystkie sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 jako źródła.  Można użyć przestrzeni adresowej IP wewnątrz sieci wirtualnej, która nie jest miejscem RFC1918.  Przy użyciu globalnie zastrzeżone, Microsoft własnością adres IP zmniejsza ryzyko konfliktu adres IP z przestrzeni adresów IP używane wewnątrz sieci wirtualnej.  Ten adres IP jest taki sam we wszystkich regionach i nie zmienia się i nie stanowi zagrożenia bezpieczeństwa, ponieważ tylko wewnętrzny składnik platformy Azure może zaopatrywać pakiet z tego adresu IP. 

Tag usługi AzureLoadBalancer identyfikuje ten źródłowy adres IP w [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md) i domyślnie zezwala na ruch sondy kondycji.

Oprócz sond kondycji modułu równoważenia obciążenia [następujące operacje używają tego adresu IP:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Umożliwia agentowi maszyny Wirtualnej komunikowanie się z platformą w celu zasygnalizowania, że jest w stanie "Gotowy"
- Umożliwia komunikację z serwerem wirtualnym DNS w celu zapewnienia rozpoznawania nazw filtrowanych klientom, którzy nie definiują niestandardowych serwerów DNS.  To filtrowanie gwarantuje, że klienci mogą rozpoznać tylko nazwy hostów ich wdrożenia.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.

## <a name="design-guidance"></a><a name="design"></a>Wskazówki dotyczące projektu

Sondy kondycji są używane do odporne usługi i umożliwić jej skalowanie. Błędna konfiguracja lub zły wzorzec projektu może mieć wpływ na dostępność i skalowalność usługi. Przejrzyj cały ten dokument i zastanów się, jaki wpływ na scenariusz ma, gdy ta odpowiedź sondy jest oznaczona lub oznaczona w górę i jak wpływa na dostępność scenariusza aplikacji.

Podczas projektowania modelu kondycji dla aplikacji, należy sondować port w punkcie końcowym wewnętrznej bazy danych, który odzwierciedla kondycję tego wystąpienia __i__ usługi aplikacji, które są świadczone.  Port aplikacji i port sondy nie muszą być takie same.  W niektórych scenariuszach może być pożądane, aby port sondy różnił się od portu, na który aplikacja udostępnia usługę.  

Czasami może być przydatne dla aplikacji do generowania odpowiedzi sondy kondycji nie tylko wykryć kondycję aplikacji, ale także sygnał bezpośrednio do modułu równoważenia obciążenia, czy wystąpienie powinno odbierać, czy nie odbierać nowe przepływy.  Można manipulować odpowiedzi sondy, aby umożliwić aplikacji do tworzenia backpressure i ograniczania dostarczania nowych przepływów do wystąpienia przez niepowodzenie sondy kondycji lub przygotować się do konserwacji aplikacji i zainicjować opróżniania scenariusza.  W przypadku korzystania ze standardowego modułu równoważenia obciążenia sygnał [sondy w dół](#probedown) zawsze umożliwia kontynuowanie przepływów TCP do momentu wystąpienia limitu czasu bezczynności lub zamknięcia połączenia. 

W przypadku równoważenia obciążenia UDP należy wygenerować niestandardowy sygnał sondy kondycji z punktu końcowego wewnętrznej bazy danych i użyć sondy kondycji TCP, HTTP lub HTTPS przeznaczonej dla odpowiedniego odbiornika, aby odzwierciedlić kondycję aplikacji UDP.

W przypadku korzystania z [reguł równoważenia obciążenia portów wysokiej](load-balancer-ha-ports-overview.md) jakości ze [standardowym modułem równoważenia obciążenia](load-balancer-standard-overview.md)wszystkie porty są równoważące obciążenie, a pojedyncza odpowiedź sondy kondycji musi odzwierciedlać stan całego wystąpienia.

Nie należy tłumaczyć ani proxy sondy kondycji za pośrednictwem wystąpienia, które odbiera sondy kondycji do innego wystąpienia w sieci wirtualnej, ponieważ ta konfiguracja może prowadzić do błędów kaskadowych w scenariuszu.  Rozważmy następujący scenariusz: zestaw urządzeń innych firm jest wdrażany w puli wewnętrznej bazy danych zasobu modułu równoważenia obciążenia w celu zapewnienia skali i nadmiarowości dla urządzeń, a sonda kondycji jest skonfigurowana do sondowania portu, który jest serwerem proxy urządzenia innej firmy lub na inne maszyny wirtualne stojące za urządzeniem.  Jeśli sondujesz ten sam port, którego używasz do tłumaczenia lub żądania serwera proxy do innych maszyn wirtualnych za urządzeniem, każda odpowiedź sondy z jednej maszyny wirtualnej za urządzeniem oznaczy samo urządzenie jako martwe. Ta konfiguracja może prowadzić do kaskadowego błędu całego scenariusza aplikacji w wyniku pojedynczego punktu końcowego wewnętrznej bazy danych za urządzeniem.  Wyzwalacz może być sporadyczne awarii sondy, która spowoduje, że moduł równoważenia obciążenia, aby oznaczyć w dół oryginalnego miejsca docelowego (wystąpienie urządzenia) i z kolei można wyłączyć cały scenariusz aplikacji. Zamiast tego sonduj kondycję samego urządzenia. Wybór sondy w celu określenia sygnału kondycji jest ważnym czynnikiem dla scenariuszy sieciowych urządzeń wirtualnych (NVA) i należy skonsultować się z dostawcą aplikacji, co jest odpowiednim sygnałem kondycji dla takich scenariuszy.

Jeśli nie zezwalasz [na źródłowy adres IP](#probesource) sondy w zasadach zapory, sonda kondycji zakończy się niepowodzeniem, ponieważ nie może dotrzeć do wystąpienia.  Z kolei moduł równoważenia obciążenia oznaczy swoje wystąpienie z powodu błędu sondy kondycji.  Ta błędna konfiguracja może spowodować niepowodzenie scenariusza aplikacji z równoważeniem obciążenia.

Aby sonda kondycji modułu równoważenia obciążenia oznaczyła wystąpienie, **należy zezwolić** na ten adres IP w dowolnej [sieciowej grupy zabezpieczeń platformy](../virtual-network/security-overview.md) Azure i lokalne zasady zapory.  Domyślnie każda grupa zabezpieczeń sieci zawiera [tag usługi](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer, aby umożliwić ruch sondy kondycji.

Jeśli chcesz przetestować błąd sondy kondycji lub oznaczyć poszczególne wystąpienie, możesz użyć [sieciowych grup zabezpieczeń,](../virtual-network/security-overview.md) aby jawnie zablokować sondę kondycji (port docelowy lub [źródłowy adres IP)](#probesource)i symulować awarię sondy.

Nie należy konfigurować sieci wirtualnej z zakresem adresów IP należących do firmy Microsoft, który zawiera 168.63.129.16.  Takie konfiguracje zderzają się z adresem IP sondy kondycji i może spowodować, że scenariusz zakończy się niepowodzeniem.

Jeśli masz wiele interfejsów na maszynie Wirtualnej, należy ubezpieczyć odpowiedzieć na sondę na interfejs, który otrzymał go na.  Może być konieczne źródło adres sieciowy przetłumaczyć ten adres na maszynie Wirtualnej na podstawie interfejsu.

Nie należy włączać [sygnatur czasowych TCP](https://tools.ietf.org/html/rfc1323).  Włączenie sygnatur czasowych TCP może spowodować niepowodzenie sond kondycji z powodu porzucania pakietów TCP przez stos TCP gościa maszyny wirtualnej, co powoduje oznaczanie modułu równoważenia obciążenia w dół odpowiedniego punktu końcowego.  Sygnatury czasowe TCP są domyślnie domyślnie włączone w obrazach maszyn wirtualnych ze wzmocnionymi zabezpieczeniami i muszą być wyłączone.

## <a name="monitoring"></a>Monitorowanie

Publiczne i wewnętrzne [standardowe moduły równoważenia obciążenia](load-balancer-standard-overview.md) uwidaczniają stan sondy kondycji punktu końcowego punktu końcowego i wewnętrznej bazy danych jako metryki wielowymiarowe za pośrednictwem usługi Azure Monitor. Te metryki mogą być używane przez inne usługi platformy Azure lub aplikacje partnerskie. 

Podstawowy publiczny moduł równoważenia obciążenia udostępnia stan sondy kondycji podsumowany na pulę wewnętrznej bazy danych za pośrednictwem dzienników usługi Azure Monitor.  Dzienniki usługi Azure Monitor nie są dostępne dla wewnętrznych podstawowych modułów równoważenia obciążenia.  Dzienniki [usługi Azure Monitor](load-balancer-monitor-log.md) umożliwiają sprawdzanie stanu kondycji sondy publicznego modułu równoważenia obciążenia i liczby sond. Rejestrowania można używać z usługą Power BI lub usługi Azure Operational Insights w celu zapewnienia statystyk dotyczących stanu kondycji modułu równoważenia obciążenia.

## <a name="limitations"></a>Ograniczenia

- Sondy HTTPS nie obsługują wzajemnego uwierzytelniania za pomocą certyfikatu klienta.
- Należy założyć, że sondy kondycji nie powiedzie się, gdy sygnatury czasowe TCP są włączone.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [standardowym równoważącym obciążenia](load-balancer-standard-overview.md)
- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w Menedżerze zasobów przy użyciu programu PowerShell](quickstart-create-standard-load-balancer-powershell.md)
- [INTERFEJS API REST dla sond kondycji](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Żądanie nowych możliwości sondy kondycji za pomocą [uservoice modułu równoważenia obciążenia](https://aka.ms/lbuservoice)
