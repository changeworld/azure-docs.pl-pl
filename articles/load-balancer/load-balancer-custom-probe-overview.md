---
title: Chronić za pomocą sondy kondycji modułu równoważenia obciążenia do usługi
titlesuffix: Azure Load Balancer
description: Dowiedz się, jak korzystać z sondy kondycji do monitorowania wystąpień za modułem równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 8ffc2a84850254451bb8356ceb0c08cd56823afd
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344214"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji modułu równoważenia obciążenia

Usługa Azure Load Balancer używa sondy kondycji, aby określić, które wystąpienia puli zaplecza będą otrzymywać nowych przepływów. Za pomocą sondy kondycji do wykrywania awarii aplikacji na wystąpienie wewnętrznej bazy danych. Można również generować niestandardowe odpowiedzi na sondę kondycji i korzystać z sondy kondycji sterowania przepływem i sygnałów do modułu równoważenia obciążenia, czy kontynuować wysyłanie nowych przepływów lub zatrzymać wysyłanie nowych przepływów z wystąpieniem wewnętrznej bazy danych. Może to służyć do zarządzania obciążenia lub planowanych przestojów. W przypadku awarii sondę kondycji modułu równoważenia obciążenia zatrzymuje wysyłanie nowych przepływów do odpowiednich wystąpień złej kondycji.

Typy dostępnych sond kondycji i sposób kondycji, które zachowują się sondy jest zależna od których jednostek SKU dla modułu równoważenia obciążenia przy użyciu. Na przykład zachowanie nowych i istniejących przepływów zależy od tego, czy przepływ to TCP lub UDP jako, którą jednostką SKU modułu równoważenia obciążenia oraz używasz.

| | Standardowy SKU | Podstawowy SKU |
| --- | --- | --- |
| [Typy sondy](#types) | TCP I HTTP, HTTPS | TCP I HTTP |
| [Badanie zachowania w dół](#probedown) | Wszystkie sondy, wszystkie przepływy TCP nadal. | Wszystkie sondy w dół, Zakończ wszystkie przepływy TCP. | 

> [!IMPORTANT]
> Sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 i nie musi zostać zablokowany dla sondy do oznaczania wystąpienia usługi.  Przegląd [źródłowego adresu IP sondy](#probesource) Aby uzyskać szczegółowe informacje.

## <a name="types"></a>Typy sondy

Sondy kondycji można obserwować dowolnego portu wystąpieniu wewnętrznej bazy danych, w tym port, na którym znajduje się nazwa rzeczywistej usługi. Protokół sondy kondycji można skonfigurować dla trzech różnych rodzajów sond kondycji:

- [Odbiorniki TCP](#tcpprobe)
- [Punktów końcowych HTTP](#httpprobe)
- [Punkty końcowe HTTPS](#httpsprobe)

Dostępne typy sond kondycji różnią się w zależności od jednostki SKU modułu równoważenia obciążenia, które zostały wybrane:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standardowy SKU |    &#9989; |   &#9989; |   &#9989; |
| Podstawowy SKU |   &#9989; |   &#9989; | &#10060; |

UDP równoważenia obciążenia, należy wygenerować sygnał sondy kondycji niestandardowe dla wystąpienia wewnętrznej bazy danych przy użyciu TCP, protokołu HTTP lub sondę kondycji protokołu HTTPS.

Korzystając z [reguły równoważenia obciążenia na porty wysokiej dostępności](load-balancer-ha-ports-overview.md) z [Standard Load Balancer](load-balancer-standard-overview.md), wszystkie porty są równoważeniem obciążenia i odpowiedzi sondy kondycji jednego muszą odzwierciedlać stan całego wystąpienia.  

Nie NAT i sondę kondycji za pomocą wystąpienia, które otrzymuje sondy kondycji do innego wystąpienia w sieci wirtualnej, ponieważ może to prowadzić do błędów kaskadowych w tym scenariuszu serwer proxy należy.

Jeśli chcesz przetestować błędu sondy kondycji lub oznaczyć szczegółów poszczególnych wystąpień, można użyć grupy zabezpieczeń do bloku jawne sondy kondycji (docelowy lub [źródła](#probesource)).

>[!IMPORTANT]
> Nie włączaj [sygnatury czasowe TCP](https://tools.ietf.org/html/rfc1323).  Włączenie protokołu TCP sygnatur czasowych spowoduje, że sondy kondycji zakończyć się niepowodzeniem z powodu pakiety TCP pomijanego przez gościa maszyny Wirtualnej dotyczącą stosu TCP systemu operacyjnego, co powoduje oznaczenie odpowiednich punktu końcowego w dół modułu równoważenia obciążenia.

### <a name="tcpprobe"></a> Sonda TCP

Sondy protokołu TCP zainicjować połączenie, wykonując trzy kierunkową Otwórz TCP uzgadniania z zdefiniowany port.  Następnie następuje to uzgadniania czterokierunkowego Zamknij TCP.

Interwał sondy minimalna to 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Łączny czas trwania nie może przekraczać 120 sekund.

Sonda TCP kończy się niepowodzeniem kiedy:
* Odbiornik TCP w wystąpieniu nie odpowiada w określonym przedziale czasu.  Sonda jest oznaczony jako w dół na podstawie liczby sondy zakończone niepowodzeniem żądania, które zostały skonfigurowane do bez odpowiedzi zanim oznaczysz sondy w dół.
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

Sondy HTTP i HTTPS nawiąż połączenie TCP i wystawić żądania HTTP GET z określoną ścieżką. Obsługa obu tych sond HTTP GET ścieżek względnych. Sondy protokołu HTTPS są takie same jak sondy HTTP, dodając Transport Layer Security (TLS, znana wcześniej jako SSL) otoki. Sonda kondycji jest oznaczony jako, gdy wystąpienie odpowiada ze stanem HTTP 200 przed upływem limitu czasu.  Kondycja tych sond próby Sprawdź port sondy kondycji skonfigurowane co 15 sekund domyślnie. Interwał sondy minimalna to 5 sekund. Łączny czas trwania nie może przekraczać 120 sekund. 

HTTP / HTTPS sond może być również przydatne, jeśli chcesz zaimplementować logikę do usuwania wystąpień z rotacji modułu równoważenia obciążenia. Na przykład możesz zdecydować usunąć wystąpienie, gdy przekracza 90% zasobów Procesora i zwrócenia stanu 200 HTTP. 

Jeśli korzystasz z usług w chmurze i mieć role sieci web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia.  Sondy HTTP zastąpienia domyślnej funkcji badania agenta gościa. 

HTTP / sondy protokołu HTTPS nie powiedzie się po:
* Końcowego sondy zwraca kod odpowiedzi HTTP inne niż 200 (na przykład, 403, 404 lub 500). To spowoduje oznaczenie sondy kondycji w dół od razu. 
* Punkt końcowy sonda nie odpowiada podczas 31 sekundy limitu czasu. W zależności od wartość limitu czasu, który jest ustawiony, wielokrotne żądania sondowania mogą zostać przekazane bez odpowiedzi, zanim sondy zostanie oznaczone jako nieuruchomiona (czyli przed SuccessFailCount sondy są wysyłane).
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

Role usługi w chmurze (role procesu roboczego i role sieci web) używać agenta gościa na potrzeby sondy monitorowania domyślnie.   Należy rozważyć, to opcja w ostateczności.  Należy zawsze zdefiniować sondy kondycji jawnie przy użyciu protokołu TCP lub HTTP sondowania. Sondowanie agenta gościa nie jest tak skuteczne, jak sondy jawnie zdefiniowane w przypadku większości scenariuszy aplikacji.  

Sondowanie agenta gościa jest wyboru agenta gościa wewnątrz maszyny Wirtualnej. Następnie odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzanie lub zatrzymywania).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sondy kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [zacznij od utworzenia publicznego modułu równoważenia obciążenia dla usług w chmurze](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Jeśli agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nie odpowiada. Następnie zatrzymuje, wysyłając przepływy do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu Sprawdź wystąpienie. 

Jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia wysyła nowych przepływów do tego wystąpienia ponownie.

Korzystając z roli sieci web, kod witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowane przez usługę Azure Service fabric lub gościa agenta. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie przyjmuje tego wystąpienia z rotacji.

## <a name="probehealth"></a>Sondy kondycji

Sondy kondycji TCP, HTTP i HTTPS są traktowane jako zdrowych i Oznacz wystąpienia roli, co działa prawidłowo, gdy:

* Sonda kondycji zakończy się pomyślnie, gdy maszyna wirtualna zostanie po raz pierwszy.
* Numer SuccessFailCount (opisanym wcześniej) definiuje wartość pomyślne sond, które są wymagane do oznaczenia wystąpienia roli jako w dobrej kondycji. Jeśli wystąpienie roli zostało usunięte, liczbę pomyślnych, kolejne sond musisz równa lub przekracza wartość SuccessFailCount do oznaczenia wystąpienia roli, co działa.

> [!NOTE]
> Jeśli zmienia się kondycję wystąpienia roli, usługi równoważenia obciążenia już przed oczekuje umieszcza wystąpienia roli w dobrej kondycji. Czas oczekiwania dodatkowe chroni użytkownika i infrastruktura i zamierzone zasady.

## <a name="probe-count-and-timeout"></a>Limit czasu i liczba sondy

Zachowanie sondy zależy od:

* Liczba pomyślnych sond, zezwalających na wystąpienie, które można oznaczyć jako czas.
* Liczba nieudanych sond, powodujące wystąpienie był oznaczony jako w dół.

Limit czasu i częstotliwość wartości SuccessFailCount ustalić, czy wystąpienie jest potwierdzone, że jest uruchomiona lub nie działa. W witrynie Azure portal limit czasu jest równa dwa razy wartości częstotliwości.

Reguły równoważenia obciążenia sondę kondycji jednego zdefiniował puli odpowiedniego zaplecza.

## <a name="probedown"></a>Badanie zachowania w dół

### <a name="tcp-connections"></a>Połączenia protokołu TCP

Nowe połączenia TCP powiedzie się z wystąpieniem wewnętrznej bazy danych, które ma dobrą kondycję i systemu operacyjnego gościa i aplikacji, które akceptuje nowy przepływ.

Jeśli sonda kondycji przypadkiem wewnętrznej bazy danych nie powiedzie się, ustanowionych połączeń TCP dla tego wystąpienia wewnętrznej bazy danych jest kontynuowane.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do puli zaplecza będą wysyłane nie nowych przepływów. Load Balancer w warstwie standardowa pozwoli na ustanowionych przepływy TCP, aby kontynuować.  Podstawowy moduł równoważenia obciążenia utracą wszystkie istniejące przepływy TCP do puli zaplecza.
 
Ponieważ przepływ jest zawsze między klientem i systemu operacyjnego gościa maszyny Wirtualnej, frontonu nie odpowiada na próby otwarcia połączenia TCP, ponieważ nie ma żadnego wystąpienia zaplecza w dobrej kondycji, aby otrzymać przepływu spowoduje, że puli za pomocą sondy wszystkich szczegółów.

### <a name="udp-datagrams"></a>Datagramy protokołu UDP

Datagramy protokołu UDP zostanie dostarczona do wystąpień zaplecza w dobrej kondycji.

Protokół UDP jest przesyłanie i nie ma żadnych stan przepływu śledzone dla protokołu UDP. Jeśli sonda kondycji dowolne wystąpienie w wewnętrznej bazie danych nie powiedzie się, istniejące przepływy UDP mogą przenieść do innego wystąpienia dobrej kondycji w puli zaplecza.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do istniejących przepływów UDP utracą ważność warstwy podstawowa i standardowa usługi równoważenia obciążenia.

## <a name="probesource"></a>Źródłowy adres IP sondy

Moduł równoważenia obciążenia używa usługami rozproszonymi badania swój model kondycji wewnętrznego. Każdy host, na którym znajdują się maszyny wirtualne można zaprogramowane w taki sposób, aby wygenerować sond kondycji dla konfiguracji klienta. Ruch sondy kondycji jest bezpośrednio między klient maszyn wirtualnych i składników infrastruktury, co generuje sondy kondycji. Wszystkie sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 jako źródła.  Po przywróceniu adresy IP do sieci wirtualnej platformy Azure, ten adres IP źródła sondy kondycji jest musi być unikatowy, ponieważ globalnie jest zarezerwowane dla firmy Microsoft.  Ten adres jest taka sama we wszystkich regionach i nie zmienia się. Go nie należy rozważyć zagrożenie bezpieczeństwa, ponieważ źródeł pakietów z tego adresu IP mogą być tylko wewnętrzne platformy Azure. 

Oprócz sondy kondycji modułu równoważenia obciążenia ten adres IP użyty następujące operacje:

- Włącza agenta maszyny wirtualnej do komunikowania się z platformą do sygnalizowania, że jest on w stanie "Gotowe"
- Umożliwia komunikację z serwerem wirtualnym DNS, aby zapewnić rozpoznawanie nazw filtrowane do klientów, którzy nie zdefiniujesz niestandardowe serwery DNS.  Filtrowanie gwarantuje, że klienci tylko może rozpoznać nazwy hostów ich wdrożenia.

Sondy kondycji modułu równoważenia obciążenia do oznaczania wystąpienie usługi możesz **musi** zezwolić na ten adres IP na dowolnej platformie Azure [grup zabezpieczeń](../virtual-network/security-overview.md) i zasady lokalne zapory.  Domyślnie co sieciowa grupa zabezpieczeń zawiera [tag usługi](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer, aby zezwalać na ruch sondy kondycji.

Jeśli w zasadach zapory nie zezwalaj na ten adres IP, sondy kondycji zakończy się niepowodzeniem, ponieważ nie można nawiązać połączenia z wystąpieniem usługi.  Z kolei modułu równoważenia obciążenia spowoduje oznaczenie dół wystąpienie usługi z powodu błędu sondy kondycji.  Może to spowodować niepowodzenie usługi równoważenia obciążenia. 

Ponadto nie należy konfigurować sieci wirtualnej z firmą Microsoft należące do zakresu adresów IP, który zawiera 168.63.129.16.  Spowoduje to kolidować z adresu IP sondy kondycji.

Jeśli masz wiele interfejsów na maszynie Wirtualnej, należy upewnić się, że możesz odpowiedzieć na sondę na interfejs, który zostało ono dostarczone licencjobiorcy na.  Może to wymagać unikatowego źródła NAT'ing ten adres na maszynie Wirtualnej na podstawie poszczególnych interfejsu.

## <a name="monitoring"></a>Monitorowanie

Zarówno public i internal [Balancer w warstwie standardowa](load-balancer-standard-overview.md) uwidocznić na stan sondy kondycji wystąpienie punktu końcowego i wewnętrznej bazy danych jako metryk wielowymiarowych za pomocą usługi Azure Monitor. To może następnie być używany przez inne usługi platformy Azure lub 3 aplikacje innych firm. 

Podstawowe publicznej usługi Load Balancer udostępnia stan sondy kondycji podsumowywane na pulę zaplecza za pomocą usługi Log Analytics.  Nie jest on dostępny w przypadku wewnętrznych modułów równoważenia obciążenia podstawowe.  Możesz użyć [dziennika analizy](load-balancer-monitor-log.md) można sprawdzić stanu zdrowia sondy modułu równoważenia obciążenia publiczny i sondowania count. Rejestrowanie może służyć za pomocą usługi Power BI lub usługi Azure Operational Insights umożliwia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.

## <a name="limitations"></a>Ograniczenia

-  Sondy protokołu HTTPS nie obsługują wzajemnego uwierzytelniania przy użyciu certyfikatu klienta.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)
- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w usłudze Resource Manager przy użyciu programu PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Interfejs API REST umożliwiający sond kondycji](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Żądanie nowych możliwości sondę kondycji za pomocą [równoważenia obciążenia w usłudze Uservoice](https://aka.ms/lbuservoice)
