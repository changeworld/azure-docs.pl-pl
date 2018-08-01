---
title: Użyj niestandardowej sondy modułu równoważenia obciążenia do monitorowania stanu kondycji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z sondy kondycji do monitorowania wystąpień za modułem równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2018
ms.author: kumud
ms.openlocfilehash: b73028935fd60945a948c1c4e1848424b615d92e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363687"
---
# <a name="load-balancer-health-probes"></a>Sondy kondycji modułu równoważenia obciążenia

Usługa Azure Load Balancer używa sondy kondycji, aby określić, które wystąpienia puli zaplecza będą otrzymywać nowych przepływów. Za pomocą sondy kondycji do wykrywania awarii aplikacji na wystąpienie wewnętrznej bazy danych. Można również generować niestandardowe odpowiedzi na sondę kondycji i korzystać z sondy kondycji sterowania przepływem i sygnałów do modułu równoważenia obciążenia, czy kontynuować wysyłanie nowych przepływów lub zatrzymać wysyłanie nowych przepływów z wystąpieniem wewnętrznej bazy danych. Może to służyć do zarządzania obciążenia lub planowanych przestojów.

W przypadku awarii sondę kondycji modułu równoważenia obciążenia zatrzymuje wysyłanie nowych przepływów do odpowiednich wystąpień złej kondycji. Zachowanie nowych i istniejących przepływów zależy od tego, czy przepływ jest TCP lub UDP jako, którą jednostką SKU modułu równoważenia obciążenia oraz używasz.  Przegląd [sondy zachowanie w dół, aby uzyskać szczegółowe informacje](#probedown).

> [!IMPORTANT]
> Sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 i nie musi zostać zablokowany dla sondy do oznaczania wystąpienia usługi.  Przegląd [źródłowego adresu IP sondy](#probesource) Aby uzyskać szczegółowe informacje.

## <a name="health-probe-types"></a>Typy sondy kondycji

Sondy kondycji można obserwować dowolnego portu wystąpieniu wewnętrznej bazy danych, w tym port, na którym znajduje się nazwa rzeczywistej usługi. Sonda kondycji obsługuje odbiorników TCP lub punktów końcowych HTTP. 

UDP równoważenia obciążenia, należy wygenerować sygnał sondy kondycji niestandardowe dla tego wystąpienia wewnętrznej bazy danych, przy użyciu sondę kondycji TCP lub HTTP.

Korzystając z [reguły równoważenia obciążenia na porty wysokiej dostępności](load-balancer-ha-ports-overview.md) z [Standard Load Balancer](load-balancer-standard-overview.md), wszystkie porty są równoważeniem obciążenia i odpowiedzi sondy kondycji jednego powinny odzwierciedlać stan całego wystąpienia.  

Nie NAT i sondę kondycji za pomocą wystąpienia, które otrzymuje sondy kondycji do innego wystąpienia w sieci wirtualnej, ponieważ może to prowadzić do błędów kaskadowych w tym scenariuszu serwer proxy należy.

Jeśli chcesz przetestować błędu sondy kondycji lub oznaczyć szczegółów poszczególnych wystąpień, można użyć grupy zabezpieczeń do bloku jawne sondy kondycji (docelowy lub [źródła](#probesource)).

### <a name="tcp-probe"></a>Sonda TCP

Sondy protokołu TCP zainicjować połączenie, wykonując trzy kierunkową Otwórz TCP uzgadniania z zdefiniowany port.  Następnie następuje to uzgadniania czterokierunkowego Zamknij TCP.

Interwał sondy minimalna to 5 sekund, a minimalna liczba odpowiedzi w złej kondycji wynosi 2.  Łączny czas trwania nie może przekraczać 120 sekund.

Sonda TCP kończy się niepowodzeniem kiedy:
* Odbiornik TCP w wystąpieniu nie odpowiada w określonym przedziale czasu.  Sonda jest oznaczony jako w dół na podstawie liczby sondy zakończone niepowodzeniem żądania, które zostały skonfigurowane do bez odpowiedzi zanim oznaczysz sondy w dół.
* Sonda odbiera TCP, zresetuj z wystąpienia.

### <a name="http-probe"></a>Sonda HTTP

Sondy HTTP nawiąż połączenie TCP i wystawić żądania HTTP GET z określoną ścieżką. Sondy HTTP obsługuje ścieżki względne HTTP GET. Sonda kondycji jest oznaczony jako, gdy wystąpienie odpowiada ze stanem HTTP 200 przed upływem limitu czasu.  HTTP kondycji sondy Próba sprawdzenia port sondy kondycji skonfigurowane co 15 sekund domyślnie. Interwał sondy minimalna to 5 sekund. Łączny czas trwania nie może przekraczać 120 sekund. 


Sondy HTTP może być również przydatne, jeśli chcesz zaimplementować logikę do usuwania wystąpień z rotacji modułu równoważenia obciążenia. Na przykład możesz zdecydować usunąć wystąpienie, gdy przekracza 90% zasobów Procesora i zwrócenia stanu 200 HTTP. 

Jeśli korzystasz z usług w chmurze i mieć role sieci web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia.  Sondy HTTP zastąpienia domyślnej funkcji badania agenta gościa. 

W przypadku sondy HTTP nie powiedzie się po:
* Punkt końcowy sondy HTTP zwraca kod odpowiedzi HTTP inne niż 200 (na przykład, 403, 404 lub 500). To spowoduje oznaczenie sondy kondycji w dół od razu. 
* Punkt końcowy HTTP sonda nie odpowiada podczas drugiego 31 okres limitu czasu. W zależności od wartość limitu czasu, który jest ustawiony, wielokrotne żądania sondowania mogą zostać przekazane bez odpowiedzi, zanim sondy zostanie oznaczone jako nieuruchomiona (czyli przed SuccessFailCount sondy są wysyłane).
* Punkt końcowy sondy HTTP zamyka połączenie za pośrednictwem resetowania TCP.

### <a name="guest-agent-probe-classic-only"></a>Sondowanie agenta gościa (tylko wersja klasyczna)

Role usługi w chmurze (role procesu roboczego i role sieci web) używać agenta gościa na potrzeby sondy monitorowania domyślnie.   Należy rozważyć, to opcja w ostateczności.  Należy zawsze zdefiniować sondy kondycji jawnie przy użyciu protokołu TCP lub HTTP sondy. Sondowanie agenta gościa nie jest tak skuteczne, jak sondy jawnie zdefiniowane w przypadku większości scenariuszy aplikacji.  

Sondowanie agenta gościa jest wyboru agenta gościa wewnątrz maszyny Wirtualnej. Następnie odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzanie lub zatrzymywania).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sondy kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [zacznij od utworzenia publicznego modułu równoważenia obciążenia dla usług w chmurze](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Jeśli agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nie odpowiada. Następnie zatrzymuje, wysyłając przepływy do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu Sprawdź wystąpienie. 

Jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia wysyła nowych przepływów do tego wystąpienia ponownie.

Korzystając z roli sieci web, kod witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowane przez usługę Azure Service fabric lub gościa agenta. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie przyjmuje tego wystąpienia z rotacji.

## <a name="probe-health"></a>Sondy kondycji

Sondy kondycji TCP i HTTP są traktowane jako zdrowych i Oznacz wystąpienia roli, co działa prawidłowo, gdy:

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

### <a name="tcp-connections"></a>Połączenia TCP

Nowe połączenia TCP zakończy się pomyślnie do wystąpienia wewnętrznej bazy danych, która jest w dobrej kondycji i ma systemu operacyjnego gościa i aplikacji, które akceptuje nowy przepływ.

Jeśli sonda kondycji przypadkiem wewnętrznej bazy danych nie powiedzie się, ustanowionych połączeń TCP dla tego wystąpienia wewnętrznej bazy danych jest kontynuowane.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do puli zaplecza będą wysyłane nie nowych przepływów. Load Balancer w warstwie standardowa pozwoli na ustanowionych przepływy TCP, aby kontynuować.  Podstawowy moduł równoważenia obciążenia utracą wszystkie istniejące przepływy TCP do puli zaplecza.
 
Ponieważ przepływ jest zawsze między klientem i systemu operacyjnego gościa maszyny Wirtualnej, frontonu nie odpowiada na próby otwarcia połączenia TCP, ponieważ nie ma żadnego wystąpienia zaplecza w dobrej kondycji, aby otrzymać przepływu spowoduje, że puli za pomocą sondy wszystkich szczegółów.

### <a name="udp-datagrams"></a>Datagramy protokołu UDP

Datagramy protokołu UDP zostanie dostarczona do wystąpień zaplecza w dobrej kondycji.

Protokół UDP jest przesyłanie i nie ma żadnych stan przepływu śledzone dla protokołu UDP. Jeśli sonda kondycji dowolne wystąpienie w wewnętrznej bazie danych nie powiedzie się, istniejące przepływy UDP mogą przenieść do innego wystąpienia dobrej kondycji w puli zaplecza.

W przypadku awarii wszystkich sondy dla wszystkich wystąpień w puli zaplecza, do istniejących przepływów UDP utracą ważność warstwy podstawowa i standardowa usługi równoważenia obciążenia.


## <a name="probesource"></a>Źródłowy adres IP sondy

Wszystkie sondy kondycji modułu równoważenia obciążenia pochodzą z adresu IP 168.63.129.16 jako źródła.  Po przywróceniu adresy IP do sieci wirtualnej platformy Azure, ten adres IP źródła sondy kondycji jest musi być unikatowy, ponieważ globalnie jest zarezerwowane dla firmy Microsoft.  Ten adres jest taka sama we wszystkich regionach i nie zmienia się. Go nie należy rozważyć zagrożenie bezpieczeństwa, ponieważ źródeł pakietów z tego adresu IP mogą być tylko wewnętrzne platformy Azure. 

Sondy kondycji modułu równoważenia obciążenia do oznaczania wystąpienie usługi możesz **musi** zezwolić na ten adres IP na dowolnej platformie Azure [grup zabezpieczeń](../virtual-network/security-overview.md) i zasady lokalne zapory.

Jeśli w zasadach zapory nie zezwalaj na ten adres IP, sondy kondycji zakończy się niepowodzeniem, ponieważ nie można nawiązać połączenia z wystąpieniem usługi.  Z kolei modułu równoważenia obciążenia spowoduje oznaczenie dół wystąpienie usługi z powodu błędu sondy kondycji.  Może to spowodować niepowodzenie usługi równoważenia obciążenia. 

Ponadto nie należy konfigurować sieci wirtualnej z firmą Microsoft należące do zakresu adresów IP, który zawiera 168.63.129.16.  Spowoduje to kolidować z adresu IP sondy kondycji.

Jeśli masz wiele interfejsów na maszynie Wirtualnej, należy upewnić się, że możesz odpowiedzieć na sondę na interfejs, który zostało ono dostarczone licencjobiorcy na.  Może to wymagać unikatowego źródła NAT'ing ten adres na maszynie Wirtualnej na podstawie poszczególnych interfejsu.

## <a name="monitoring"></a>Monitorowanie

Wszystkie [Balancer w warstwie standardowa](load-balancer-standard-overview.md) uwidacznia sondę kondycja jako metryk wielowymiarowych na wystąpienie przy użyciu usługi Azure Monitor.

Podstawowy moduł równoważenia obciążenia przedstawia stan sondy kondycji każdej puli wewnętrznej bazy danych za pomocą usługi Log Analytics.  Jest to tylko dostępne dla publicznych podstawowe usługi równoważenia obciążenia i nie jest dostępna dla wewnętrznych modułów równoważenia obciążenia podstawowe.  Możesz użyć [dziennika analizy](load-balancer-monitor-log.md) można sprawdzić stanu zdrowia sondy modułu równoważenia obciążenia publiczny i sondowania count. Rejestrowanie może służyć za pomocą usługi Power BI lub usługi Azure Operational Insights umożliwia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.


## <a name="limitations"></a>Ograniczenia

-  Sonda kondycji HTTP nie obsługuje protokołu TLS (HTTPS).  Zamiast tego użyj sondowaniem TCP na porcie 443.

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w usłudze Resource Manager przy użyciu programu PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [Interfejs API REST umożliwiający sond kondycji](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

