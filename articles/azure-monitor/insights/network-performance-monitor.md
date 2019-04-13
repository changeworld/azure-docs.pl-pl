---
title: Network Performance Monitor rozwiązanie na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązanie Network Performance Monitor na platformie Azure pomaga monitorować wydajność sieci, niemal w czasie rzeczywistym, aby wykrywać i zlokalizować wąskie gardła wydajności sieci.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ac10f1f1c6336098afc2abd2b8f720cedfb6f579
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548939"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Network Performance Monitor rozwiązanie na platformie Azure

![Symbol monitora wydajności sieci](./media/network-performance-monitor/npm-symbol.png)


Rozwiązanie Network Performance Monitor jest oparte na chmurze hybrydowej sieci rozwiązanie monitorujące, które pomaga monitorować wydajność sieci między różne punkty w infrastrukturze sieci. Ułatwia ono również monitorowanie łączności sieciowej z punktami końcowymi usług i aplikacji oraz monitorowanie wydajności usługi Azure ExpressRoute. 

Rozwiązanie Network Performance Monitor wykrywa problemy sieciowe, takie jak blackholing ruchu, routingu błędy i problemy, które metody monitorowania sieci są konwencjonalne funkcje nie są w stanie wykryć. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Rozwiązanie Network Performance Monitor oferuje trzy szerokie możliwości: 

* [Monitor wydajności](network-performance-monitor-performance-monitor.md): Możesz monitorować łączność sieciową między wdrożeniami w chmurze i lokalizacjami lokalnymi, wiele centrów danych i biur oddziałów i kluczowych dla działalności aplikacji wielowarstwowej lub mikrousług. Za pomocą Monitora wydajności można wykrywać problemy z siecią, zanim użytkownicy zaczną zgłaszać skargi.

* [Monitor łączności usługi](network-performance-monitor-service-connectivity.md): Możesz monitorować łączność użytkowników z usługami interesujące, określić, jaka infrastruktura znajduje się w ścieżce i zidentyfikować, gdzie występują wąskie gardła w sieci. Możesz dowiedzieć się o przerwach w działaniu przed użytkownikami i zobaczyć dokładną lokalizację problemów w ścieżce Twojej sieci. 

    Ta funkcja umożliwia wykonywanie testu w zależności od protokołu HTTP, HTTPS, TCP i ICMP, do monitorowania w czasie rzeczywistym i historycznie dostępność i czas odpowiedzi usługi. Można również monitorować udziału sieci w utracie pakietów i opóźnienia. Za pomocą mapy topologii sieci można izolować spowolnienie sieci. Można zidentyfikować problem punkty, występujących na ścieżce sieciowej z węzła do usługi z danymi opóźnienie każdego przeskoku. Za pomocą wbudowanych testów możesz monitorować łączność sieciową do usługi Office 365 i Dynamics CRM, bez żadnych Konfiguracja wstępna. Dzięki tej możliwości możesz monitorować łączność sieciową do dowolnego punktu końcowego TCP możliwością, takich jak witryny sieci Web, aplikacji SaaS, PaaS aplikacji i baz danych SQL.

* [ExpressRoute Monitor](network-performance-monitor-expressroute.md): Monitoruj łączność przez usługę Azure ExpressRoute między oddziałami firmy i platformą Azure oraz jej wydajność.  

Więcej informacji na temat różnych funkcji obsługiwanych przez [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępna w trybie online.
 
## <a name="supported-regions"></a>Obsługiwane regiony
NPM monitorować łączność między sieciami i aplikacji w dowolnej części świata, z obszaru roboczego, który znajduje się w następujących regionach:
* Europa Zachodnia
* Środkowo-zachodnie stany USA
* Wschodnie stany USA
* Japonia Wschodnia
* Azja Południowo-Wschodnia
* Południowa Australia Wschodnia
* Południowe Zjednoczone Królestwo
* Dla instytucji rządowych USA — Wirginia

Lista obsługiwanych regionów dla Monitor usługi ExpressRoute jest dostępna w [dokumentacji](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Instalowanie i konfigurowanie

### <a name="install-and-configure-agents"></a>Instalacja i konfiguracja agentów 

Użyj podstawowych procesów, aby zainstalować agentów na [Windows łączenie komputerów do usługi Azure Monitor](../platform/agent-windows.md) i [łączenie programu Operations Manager do usługi Azure Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Gdzie można zainstalować agentów 

* **Monitor wydajności**: Zainstaluj agentów usługi Log Analytics na co najmniej jednym węźle połączonym z każdą podsiecią, z którego chcesz monitorować łączność sieciową do innych podsieci.

    Aby monitorować połączenia sieciowego, należy zainstalować agentów na oba punkty końcowe ten link. Jeśli wiesz o topologii sieci, należy zainstalować agentów na serwerach z obciążeń o znaczeniu krytycznym, między którymi chcesz monitorować wydajność sieci. Na przykład jeśli chcesz monitorować połączenie sieciowe między serwerem sieci web oraz serwera z oprogramowaniem SQL, należy zainstalować agenta na obu serwerach. Agenci monitorować łączność sieciową (linki) między hostami, a nie hosty samodzielnie. 

* **Monitor łączności usługi**: Zainstaluj agenta usługi Log Analytics w każdym węźle, z którego chcesz monitorować łączność sieciową z punktem końcowym usługi. Przykładem jest, jeśli chcesz monitorować łączność sieciową do usługi Office 365 z etykietą O1, O2 i O3 Lokacje pakietu office. Zainstaluj agenta usługi Log Analytics na co najmniej jeden węzeł w O1, O2 i O3. 

* **ExpressRoute Monitor**: Zainstaluj co najmniej jednego agenta usługi Log Analytics w usłudze Azure virtual network. Co najmniej jednego agenta należy również zainstalować w podsieci lokalnej, która jest połączona za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurowanie agentów usługi Log Analytics do monitorowania 

Rozwiązanie Network Performance Monitor używa transakcji syntetycznych do monitorowania wydajności sieci między agentami źródłowe i docelowe. Możesz wybrać TCP i ICMP jako protokół monitorowania w funkcji Monitora wydajności i Monitor łączności usługi. Tylko protokoły TCP jest dostępny jako protokół monitorowania dla monitora usługi ExpressRoute. Upewnij się, że zapora zezwala na komunikację między agentów usługi Log Analytics, używany do monitorowania na protokół, który wybierzesz. 

* **Protokół TCP**: Jeśli wybierzesz opcję TCP jako protokół monitorowania, należy otworzyć port zapory na agentów używanych dla rozwiązania Network Performance Monitor i Monitor usługi ExpressRoute, aby upewnić się, że agenci mogą łączyć się ze sobą. Aby otworzyć port, uruchom [EnableRules.ps1](https://aka.ms/npmpowershellscript) skrypt programu PowerShell bez żadnych parametrów w oknie programu PowerShell z uprawnieniami administracyjnymi.

    Skrypt tworzy kluczy rejestru wymaganych przez to rozwiązanie. Tworzy również reguły zapory Windows, aby umożliwić agentów do utworzenia połączenia TCP ze sobą. Klucze rejestru, utworzone przez skrypt Określ, czy do rejestrowania, dzienniki debugowania oraz ścieżkę do plików dzienników. Skrypt definiuje również port TCP agenta, które są używane do komunikacji. Wartości te klucze są automatycznie ustawiane przy użyciu skryptu. Nie należy zmieniać ręcznie tych kluczy. Port domyślnie otwierany jest port 8084. Można użyć port niestandardowy, zapewniając numer_portu parametr do skryptu. Użyj tego samego portu na wszystkich komputerach, w którym skrypt jest uruchamiany. 

    >[!NOTE]
    > Skrypt konfiguruje tylko Windows zapory lokalnie. Jeśli Zapora sieciowa, upewnij się, że umożliwia ruch przeznaczony dla port TCP używany przez rozwiązania Network Performance Monitor.

    >[!NOTE]
    > Nie trzeba uruchomić [EnableRules.ps1](https://aka.ms/npmpowershellscript ) skrypt programu PowerShell do monitorowania łączności usługi.

    

* **Protokół ICMP**: Jeśli wybierzesz ICMP jako protokół monitorowania, należy włączyć następujące reguły zapory w celu niezawodnie korzystać z protokołu ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Skonfiguruj rozwiązanie 

1. Dodaj rozwiązanie Network Performance Monitor do obszaru roboczego z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Możesz też użyć procesu opisanego w [rozwiązań Dodaj usługi Azure Monitor z galerii rozwiązań](../../azure-monitor/insights/solutions.md). 
2. Otwórz obszar roboczy usługi Log Analytics, a następnie wybierz pozycję **Przegląd** kafelka. 
3. Wybierz **rozwiązania Network Performance Monitor** Kafelek z komunikatem *rozwiązanie wymaga dodatkowej konfiguracji*.

   ![Kafelek monitora wydajności sieci](media/network-performance-monitor/npm-config.png)

4. Na **instalacji** stronie zobaczysz opcję, aby zainstalować agentów usługi Log Analytics i konfigurować agentów do monitorowania w **typowe ustawienia** widoku. Jak wyjaśniono wcześniej, jeśli zainstalowano i skonfigurowano agentów usługi Log Analytics wybierz **Instalatora** do skonfigurowania możliwości widoku, którego chcesz użyć. 

   **Monitor wydajności**: Wybierz protokół do użycia na potrzeby transakcji syntetycznych w **domyślne** reguły monitora wydajności, a następnie wybierz **Zapisz i Kontynuuj**. Ten wybór protokołu zawiera tylko dla reguły domyślnej generowanych przez system. Należy wybrać protokół każdorazowo, należy jawnie utworzyć regułę, Monitor wydajności. Zawsze można przenieść do **domyślne** reguła ustawienia **monitora wydajności** kartę (wyświetlany po zakończeniu konfiguracji dnia 0) i Zmień protokół później. Jeśli nie chcesz, aby funkcja monitora wydajności, można wyłączyć reguły domyślnej z **domyślne** reguła ustawienia **monitora wydajności** kartę.

   ![Widok Monitora wydajności](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor łączności usługi**: Ta funkcja udostępnia wbudowane testy wstępnie skonfigurowane, aby monitorować łączność sieciową do usługi Office 365 i Dynamics 365 z agentów. Wybierz usługi Office 365 i Dynamics 365, które mają być monitorowane przez zaznaczenie pól wyboru obok nich. Aby wybrać agentów, z których chcesz monitorować, wybierz **dodawanie agentów**. Jeśli nie chcesz używać tej funkcji lub chcesz później skonfigurować nie wybierz nic i wybierz pozycję **Zapisz i Kontynuuj**.

   ![Monitor łączności usługi widoku](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute Monitor**: Wybierz **odnajdywanie teraz** do odnajdywania wszystkich ExpressRoute prywatnej komunikacji równorzędnej, które są połączone z sieciami wirtualnymi w subskrypcji platformy Azure są połączone z tym obszarem roboczym usługi Log Analytics. 

   ![Widok monitorowania usługi ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Po zakończeniu odnajdywania odnalezionych obwodów i komunikacji równorzędnej są wymienione w tabeli. 

   ![Stronę Konfiguracja Monitora wydajności sieci](media/network-performance-monitor/npm-private-peerings.png)
    
Monitorowanie tych obwodów i komunikacji równorzędnej początkowo jest w stanie wyłączenia. Wybierz wszystkie zasoby, które chcesz monitorować, a następnie skonfigurować monitorowanie dla nich z widoku szczegółów po prawej stronie. Wybierz **Zapisz** Aby zapisać konfigurację. Aby dowiedzieć się więcej, zobacz artykuł "Monitorowanie Konfigurowanie usługi ExpressRoute". 

Po zakończeniu pracy Instalatora trwa 30 minut do godziny danych do wypełnienia. Gdy rozwiązanie agregują dane z Twojej sieci, zostanie wyświetlony komunikat *rozwiązanie wymaga dodatkowej konfiguracji* na Network Performance Monitor **Przegląd** kafelka. Po dane są zbierane i indeksowane, **Przegląd** kafelka zmienia się i informuje Cię o kondycję swojej sieci w podsumowaniu. Następnie można edytować monitorowanie węzłów, na które Log Analytics agenci są zainstalowani, a także podsieci, odnalezione ze środowiska.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edytowanie ustawień monitorowania dla podsieci i węzłów 

Wszystkie podsieci z co najmniej jeden agent zainstalowany są wyświetlane na **podsieci** karty na stronie konfiguracji. 


Aby włączyć lub wyłączyć monitorowanie określonej podsieci:

1. Zaznacz lub wyczyść pole wyboru obok pozycji **identyfikator podsieci**. Następnie upewnij się, że **na użytek monitorowania** jest zaznaczona lub wyczyszczone, odpowiednio. Możesz zaznaczyć lub wyczyścić wiele podsieci. Po wyłączeniu podsieci nie są monitorowane i agenci są aktualizowane, aby zatrzymać wysyłanie polecenia ping innych agentów. 
2. Wybierz węzły, które mają być monitorowane w określonej podsieci. Wybierz podsieć z listy i wymaganych węzłów przenoszeniu ich do listy, które zawierają węzły niemonitorowane i monitorowane. Opis niestandardowej można dodać do podsieci.
3. Wybierz **Zapisz** Aby zapisać konfigurację. 

#### <a name="choose-nodes-to-monitor"></a>Wybierz węzły do monitorowania

Wszystkie węzły, które zainstalowano na nich agenta są wyświetlane na **węzłów** kartę. 

1. Zaznacz lub wyczyść węzły, które chcesz monitorować lub zatrzymywać monitorowanie. 
2. Wybierz **na użytek monitorowania**, lub wyczyść pola wyboru, zgodnie z potrzebami. 
3. Wybierz pozycję **Zapisz**. 


Konfigurowanie funkcji, które mają:

- [Monitor wydajności](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor łączności usługi](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Szczegóły dotyczące zbierania danych
Aby zebrać informacje strat i opóźnień, rozwiązania Network Performance Monitor używa pakietów uzgadnianie TCP SYN SYNACK potwierdzenie, w przypadku wybrania protokołu TCP. Rozwiązanie Network Performance Monitor używa odpowiedzi ECHA ICMP ECHA protokołu ICMP, w przypadku wybrania protokołu ICMP jako protokół. Aby uzyskać informacje o topologii służy także narzędzie śledzenia trasy.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu dane są zbierane dla rozwiązania Network Performance Monitor.

| Platforma | Agent bezpośredni | Agenta programu System Center Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Komunikatów ECHA protokołu ICMP/uzgodnienia TCP co 5 sekund danych wysyłane co 3 minuty |
 

 
Rozwiązanie używa transakcji syntetycznych do oceny kondycji sieci. Agenci analizy dziennika zainstalowany w różnych punktach pakiety TCP exchange sieci lub echa ICMP ze sobą. Czy agenci używać pakietów protokołu TCP lub ICMP Echo jest zależna od protokołu, który wybrany do monitorowania. W procesie agentów Dowiedz się, obustronne utraty czasu i pakietów, jeśli istnieje. Okresowo każdy agent wykonuje również śledzenie trasy, aby inni agenci, aby znaleźć wszystkie różne trasy w sieci, które muszą zostać przetestowane. Przy użyciu tych danych, agentów można wywnioskować, opóźnienie sieci i strat pakietów. Testy są powtarzane co pięć sekund. Dane są agregowane przez około 3 minuty przez agentów, przed przesłaniem do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.



>[!NOTE]
> Mimo że agentów komunikują się ze sobą często, nie generują one znaczne obciążenie sieci podczas przeprowadzania testów. Agentów zależy od tylko pakiety uzgadnianie protokołu TCP SYN SYNACK potwierdzenie ustalenie strat i opóźnień. Nie pakiety danych są wymieniane. W trakcie tego procesu agentów komunikują się ze sobą tylko wtedy, gdy jest to wymagane. Topologia komunikacji agenta jest zoptymalizowana pod kątem ograniczenia ruchu sieciowego.

## <a name="use-the-solution"></a>Korzystanie z odpowiedniego rozwiązania 

### <a name="network-performance-monitor-overview-tile"></a>Kafelek Omówienie narzędzia Monitor wydajności sieci 

Po włączeniu rozwiązania Network Performance Monitor, Kafelek rozwiązania **Przegląd** strona zawiera krótkie podsumowanie kondycji sieci. 

 ![Kafelek Omówienie narzędzia Monitor wydajności sieci](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Pulpit nawigacyjny monitora wydajności sieci 

* **Najważniejsze zdarzenia kondycji sieci**: Ta strona zawiera listę najnowszych zdarzeń, kondycji i alertów w systemie oraz czasu, ponieważ zdarzenia byli aktywni. Zdarzenie kondycji lub alert jest generowany zawsze wtedy, gdy wartość wybranej metryki (utraty, czas oczekiwania, czas odpowiedzi lub wykorzystanie przepustowości) dla reguły monitorowania przekracza wartość progową. 

* **ExpressRoute Monitor**: Ta strona zawiera podsumowania kondycji dla różnych połączeń komunikacji równorzędnej usługi ExpressRoute z rozwiązania monitorów. **Topologii** Kafelek zawiera liczbę ścieżek sieciowych za pośrednictwem obwodów usługi ExpressRoute, które są monitorowane w sieci. Wybierz ten Kafelek, aby przejść do **topologii** widoku.

* **Monitor łączności usługi**: Ta strona zawiera podsumowania kondycji dla różnych testów, które utworzono. **Topologii** Kafelek pokazuje liczbę punktów końcowych, które są monitorowane. Wybierz ten Kafelek, aby przejść do **topologii** widoku.

* **Monitor wydajności**: Ta strona zawiera podsumowania kondycji dla **sieci** łącza i **podsieci** łącza, który monitoruje rozwiązania. **Topologii** Kafelek zawiera liczbę ścieżek sieciowych, które są monitorowane w sieci. Wybierz ten Kafelek, aby przejść do **topologii** widoku. 

* **Typowe zapytania dotyczące**: Ta strona zawiera zestaw zapytań wyszukiwania, które pobierają sieci nieprzetworzone dane monitorowania bezpośrednio. Te zapytania można użyć jako punktu wyjścia do tworzenia zapytań niestandardowych raportowania. 

   ![Pulpit nawigacyjny monitora wydajności sieci](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Przechodzenie do szczegółów dla głębokości 

Można wybrać różne linki na pulpicie nawigacyjnym rozwiązania, aby przejść głębiej do szczegółów dowolnego obszaru zainteresowania. Na przykład gdy zostanie wyświetlony alert lub łącze sieci w złej kondycji, są wyświetlane na pulpicie nawigacyjnym, wybierz go do dalszego badania. Strona zawiera listę wszystkich łączy podsieci dla tego połączenia określonej sieci. Widać, strat, opóźnienia i stanu każdego łącza podsieci. Można szybko znaleźć link podsieci, która powoduje występowanie problemów. Wybierz **Wyświetl linki węzłów** wyświetlić wszystkich łączy węzłów dla łącza podsieci w złej kondycji. Następnie można wyświetlić poszczególnymi łączami między węzłami i skorzystaj z łączy węzłów w złej kondycji. 

Wybierz **wyświetlanie topologii** Aby wyświetlić topologię przeskoku przeskoku tras między węzły źródłowe i docelowe. Zła trasy są wyświetlane w kolorze czerwonym. Można wyświetlić opóźnienie pochodzącego z każdego przeskoku, dzięki czemu można szybko zidentyfikować problem, aby określonej części sieci.

 

### <a name="network-state-recorder-control"></a>Stan sieci rejestratora kontroli

Każdy widok zawiera migawkę kondycję swojej sieci w określonym punkcie czasu. Domyślnie jest wyświetlany stan najnowszej. Na pasku w górnej części strony zawiera punkt w czasie, dla którego stan jest wyświetlany. Zaznacz, aby wyświetlić migawkę kondycję swojej sieci w danym momencie poprzedniego **akcje**. Możesz również można włączyć lub wyłączyć automatyczne odświeżanie dla dowolnej strony podczas wyświetlić najnowszy stan. 

 ![Network State Recorder](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Wykresy trendów 

Na każdym poziomie przechodzenia do szczegółów można wyświetlić trend dotyczy metryki. Może być utrata, czas oczekiwania, czas odpowiedzi lub wykorzystanie przepustowości. Aby zmienić przedział czasowy na potrzeby trend, formant czasu w górnej części wykresu. 

Wykresy trendów Pokaż historycznego punktu widzenia wydajności metryki wydajności. Niektóre problemy z siecią są przejściowy z natury i są trudne do przechwytywania, analizując tylko bieżący stan sieci. Problemy można szybko powierzchni i znikają przed każdy powiadomienia tylko w celu w pojawią się ponownie w późniejszym czasie. Ponadto takie problemy przejściowe może być trudne dla administratorów aplikacji. Problemów często są wyświetlane jako niewyjaśniony wzrost czas odpowiedzi aplikacji, nawet wtedy, gdy wszystkie składniki aplikacji widocznych gwarantującego bezproblemowe działanie. 

Patrząc na wykres trendu, można łatwo wykryć tego rodzaju problemów. Ten problem zostanie wyświetlony jako nagłym skokiem opóźnieniem sieci lub utrata pakietów. Aby zbadać problem, wybierz formant rejestratora stan sieci i Wyświetl migawki sieć i topologię dla tego punktu w czasie, kiedy wystąpił problem.

 
![Wykresy trendów](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapy topologii 

Rozwiązanie Network Performance Monitor przedstawia topologię przeskoku przeskoku trasy między źródłowego i docelowego punktu końcowego na mapie topologii interaktywne. Zaznacz, aby wyświetlić mapę topologii **topologii** Kafelek na pulpicie nawigacyjnym rozwiązania. Możesz również wybrać **wyświetlanie topologii** łącze na stronach Przechodzenie do szczegółów. 

Wyświetla mapy topologii, ile trasy są między miejscem źródłowym i docelowym i co ścieżki zająć pakiety danych. Opóźnienie pochodzącego z każdego przeskoku sieci jest również widoczne. Wszystkie ścieżki, dla których czas oczekiwania całkowita ścieżka jest powyżej wartości progowej (ustawiana w odpowiednią regułę monitorowania) są wyświetlane w kolorze czerwonym. 

Po wybraniu węzła lub umieść kursor nad nią na mapie topologii, zostaną wyświetlone właściwości węzła, takie jak nazwa FQDN i adres IP. Wybierz przeskoków, aby wyświetlić jego adres IP. Czas oczekiwania, który przyczynia się po raz pierwszy można zidentyfikować przeskok problematycznych sieci. Aby filtrować określonej trasy, Użyj filtrów w okienku akcji zwijane. Aby uprościć topologii sieci, ukryć przeskoków pośrednich za pomocą suwaka w okienku akcji. Można powiększyć lub pomniejszyć mapy topologii przy użyciu kółka myszy. 

Topologia wyświetlane na mapie topologii warstwy 3 i nie zawiera warstwy 2, urządzeń i połączeń. 

 
![Mapy topologii](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Dziennik zapytań w usłudze Azure Monitor

Wszystkie dane, które są uwidocznione w formie graficznej za pośrednictwem pulpitu nawigacyjnego rozwiązania Network Performance Monitor i przechodzenie do szczegółów strony jest również dostępna natywnie w [rejestrowania zapytań](../log-query/log-query-overview.md). Można wykonywać interakcyjne analizy danych w repozytorium i korelowanie danych z różnych źródeł. Możesz również utworzyć niestandardowe alerty i widoki i wyeksportować dane do programu Excel, Power BI lub możliwe do udostępnienia łącza.  **Typowe zapytania dotyczące** obszaru na pulpicie nawigacyjnym zawiera niektóre przydatne zapytania, które służą jako punktu wyjścia do tworzenia własnych zapytań i raportów. 

## <a name="alerts"></a>Alerty

Rozwiązanie Network Performance Monitor korzysta z możliwości alertów [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Oznacza to, że wszystkie powiadomienia są zarządzane przy użyciu [grup akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#overview).  

Jeśli jesteś użytkownikiem NPM tworzenia alertu za pomocą usługi Log Analytics: 
1. Zostanie wyświetlony link, do którego nastąpi przekierowanie do witryny Azure portal. Kliknij go, aby uzyskać dostęp do portalu.
2. Kliknij Kafelek rozwiązania Network Performance Monitor. 
3. Przejdź do skonfigurowania.  
4. Wybierz test, aby utworzyć alertu dla i postępuj zgodnie z wymienione poniżej kroki.

Jeśli jesteś użytkownikiem NPM tworzenia alertu za pośrednictwem witryny Azure portal:  
1. Użytkownik może wprowadzić swój adres e-mail, bezpośrednio lub możesz utworzyć alerty w wiadomościach grup akcji.
2. Jeśli zdecydujesz się bezpośrednio wprowadź swój adres e-mail, akcji grupy o nazwie **NPM Email ActionGroup** jest tworzony i identyfikator poczty e-mail zostanie dodany do tej grupy akcji.
3. Jeśli zdecydujesz się użyć grup akcji, należy wybrać grupy akcji utworzone wcześniej. Możesz dowiedzieć się, jak utworzyć grupy akcji [tutaj.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Po pomyślnym utworzeniu alertu, można użyć łącza Zarządzanie alertami zarządzania alertami. 

W przypadku tworzenia alertu, NPM tworzy na podstawie zapytania reguł alertów dzienników w usłudze Azure Monitor. To zapytanie jest wyzwalana co 5 minut, domyślnie. Usługa Azure monitor nie jest opłata w wysokości 250 pierwszego logowania reguły alertów utworzone, a wszystkie reguły alertów powyżej limit 250 reguł alertów dzienników będą naliczane zgodnie [alerty ceny w usłudze Azure Monitor stronę z cennikiem](https://azure.microsoft.com/en-us/pricing/details/monitor/).
Powiadomienia są rozliczane osobno zgodnie [powiadomienia ceny w usłudze Azure Monitor stronę z cennikiem](https://azure.microsoft.com/en-us/pricing/details/monitor/).


## <a name="pricing"></a>Cennik

Informacje na temat cen są dostępne [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Przekazywanie opinii 

* **UserVoice:** Możesz zadać pomysłami dotyczącymi funkcji rozwiązania Network Performance Monitor, które chcesz pracować. Odwiedź stronę [stronie UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Dołącz do naszego kohorty:** Zawsze możemy interesują Cię o nowych klientów, Dołącz do naszego kohorty. Jako jej część możesz uzyskać wczesny dostęp do nowych funkcji i możliwość pomóc w ulepszaniu rozwiązania Network Performance Monitor. Jeśli interesuje Cię dołączania, Wypełnij ten [krótką ankietę](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Kolejne kroki 
Dowiedz się więcej o [monitora wydajności](network-performance-monitor-performance-monitor.md), [Monitor łączności usługi](network-performance-monitor-performance-monitor.md), i [Monitor usługi ExpressRoute](network-performance-monitor-expressroute.md). 
