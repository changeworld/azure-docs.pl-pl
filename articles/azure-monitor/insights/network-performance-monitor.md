---
title: Rozwiązanie Monitor wydajności sieci na platformie Azure | Dokumenty firmy Microsoft
description: Monitor wydajności sieci na platformie Azure ułatwia monitorowanie wydajności sieci w czasie zbliżonym do rzeczywistego w celu wykrywania i lokalizowania wąskich gardeł wydajności sieci.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480882"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Rozwiązanie Monitor wydajności sieci na platformie Azure

![Symbol Monitora wydajności sieci](./media/network-performance-monitor/npm-symbol.png)


Monitor wydajności sieci to oparte na chmurze rozwiązanie do monitorowania sieci hybrydowej, które pomaga monitorować wydajność sieci między różnymi punktami w infrastrukturze sieciowej. Ułatwia ono również monitorowanie łączności sieciowej z punktami końcowymi usług i aplikacji oraz monitorowanie wydajności usługi Azure ExpressRoute. 

Monitor wydajności sieci wykrywa problemy z siecią, takie jak blackholing ruchu, błędy routingu i problemy, których konwencjonalne metody monitorowania sieci nie są w stanie wykryć. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Monitor wydajności sieci oferuje trzy szerokie możliwości: 

* [Monitor wydajności:](network-performance-monitor-performance-monitor.md)Można monitorować łączność sieciową między wdrożeniami w chmurze i lokalizacjami lokalnymi, wieloma centrami danych i oddziałami oraz aplikacjami o znaczeniu wielowarstwowym lub mikrousługami o znaczeniu krytycznym. Za pomocą Monitora wydajności można wykryć problemy z siecią, zanim użytkownicy zgodą.

* [Monitor łączności](network-performance-monitor-service-connectivity.md)usługi: Można monitorować łączność od użytkowników do usług, na których Ci zależy, określić, jaka infrastruktura znajduje się na ścieżce i określić, gdzie występują wąskie gardła sieci. Możesz wiedzieć o awariach przed użytkownikami i zobaczyć dokładną lokalizację problemów wzdłuż ścieżki sieciowej. 

    Ta funkcja ułatwia wykonywanie testów opartych na protokołach HTTP, HTTPS, TCP i ICMP w celu monitorowania w czasie zbliżonym do rzeczywistego lub historycznie dostępności i czasu odpowiedzi usługi. Można również monitorować udział sieci w utracie pakietów i opóźnieniu. Dzięki mapie topologii sieci można izolować spowolnienia sieci. Można zidentyfikować miejsca problemów, które występują wzdłuż ścieżki sieciowej z węzła do usługi, z danymi opóźnienia na każdym przeskoku. Dzięki wbudowanym testom można monitorować łączność sieciową z pakietem Office 365 i programem Dynamics CRM bez wstępnej konfiguracji. Dzięki tej funkcji można monitorować łączność sieciową z dowolnym punktem końcowym obsługującym protokół TCP, takim jak witryny sieci Web, aplikacje SaaS, aplikacje PaaS i bazy danych SQL.

* [Monitor usługi ExpressRoute:](network-performance-monitor-expressroute.md)monitorowanie kompleksowej łączności i wydajności między oddziałami firmy a platformą Azure za pomocą usługi Azure ExpressRoute.  

Więcej informacji na temat różnych możliwości obsługiwanych przez [Monitor wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępny w trybie online.
 
## <a name="supported-regions"></a> Obsługiwane regiony
Serwer NPM może monitorować łączność między sieciami i aplikacjami w dowolnej części świata, z obszaru roboczego hostowanego w jednym z następujących regionów:
* Europa Północna
* Europa Zachodnia
* Francja Środkowa
* Kanada Środkowa
* Zachodnie stany USA
* Zachodnio-środkowe stany USA
* Północno-środkowe stany USA
* Południowo-środkowe stany USA
* Środkowe stany USA
* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA 2
* Japonia Wschodnia
* Azja Południowo-Wschodnia
* Australia Południowo-Wschodnia
* Australia Środkowa
* Australia Wschodnia
* Południowa Wielka Brytania
* Azja Wschodnia
* Korea Środkowa
* Indie Środkowe
* Rząd STANÓW Zjednoczonych Virginia
* Chiny Wschodnie 2


Lista obsługiwanych regionów dla monitora usługi ExpressRoute jest dostępna w [dokumentacji](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Konfigurowanie i konfigurowanie

### <a name="install-and-configure-agents"></a>Instalacja i konfiguracja agentów 

Podstawowych procesów można instalować agentów w [usłudze Podłącz komputery z systemem Windows do usługi Azure Monitor](../platform/agent-windows.md) i [połącz program Operations Manager z usługą Azure Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Gdzie zainstalować agentów 

* **Monitor wydajności:** Zainstaluj agentów analizy dzienników w co najmniej jednym węźle podłączonym do każdej podsieci, z której chcesz monitorować łączność sieciową z innymi podsieciami.

    Aby monitorować łącze sieciowe, zainstaluj agentów w obu punktach końcowych tego łącza. Jeśli nie masz pewności co do topologii sieci, zainstaluj agentów na serwerach z krytycznymi obciążeniami, między którymi chcesz monitorować wydajność sieci. Jeśli na przykład chcesz monitorować połączenie sieciowe między serwerem sieci web a serwerem z uruchomionym programem SQL, zainstaluj agenta na obu serwerach. Agenci monitorują łączność sieciową (łącza) między hostami, a nie samymi hostami. 

* **Monitor łączności usługi:** Zainstaluj agenta analizy dzienników w każdym węźle, z którego chcesz monitorować łączność sieciową z punktem końcowym usługi. Przykładem jest, jeśli chcesz monitorować łączność sieciową z usługi Office 365 z witryn biurowych oznaczonych jako O1, O2 i O3. Zainstaluj agenta usługi Log Analytics w co najmniej jednym węźle w systemach O1, O2 i O3. 

* **Monitor usługi ExpressRoute:** Zainstaluj co najmniej jednego agenta usługi Log Analytics w sieci wirtualnej platformy Azure. Zainstaluj również co najmniej jednego agenta w lokalnej podsieci, która jest połączona za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Konfigurowanie agentów usługi Log Analytics do monitorowania 

Monitor wydajności sieci używa transakcji syntetycznych do monitorowania wydajności sieci między agentami źródłowymi i docelowymi. Można wybrać między TCP i ICMP jako protokół monitorowania w monitorze wydajności i monitorze łączności usług. Tylko protokół TCP jest dostępny jako protokół monitorowania monitora usługi ExpressRoute. Upewnij się, że zapora umożliwia komunikację między agentami usługi Log Analytics używanymi do monitorowania wybranego protokołu. 

* **Protokół TCP:** Jeśli jako protokół monitorowania wybierzesz protokół TCP, otwórz port zapory na agentach używanych w Monitorze wydajności sieci i monitorze usługi ExpressRoute, aby upewnić się, że agenci mogą się ze sobą łączyć. Aby otworzyć port, uruchom skrypt [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell bez żadnych parametrów w oknie programu PowerShell z uprawnieniami administracyjnymi.

    Skrypt tworzy klucze rejestru wymagane przez rozwiązanie. Tworzy również reguły Zapory systemu Windows, aby umożliwić agentom tworzenie połączeń TCP ze sobą. Klucze rejestru utworzone przez skrypt określają, czy dzienniki debugowania i ścieżka dla pliku dzienników. Skrypt definiuje również port TCP agenta używany do komunikacji. Wartości dla tych kluczy są automatycznie ustawiane przez skrypt. Nie zmieniaj ręcznie tych klawiszy. Port otwarty domyślnie to 8084. Można użyć portu niestandardowego, podając parametr portNumber do skryptu. Użyj tego samego portu na wszystkich komputerach, na których jest uruchamiany skrypt. 

    >[!NOTE]
    > Skrypt konfiguruje tylko Zaporę systemu Windows lokalnie. Jeśli masz zaporę sieciową, upewnij się, że zezwala ona na ruch przeznaczony dla portu TCP używanego przez Monitor wydajności sieci.

    >[!NOTE]
    > Nie trzeba uruchamiać skryptu [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell dla Monitora łączności usługi.

    

* **Protokół ICMP**: Jeśli wybierzesz protokół ICMP jako protokół monitorowania, włącz następujące reguły zapory, aby niezawodnie korzystać z protokołu ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurowanie rozwiązania 

1. Dodaj rozwiązanie Monitor wydajności sieci do obszaru roboczego z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Można również użyć procesu opisanego w [add Azure Monitor solutions from the Solutions Gallery](../../azure-monitor/insights/solutions.md). 
2. Otwórz obszar roboczy usługi Log Analytics i wybierz kafelek **Przegląd.** 
3. Wybierz kafelek **Monitor wydajności sieci** z komunikatem Rozwiązanie wymaga *dodatkowej konfiguracji*.

   ![Kafelek Monitor wydajności sieci](media/network-performance-monitor/npm-config.png)

4. Na stronie **Ustawienia** zostanie wyświetlona opcja zainstalowania agentów usługi Log Analytics i skonfigurowania agentów do monitorowania w widoku **Ustawienia wspólne.** Jak wcześniej wyjaśniono, jeśli zainstalowano i skonfigurowano agentów usługi Log Analytics, wybierz widok **Instalatora,** aby skonfigurować możliwości, których chcesz użyć. 

   **Monitor wydajności:** Wybierz protokół używany dla transakcji syntetycznych w regule **Domyślnego** Monitora wydajności i wybierz pozycję **Zapisz & Kontynuuj**. Ten wybór protokołu dotyczy tylko reguły domyślnej wygenerowanej przez system. Należy wybrać protokół przy każdym utworzeniu reguły Monitora wydajności jawnie. Zawsze można przejść do **domyślnych** ustawień reguły na karcie **Monitor wydajności** (pojawia się po zakończeniu konfiguracji dnia 0) i później zmienić protokół. Jeśli nie chcesz mieć możliwości Monitora wydajności, możesz wyłączyć regułę **domyślną** z domyślnych ustawień reguły na karcie **Monitor wydajności.**

   ![Widok Monitora wydajności](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor łączności**usług: Ta funkcja zapewnia wbudowane wstępnie skonfigurowane testy monitorowania łączności sieciowej z usługą Office 365 i Dynamics 365 od agentów. Wybierz usługi Office 365 i Dynamics 365, które chcesz monitorować, zaznaczając pola wyboru obok nich. Aby wybrać agentów, z których chcesz monitorować, wybierz pozycję **Dodaj agentów**. Jeśli nie chcesz korzystać z tej funkcji lub chcesz ją później skonfigurować, nie wybieraj niczego i wybierz pozycję **Zapisz & Kontynuuj**.

   ![Widok Monitor łączności usługi](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor usługi ExpressRoute:** Wybierz **odnajduj teraz,** aby odkryć wszystkie prywatne komunikacji równorzędnej usługi ExpressRoute, które są połączone z sieciami wirtualnymi w ramach subskrypcji platformy Azure połączonych z tym obszarem roboczym usługi Log Analytics. 

   ![Widok monitora usługi ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Po zakończeniu odnajdywania odnalezione obwody i komunikacji równorzędnej są wymienione w tabeli. 

   ![Strona Konfiguracji Monitora wydajności sieci](media/network-performance-monitor/npm-private-peerings.png)
    
Monitorowanie dla tych obwodów i komunikacji równorzędnej jest początkowo w stanie wyłączonym. Wybierz każdy zasób, który chcesz monitorować, i skonfiguruj monitorowanie dla nich z widoku szczegółów po prawej stronie. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. Aby dowiedzieć się więcej, zobacz artykuł "Konfigurowanie monitorowania usługi ExpressRoute". 

Po zakończeniu instalacji wypełnianie danych trwa od 30 minut do godziny. Podczas gdy rozwiązanie agreguje dane z sieci, zostanie wyświetlony komunikat *Rozwiązanie wymaga dodatkowej konfiguracji* na kafelka Przegląd **wydajności** sieci. Po zebraniu i zindeksowaniu danych kafelek **Przegląd** zmienia się i informuje o kondycji sieci w podsumowaniu. Następnie można edytować monitorowanie węzłów, w których są zainstalowane agenci usługi Log Analytics, a także podsieci wykryte w środowisku.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edytowanie ustawień monitorowania podsieci i węzłów 

Wszystkie podsieci z zainstalowanym co najmniej jednym agentem są wyświetlane na karcie **Podsieci** na stronie konfiguracji. 


Aby włączyć lub wyłączyć monitorowanie poszczególnych podsieci:

1. Zaznacz lub wyczyść pole wyboru obok **identyfikatora podsieci**. Następnie upewnij się, że **opcja Użyj do monitorowania** jest zaznaczona lub wyczyszczona, stosownie do przypadku. Można wybrać lub wyczyścić wiele podsieci. Po wyłączeniu podsieci nie są monitorowane, a agenci są aktualizowani, aby zatrzymać pingowanie innych agentów. 
2. Wybierz węzły, które mają być monitorowane w określonej podsieci. Wybierz podsieć z listy i przenieś wymagane węzły między listami zawierającymi niemonitorowane i monitorowane węzły. Do podsieci można dodać opis niestandardowy.
3. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. 

#### <a name="choose-nodes-to-monitor"></a>Wybieranie węzłów do monitorowania

Wszystkie węzły, które mają agenta zainstalowanego na nich są wymienione na karcie **Węzły.** 

1. Wybierz lub wyczyść węzły, które chcesz monitorować lub zatrzymać monitorowanie. 
2. Wybierz **opcję Użyj do monitorowania**lub wyczyść go, stosownie do przypadku. 
3. Wybierz **pozycję Zapisz**. 


Skonfiguruj żądane funkcje:

- [Monitorowanie wydajności](network-performance-monitor-performance-monitor.md#configuration)
- [Monitorowanie łączności usług](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Szczegóły gromadzenia danych
Aby zbierać informacje o utracie i opóźnieniach, Monitor wydajności sieci używa pakietów uzgadniania TCP SYN-SYNACK-ACK po wybraniu protokołu TCP. Monitor wydajności sieci używa ICMP ECHO ICMP ECHO REPLY po wybraniu protokołu ICMP. Trasa śledzenia jest również używana do uzyskania informacji topologii.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych dla Monitora wydajności sieci.

| Platforma | Agent bezpośredni | Agent programu Operations Manager centrum systemu | Azure Storage | Wymagany jest program Operations Manager? | Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Komunikaty TCP handshakes/ICMP ECHO co 5 sekund, dane wysyłane co 3 minuty |
 

 
Rozwiązanie wykorzystuje transakcje syntetyczne do oceny kondycji sieci. Agenci log Analytics zainstalowane w różnych punktach w sieci wymiany pakietów TCP lub ICMP Echo ze sobą. To, czy agenci używają pakietów TCP, czy ICMP Echo, zależy od protokołu wybranego do monitorowania. W procesie, agenci uczą się czasu w obie strony i utraty pakietów, jeśli w ogóle. Okresowo każdy agent wykonuje również trasę śledzenia do innych agentów, aby znaleźć wszystkie różne trasy w sieci, które muszą być testowane. Korzystając z tych danych, agenci mogą wywnić dane dotyczące opóźnienia sieci i utraty pakietów. Testy są powtarzane co pięć sekund. Dane są agregowane przez około trzy minuty przez agentów, zanim zostanie przekazana do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.



>[!NOTE]
> Chociaż agenci komunikują się ze sobą często, nie generują znaczącego ruchu sieciowego podczas przeprowadzania testów. Agenci polegają tylko na pakietach uzgadniania TCP SYN-SYNACK-ACK w celu określenia utraty i opóźnienia. Nie są wymieniane żadne pakiety danych. Podczas tego procesu agenci komunikują się ze sobą tylko wtedy, gdy jest to potrzebne. Topologia komunikacji agenta jest zoptymalizowana w celu zmniejszenia ruchu sieciowego.

## <a name="use-the-solution"></a>Użyj rozwiązania 

### <a name="network-performance-monitor-overview-tile"></a>Kafelek Przegląd wydajności sieci 

Po włączeniu rozwiązania Monitor wydajności sieci kafelek rozwiązania na stronie **Przegląd** zawiera szybkie omówienie kondycji sieci. 

 ![Kafelek Przegląd wydajności sieci](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Pulpit nawigacyjny Monitora wydajności sieci 

* **Najważniejsze zdarzenia kondycji sieci:** Ta strona zawiera listę najnowszych zdarzeń kondycji i alertów w systemie oraz czas od zdarzenia były aktywne. Zdarzenie kondycji lub alert jest generowany, gdy wartość wybranej metryki (strata, opóźnienie, czas odpowiedzi lub wykorzystanie przepustowości) dla reguły monitorowania przekracza próg. 

* **Monitor usługi ExpressRoute:** Ta strona zawiera podsumowania kondycji dla różnych połączeń równorzędnych usługi ExpressRoute monitorów rozwiązania. Kafelek **Topologia** pokazuje liczbę ścieżek sieciowych za pośrednictwem obwodów usługi ExpressRoute, które są monitorowane w sieci. Wybierz ten kafelek, aby przejść do widoku **Topologia.**

* **Monitor łączności usługi:** Ta strona zawiera podsumowania kondycji dla różnych testów, które zostały utworzone. **Kafelek Topologia** pokazuje liczbę punktów końcowych, które są monitorowane. Wybierz ten kafelek, aby przejść do widoku **Topologia.**

* **Monitor wydajności:** Ta strona zawiera podsumowania kondycji dla łączy **sieciowych** i łączy **podsieci,** które monitoruje rozwiązanie. Kafelek **Topologia** pokazuje liczbę ścieżek sieciowych monitorowanych w sieci. Wybierz ten kafelek, aby przejść do widoku **Topologia.** 

* **Typowe zapytania:** Ta strona zawiera zestaw zapytań wyszukiwania, które pobierają dane monitorowania nieprzetworzonej sieci bezpośrednio. Za pomocą tych zapytań można tworzyć własne zapytania do niestandardowych raportów. 

   ![Pulpit nawigacyjny Monitora wydajności sieci](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Drąż w dół, aby uzyskać głębokość 

Można wybrać różne łącza na pulpicie nawigacyjnym rozwiązania, aby przejść głębiej do dowolnego obszaru zainteresowania. Na przykład po wyświetleniu alertu lub w złej kondycji sieci łącze pojawiają się na pulpicie nawigacyjnym, wybierz go do zbadania dalej. Strona zawiera listę wszystkich łączy podsieci dla określonego łącza sieciowego. Można zobaczyć utratę, opóźnienie i stan kondycji każdego łącza podsieci. Możesz szybko dowiedzieć się, które łącze podsieci powoduje problemy. Wybierz **pozycję Wyświetl łącza węzłów,** aby wyświetlić wszystkie łącza węzłów dla łącza w złej kondycji podsieci. Następnie można wyświetlić poszczególne łącza węzeł do węzła i znaleźć łącza węzła w złej kondycji. 

Wybierz **opcję Wyświetl topologię,** aby wyświetlić topologię przeskoku po przeskoku tras między węzłami źródłowymi i docelowymi. Niezdrowe trasy są wyświetlane na czerwono. Można wyświetlić opóźnienie wniesione przez każdy przeskok, dzięki czemu można szybko zidentyfikować problem do określonej części sieci.

 

### <a name="network-state-recorder-control"></a>Kontrolka rejestratora stanu sieci

Każdy widok wyświetla migawkę kondycji sieci w określonym momencie. Domyślnie wyświetlany jest najnowszy stan. Pasek w górnej części strony pokazuje punkt w czasie, dla którego stan jest wyświetlany. Aby wyświetlić migawkę kondycji sieci w poprzednim czasie, wybierz pozycję **Akcje**. Można również włączyć lub wyłączyć automatyczne odświeżanie dla dowolnej strony podczas wyświetlania najnowszego stanu. 

 ![Rejestrator stanu sieci](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Wykresy trendów 

Na każdym poziomie, który można przejść do szczegółów, można zobaczyć trend odpowiedniego metryki. Może to być utrata, opóźnienie, czas odpowiedzi lub wykorzystanie przepustowości. Aby zmienić przedział czasu dla trendu, użyj formantu czasu u góry wykresu. 

Wykresy trendu pokazują historyczną perspektywę wydajności metryki wydajności. Niektóre problemy z siecią mają charakter przejściowy i są trudne do złapania, patrząc tylko na aktualny stan sieci. Problemy mogą pojawić się szybko i znikają, zanim ktokolwiek zauważy, tylko po to, aby pojawić się ponownie w późniejszym czasie. Takie przejściowe problemy mogą być również trudne dla administratorów aplikacji. Problemy często pojawiają się jako niewyjaśnione zwiększenie czasu odpowiedzi aplikacji, nawet wtedy, gdy wszystkie składniki aplikacji wydają się działać płynnie. 

Można łatwo wykryć tego rodzaju problemy, patrząc na wykres trendu. Problem pojawia się jako nagły skok opóźnienia sieci lub utraty pakietów. Aby zbadać problem, użyj kontrolki Rejestrator stanu sieci, aby wyświetlić migawkę sieci i topologię dla tego punktu w czasie, gdy wystąpił problem.

 
![Wykresy trendów](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa topologii 

Monitor wydajności sieci pokazuje topologię przeskoku po przeskoku tras między źródłowym i docelowym punktem końcowym na interaktywnej mapie topologii. Aby wyświetlić mapę topologii, wybierz kafelek **Topologia** na pulpicie nawigacyjnym rozwiązania. Można również wybrać łącze **Wyświetl topologię** na stronach przechodzenia do szczegółów. 

Mapa topologii pokazuje, ile tras znajduje się między źródłem a miejscem docelowym oraz jakie ścieżki prowadzą pakiety danych. Opóźnienie współtworzone przez każdy przeskok sieciowy jest również widoczne. Wszystkie ścieżki, dla których całkowite opóźnienie ścieżki jest powyżej progu (ustawionego w odpowiedniej regule monitorowania) są wyświetlane na czerwono. 

Po wybraniu węzła lub najechaniu kursorem na niego na mapie topologii zobaczysz właściwości węzła, takie jak FQDN i adres IP. Wybierz przeskok, aby wyświetlić jego adres IP. Można zidentyfikować kłopotliwe przeskok sieciowy, zauważając opóźnienie, które przyczynia się. Aby filtrować określone trasy, użyj filtrów w okienku akcji zwijane. Aby uprościć topologie sieci, ukryj przeskoki pośrednie za pomocą suwaka w okienku akcji. Za pomocą kółka myszy można powiększać lub pomniejszać mapę topologii. 

Topologia pokazana na mapie jest topologią warstwy 3 i nie zawiera urządzeń i połączeń warstwy 2. 

 
![Mapa topologii](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Rejestrowanie zapytań w usłudze Azure Monitor

Wszystkie dane, które są udostępniane graficznie za pośrednictwem pulpitu nawigacyjnego Monitora wydajności sieci i stron przechodzenia do szczegółów, są również dostępne natywnie w [zapytaniach dziennika.](../log-query/log-query-overview.md) Można wykonać interaktywną analizę danych w repozytorium i skorelować dane z różnych źródeł. Można również tworzyć niestandardowe alerty i widoki oraz eksportować dane do programu Excel, usługi Power BI lub łącza współużytkowane. Obszar Typowe zapytania na **pulpicie** nawigacyjnym ma kilka przydatnych zapytań, których można użyć jako punktu wyjścia do tworzenia własnych zapytań i raportów. 

## <a name="alerts"></a>Alerty

Monitor wydajności sieci korzysta z funkcji alertów [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Oznacza to, że wszystkie powiadomienia są zarządzane za pomocą [grup akcji](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Jeśli użytkownik NPM tworzy alert za pośrednictwem usługi Log Analytics: 
1. Zostanie wyświetlenie łącza, które przekieruje Cię do witryny Azure portal. Kliknij go, aby uzyskać dostęp do portalu.
2. Kliknij kafelek rozwiązania Monitor wydajności sieci. 
3. Przejdź do pozycji Konfiguruj.  
4. Wybierz test, na którym chcesz utworzyć alert, i wykonaj poniższe kroki.

Jeśli użytkownik NPM tworzy alert za pośrednictwem witryny Azure portal:  
1. Możesz wprowadzić adres e-mail bezpośrednio lub utworzyć alerty za pośrednictwem grup akcji.
2. Jeśli zdecydujesz się wprowadzić wiadomość e-mail bezpośrednio, zostanie utworzona grupa akcji o nazwie **NPM Email ActionGroup** i identyfikator wiadomości e-mail zostanie dodany do tej grupy akcji.
3. Jeśli zdecydujesz się użyć grup akcji, musisz wybrać wcześniej utworzoną grupę akcji. Tutaj dowiesz się, jak utworzyć grupę [akcji.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Po pomyślnym utworzeniu alertu można użyć łącza Zarządzaj alertami, aby zarządzać alertami. 

Za każdym razem, gdy tworzysz alert, npm tworzy regułę alertów dziennika opartych na kwerendach w usłudze Azure Monitor. Ta kwerenda jest domyślnie wyzwalana co 5 minut. Usługa Azure monitor nie pobiera opłat za pierwsze utworzone reguły alertów dziennika 250, a wszystkie reguły alertów powyżej limitu reguł alertów dziennika 250 będą naliczane zgodnie z [cenami alertów na stronie cenowej usługi Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)
Powiadomienia są naliczane oddzielnie zgodnie z [cenami powiadomień na stronie cenowej Usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Cennik

Informacje o cenach są dostępne [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Przekazywanie opinii 

* **UserVoice:** Możesz publikować swoje pomysły na funkcje Monitora wydajności sieci, nad którymi chcesz, abyśmy pracowali. Odwiedź [stronę UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Dołącz do naszej kohorty:** Zawsze jesteśmy zainteresowani dołączeniem nowych klientów do naszej kohorty. W ramach tego otrzymujesz wczesny dostęp do nowych funkcji i możliwość ulepszenia Monitora wydajności sieci. Jeśli chcesz dołączyć, wypełnij tę [szybką ankietę](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Następne kroki 
Dowiedz się więcej o [Monitorze wydajności,](network-performance-monitor-performance-monitor.md) [Monitorze łączności usług](network-performance-monitor-performance-monitor.md)i [Monitorze usługi ExpressRoute](network-performance-monitor-expressroute.md). 
