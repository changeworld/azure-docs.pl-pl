---
title: Często zadawane pytania — Rozwiązanie Monitor wydajności sieci na platformie Azure | Dokumenty firmy Microsoft
description: W tym artykule przechwytywanie często zadawanych pytań dotyczących Monitora wydajności sieci na platformie Azure. Monitor wydajności sieci (NPM) pomaga monitorować wydajność sieci w czasie zbliżonym do rzeczywistego oraz wykrywać i lokalizać wąskie gardła wydajności sieci.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096225"
---
# <a name="network-performance-monitor-solution-faq"></a>— często zadawane pytania dotyczące rozwiązania Monitor wydajności sieci

![Symbol Monitora wydajności sieci](media/network-performance-monitor-faq/npm-symbol.png)

W tym artykule przechwytywanie często zadawanych pytań dotyczących Monitora wydajności sieci (NPM) na platformie Azure

[Monitor wydajności sieci](/azure/networking/network-monitoring-overview) to oparte na chmurze [rozwiązanie do monitorowania sieci hybrydowej,](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) które pomaga monitorować wydajność sieci między różnymi punktami w infrastrukturze sieciowej. Pomaga również monitorować łączność sieciową z [punktami końcowymi usług i aplikacji](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) oraz [monitorować wydajność usługi Azure ExpressRoute.](../../azure-monitor/insights/network-performance-monitor-expressroute.md) 

Monitor wydajności sieci wykrywa problemy z siecią, takie jak blackholing ruchu, błędy routingu i problemy, których konwencjonalne metody monitorowania sieci nie są w stanie wykryć. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Więcej informacji na temat różnych możliwości obsługiwanych przez [Monitor wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępny w trybie online.

## <a name="set-up-and-configure-agents"></a>Konfigurowanie i konfigurowanie agentów

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania platformy dla węzłów, które mają być używane do monitorowania przez NPM?
Poniżej wymieniono wymagania dotyczące platformy dla różnych możliwości NPM:

- Funkcje Monitora wydajności i Monitora łączności usług NPM obsługują zarówno serwer Windows, jak i komputery stacjonarne/klienckie systemy operacyjne Windows. Obsługiwane wersje systemu operacyjnego systemu Windows server to dodatek SP1 z 2008 r. lub nowszy. Obsługiwane komputery stacjonarne/wersje klienckie systemu Windows to windows 10, Windows 8.1, Windows 8 i Windows 7. 
- Funkcja Monitora usługi ExpressRoute monitor firmy NPM obsługuje tylko system operacyjny Windows Server (2008 SP1 lub nowszy).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Czy mogę używać maszyn z systemem Linux jako węzłów monitorowania w NPM?
Możliwość monitorowania sieci przy użyciu węzłów opartych na systemie Linux jest obecnie w wersji zapoznawczej. Skontaktuj się ze swoim Menedżerem konta, aby dowiedzieć się więcej. Agenci systemu Linux zapewniają możliwość monitorowania tylko dla funkcji Monitora wydajności npm i nie są dostępne dla funkcji Monitor łączności usługi i monitora usługi ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania dotyczące rozmiaru węzłów, które mają być używane do monitorowania przez npm?
W celu uruchomienia rozwiązania NPM na maszynach wirtualnych węzłów do monitorowania sieci węzły powinny mieć co najmniej 500 MB pamięci i jeden rdzeń. Nie trzeba używać oddzielnych węzłów do uruchamiania NPM. Rozwiązanie można uruchomić w węzłach, które mają inne obciążenia uruchomione na nim. Rozwiązanie ma możliwość zatrzymania procesu monitorowania, jeśli używa więcej niż 5% procesora CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Aby używać funkcji NPM, czy należy połączyć węzły jako agenta bezpośredniego lub za pośrednictwem programu System Center Operations Manager?
Zarówno Monitor wydajności, jak i monitor łączności usług obsługują węzły [połączone jako agenci bezpośredni](../../azure-monitor/platform/agent-windows.md) i połączone za pośrednictwem programu Operations [Manager](../../azure-monitor/platform/om-agents.md).

W przypadku funkcji Monitora usługi ExpressRoute węzły platformy Azure powinny być połączone tylko jako agenci bezpośredni. Węzły platformy Azure, które są połączone za pośrednictwem programu Operations Manager nie są obsługiwane. W przypadku węzłów lokalnych węzły połączone jako agenci bezpośredni i za pośrednictwem programu Operations Manager są obsługiwane do monitorowania obwodu usługi ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Jaki protokół między TCP i ICMP należy wybrać do monitorowania?
Jeśli monitorujesz sieć przy użyciu węzłów opartych na systemie Windows, zaleca się użycie protokołu TCP jako protokołu monitorowania, ponieważ zapewnia on większą dokładność. 

ICMP jest zalecany dla pulpitów systemu Windows/węzłów opartych na systemie operacyjnym. Ta platforma nie zezwala na przesyłanie danych TCP za pośrednictwem nieprzetworzonych gniazd, których npm używa do odnajdywanie topologii sieci.

Więcej informacji na temat względnych zalet każdego protokołu można uzyskać [tutaj.](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol)

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Jak skonfigurować węzeł do obsługi monitorowania przy użyciu protokołu TCP?
Aby węzeł obsługiwał monitorowanie przy użyciu protokołu TCP: 
* Upewnij się, że platformą węzłów jest system Windows Server (2008 z dodatku SP1 lub nowszym).
* Uruchom skrypt [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell w węźle. Zobacz [instrukcje,](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) aby uzyskać więcej informacji.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak zmienić port TCP używany przez npm do monitorowania?
Port TCP używany przez serwer NPM do monitorowania można zmienić, uruchamiając skrypt [EnableRules.ps1.](https://aka.ms/npmpowershellscript) Należy wprowadzić numer portu, którego chcesz użyć jako parametru. Na przykład, aby włączyć protokół TCP na `EnableRules.ps1 8060`porcie 8060, uruchom opcję . Upewnij się, że używasz tego samego portu TCP we wszystkich węzłach używanych do monitorowania.

Skrypt konfiguruje tylko Zaporę systemu Windows lokalnie. Jeśli masz zaporę sieciową lub reguły sieciowej grupy zabezpieczeń (NSG), upewnij się, że zezwalają one na ruch przeznaczony dla portu TCP używanego przez npm.

### <a name="how-many-agents-should-i-use"></a>Ile agentów należy używać?
Należy użyć co najmniej jednego agenta dla każdej podsieci, którą chcesz monitorować.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Jaka jest maksymalna liczba agentów, których mogę użyć lub widzę błąd ".... osiągnąłeś limit konfiguracji"?
Npm ogranicza liczbę serwerów IP do 5000 ip na obszar roboczy. Jeśli węzeł ma adresy IPv4 i IPv6, będzie to liczone jako 2 adresy IP dla tego węzła. W związku z tym ten limit 5000 ip decydowałoby o górnej granicy liczby agentów. Nieaktywnych agentów można usunąć z karty Węzły w >> konfigurowania NPM. NPM prowadzi również historię wszystkich adresów IP, które kiedykolwiek zostały przypisane do maszyny Wirtualnej obsługującej agenta i każdy jest liczony jako oddzielny adres IP przyczyniający się do tego górnego limitu 5000 adresów IP. Aby zwolnić pliki IP dla obszaru roboczego, można użyć strony Węzły, aby usunąć pliki IP, które nie są używane.

## <a name="monitoring"></a>Monitorowanie

### <a name="how-are-loss-and-latency-calculated"></a>Jak obliczane są straty i opóźnienia
Agenci źródła wysyłają żądania TCP SYN (jeśli protokół TCP jest wybrany jako protokół monitorowania) lub żądania ICMP ECHO (jeśli protokół ICMP jest wybrany jako protokół monitorowania) do docelowego adresu IP w regularnych odstępach czasu, aby upewnić się, że wszystkie ścieżki między adresem IP docelowego źródła są objęte. Procent odebranych pakietów i czas podróży w obie strony pakietu jest mierzony w celu obliczenia utraty i opóźnienia każdej ścieżki. Te dane są agregowane w interwale sondowania i na wszystkich ścieżkach, aby uzyskać zagregowane wartości utraty i opóźnienia dla kombinacji IP dla określonego interwału sondowania.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Z jaką częstotliwością agent źródłowy wysyła pakiety do miejsca docelowego w celu monitorowania?
W przypadku funkcji Monitora wydajności i monitora usługi ExpressRoute źródło wysyła pakiety co 5 sekund i rejestruje pomiary sieciowe. Te dane są agregowane w 3-minutowym interwale sondowania, aby obliczyć średnie i szczytowe wartości strat i opóźnień. W przypadku funkcji Monitor łączności usługi częstotliwość wysyłania pakietów do pomiaru sieci zależy od częstotliwości wprowadzonej przez użytkownika dla określonego testu podczas konfigurowania testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Ile pakietów jest wysyłanych do monitorowania?
Liczba pakietów wysłanych przez agenta źródłowego do miejsca docelowego w sondowaniu jest adaptacyjna i jest ustalana przez nasz zastrzeżony algorytm, który może być różny dla różnych topologii sieci. Więcej liczby ścieżek sieciowych między kombinacją adresów IP źródło-miejsce docelowe, więcej jest liczba pakietów, które są wysyłane. System zapewnia, że wszystkie ścieżki między kombinacją adresów IP źródła docelowego są objęte.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>W jaki sposób npm odnajduje topologię sieci między źródłem a miejscem docelowym?
NPM używa zastrzeżonego algorytmu opartego na Traceroute, aby odkryć wszystkie ścieżki i przeskoki między źródłem a miejscem docelowym.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Czy NPM zapewnia informacje o poziomie routingu i przełączania 
Chociaż npm można wykryć wszystkie możliwe trasy między agentem źródłowym a miejscem docelowym, nie zapewnia wglądu w to, która trasa została podjęta przez pakiety wysyłane przez określone obciążenia. Rozwiązanie może pomóc w zidentyfikowaniu ścieżek i podstawowych przeskoków sieciowych, które dodają więcej opóźnień niż oczekiwano.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Dlaczego niektóre ścieżki są niezdrowe?
Różne ścieżki sieciowe mogą istnieć między źródłowymi i docelowymi adresami IP, a każda ścieżka może mieć inną wartość utraty i opóźnienia. NPM oznacza te ścieżki jako złej kondycji (oznaczone kolorem czerwonym), dla których wartości straty i/lub opóźnienia jest większa niż odpowiedni próg ustawiony w konfiguracji monitorowania.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co oznacza przeskok w kolorze czerwonym na mapie topologii sieci?
Jeśli przeskok jest czerwony, oznacza to, że jest częścią co najmniej jednej ścieżki w złej kondycji. NPM oznacza tylko ścieżki jako złej kondycji, nie segreguje stanu kondycji każdej ścieżki. Aby zidentyfikować kłopotliwe przeskoki, można wyświetlić opóźnienie przeskoku i segregować te, które dodają więcej niż oczekiwane opóźnienie.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak działa lokalizacja błędów w Monitorze wydajności?
NPM używa mechanizmu probabilistycznego do przypisywania prawdopodobieństwa błędów do każdej ścieżki sieciowej, segmentu sieci i przeskoków sieci składowych na podstawie liczby ścieżek w złej kondycji, których są częścią. W miarę jak segmenty sieciowe i przeskoki stają się częścią większej liczby ścieżek w złej kondycji, zwiększa się prawdopodobieństwo wystąpienia błędu z nimi związane. Ten algorytm działa najlepiej, gdy masz wiele węzłów z agentem NPM połączonych ze sobą, ponieważ zwiększa to punkty danych do obliczania prawdopodobieństwa błędu.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak utworzyć alerty w npm?
Zapoznaj się z [sekcją alerty w dokumentacji,](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) aby uzyskać instrukcje krok po kroku.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Jakie są domyślne zapytania usługi Log Analytics dla alertów
Kwerenda monitora wydajności

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Kwerenda monitora łączności usługi

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
Kwerendy monitora usługi ExpressRoute: kwerenda obwodów

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Prywatna komunikacja równorzędna

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Komunikacja równorzędna firmy Microsoft

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Wspólna kwerenda   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Czy NPM może monitorować routery i serwery jako poszczególne urządzenia?
Serwer NPM identyfikuje tylko adres IP i nazwę hosta podstawowych przeskoków sieciowych (przełączników, routerów, serwerów itp.) między źródłowymi i docelowymi adresami IP. Identyfikuje również opóźnienie między tymi zidentyfikowanymi przeskokami. Nie monitoruje indywidualnie tych podstawowych przeskoków.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Czy npm może służyć do monitorowania łączności sieciowej między platformą Azure i AWS?
Tak. Szczegółowe informacje można znaleźć w artykule [Monitoruj platformę Azure, usługi AWS i sieci lokalne przy użyciu usługi NPM.](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Czy użycie przepustowości usługi ExpressRoute jest przychodzące lub wychodzące?
Użycie przepustowości to całkowita przepustowość przychodząca i wychodząca. Jest wyrażona w bitach/s.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Czy możemy uzyskać informacje o przepustowości przychodzącej i wychodzącej dla usługi ExpressRoute?
Można przechwycić wartości przychodzące i wychodzące zarówno dla przepustowości podstawowej, jak i pomocniczej.

Aby uzyskać informacje na poziomie komunikacji równorzędnej MS, użyj poniższej kwerendy w funkcji Wyszukiwanie dzienników

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Aby uzyskać prywatne informacje na poziomie komunikacji równorzędnej, użyj poniższej kwerendy w funkcji Wyszukiwanie dzienników

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Aby uzyskać informacje o poziomie obwodu, użyj poniższej kwerendy w funkcji Wyszukiwanie dzienników

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Które regiony są obsługiwane dla Monitora wydajności npm?
Serwer NPM może monitorować łączność między sieciami w dowolnej części świata, z obszaru roboczego hostowanego w jednym z [obsługiwanych regionów](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Które regiony są obsługiwane dla monitora łączności usług NPM?
Npm może monitorować łączność z usługami w dowolnej części świata, z obszaru roboczego hostowanego w jednym z [obsługiwanych regionów](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Które regiony są obsługiwane dla monitora usługi ExpressRoute monitora npm?
Serwer NPM może monitorować obwody usługi ExpressRoute znajdujące się w dowolnym regionie platformy Azure. Aby ować na pokładzie do npm, trzeba będzie log Analytics obszar roboczy, który musi być obsługiwany w jednym z [obsługiwanych regionów](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Dlaczego niektóre przeskoki są oznaczone jako niezidentyfikowane w widoku topologii sieci?
NPM używa zmodyfikowanej wersji traceroute do odnajdywanie topologii od agenta źródłowego do miejsca docelowego. Niezidentyfikowany przeskok reprezentuje, że przeskok sieciowy nie odpowiedział na żądanie traceroute agenta źródłowego. Jeśli trzy kolejne przeskoki sieciowe nie odpowiadają na traceroute agenta, rozwiązanie oznacza przeskok nieodpowiadające jako niezidentyfikowane i nie próbuje odkryć więcej przeskoków.

Przeskok może nie odpowiadać na traceroute w jednym lub kilku z poniższych scenariuszy:

* Routery zostały skonfigurowane tak, aby nie ujawniały swojej tożsamości.
* Urządzenia sieciowe nie zezwalają na ICMP_TTL_EXCEEDED ruchu.
* Zapora blokuje odpowiedź ICMP_TTL_EXCEEDED z urządzenia sieciowego.

Gdy jeden z punktów końcowych znajduje się na platformie Azure, traceroute wyświetla niezidentyfikowane przeskoki, ponieważ infrastruktura platformy Azure nie ujawnia tożsamości do traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Otrzymuję alerty dla niezdrowych testów, ale nie widzę wysokich wartości na wykresie strat i opóźnień NPM. Jak sprawdzić, co jest niezdrowe?
NPM podnosi alert, jeśli koniec do końca opóźnienia między źródłem i miejscem docelowym przekracza próg dla dowolnej ścieżki między nimi. Niektóre sieci mają wiele ścieżek łączących to samo źródło i miejsce docelowe. NPM podnosi alert jest każda ścieżka jest w złej kondycji. Strata i opóźnienie widoczne na wykresach jest średnią wartością dla wszystkich ścieżek, w związku z tym może nie wyświetlać dokładnej wartości pojedynczej ścieżki. Aby zrozumieć, gdzie próg został naruszony, poszukaj kolumny "SubType" w alercie. Jeśli problem jest spowodowany przez ścieżkę wartość Podtypu będzie NetworkPath (dla testów Monitora wydajności), EndpointPath (dla testów Monitor łączności usługi) i ExpressRoutePath (dla testów Monitora ExpressRotue). 

Przykładowe zapytanie do znalezienia jest ścieżka jest w złej kondycji:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Dlaczego mój test pokazuje niezdrowe, ale topologia nie 
Npm monitoruje straty end-to-end, opóźnienia i topologii w różnych odstępach czasu. Straty i opóźnienia są mierzone raz na 5 sekund i agregowane co trzy minuty (dla Monitora wydajności i monitora trasy ekspresowej), podczas gdy topologia jest obliczana przy użyciu traceroute raz na 10 minut. Na przykład między 3:44 a 4:04 topologia może być aktualizowana trzy razy (3:44, 3:54, 4:04), ale strata i opóźnienie są aktualizowane około siedem razy (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologia wygenerowana w 3:54 zostanie renderowana z powodu utraty i opóźnienia, które zostanie obliczone na 3:56, 3:59 i 4:02. Załóżmy, że otrzymasz alert, że obwód ER był w złej kondycji o 3:59. Zaloguj się do NPM i spróbuj ustawić czas topologii na 3:59. NPM renderuje topologię generowaną o godzinie 3:54. Aby zrozumieć ostatnią znaną topologię sieci, porównaj pola TimeProcessed (czas, w którym obliczono utratę i opóźnienie) i TracerouteCompletedTime(czas, w którym obliczono topologię). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Jaka jest różnica między polami E2EMedianLatency i AvgHopLatencyList w tabeli NetworkMonitoring
E2EMedianLatency jest opóźnienie aktualizowane co trzy minuty po agregacji wyników testów ping tcp, natomiast AvgHopLatencyList jest aktualizowany co 10 minut na podstawie traceroute. Aby zrozumieć dokładny czas, w którym E2EMedianLatency został obliczony, należy użyć pola TimeProcessed. Aby zrozumieć dokładną godzinę, w której traceroute ukończone i zaktualizowane AvgHopLatencyList, użyj pola TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Dlaczego numery opóźnienia przeskoku po przeskoku różnią się od hoplatencyValues 
HopLatencyValues są źródłem do punktu końcowego.
Na przykład: Chmiel - A,B,C. ŚredniaHopLatency - 10,15,20. Oznacza to źródło do opóźnienie = 10, źródło do opóźnienia B = 15 i źródło do C opóźnienie wynosi 20. Interfejs użytkownika obliczy opóźnienie przeskoku A-B jako 5 w topologii

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Rozwiązanie pokazuje 100% straty, ale istnieje łączność między źródłem a miejscem docelowym
Może się tak zdarzyć, jeśli zapora hosta lub zapora pośrednia (zapora sieciowa sieciowa lub sieć sieciowa platformy Azure Azure) blokuje komunikację między agentem źródłowym a miejscem docelowym za pośrednictwem portu używanego do monitorowania przez npm (domyślnie port ma wartość 8084, chyba że klient to zmienił).

* Aby sprawdzić, czy zapora hosta nie blokuje komunikacji na wymaganym porcie, wyświetl stan kondycji węzłów źródłowych i docelowych z następującego widoku: Monitor wydajności sieci -> Konfiguracja -> węzłów. 
  Jeśli są w złej kondycji, zobacz instrukcje i podjąć działania naprawcze. Jeśli węzły są w dobrej kondycji, przejdź do kroku b. poniżej.
* Aby sprawdzić, czy zapora sieciowa sieciowej platformy Azure lub sieciowej sieciowej platformy Azure nie blokuje komunikacji na wymaganym porcie, użyj narzędzia PsPing innej firmy, korzystając z poniższych instrukcji:
  * psping narzędzie jest dostępny do pobrania [tutaj](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Uruchom następujące polecenie z węzła źródłowego.
    * psping -n 15 \<węzeł docelowy IPAddress\>:portNumber Domyślnie NPM używa portu 8084. W przypadku jawnego zmiany tego za pomocą skryptu EnableRules.ps1 wprowadź niestandardowy numer portu, którego używasz). Jest to ping z komputera platformy Azure do lokalnego
* Sprawdź, czy pingi się powiodły. Jeśli nie, to wskazuje, że zapora sieci pośredniej lub sieciowej platformy Azure sieciowej blokuje ruch na tym porcie.
* Teraz uruchom polecenie z węzła docelowego do adresu IP węzła źródłowego.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Istnieje utrata z węzła A do B, ale nie z węzła B do A. Dlaczego?
Ponieważ ścieżki sieciowe między A do B mogą się różnić od ścieżek sieciowych między B do A, można zaobserwować różne wartości strat i opóźnień.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Dlaczego nie są odnajdowywane wszystkie obwody usługi ExpressRoute i połączenia komunikacji równorzędnej?
Serwer NPM odnajduje teraz obwody usługi ExpressRoute i połączenia komunikacji równorzędnej we wszystkich subskrypcjach, do których użytkownik ma dostęp. Wybierz wszystkie subskrypcje, w których są połączone zasoby trasy ekspresowej, i włącz monitorowanie dla każdego odnalezionego zasobu. NPM wyszukuje obiekty połączeń podczas odnajdywania prywatnej komunikacji równorzędnej, więc sprawdź, czy sieć wirtualna jest skojarzona z komunikacją równorzędną.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkcja monitora ER ma komunikat diagnostyczny "Ruch nie przechodzi przez żaden obwód". Co to oznacza?

Może istnieć scenariusz, w którym istnieje w dobrej kondycji połączenie między węzłami lokalnym i platformy Azure, ale ruch nie będzie za pomocą obwodu usługi ExpressRoute skonfigurowany do monitorowania przez npm. 

Może się tak zdarzyć, jeśli:

* Obwód ER jest w dół.
* Filtry trasy są skonfigurowane w taki sposób, że nadają pierwszeństwo innym trasom (takim jak połączenie sieci VPN lub inny obwód usługi ExpressRoute) w zamierzonym obwodzie usługi ExpressRoute. 
* Lokalne i platformy Azure węzłów wybranych do monitorowania obwodu usługi ExpressRoute w konfiguracji monitorowania, nie mają łączności ze sobą za pomocą zamierzonego obwodu usługi ExpressRoute. Upewnij się, że wybrano poprawne węzły, które mają łączność ze sobą za pomocą obwodu usługi ExpressRoute, który zamierzasz monitorować.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Podczas konfigurowania monitorowania mojego obwodu usługi ExpressRoute węzły platformy Azure nie są wykrywane.
Może się tak zdarzyć, jeśli węzły platformy Azure są połączone za pośrednictwem programu Operations Manager. Funkcja Monitora usługi ExpressRoute obsługuje tylko te węzły platformy Azure, które są połączone jako agenci bezpośredni.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nie mogę odnajdyć przez obwody usługi ExpressRoute w portalu OMS
Chociaż npm może być używany zarówno z witryny Azure portal, jak i portalu OMS, odnajdowanie obwodów w usłudze Monitora usługi ExpressRoute działa tylko za pośrednictwem witryny Azure portal. Po wykryciu obwodów za pośrednictwem witryny Azure portal, można następnie użyć możliwości w jednym z dwóch portali. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>W obszarze Monitor łączności usług czas reakcji usługi, utrata sieci oraz opóźnienie są wyświetlane jako NA
Może się tak zdarzyć, jeśli jedna lub więcej jest prawdziwa:

* Usługa jest wyłączna.
* Węzeł używany do sprawdzania łączności sieciowej z usługą jest wyłączany.
* Obiekt docelowy wprowadzony w konfiguracji testowej jest niepoprawny.
* Węzeł nie ma żadnej łączności sieciowej.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>W obszarze Monitor łączności usług wyświetlany jest prawidłowy czas odpowiedzi usługi, ale utrata sieci oraz opóźnienie są wyświetlane jako NA
 Może się tak zdarzyć, jeśli jedna lub więcej jest prawdziwa:

* Jeśli węzłem używanym do sprawdzania łączności sieciowej z usługą jest komputer kliencki systemu Windows, usługa docelowa blokuje żądania ICMP lub zapora sieciowa blokuje żądania ICMP pochodzące z węzła.
* Pole wyboru Wykonywanie pomiarów sieciowych jest puste w konfiguracji testowej.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>W obszarze Monitor łączności usług czas odpowiedzi usługi to NA, ale utrata sieci, a także opóźnienie są prawidłowe
Może się tak zdarzyć, jeśli usługa docelowa nie jest aplikacją sieci web, ale test jest skonfigurowany jako test sieci Web. Edytuj konfigurację testu i wybierz typ testu jako Sieć zamiast sieci Web.

## <a name="miscellaneous"></a>Różne

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Czy istnieje wpływ na wydajność węzła używanego do monitorowania?
Proces NPM jest skonfigurowany do zatrzymania, jeśli wykorzystuje więcej niż 5% zasobów procesora CPU hosta. Ma to na celu zapewnienie, że można zachować przy użyciu węzłów dla ich zwykłych obciążeń bez wpływu na wydajność.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Czy NPM edytuje reguły zapory do monitorowania?
NPM tworzy tylko lokalną regułę Zapory systemu Windows w węzłach, na których jest uruchamiany skrypt EnableRules.ps1 Powershell, aby umożliwić agentom tworzenie połączeń TCP ze sobą na określonym porcie. Rozwiązanie nie modyfikuje żadnych reguł zapory sieciowej ani sieciowej grupy zabezpieczeń (NSG).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak sprawdzić kondycję węzłów używanych do monitorowania?
Stan kondycji węzłów używanych do monitorowania można wyświetlić w następującym widoku: Monitor wydajności sieci -> konfiguracja -> węzłów. Jeśli węzeł jest w złej kondycji, można wyświetlić szczegóły błędu i podjąć sugerowaną akcję.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Czy npm raport liczby opóźnień w mikrosekundach?
Npm zaokrągla liczby opóźnień w interfejsie użytkownika i w milisekundach. Te same dane są przechowywane z większą szczegółowością (czasami maksymalnie z czterema miejscami po przecinku).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o Monitorze wydajności sieci, odwołując się do [rozwiązania Monitor wydajności sieci na platformie Azure.](../../azure-monitor/insights/network-performance-monitor.md)
