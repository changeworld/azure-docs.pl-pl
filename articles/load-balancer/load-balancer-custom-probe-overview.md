---
title: Sondy kondycji do skalowania i zapewnienia wysokiej dostępności dla usługi
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak używać sond kondycji do monitorowania wystąpień za Azure Load Balancer
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
ms.openlocfilehash: 46d566dc7527097d36b72886ada1f8c94f727535
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198755"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji usługi Load Balancer

W przypadku używania reguł równoważenia obciążenia z Azure Load Balancer należy określić sondy kondycji, aby umożliwić Load Balancer wykryć stan punktu końcowego zaplecza.  Konfiguracja sondy kondycji i odpowiedzi sondy określają, które wystąpienia puli zaplecza będą otrzymywać Nowe przepływy. Sond kondycji można użyć do wykrywania awarii aplikacji w punkcie końcowym zaplecza. Możesz również wygenerować niestandardową odpowiedź na sondę kondycji i użyć sondy kondycji do sterowania przepływem, aby zarządzać obciążeniem lub planowanym przestojem. Gdy sonda kondycji zakończy się niepowodzeniem, Load Balancer przestanie wysyłać Nowe przepływy do odpowiedniego wystąpienia w złej kondycji. Nie ma to wpływu na łączność wychodzącą. dotyczy to tylko łączności przychodzącej.

Sondy kondycji obsługują wiele protokołów. Dostępność określonego protokołu sondy kondycji zależy od jednostki SKU Load Balancer.  Ponadto zachowanie usługi różni się w zależności od Load Balancer jednostki SKU, jak pokazano w poniższej tabeli:

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| [Typy sond](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Zachowanie podczas sondowania](#probedown) | Wszystkie sondy, wszystkie przepływy TCP nadal. | Wszystkie sondy w dół, wszystkie przepływy TCP wygasają. | 


>[!IMPORTANT]
>Zapoznaj się z tym dokumentem w całości, w tym ważne [wskazówki dotyczące projektowania](#design) poniżej, aby utworzyć niezawodną usługę.

>[!IMPORTANT]
>Sondy kondycji Load Balancer pochodzą z adresu IP 168.63.129.16 i nie mogą być blokowane w przypadku sondowania do oznaczania wystąpienia.  Przejrzyj [źródłowy adres IP sondy](#probesource) , aby uzyskać szczegółowe informacje.

## <a name="probes"></a>Konfiguracja sondy

Konfiguracja sondy kondycji składa się z następujących elementów:

- Czas trwania interwału między indywidualnymi sondami
- Liczba odpowiedzi sondy, które muszą zostać zaobserwowane przed przejściem sondy do innego stanu
- Protokół sondy
- Port sondy
- Ścieżka HTTP do użycia w przypadku protokołu HTTP GET podczas korzystania z sond HTTP (S)

>[!NOTE]
>Definicja sondy nie jest obowiązkowa lub sprawdzana w przypadku używania Azure PowerShell, interfejsu wiersza polecenia platformy Azure, szablonów lub interfejsu API. Testy weryfikacyjne sondy są wykonywane tylko w przypadku korzystania z witryny Azure Portal.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Informacje o sygnale aplikacji, wykrywaniu sygnału i reakcji platformy

Liczba odpowiedzi sondy odnosi się do obu

- Liczba pomyślnych sond, które zezwalają na oznaczenie wystąpienia jako up, i
- liczba nieudanych sond, które powodują oznaczenie wystąpienia jako wyłączonego.

Określone wartości limitu czasu i interwału określają, czy wystąpienie zostanie oznaczone jako w górę, czy w dół.  Czas trwania interwału pomnożony przez liczbę odpowiedzi sondy określa czas, w którym należy wykryć odpowiedzi sondy.  A usługa będzie reagować po uzyskaniu wymaganych sond.

Można również przedstawić zachowanie na przykład. Jeśli ustawiono liczbę odpowiedzi sondy do 2 i interwału do 5 sekund, oznacza to, że awarie sondy muszą być zaobserwowane w ciągu 10-sekundowego interwału.  Ponieważ czas, w którym sonda jest wysyłana, nie jest zsynchronizowany, gdy aplikacja może zmienić stan, możemy powiązać się z czasem wykrywania przez dwa scenariusze:

1. Jeśli aplikacja zacznie tworzyć niepowodzenie reakcji sondy tuż przed nadejściem pierwszej sondy, wykrywanie tych zdarzeń będzie trwać 10 sekund (2 x 5 sekund) i czas trwania aplikacji, która zaczyna sygnalizować awarię podczas pierwszego dotrze sondy.  Można założyć, że to wykrywanie zajmie nieco ponad 10 sekund.
2. Jeśli aplikacja zacznie tworzyć niepowodzeniem odpowiedź sondy tuż po nadejściu pierwszej sondy, wykrywanie tych zdarzeń nie rozpocznie się do momentu odebrania kolejnej sondy (i niepowodzenia) plus inne 10 sekund (2 x 5 sekund).  Można założyć, że wykrywanie ma trwać zaledwie 15 sekund.

Na potrzeby tego przykładu, gdy wystąpiło wykrycie, platforma zajmie trochę czasu na zareagowanie na tę zmianę.  Oznacza to, że w zależności od 

1. gdy aplikacja zacznie zmieniać stan i
2. Gdy ta zmiana zostanie wykryta i spełnione są wymagane kryteria (liczba sond wysyłanych w określonym interwale) i
3. gdy wykrycie zostało przekazane na platformie 

można założyć, że reakcja na niepowodzenie sondy trwa od minimum do 10 sekund, a maksymalnie 15 sekund, aby reagować na zmianę sygnału z aplikacji.  W tym przykładzie przedstawiono, jak to zrobić, ale nie jest możliwe prognozowanie dokładnego czasu trwania Poza powyższymi wskazówkami przedstawionymi w tym przykładzie.
 
## <a name="types"></a>Typy sond

Protokół używany przez sondę kondycji można skonfigurować na jedną z następujących czynności:

- [Odbiorniki TCP](#tcpprobe)
- [Punkty końcowe HTTP](#httpprobe)
- [Punkty końcowe HTTPS](#httpsprobe)

Dostępne protokoły zależą od używanej jednostki SKU Load Balancer:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardowy SKU |    &#9989; |   &#9989; |   &#9989; |
| Podstawowy SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a>Sonda TCP

Sondy protokołu TCP zainicjować połączenie, wykonując trzy kierunkową Otwórz TCP uzgadniania z zdefiniowany port.  Sondy TCP przerywają połączenie z czterema sposobem zamykania protokołu TCP.

Interwał sondy minimalna to 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Łączny czas trwania wszystkich interwałów nie może przekroczyć 120 sekund.

Sonda TCP kończy się niepowodzeniem kiedy:
* Odbiornik TCP w wystąpieniu nie odpowiada w określonym przedziale czasu.  Sonda jest oznaczona w dół w oparciu o liczbę nieudanych żądań sondowania, które zostały skonfigurowane w taki sposób, aby nie odpowiadały przed oznaczeniem sondy.
* Sonda odbiera TCP, zresetuj z wystąpienia.

Poniżej pokazano, jak można wyrazić ten rodzaj konfiguracji sondy w szablonie Menedżer zasobów:

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

### <a name="httpprobe"></a><a name="httpsprobe"></a> Sonda protokołu HTTP/HTTPS

>[!NOTE]
>Sonda HTTPS jest dostępna tylko dla [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

Sondy protokołu HTTP i HTTPS kompilują sondę TCP i wystawią HTTP GET z określoną ścieżką. Obsługa obu tych sond HTTP GET ścieżek względnych. Sondy protokołu HTTPS są takie same jak sondy HTTP, dodając Transport Layer Security (TLS, znana wcześniej jako SSL) otoki. Sonda kondycji jest oznaczony jako, gdy wystąpienie odpowiada ze stanem HTTP 200 przed upływem limitu czasu.  Sonda kondycji próbuje domyślnie sprawdzić skonfigurowany port sondy kondycji co 15 sekund. Interwał sondy minimalna to 5 sekund. Łączny czas trwania wszystkich interwałów nie może przekroczyć 120 sekund.

Sondy protokołu HTTP/HTTPS mogą również być przydatne do implementowania własnej logiki do usuwania wystąpień z rotacji modułu równoważenia obciążenia, jeśli port sondy jest również odbiornikiem dla samej usługi. Na przykład możesz zdecydować usunąć wystąpienie, gdy przekracza 90% zasobów Procesora i zwrócenia stanu 200 HTTP. 

> [!NOTE] 
> Sonda HTTPS wymaga użycia certyfikatów opartych na minimalnym skrócie podpisu SHA256 w całym łańcuchu.

Jeśli korzystasz z usług w chmurze i mieć role sieci web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia.

HTTP / sondy protokołu HTTPS nie powiedzie się po:
* Końcowego sondy zwraca kod odpowiedzi HTTP inne niż 200 (na przykład, 403, 404 lub 500). Spowoduje to natychmiastowe oznaczenie sondy kondycji. 
* Punkt końcowy sondy nie odpowiada wcale w czasie minimalnym interwału sondowania i 30-sekundowym przedziale czasu. Jeśli sonda zostanie oznaczona jako Nieuruchomiona i zostanie osiągnięta suma wszystkich przedziałów czasu, nie ma odpowiedzi na wiele żądań sondowania.
* Końcowego sondy zamyka połączenie za pośrednictwem resetowania TCP.

Poniżej pokazano, jak można wyrazić ten rodzaj konfiguracji sondy w szablonie Menedżer zasobów:

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

### <a name="guestagent"></a>Sonda agenta gościa (tylko wersja klasyczna)

Role usługi w chmurze (role procesu roboczego i role sieci web) używać agenta gościa na potrzeby sondy monitorowania domyślnie.  Sonda agenta gościa jest ostatnią konfiguracją.  Zawsze używaj sondy kondycji jawnie z sondą TCP lub HTTP. Sondowanie agenta gościa nie jest tak skuteczne, jak sondy jawnie zdefiniowane w przypadku większości scenariuszy aplikacji.

Sondowanie agenta gościa jest wyboru agenta gościa wewnątrz maszyny Wirtualnej. Następnie odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzanie lub zatrzymywania).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sond kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub Rozpoczynanie [pracy przez utworzenie publicznego modułu równoważenia obciążenia dla usług Cloud Services](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Jeśli agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nie odpowiada. Następnie zatrzymuje, wysyłając przepływy do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu Sprawdź wystąpienie. 

Jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia wysyła nowych przepływów do tego wystąpienia ponownie.

Korzystając z roli sieci web, kod witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowane przez usługę Azure Service fabric lub gościa agenta. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie przyjmuje tego wystąpienia z rotacji.

<a name="health"></a>
## <a name="probehealth"></a>Zachowanie sondowania

Sondy kondycji protokołów TCP, HTTP i HTTPS są uznawane za zdrowe i oznaczają, że punkt końcowy wewnętrznej bazy danych jest w dobrej kondycji, gdy:

* Sonda kondycji kończy się pomyślnie po uruchomieniu maszyny wirtualnej.
* Określona liczba sond wymaganych do oznaczenia punktu końcowego zaplecza w miarę osiągnięcia w dobrej kondycji.

Każdy punkt końcowy zaplecza, który osiągnął prawidłowy stan, jest uprawniony do otrzymywania nowych przepływów.  

> [!NOTE]
> Jeśli sonda kondycji jest zmieniana, moduł równoważenia obciążenia czeka dłużej przed ponownym umieszczeniem punktu końcowego zaplecza w dobrej kondycji. Czas oczekiwania dodatkowe chroni użytkownika i infrastruktura i zamierzone zasady.

## <a name="probedown"></a>Zachowanie podczas sondowania

### <a name="tcp-connections"></a>Połączenia protokołu TCP

Nowe połączenia TCP będą pomyślnie pozostawać w dalszym punkcie końcowym zaplecza.

Jeśli sonda kondycji punktu końcowego zaplecza nie powiedzie się, ustanowiono połączenia TCP z tym punktem końcowym zaplecza.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do puli zaplecza będą wysyłane nie nowych przepływów. Load Balancer w warstwie standardowa pozwoli na ustanowionych przepływy TCP, aby kontynuować.  Podstawowy moduł równoważenia obciążenia utracą wszystkie istniejące przepływy TCP do puli zaplecza.
 
Load Balancer to usługa przekazująca (nie przerywa połączeń TCP), a przepływ jest zawsze między klientem i systemem operacyjnym gościa maszyny wirtualnej i aplikacją. Pula ze wszystkimi sondami w dół spowoduje, że fronton nie odpowie na otwarte próby połączenia TCP (SYN), ponieważ nie ma żadnego punktu końcowego wewnętrznej bazy danych do odebrania przepływu i zareaguje na potwierdzenie SYN.

### <a name="udp-datagrams"></a>Datagramy protokołu UDP

Datagramy UDP zostaną dostarczone do prawidłowych punktów końcowych zaplecza.

Protokół UDP jest przesyłanie i nie ma żadnych stan przepływu śledzone dla protokołu UDP. Jeśli sonda kondycji dowolnego punktu końcowego zaplecza nie powiedzie się, istniejące przepływy UDP przechodzą na inne wystąpienie w dobrej kondycji w puli zaplecza.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do istniejących przepływów UDP utracą ważność warstwy podstawowa i standardowa usługi równoważenia obciążenia.

<a name="source"></a>
## <a name="probesource"></a>Źródłowy adres IP sondy

Moduł równoważenia obciążenia używa usługami rozproszonymi badania swój model kondycji wewnętrznego. Usługa Bing znajduje się na każdym hoście, na którym maszyny wirtualne i może być zaprogramowana na żądanie w celu wygenerowania sond kondycji zgodnie z konfiguracją klienta. Ruch sondy kondycji jest bezpośrednio między usługą sondowania, która generuje sondę kondycji i maszynę wirtualną klienta. Wszystkie sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 jako źródła.  Możesz użyć przestrzeni adresów IP wewnątrz sieci wirtualnej, która nie jest RFC1918.  Użycie zastrzeżonej globalnie adresu IP firmy Microsoft zmniejsza szansę, że adres IP jest w konflikcie z przestrzenią adresów IP używaną wewnątrz sieci wirtualnej.  Ten adres IP jest taki sam we wszystkich regionach i nie jest narażony na bezpieczeństwo, ponieważ tylko wewnętrzny składnik platformy platformy Azure może posłużyć do podzielenia pakietu z tego adresu IP. 

Tag usługi AzureLoadBalancer identyfikuje ten źródłowy adres IP w [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md) i domyślnie zezwala na ruch sondy kondycji.

Oprócz Load Balancer sond kondycji [następujące operacje używają tego adresu IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Włącza agenta maszyny wirtualnej do komunikowania się z platformą do sygnalizowania, że jest on w stanie "Gotowe"
- Umożliwia komunikację z serwerem wirtualnym DNS, aby zapewnić rozpoznawanie nazw filtrowane do klientów, którzy nie zdefiniujesz niestandardowe serwery DNS.  Filtrowanie gwarantuje, że klienci tylko może rozpoznać nazwy hostów ich wdrożenia.
- Umożliwia maszynie wirtualnej uzyskanie dynamicznego adresu IP z usługi DHCP na platformie Azure.

## <a name="design"></a>Wskazówki dotyczące projektowania

Sondy kondycji są używane do odporności usługi i umożliwienia jej skalowania. Niepoprawna konfiguracja lub nieodpowiedni Wzorzec projektowy może mieć wpływ na dostępność i skalowalność usługi. Zapoznaj się z tym całym dokumentem i zastanów się, co ma wpływ na Twój scenariusz, gdy ta odpowiedź sondy jest oznaczona jako wyłączona lub oznaczona jako taka, jak ma to wpływ na dostępność scenariusza aplikacji.

Podczas projektowania modelu kondycji aplikacji należy sondować port w punkcie końcowym zaplecza, który odzwierciedla kondycję tego wystąpienia __i__ usługi aplikacji, którą udostępniasz.  Port aplikacji i port sondy nie muszą być takie same.  W niektórych scenariuszach może być pożądane, aby port sondy był różny od portu, w którym usługa jest udostępniana przez aplikację.  

Czasami może być przydatne, aby aplikacja mogła wygenerować odpowiedź sondy kondycji, aby nie tylko wykrywać kondycję aplikacji, ale również sygnalizować bezpośrednio Load Balancer, czy Twoje wystąpienie powinno otrzymywać i nie odbierać nowych przepływów.  Można manipulować odpowiedzią sondy, aby umożliwić aplikacji Tworzenie i ograniczanie dostarczania nowych przepływów do wystąpienia przez niepowodzenie sondy kondycji lub przygotowanie do konserwacji aplikacji i Inicjowanie opróżniania scenariusza.  W przypadku korzystania z usługa Load Balancer w warstwie Standardowa sygnał [w dół sondowania](#probedown) będzie zawsze zezwalał na kontynuowanie PRZEPŁYWów TCP do momentu upłynięcia limitu czasu bezczynności lub zamknięcia połączenia. 

W przypadku równoważenia obciążenia UDP należy wygenerować niestandardowy sygnał sondy kondycji z punktu końcowego zaplecza i użyć sondy kondycji protokołu TCP, HTTP lub HTTPS dla odpowiedniego odbiornika, aby odzwierciedlić kondycję aplikacji UDP.

W przypadku używania [portów ha zasad równoważenia obciążenia](load-balancer-ha-ports-overview.md) z [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)wszystkie porty są zrównoważone obciążenie, a pojedyncza odpowiedź sondy kondycji musi odzwierciedlać stan całego wystąpienia.

Nie należy tłumaczyć ani proxy sondy kondycji za pomocą wystąpienia, które odbiera sondę kondycji z innym wystąpieniem w sieci wirtualnej, ponieważ ta konfiguracja może prowadzić do błędów kaskadowych w Twoim scenariuszu.  Rozważmy następujący scenariusz: zestaw urządzeń innych firm jest wdrażany w puli zaplecza zasobu Load Balancer w celu zapewnienia skalowalności i nadmiarowości dla urządzeń, a sonda kondycji jest skonfigurowana do sondowania portu, który jest używany przez serwery proxy urządzenia innej firmy lub tłumaczy na inne maszyny wirtualne znajdujące się za urządzeniem.  W przypadku sondowania tego samego portu, który jest używany do tłumaczenia lub żądania proxy na inne maszyny wirtualne za urządzeniem, Każda odpowiedź sondy z pojedynczej maszyny wirtualnej za urządzeniem spowoduje oznaczenie samego urządzenia jako nieaktywnego. Ta konfiguracja może prowadzić do niepowodzenia kaskadowego całego scenariusza aplikacji w wyniku pojedynczego punktu końcowego zaplecza za urządzeniem.  Wyzwalacz może być sporadycznym błędem sondy, co spowoduje, że Load Balancer oznaczyć pierwotne miejsce docelowe (wystąpienie urządzenia) i z kolei może wyłączyć cały scenariusz aplikacji. Należy sondować kondycję samego urządzenia. Wybór sondy do określenia sygnału kondycji jest ważnym zagadnieniem dotyczącym scenariuszy sieciowych urządzeń wirtualnych (urządzenie WUS) i należy skontaktować się z dostawcą aplikacji w celu uzyskania odpowiedniego sygnału kondycji dla takich scenariuszy.

Jeśli nie zezwolisz na [źródłowy adres IP](#probesource) sondy w zasadach zapory, sonda kondycji zakończy się niepowodzeniem, ponieważ nie można nawiązać połączenia z wystąpieniem.  Z kolei modułu równoważenia obciążenia spowoduje oznaczenie dół wystąpienie usługi z powodu błędu sondy kondycji.  Ta niepowodzna konfiguracja może spowodować niepowodzenie scenariusza aplikacji ze zrównoważonym obciążeniem.

Aby można było oznaczyć wystąpienie Load Balancer sondy kondycji, **należy** zezwolić na ten adres IP w dowolnych [grupach zabezpieczeń sieci](../virtual-network/security-overview.md) platformy Azure i lokalnych zasadach zapory.  Domyślnie każda sieciowa Grupa zabezpieczeń zawiera [tag usługi](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer, aby zezwolić na ruch sondy kondycji.

Jeśli chcesz przetestować błąd sondy kondycji lub oznaczyć pojedyncze wystąpienie, możesz użyć [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) , aby jawnie zablokować sondę kondycji (port docelowy lub [źródłowy adres IP](#probesource)) i symulować awarię sondy.

Nie należy konfigurować sieci wirtualnej z zakresem adresów IP należącym do firmy Microsoft, który zawiera 168.63.129.16.  Takie konfiguracje kolidują z adresem IP sondy kondycji i mogą spowodować niepowodzenie scenariusza.

Jeśli masz wiele interfejsów na maszynie Wirtualnej, należy upewnić się, że możesz odpowiedzieć na sondę na interfejs, który zostało ono dostarczone licencjobiorcy na.  Może być konieczne założenie, że adres sieciowy będzie przetłumaczyć ten adres na maszynę wirtualną dla poszczególnych interfejsów.

Nie włączaj [sygnatur czasowych protokołu TCP](https://tools.ietf.org/html/rfc1323).  Włączenie sygnatur czasowych protokołu TCP może spowodować niepowodzenie sondowania kondycji z powodu porzucenia pakietów TCP przez stos TCP systemu operacyjnego gościa maszyny wirtualnej, co powoduje Load Balancer oznaczania odpowiedniego punktu końcowego.  Sygnatury czasowe protokołu TCP są rutynowo włączane domyślnie w obrazach maszyny wirtualnej z zaostrzonymi zabezpieczeniami i muszą zostać wyłączone.

## <a name="monitoring"></a>Monitorowanie

Publiczna i wewnętrzna [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md) uwidaczniają na punkt końcowy, a stan sondy kondycji punktu końcowego zaplecza jako metryki wielowymiarowe za Azure monitor. Te metryki mogą być używane przez inne usługi platformy Azure lub Aplikacje partnerskie. 

Podstawowa Publiczna Load Balancer udostępnia stan sondy kondycji podsumowany dla puli zaplecza za pośrednictwem dzienników Azure Monitor.  Dzienniki Azure Monitor nie są dostępne dla wewnętrznych podstawowych modułów równoważenia obciążenia.  Możesz użyć [dzienników Azure monitor](load-balancer-monitor-log.md) , aby sprawdzić stan kondycji sondowania publicznego modułu równoważenia obciążenia i liczbę sond. Rejestrowanie może służyć za pomocą usługi Power BI lub usługi Azure Operational Insights umożliwia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.

## <a name="limitations"></a>Ograniczenia

- Sondy protokołu HTTPS nie obsługują wzajemnego uwierzytelniania przy użyciu certyfikatu klienta.
- W przypadku włączenia znaczników czasu TCP należy założyć, że sondy kondycji zakończą się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w Menedżer zasobów przy użyciu programu PowerShell](quickstart-create-standard-load-balancer-powershell.md)
- [Interfejs API REST dla sond kondycji](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Zażądaj nowych możliwości sondowania kondycji za pomocą usługi [Uservoice Load Balancer](https://aka.ms/lbuservoice)
