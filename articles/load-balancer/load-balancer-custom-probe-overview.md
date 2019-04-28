---
title: Użyj usługi Azure Load Balancer sondy kondycji do skalowania i zapewnić wysoką dostępność usługi
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak korzystać z sondy kondycji do monitorowania wystąpień za modułem równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2018
ms.author: kumud
ms.openlocfilehash: 913693e684ba8640a93f50d21dd3df6a6295e1c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884762"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji modułu równoważenia obciążenia

Usługa Azure Load Balancer zapewnia sondy kondycji do użycia za pomocą reguł równoważenia obciążenia.  Odpowiedzi sond kondycji konfiguracji i badania określają, które wystąpienia puli zaplecza będą otrzymywać nowych przepływów. Za pomocą sondy kondycji do wykrywania awarii aplikacji na wystąpienie wewnętrznej bazy danych. Można również generować niestandardowe odpowiedzi na sondę kondycji i używaj sondy kondycji sterowania przepływem do zarządzania obciążenia lub planowanych przestojów. W przypadku awarii sondę kondycji modułu równoważenia obciążenia zatrzymuje wysyłanie nowych przepływów do odpowiednich wystąpień złej kondycji.

Sondy kondycji obsługują wiele protokołów. Dostępność dla określonego typu sondy kondycji do obsługi określonego protokołu, zależy od jednostki SKU modułu równoważenia obciążenia.  Ponadto zachowanie usługi jest zależna od jednostki SKU modułu równoważenia obciążenia.

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| [Typy sondy](#types) | TCP I HTTP, HTTPS | TCP I HTTP |
| [Badanie zachowania w dół](#probedown) | Wszystkie sondy, wszystkie przepływy TCP nadal. | Wszystkie sondy w dół, Zakończ wszystkie przepływy TCP. | 

> [!IMPORTANT]
> Sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 i nie musi zostać zablokowany dla sondy do oznaczania wystąpienia usługi.  Przegląd [źródłowego adresu IP sondy](#probesource) Aby uzyskać szczegółowe informacje.

## <a name="types"></a>Typy sondy

Sonda kondycji można skonfigurować dla obiektów nasłuchujących przy użyciu następujących trzech protokołów:

- [Odbiorniki TCP](#tcpprobe)
- [Punktów końcowych HTTP](#httpprobe)
- [Punkty końcowe HTTPS](#httpsprobe)

Dostępne typy sond kondycji różnią się w zależności od jednostki SKU modułu równoważenia obciążenia, które zostały wybrane:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardowy SKU |    &#9989; |   &#9989; |   &#9989; |
| Podstawowy SKU |   &#9989; |   &#9989; | &#10060; |

Niezależnie od tego, jakiego typu sondowania, możesz wybrać sond kondycji można obserwować dowolnego portu wystąpieniu wewnętrznej bazy danych, w tym port, na którym znajduje się nazwa rzeczywistej usługi.

### <a name="tcpprobe"></a> Sonda TCP

Sondy protokołu TCP zainicjować połączenie, wykonując trzy kierunkową Otwórz TCP uzgadniania z zdefiniowany port.  Sondy protokołu TCP zakończyć połączenie z uzgadniania czterokierunkowego Zamknij TCP.

Interwał sondy minimalna to 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Łączny czas trwania wszystkich interwałów nie może przekraczać 120 sekund.

Sonda TCP kończy się niepowodzeniem kiedy:
* Odbiornik TCP w wystąpieniu nie odpowiada w określonym przedziale czasu.  Sonda jest oznaczony jako w dół na podstawie liczby sondy zakończone niepowodzeniem żądania, które zostały skonfigurowane do bez odpowiedzi zanim oznaczysz dół sondy.
* Sonda odbiera TCP, zresetuj z wystąpienia.

#### <a name="resource-manager-template"></a>Szablon usługi Resource Manager

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

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS sondowania

> [!NOTE]
> Sondy protokołu HTTPS jest dostępna tylko dla [Balancer w warstwie standardowa](load-balancer-standard-overview.md).

Sondy HTTP i HTTPS rozwijać sonda TCP i wystawić żądania HTTP GET z określoną ścieżką. Obsługa obu tych sond HTTP GET ścieżek względnych. Sondy protokołu HTTPS są takie same jak sondy HTTP, dodając Transport Layer Security (TLS, znana wcześniej jako SSL) otoki. Sonda kondycji jest oznaczony jako, gdy wystąpienie odpowiada ze stanem HTTP 200 przed upływem limitu czasu.  Sonda kondycji próbuje sprawdzić port sondy kondycji skonfigurowane co 15 sekund domyślnie. Interwał sondy minimalna to 5 sekund. Łączny czas trwania wszystkich interwałów nie może przekraczać 120 sekund.

HTTP / HTTPS sond może być również przydatne, jeśli chcesz wyrazić sondy kondycji.  Implementowanie własnej logiki, aby usunąć wystąpienia z rotacji modułu równoważenia obciążenia, jeśli port sondy jest również detektorów za samą usługę. Na przykład możesz zdecydować usunąć wystąpienie, gdy przekracza 90% zasobów Procesora i zwrócenia stanu 200 HTTP. 

Jeśli korzystasz z usług w chmurze i mieć role sieci web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia.

HTTP / sondy protokołu HTTPS nie powiedzie się po:
* Końcowego sondy zwraca kod odpowiedzi HTTP inne niż 200 (na przykład, 403, 404 lub 500). Spowoduje to oznaczenie dół sondy kondycji natychmiast. 
* Punkt końcowy sonda nie odpowiada w okresie 31-sekundowy limit. Wielokrotne żądania sondowania mogą zostać przekazane bez odpowiedzi zanim sondy zostanie oznaczone jako nie jest uruchomiona, i do czasu osiągnięcia sumę wszystkich interwałów czasu.
* Końcowego sondy zamyka połączenie za pośrednictwem resetowania TCP.

#### <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

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

### <a name="guestagent"></a>Sondowanie agenta gościa (tylko wersja klasyczna)

Role usługi w chmurze (role procesu roboczego i role sieci web) używać agenta gościa na potrzeby sondy monitorowania domyślnie.  Sondowanie agenta gościa jest ostatnia Konfiguracja czynność.  Zawsze używaj sondy kondycji jawnie przy użyciu protokołu TCP lub HTTP sondowania. Sondowanie agenta gościa nie jest tak skuteczne, jak sondy jawnie zdefiniowane w przypadku większości scenariuszy aplikacji.

Sondowanie agenta gościa jest wyboru agenta gościa wewnątrz maszyny Wirtualnej. Następnie odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzanie lub zatrzymywania).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sondy kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [zacznij od utworzenia publicznego modułu równoważenia obciążenia dla usług w chmurze](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Jeśli agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nie odpowiada. Następnie zatrzymuje, wysyłając przepływy do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu Sprawdź wystąpienie. 

Jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia wysyła nowych przepływów do tego wystąpienia ponownie.

Korzystając z roli sieci web, kod witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowane przez usługę Azure Service fabric lub gościa agenta. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie przyjmuje tego wystąpienia z rotacji.

<a name="health"></a>
## <a name="probehealth"></a>Sondy kondycji

Sondy kondycji TCP, HTTP i HTTPS są traktowane jako zdrowych i Oznacz wystąpienia roli, co działa prawidłowo, gdy:

* Sonda kondycji zakończy się jeden raz po uruchomieniu maszyny Wirtualnej.
* Został osiągnięty określoną liczbę sond wymagane w celu oznaczenia wystąpienia roli jako w dobrej kondycji.

> [!NOTE]
> Jeśli zmienia się sondy kondycji modułu równoważenia obciążenia już przed oczekuje umieszcza wystąpienia roli w dobrej kondycji. Czas oczekiwania dodatkowe chroni użytkownika i infrastruktura i zamierzone zasady.

## <a name="probe-count-and-timeout"></a>Limit czasu i liczba sondy

Zachowanie sondy zależy od:

* Liczba pomyślnych sond, zezwalających na wystąpienie, które można oznaczyć jako czas.
* Liczba nieudanych sond, powodujące wystąpienie był oznaczony jako w dół.

Określone wartości limitu czasu i interwał ustalić, czy wystąpienie jest oznaczony jako w górę lub w dół.

## <a name="probedown"></a>Badanie zachowania w dół

### <a name="tcp-connections"></a>Połączenia protokołu TCP

Nowe połączenia TCP zakończy się pomyślnie do pozostałych wystąpień zaplecza w dobrej kondycji.

Jeśli sonda kondycji przypadkiem wewnętrznej bazy danych nie powiedzie się, ustanowionych połączeń TCP dla tego wystąpienia wewnętrznej bazy danych jest kontynuowane.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do puli zaplecza będą wysyłane nie nowych przepływów. Load Balancer w warstwie standardowa pozwoli na ustanowionych przepływy TCP, aby kontynuować.  Podstawowy moduł równoważenia obciążenia utracą wszystkie istniejące przepływy TCP do puli zaplecza.
 
Moduł równoważenia obciążenia jest przekazywania service (nie kończy połączeń TCP) i przepływ będzie zawsze między klientem i systemu operacyjnego gościa i aplikacji maszyny Wirtualnej. Puli za pomocą sondy wszystkich szczegółów spowoduje, że frontonu nie odpowiada na połączenie TCP próby otwarcia (SYN) się żadne wystąpienie zaplecza w dobrej kondycji do odbierania przepływ i elastyczniejsze SYN ACK.

### <a name="udp-datagrams"></a>Datagramy protokołu UDP

Datagramy protokołu UDP zostanie dostarczona do wystąpień zaplecza w dobrej kondycji.

Protokół UDP jest przesyłanie i nie ma żadnych stan przepływu śledzone dla protokołu UDP. Jeśli sonda kondycji dowolne wystąpienie w wewnętrznej bazie danych nie powiedzie się, istniejące przepływy UDP mogą przenieść do innego wystąpienia dobrej kondycji w puli zaplecza.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do istniejących przepływów UDP utracą ważność warstwy podstawowa i standardowa usługi równoważenia obciążenia.

<a name="source"></a>
## <a name="probesource"></a>Źródłowy adres IP sondy

Moduł równoważenia obciążenia używa usługami rozproszonymi badania swój model kondycji wewnętrznego. Usługa badania znajdują się na każdym hoście gdzie maszyn wirtualnych i może być zaplanowany na żądanie, aby wygenerować sond kondycji dla konfiguracji klienta. Ruch sondy kondycji jest bezpośrednio między sondowania usługa, która generuje sondy kondycji i klienta maszyny Wirtualnej. Wszystkie sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 jako źródła.  Możesz użyć przestrzeni adresów IP w sieci wirtualnej, która nie ma miejsca RFC1918.  Za pomocą globalnie zarezerwowane, Microsoft należące do adresu IP zmniejsza prawdopodobieństwo konfliktu adresu IP z obszaru adresów IP, którego używasz wewnątrz sieci wirtualnej.  Ten adres IP jest taka sama we wszystkich regionach i nie zmienia się i nie jest to zagrożenie bezpieczeństwa, ponieważ źródeł pakietów z tego adresu IP mogą być tylko składnik wewnętrznych platformy Azure. 

Numer seryjny AzureLoadBalancer identyfikuje ten źródłowy adres IP w sieci [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i zezwala na ruch sondy kondycji, domyślnie.

Oprócz sondy kondycji modułu równoważenia obciążenia ten adres IP użyty następujące operacje:

- Włącza agenta maszyny wirtualnej do komunikowania się z platformą do sygnalizowania, że jest on w stanie "Gotowe"
- Umożliwia komunikację z serwerem wirtualnym DNS, aby zapewnić rozpoznawanie nazw filtrowane do klientów, którzy nie zdefiniujesz niestandardowe serwery DNS.  Filtrowanie gwarantuje, że klienci tylko może rozpoznać nazwy hostów ich wdrożenia.
- Umożliwia maszynie Wirtualnej można uzyskać dynamicznego adresu IP z usługi DHCP w systemie Azure.

## <a name="design"></a> Wskazówki dotyczące projektowania

Sondy kondycji są używane usługi odporne na błędy i zezwolić na jego skalowanie. Błędnej konfiguracji lub wzorzec projektowy zły może mieć wpływ na dostępność i skalowalność usługi. Przejrzyj to całego dokumentu, a następnie należy wziąć pod uwagę co wpływ w przypadku Twojego scenariusza jest w przypadku tej odpowiedzi sondowania jest oznaczony w dół lub oznaczone i jak wpływa na dostępność scenariusz aplikacji.

Podczas projektowania modelu kondycji dla aplikacji, należy sondowania portu w wystąpieniu wewnętrznej bazy danych, które odzwierciedla kondycję tego wystąpienia __i__ usługi aplikacji, które mają być udostępniane.  Port aplikacji i port sondy nie muszą być takie same.  W niektórych scenariuszach może być pożądane, jeśli port sondy jest inny niż port, który aplikacja udostępnia usługi na.  

Czasami może być przydatne w przypadku aplikacji do generowania odpowiedzi sonda kondycji nie tylko wykrywania kondycji aplikacji, ale także sygnał bezpośrednio do modułu równoważenia obciążenia, czy wystąpienie powinno odbierać lub nie otrzymywać nowych przepływów.  Możesz manipulować odpowiedzi sondowania, aby umożliwić aplikacji Utwórz wsteczne i ograniczenia dostarczania nowych przepływów na wystąpienie nie sondy kondycji lub przygotowania do obsługi aplikacji i Inicjowanie opróżniania danego scenariusza.  Gdy użycie standardowego modułu równoważenia obciążenia [sondowania w dół](#probedown) sygnału zawsze będzie dopuszczany przepływy TCP, aby kontynuować do czasu bezczynności połączenia lub limit czasu zamknięcia. 

UDP równoważenia obciążenia, należy wygenerować sygnał sondy kondycji niestandardowe z wystąpienia wewnętrznej bazy danych i użyć sondę kondycji TCP, HTTP lub HTTPS, przeznaczone dla odpowiedniego odbiornika w celu odzwierciedlenia kondycji aplikacji UDP.

Korzystając z [reguł równoważenia obciążenia porty wysokiej dostępności](load-balancer-ha-ports-overview.md) z [Standard Load Balancer](load-balancer-standard-overview.md), wszystkie porty są równoważeniem obciążenia i odpowiedzi sondy kondycji jednego muszą odzwierciedlać stan całego wystąpienia.

Tłumaczy lub serwera proxy sondę kondycji za pomocą wystąpienia, które odbiera sondy kondycji do innego wystąpienia w sieci wirtualnej, ponieważ ta konfiguracja może prowadzić do błędów kaskadowych w danym scenariuszu.  Rozważmy następujący scenariusz: zestawu urządzeń innej firmy jest wdrożona w puli zaplecza zasobu modułu równoważenia obciążenia w celu zapewnienia skalowalności i nadmiarowości dla urządzeń i sondy kondycji jest skonfigurowany do sondowania portu, serwery proxy urządzenia innych firm lub przekłada się maszynami wirtualnymi za urządzenie.  Jeśli badanie jest tego samego portu, którego używasz do tłumaczenia lub żądania serwera proxy dla innych maszyn wirtualnych za modułem urządzenia, sondy odpowiedzi od jednej maszyny wirtualnej za modułem urządzenia spowoduje oznaczenie urządzenia, sama martwe. Ta konfiguracja może prowadzić do awarii kaskadowych scenariusza całej aplikacji, w wyniku wystąpienia pojedynczego zaplecza za urządzenie.  Wyzwalacz może być awaria sporadyczne sondowania spowoduje, że moduł równoważenia obciążenia oznaczyć w dół do początkowego miejsca docelowego (wystąpienia urządzenia), która z kolei może wyłączyć scenariusz całej aplikacji. Zamiast tego sondy kondycji urządzenia, sam. Wybór sondowania, aby określić sygnał kondycji jest ważną kwestią sieci wirtualnych urządzeń sieciowych (WUS w) scenariuszach i należy zapoznać się z dostawcą aplikacji przygotujmy się sygnał kondycji odpowiednie w przypadku takich scenariuszy.

Jeśli nie zezwalaj na [źródłowy adres IP](#probesource) sondy w zasadach zapory sonda kondycji nie powiedzie się nie można nawiązać połączenia z wystąpieniem usługi.  Z kolei modułu równoważenia obciążenia spowoduje oznaczenie dół wystąpienie usługi z powodu błędu sondy kondycji.  Ten błąd konfiguracji może spowodować scenariusz aplikacji równoważenia obciążenia nie powiedzie się.

Sondy kondycji modułu równoważenia obciążenia do oznaczania wystąpienia możesz **musi** zezwolić na ten adres IP na dowolnej platformie Azure [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) i zasady lokalne zapory.  Domyślnie co sieciowa grupa zabezpieczeń zawiera [tag usługi](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer, aby zezwalać na ruch sondy kondycji.

Jeśli chcesz przetestować błędu sondy kondycji lub oznaczyć szczegółów poszczególnych wystąpień, możesz użyć [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) jawnie zablokować sondy kondycji (portu docelowego lub [źródłowy adres IP](#probesource)) i symulować Błąd sondy.

Nie należy konfigurować sieci wirtualnej z firmą Microsoft należące do zakresu adresów IP, który zawiera 168.63.129.16.  Takie konfiguracje będą kolidować z adresu IP sondy kondycji i może spowodować, że scenariusza nie powiedzie się.

Jeśli masz wiele interfejsów na maszynie Wirtualnej, należy upewnić się, że możesz odpowiedzieć na sondę na interfejs, który zostało ono dostarczone licencjobiorcy na.  Konieczne może być Sieć źródłowa adres tłumaczą ten adres na maszynie Wirtualnej na podstawie poszczególnych interfejsu.

Nie włączaj [sygnatury czasowe TCP](https://tools.ietf.org/html/rfc1323).  Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji zakończyć się niepowodzeniem z powodu pakiety TCP pomijanego przez gościa maszyny Wirtualnej dotyczącą stosu TCP systemu operacyjnego, co skutkuje oznaczanie szczegółów odpowiedniego punktu końcowego modułu równoważenia obciążenia.  Sygnatury czasowe TCP rutynowo są włączone domyślnie w systemie zabezpieczeń wzmocnione obrazy maszyn wirtualnych i musi zostać wyłączona.

## <a name="monitoring"></a>Monitorowanie

Zarówno public i internal [Balancer w warstwie standardowa](load-balancer-standard-overview.md) uwidocznić na stan sondy kondycji wystąpienie punktu końcowego i wewnętrznej bazy danych jako metryk wielowymiarowych za pomocą usługi Azure Monitor. Metryki te mogą być używane przez inne usługi platformy Azure lub aplikacje partnerów. 

Podstawowe publicznej usługi Load Balancer udostępnia podsumowywane na pulę zaplecza przy użyciu dzienników usługi Azure Monitor stanu sondy kondycji.  Dzienniki platformy Azure Monitor nie są dostępne dla wewnętrznych modułów równoważenia obciążenia podstawowe.  Możesz użyć [dzienniki usługi Azure Monitor](load-balancer-monitor-log.md) można sprawdzić stanu zdrowia sondy modułu równoważenia obciążenia publiczny i sondowania count. Rejestrowanie może służyć za pomocą usługi Power BI lub usługi Azure Operational Insights umożliwia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.

## <a name="limitations"></a>Ograniczenia

- Sondy protokołu HTTPS nie obsługują wzajemnego uwierzytelniania przy użyciu certyfikatu klienta.
- Sondy kondycji zakończy się niepowodzeniem, gdy włączone są sygnatury czasowe TCP.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w usłudze Resource Manager przy użyciu programu PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Interfejs API REST umożliwiający sond kondycji](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Żądanie nowych możliwości sondę kondycji za pomocą [równoważenia obciążenia w usłudze Uservoice](https://aka.ms/lbuservoice)
