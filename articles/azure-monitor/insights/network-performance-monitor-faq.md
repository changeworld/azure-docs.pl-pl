---
title: Często zadawane pytania — rozwiązanie Network Performance Monitor na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera często zadawane pytania dotyczące rozwiązania Network Performance Monitor na platformie Azure. Sieć Performance Monitor (NPM) pomaga monitorować wydajność sieci, niemal w czasie rzeczywistym oraz wykrywanie i Znajdź sieci wąskich gardeł wydajności.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinynigam
ms.openlocfilehash: d573b7ad9edac6b1502744b61e85cba3402a6f68
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232658"
---
# <a name="network-performance-monitor-solution-faq"></a>Network Performance Monitor rozwiązania — często zadawane pytania

![Symbol monitora wydajności sieci](media/network-performance-monitor-faq/npm-symbol.png)

Ten artykuł zawiera często zadawane pytania (FAQ) dotyczących Network Performance Monitor (NPM) na platformie Azure

[Monitor wydajności sieci](/azure/networking/network-monitoring-overview) jest oparta na chmurze [monitorowania sieci hybrydowych](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) rozwiązanie, które pomaga monitorować wydajność sieci, między różnymi punktami w infrastrukturze sieci. Pozwala on także monitorować łączność sieciową do [punktów końcowych usług i aplikacji](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) i [monitorowanie wydajności usługi Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Rozwiązanie Network Performance Monitor wykrywa problemy sieciowe, takie jak blackholing ruchu, routingu błędy i problemy, które metody monitorowania sieci są konwencjonalne funkcje nie są w stanie wykryć. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Więcej informacji na temat różnych funkcji obsługiwanych przez [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępna w trybie online.

## <a name="set-up-and-configure-agents"></a>Instalowanie i konfigurowanie agentów

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania dotyczące platformy dla węzłów, które ma być używany do monitorowania Menedżera NPM?
Poniżej wymieniono wymagania dotyczące platformy programu NPM dla różnych funkcji:

- Monitor wydajności i możliwości Monitor łączności usługi NPM obsługuje zarówno Windows server i Windows, komputerów stacjonarnych/klienckie systemy operacyjne. Obsługiwane wersje systemu operacyjnego serwera Windows są 2008 z dodatkiem SP1 lub nowszym. Obsługiwane wersje pulpitów/klienta Windows to Windows 10, Windows 8.1, Windows 8 i Windows 7. 
- Możliwość monitorowania usługi ExpressRoute NPM obsługuje tylko Windows server (2008 z dodatkiem SP1 lub nowszym) systemu operacyjnego.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Czy można używać maszyny z systemem Linux, jak monitorowanie węzłów w NPM?
Możliwość monitorowania sieci przy użyciu węzłów opartych na systemie Linux jest obecnie w wersji zapoznawczej. Dotrzyj do Menedżera konta, aby dowiedzieć się więcej. Agenci dla systemu Linux zapewniają możliwość monitorowania tylko w przypadku funkcji Monitora wydajności Menedżera NPM, a nie są dostępne dla możliwości usługi Monitor łączności i Monitor usługi ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania rozmiar węzłów, ma być używany do monitorowania Menedżera NPM?
Uruchamianie rozwiązania NPM w węźle Monitorowanie sieci maszyn wirtualnych, węzły powinny mieć co najmniej 500 MB pamięci i jednego rdzenia. Nie należy używać oddzielnych węzłów do uruchamiania Menedżera NPM. Rozwiązanie można uruchomić na węzły, które mają innych obciążeń uruchomionych na nim. Rozwiązanie ma możliwości, aby zatrzymać proces monitorowania za pomocą ponad 5% zasobów Procesora.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Aby użyć Menedżera NPM, należy się połączyć mojego węzłów jako funkcja agentów bezpośrednich lub za pomocą programu System Center Operations Manager?
Monitor wydajności i możliwości usługi Monitor łączności obsługują węzłów [podłączony jako agentów bezpośrednich](../../azure-monitor/platform/agent-windows.md) i [połączone za pośrednictwem programu Operations Manager](../../azure-monitor/platform/om-agents.md).

Aby uzyskać możliwość monitorowania usługi ExpressRoute węzły na platformie Azure powinny być połączone jako agentów bezpośrednich tylko. Węzły platformy Azure, które są połączone za pomocą programu Operations Manager nie są obsługiwane. Dla węzłów w środowisku lokalnym węzły połączone jako agentów bezpośrednich i za pomocą programu Operations Manager są obsługiwane w przypadku monitorowania obwodu usługi ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Należy wybrać protokół TCP i ICMP do monitorowania?
Jeśli monitorujesz sieci przy użyciu węzłów na serwerze Windows zalecamy użyć TCP jako protokół monitorowania, ponieważ zapewnia większą dokładność. 

Protokół ICMP jest zalecana dla Windows pulpitów/kliencie systemu Windows w oparciu o system operacyjny węzłów. Ta does'nt platformy umożliwiają danych TCP do przesyłania za pośrednictwem gniazd sieciowych, które używa NPM, aby odnaleźć topologię sieci.

Więcej informacji można uzyskać na względne zalety każdego protokołu [tutaj](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Jak można skonfigurować węzła do obsługi monitorowania przy użyciu protokołu TCP
Dla węzła do obsługi monitorowania przy użyciu protokołu TCP: 
* Upewnij się, że platforma węzeł systemu Windows Server (2008 z dodatkiem SP1 lub nowszym).
* Uruchom [EnableRules.ps1](https://aka.ms/npmpowershellscript) skrypt programu Powershell w węźle. Zobacz [instrukcje](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) Aby uzyskać więcej informacji.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak zmienić port TCP używany przez narzędzie NPM monitorowania?
Możesz zmienić port TCP używany przez narzędzie NPM dla monitorowania, uruchamiając [EnableRules.ps1](https://aka.ms/npmpowershellscript) skryptu. Należy wprowadzić numer portu, którego zamierzasz użyć jako parametru. Na przykład, aby umożliwić ruch TCP na porcie 8060, należy uruchomić `EnableRules.ps1 8060`. Upewnij się, że używasz tego samego portu TCP we wszystkich węzłach używanych do monitorowania.

Skrypt konfiguruje tylko Windows zapory lokalnie. Jeśli masz Zapora sieciowa lub reguł sieciowej grupy zabezpieczeń (NSG), upewnij się, że zezwalają na ruch kierowany do portu TCP używanego przez NPM.

### <a name="how-many-agents-should-i-use"></a>Ile agentów należy używać?
Dla każdej podsieci, które mają być monitorowane, należy użyć co najmniej jednego agenta.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Co to jest maksymalna liczba agentów, których można używać lub widzę błąd w "menu... Osiągnięto limit konfiguracji"?
NPM ogranicza liczbę adresów IP adresy IP 5000 danego obszaru roboczego. Jeśli węzeł ma adresy IPv4 i IPv6, to będzie liczone jako 2 adresy IP, dla tego węzła. W związku z tym ten limit 5000 adresy IP zdecyduje, górny limit liczby agentów. Nieaktywnych agentów można usunąć z węzłów karcie NPM >> Konfiguruj. NPM udostępnia również historię wszystkich adresów IP, który nigdy nie zostały przypisane do maszyny Wirtualnej hostującym agenta oraz każdy jest traktowana jako oddzielne adresów IP, przyczyniając się do tego górny limit 5000 adresów IP. Do Zwolnij adresy IP dla Twojego obszaru roboczego służy strona węzły można usunąć adresy IP, które nie są używane.

## <a name="monitoring"></a>Monitorowanie

### <a name="how-are-loss-and-latency-calculated"></a>Jak są strat i opóźnień obliczane
Źródło agenci wysyłają albo TCP SYN żądania (Jeśli protokół TCP jest wybierany jako protokół monitorowania) lub realizacji żądań ECHA protokołu ICMP (Jeśli protokół ICMP jest wybierany jako protokół monitorowania) do docelowego adresu IP w regularnych odstępach czasu, aby upewnić się, że wszystkie ścieżki między źródłowy i docelowy adres IP Kombinacja zostały uwzględnione. Procent odebranych pakietów i czas obustronnej konwersji pakietów jest zaokrąglana do obliczania strat i opóźnień, każdej ścieżki. Te dane są agregowane dla interwału sondowania i za pośrednictwem wszystkich ścieżek, które można pobrać wartości zagregowane strat i opóźnień dla kombinacji adresu IP dla określonego interwału sondowania.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Częstotliwość źródłowym agencie wysyłanie pakietów do miejsca docelowego do monitorowania?
Monitor wydajności i Monitor usługi ExpressRoute możliwości źródło wysyła pakiety co 5 sekund i rejestruje pomiarów sieci. Tych danych jest agregowana w przedziałach sondowania 3-minutowy do obliczania średniej i szczytowe użycie wartości strat i opóźnień. Dla funkcji usługi Monitor łączności częstotliwość wysyłania pakietów do pomiaru sieci zależy od częstotliwości wprowadzonej przez użytkownika dla specyficznego testu podczas konfigurowania testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Ile pakiety są wysyłane do monitorowania?
Liczba pakietów wysłanych przez agenta źródła do miejsca docelowego w sondowania jest adaptacyjne i zadecyduje o naszego algorytmu mogą być różne dla różnych sieci topologii. Zwiększenie liczby ścieżek sieciowych między kombinacji adresu IP źródłowy i docelowy, więcej jest liczba pakietów, które są wysyłane. System zapewnia, że wszystkie ścieżki między kombinacji adresu IP źródłowy i docelowy znajdują się.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Jak NPM Odnajdywanie topologii sieci, między źródłowym i docelowym?
NPM używa algorytmu, w oparciu o Traceroute do odnajdywania, ścieżki i przeskoków między źródłowym i docelowym.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>NPM zapewnia routing i przełączania poziomu info 
Chociaż NPM może wykryć wszystkie możliwe trasy między agentem źródło i miejsce docelowe, nie zapewnia widoczność, do którego trasa została wykonana przez pakiety wysłane przez konkretnych obciążeń. Rozwiązanie może pomóc w identyfikacji ścieżek i podstawowych przeskoków sieciowych, które dodajesz więcej opóźnienia, niż oczekiwano.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Dlaczego są niektóre ścieżki złej kondycji?
Innymi ścieżkami sieciowymi mogą istnieć między źródłowe i docelowe adresy IP, a każda ścieżka może mieć inną wartość strat i opóźnień. NPM oznacza tych ścieżek o złej kondycji (oznaczone kolorem czerwonym) dla wartości utraty i/lub opóźnienia jest większy niż próg odpowiednich w konfiguracji monitorowania.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co to przeskoków w kolorze czerwonym oznaczającego w mapie topologii sieci?
W przypadku czerwoną skoku, oznacza to, że jest częścią co najmniej jedna ścieżka w złej kondycji. NPM oznacza tylko ścieżki o złej kondycji, go nie oddzielenie czynności związanych z stan kondycji każdej ścieżki. Aby zidentyfikować problematycznych przeskoków, można wyświetlić opóźnienie przeskoku przeskoku i oddzielenie czynności związanych z tymi, dodając więcej niż oczekiwane opóźnienie.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak działa Lokalizacja błędu w Monitorze wydajności
NPM przypisuje prawdopodobieństwa błędów każdej ścieżce sieciowej segmentu sieci przy użyciu mechanizmu Probabilistyczne i przeskoków sieciowych składników, na podstawie liczby ścieżki w złej kondycji są częścią. Gdy segmentów sieci i przeskoków stają się częścią zwiększenie liczby ścieżki w złej kondycji, zwiększa prawdopodobieństwo błędów skojarzonych z nimi. Ten algorytm sprawdza się najlepiej, jeśli masz wiele węzłów za pomocą agenta menedżera NPM, połączone ze sobą, ponieważ zwiększa to punktów danych na obliczanie prawdopodobieństwa błędów.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak utworzyć alerty w NPM
Zapoznaj się [alerty sekcji w dokumentacji](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) instrukcje krok po kroku.

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>NPM monitorować serwery i routerów jako poszczególnych urządzeń?
NPM identyfikuje tylko adresów IP i hosta nazwę podstawowej przeskoków sieciowych (przełączniki, routery, serwery, itp.) między źródłowe i docelowe adresy IP. Określa on także opóźnienia między te zidentyfikowanych przeskoków. Nie indywidualnie monitoruje ona tych podstawowych przeskoków.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Może służyć do monitorowania łączności sieciowej między platformą Azure i AWS NPM?
Tak. Zapoznaj się z artykułem [monitorowanie platformy Azure, AWS i sieciami lokalnymi za pomocą Menedżera NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) Aby uzyskać szczegółowe informacje.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Przychodzące lub wychodzące, jest użycie przepustowości usługi ExpressRoute?
Wykorzystanie przepustowości jest łączną przepustowość przychodzących i wychodzących. Jest on wyrażany w bitach na sekundę.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Firma Microsoft znajdziesz informacje przychodzące i wychodzące przepustowości dla usługi ExpressRoute?
Mogą być przechwytywane wartości przychodzących i wychodzących dla przepustowości podstawowym i pomocniczym.

Aby uzyskać informacje dotyczące komunikacji równorzędnej poziomu, użyj poniżej wymienionych zapytania podczas wyszukiwania dziennika

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Aby uzyskać informacje o poziomie obwodu, użyj poniżej wymienionych zapytania 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Które regiony są obsługiwane w przypadku monitora wydajności Menedżera NPM?
NPM można monitorować łączność między sieciami w dowolnej części świata, z obszaru roboczego, który znajduje się w jednym z [obsługiwane regiony](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Które regiony są obsługiwane w przypadku Monitor łączności usługi NPM?
NPM można monitorować łączność z usługami w dowolnej części świata, z obszaru roboczego, który znajduje się w jednym z [obsługiwane regiony](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Które regiony są obsługiwane w przypadku rozwiązania NPM Monitor usługi ExpressRoute?
NPM można monitorować obwodów usługi ExpressRoute znajduje się w dowolnym regionie systemu Azure. Aby dołączyć do usługi NPM, będzie wymagać obszaru roboczego usługi Log Analytics, który musi być hostowany w jednym z [obsługiwane regiony](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Dlaczego są przeskoków oznaczone jako niezidentyfikowanych w widoku topologii sieci?
NPM używa zmodyfikowanej wersji traceroute umożliwia odnalezienie topologii z agenta źródła do miejsca docelowego. Niezidentyfikowany przeskok reprezentuje to, że przeskok sieci nie odpowiedziała na żądanie traceroute agenta źródłowego. Jeśli trzy przeskoków sieciowych kolejnych odpowiada na polecenie traceroute agenta, rozwiązanie oznacza odpowiadać przeskoków jako niezidentyfikowane i nie podejmuje próby odkryć więcej przeskoków.

Przeskok nie mogą odpowiadać na traceroute w co najmniej jeden z poniższych scenariuszy:

* Routery skonfigurowano nie ujawnił swojej tożsamości.
* Urządzenia sieciowe nie zezwalają na ruch ICMP_TTL_EXCEEDED.
* Zapora nie blokuje odpowiedzi ICMP_TTL_EXCEEDED z urządzenia sieciowego.

### <a name="why-does-my-link-show-unhealthy-but-the-topology-does-not"></a>Dlaczego zła pokazu łącza, ale topologii nie 
NPM monitoruje utraty end-to-end, opóźnienia i topologii w różnych interwałach. Strat i opóźnień są mierzone co 5 sekund i agregowane co trzy minuty (dla monitora wydajności i monitora Express Route) podczas gdy topologia jest obliczana przy użyciu traceroute raz na 10 minut. Na przykład między 3:44 i 4:04 topologia może zostać zaktualizowany trzy razy (3:44, 3:54 4:04), ale strat i opóźnień zostaną zaktualizowane o siedem razy (3:44, 3:47 3:50, 3:53 3:56 3:59, 4:02). Topologia generowane w 3:54 będzie renderowana dla strat i opóźnień, która pobiera obliczona na 3:56 3:59 i 4:02. Załóżmy, że otrzymasz alert obwodu ER była zła 3:59. Zaloguj się do usługi NPM i próbuje ustawić czas topologii do 3:59. NPM spowoduje, że topologia generowane w 3:54. Aby poznać ostatnie znane topologii sieci, porównanie pola TimeProcessed (czas, w których strat i opóźnień obliczono) i TracerouteCompletedTime (czas w topologii, która została obliczona). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Jaka jest różnica między polami E2EMedianLatency i AvgHopLatencyList w tabeli NetworkMonitoring
E2EMedianLatency to opóźnienie aktualizowane co trzy minuty po uaktualnieniu do większej agregacji wyników testów ping protokołu tcp, jest AvgHopLatencyList co 10 minut, w oparciu o traceroute. Aby poznać dokładny czas, w którym została obliczona E2EMedianLatency, należy użyć pola TimeProcessed. Aby poznać dokładny czas, w których traceroute zostało ukończone i zaktualizowano AvgHopLatencyList, należy użyć pola TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Dlaczego liczby przeskoków przeskoku opóźnienia jest różnica między HopLatencyValues 
HopLatencyValues są źródłem do punktu końcowego.
Na przykład: Przeskoki — A, B, c AvgHopLatency - 10,15,20. Oznacza to, że źródło do opóźnieniem = 10, źródło do opóźnienia B = 15 oraz źródła do C opóźnienia wynosi 20. Interfejs użytkownika będzie obliczać opóźnienie przeskoku A i B, jak 5 w topologii

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>To rozwiązanie przedstawia utraty 100%, ale istnieje łączność między miejscem źródłowym i docelowym
Może się to zdarzyć, jeśli Zapora hosta lub pośredniego zapory (Zapora sieciowa lub sieciowej grupy zabezpieczeń platformy Azure) blokuje komunikację między agentem źródło i miejsce docelowe za pośrednictwem portów używanych do monitorowania przez narzędzie NPM (domyślnie port jest port 8084, chyba że Klient został zmieniony to).

* Aby sprawdzić, czy Zapora hosta nie blokuje komunikację na porcie wymagane, Wyświetl stan kondycji węzłów źródłowych i docelowych z następującego widoku: Rozwiązanie Network Performance Monitor -> Konfiguracja -> węzłów. 
  Jeśli są w złej kondycji, Wyświetl instrukcje i podejmij działania naprawcze. Jeśli węzły są w dobrej kondycji, przejdź do kroku b. poniżej.
* Aby sprawdzić, czy Zapora sieciowa pośrednie lub sieciowej grupy zabezpieczeń platformy Azure nie blokuje komunikację na porcie wymagane, użyj do innych firm PsPing narzędzie przy użyciu poniższych instrukcji:
  * Narzędzie psping jest dostępna do pobrania [tutaj](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Uruchom następujące polecenie z węzeł źródłowy.
    * Narzędzie psping -n 15 \<węzeł docelowy adres IP\>: numer_portu domyślnie NPM wykorzystuje 8084 port. W przypadku możesz jawnie zmieniono to za pomocą skrypt EnableRules.ps1, wprowadź numer portu niestandardowego, którego używasz). Jest to ping z maszyny platformy Azure do środowiska lokalnego
* Sprawdź, czy pomyślnie polecenia ping. Jeśli nie, następnie oznacza to, że Zapora sieciowa pośrednie lub sieciowej grupy zabezpieczeń usługi Azure blokuje ruch na tym porcie.
* Teraz uruchom polecenie w węźle docelowym i źródłowy adres IP węzła.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Następuje utrata z węzła A do B, ale nie z węzła B do serwera A. Dlaczego?
Jak ścieżek sieciowych, od A do B może różnić się od ścieżek sieciowych między B do A, można zaobserwować różne wartości dla strat i opóźnień.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Dlaczego są wszystkie obwody usługi ExpressRoute i połączeń komunikacji równorzędnej nie odnaleziono?
NPM wykrywa teraz obwody usługi ExpressRoute i połączeń komunikacji równorzędnej w przypadku wszystkich subskrypcji, do których użytkownik ma dostęp. Wybierz wszystkie subskrypcje, w którym zasoby usługi Express Route są połączone i Włącz monitorowanie dla każdego odnalezionego zasobu. NPM szuka obiekty połączenia podczas rozpoznawania prywatnej komunikacji równorzędnej, dlatego Sprawdź, czy sieć wirtualna jest skojarzona z usługi komunikacji równorzędnej.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkcja ER Monitor ma komunikat diagnostyczny "Ruch nie przechodzi przez wszystkie obwód". Co to oznacza?

Może wystąpić scenariusz, w którym ma dobrej kondycji połączenie między usługą lokalną i węzły na platformie Azure, ale ruch nie przechodzi w obwodzie usługi ExpressRoute skonfigurowana do monitorowania przez narzędzie NPM. 

Może się to zdarzyć, jeśli:

* Obwód ER nie działa.
* Filtry tras są skonfigurowane w taki sposób, mogą nadać priorytet do innych tras (np. połączenia sieci VPN lub innego obwodu usługi ExpressRoute) za pośrednictwem zakładanego obwodu usługi ExpressRoute. 
* Lokalne i węzły wybrany do monitorowania obwód usługi ExpressRoute w konfiguracji monitorowania na platformie Azure nie ma połączenia ze sobą za pośrednictwem zakładanego obwodu usługi ExpressRoute. Upewnij się, wybrano poprawny węzły, które mają łączność do siebie nawzajem za pośrednictwem obwodu usługi ExpressRoute, które chcesz monitorować.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Podczas konfigurowania, monitorowania z obwodem usługi ExpressRoute, nie są zostały wykryte węzły na platformie Azure.
Może to nastąpić, jeśli węzły na platformie Azure są połączone za pomocą programu Operations Manager. Funkcja Monitor usługi ExpressRoute obsługuje tylko tych węzłów platformy Azure, które są połączone agentów bezpośrednich.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nie można odnaleźć przez obwodów usługi ExpressRoute w portalu pakietu OMS
Chociaż można użyć Menedżera NPM, zarówno z witryny Azure portal, jak również w portalu pakietu OMS, odnajdywanie obwód w możliwości Monitor usługi ExpressRoute działa wyłącznie za pośrednictwem witryny Azure portal. Po odnalezieniu obwodów za pośrednictwem witryny Azure portal możesz użyć możliwości w jednym z tych dwóch portali. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>W możliwości usługi Monitor łączności czasu odpowiedzi usługi, utrata połączenia sieciowego, jak również opóźnienia są wyświetlane jako n/d
Może się to zdarzyć, jeśli co najmniej jeden:

* Usługa nie działa.
* Węzeł, używany do sprawdzania łączności sieciowej z usługą nie działa.
* Element docelowy w konfiguracji testu jest nieprawidłowy.
* Węzeł nie ma łączności sieciowej.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>W możliwości usługi Monitor łączności usługi prawidłowy czas odpowiedzi jest wyświetlane, ale utrata połączenia sieciowego, a także opóźnienia są wyświetlane jako n/d
 Może się to zdarzyć, jeśli co najmniej jeden:

* Węzeł używany do sprawdzania łączności sieciowej do usługi w przypadku komputera klienta Windows, Usługa docelowa blokuje żądania protokołu ICMP lub Zapora sieci blokuje żądania protokołu ICMP, które pochodzą z węzła.
* Pole wyboru wykonaj pomiary sieci jest puste w konfiguracji testu.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>W możliwości Monitor łączności usługi Czas odpowiedzi usługi to nazwa, ale utrata połączenia sieciowego, a także opóźnienia są prawidłowe
Może się to zdarzyć, jeśli Usługa docelowa nie jest aplikacją sieci web, ale test został skonfigurowany jako test sieci Web. Edytuj konfigurację testu, a następnie wybierz typ testu jako sieci zamiast sieci Web.

## <a name="miscellaneous"></a>Różne

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>W węźle, używanych do monitorowania jest negatywny wpływ na wydajność?
Proces NPM jest skonfigurowany tak, aby zatrzymać, jeśli jego korzysta z więcej niż 5% zasobów Procesora hosta. To, aby upewnić się, że możesz nadal korzystać z węzły dla swoich obciążeń zwykle bez wpływu na wydajność.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>NPM edytować reguły zapory na potrzeby monitorowania?
NPM tworzy tylko lokalne reguły zapory Windows w węzłach, na których uruchomiono skrypt EnableRules.ps1 programu Powershell umożliwia agentów do utworzenia połączenia TCP ze sobą na określonym porcie. Rozwiązanie nie powoduje modyfikacji, wszelkie zapory sieciowej lub reguł sieciowej grupy zabezpieczeń (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak można sprawdzić kondycji węzłów używanych do monitorowania
Można wyświetlić stan kondycji węzłów używanych do monitorowania z następującego widoku: Rozwiązanie Network Performance Monitor -> Konfiguracja -> węzłów. Jeśli węzeł jest w złej kondycji, można wyświetlić szczegóły błędów i sugerowane akcje.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Można NPM raport wartościami opóźnień w mikrosekundach
NPM zaokrągla liczby opóźnienia w Interfejsie i w milisekundach. Te same dane są przechowywane na większą szczegółowość (czasami do czterech miejsc po przecinku).

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat rozwiązania Network Performance Monitor, odwołując się do [rozwiązanie Network Performance Monitor na platformie Azure](../../azure-monitor/insights/network-performance-monitor.md).
