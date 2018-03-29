---
title: Sieci monitora wydajności rozwiązania na platformie Azure | Dokumentacja firmy Microsoft
description: Monitor wydajności sieci na platformie Azure pomaga monitorować wydajność sieci, w czasie rzeczywistym near, aby wykryć i zlokalizuj wąskich gardeł wydajności sieci.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 847213a5b8aab908d412ecc9aecec984aec1a9ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Sieci monitora wydajności rozwiązania na platformie Azure

![Symbol monitora wydajności sieci](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Monitor wydajności sieci jest sieci opartej na chmurze hybrydowe rozwiązanie monitorujące, które pomaga monitorować wydajność sieci między różnymi punktami w infrastrukturze sieci. Ułatwia on także monitorować łączność sieciową usługi i punkty końcowe aplikacji i monitorowanie wydajności usługi Azure ExpressRoute. 

Monitor wydajności sieci wykrywa, takie jak blackholing ruchu, błędy routingu i problemów, które metody monitorowania z konwencjonalnej sieci nie może wykryć problemy z siecią. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy naruszenia progu dla połączenia sieciowego. Również zapewnia szybkie wykrycie problemów z wydajnością sieci i lokalizuje źródłem problemu konkretnym segmencie sieci lub urządzenia. 

Monitor wydajności sieci oferuje trzy szerokie możliwości: 

* [Monitor wydajności](log-analytics-network-performance-monitor-performance-monitor.md): można monitorować łączność sieciową między wdrożeń chmury oraz lokalizacji lokalnej wielu centrów danych i biur oddziałów i kluczowych aplikacji wielowarstwowej lub mikrousług. Przed użytkownicy skarżą się z Monitora wydajności, można wykryć problemy z siecią.

* [Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-service-endpoint.md): można monitorować łączność od użytkowników do usługi interesujących, określić infrastrukturę znajduje się w ścieżce i określenie miejsca występowania wąskich gardeł sieci. Wiedzieć o awarii przed użytkowników i zobacz dokładnej lokalizacji problemy wzdłuż ścieżki sieciowej. 

    Ta funkcja pomaga wykonywania testów na podstawie HTTP, HTTPS, TCP i ICMP, do monitorowania w niemal w czasie rzeczywistym lub w przeszłości dostępności i czasu odpowiedzi usługi. Może także monitorować udział w utraty pakietów i opóźnienia sieci. Z mapy topologii sieci można odizolować spowolnienie sieci. Można zidentyfikować problem miejsc, pojawiające się na ścieżce sieciowej z węzła do usługi, z danymi czas oczekiwania na każdym przeskoku. Za pomocą wbudowanych testów można monitorować łączność sieciową usługi Office 365 i usługi Dynamics CRM bez żadnych Konfiguracja wstępna. Dzięki tej możliwości można monitorować łączność sieciową z dowolnego punktu końcowego TCP funkcją, takich jak witryny sieci Web, aplikacji SaaS PaaS aplikacji i baz danych.

* [Monitor usługi ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): monitorować wydajność między oddziałach i Azure i na trasie łączności za pośrednictwem usługi Azure ExpressRoute.  

Więcej informacji o różnych funkcjach obsługiwanych przez [monitora wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępna w trybie online.
 
## <a name="supported-regions"></a>Obsługiwane regiony
NPM można monitorować łączność sieci i aplikacji w dowolnej części świecie z obszaru roboczego, który znajduje się w jednym z następujących regionach:
* Europa Zachodnia
* Środkowo-zachodnie stany USA
* Wschodnie stany USA
* Azja Południowo-Wschodnia
* Australia Wschodnią Południowa
* Virginia dla instytucji rządowych USA

Lista obsługiwane regiony dla monitora ExpressRoute jest dostępna w [dokumentacji](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Instalowanie i konfigurowanie

### <a name="install-and-configure-agents"></a>Instalowanie i konfigurowanie agentów 

Należy zainstalować agentów na podstawowy proces [komputery Windows połączyć z usługą Analiza dzienników Azure](log-analytics-windows-agents.md) i [połączenie programu Operations Manager do analizy dzienników](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Gdzie chcesz zainstalować agentów 

* **Monitor wydajności**: Zainstaluj Operations Management Suite agentów na co najmniej jeden węzeł podłączony do poszczególnych podsieci, z którego chcesz monitorować łączność sieciową z innych podsieci.

    Aby monitorować połączenia sieciowego, należy zainstalować agentów na obu punktów końcowych tego łącza. Jeśli nie wiesz o topologii sieci, należy zainstalować agentów na serwerach z krytycznych obciążeń, między którymi chcesz monitorować wydajność sieci. Na przykład jeśli chcesz monitorować połączenie sieciowe między serwerem sieci web a serwerem SQL, należy zainstalować agenta na obu serwerach. Agenci monitorują łączność sieciową (linki) między hostami, nie hosty samodzielnie. 

* **Monitor punktu końcowego usługi**: Zainstaluj agenta Operations Management Suite w każdym węźle, z którego chcesz monitorować łączność sieciową z punktem końcowym usługi. Przykładem jest, jeśli chcesz monitorować łączność usługi Office 365 z etykietą O1, O2 i O3 Lokacje pakietu office. Zainstaluj agenta programu Operations Management Suite na co najmniej jeden węzeł w O1, O2 i O3. 

* **Monitor usługi ExpressRoute**: Zainstaluj co najmniej jednego agenta Operations Management Suite w sieci wirtualnej platformy Azure. Co najmniej jednego agenta należy również zainstalować w podsieci lokalnej, w którym jest połączony za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Konfigurowanie usługi Operations Management Suite agentów do monitorowania 

Monitor wydajności sieci używa transakcji syntetycznych do monitorowania wydajności sieci między agentami źródłowym i docelowym. Można wybrać TCP i ICMP, jako protokół monitorowania w funkcji Monitora wydajności i Monitor punktu końcowego usługi. TCP jest używany dla monitora usługi ExpressRoute. Upewnij się, że zapora zezwala na komunikację między agentami Operations Management Suite służący do monitorowania wybranego protokołu. 

* **Protokół TCP**: wybranie TCP protokołu służy do monitorowania, należy otworzyć port zapory na agentów używanych dla monitora wydajności sieci i monitorowanie usługi ExpressRoute, aby upewnić się, że agenci mogą się ze sobą łączyć. Do otwarcia portu, należy uruchomić skrypt programu PowerShell EnableRules.ps1 bez żadnych parametrów w oknie programu PowerShell z uprawnieniami administracyjnymi.

    Skrypt tworzy kluczy rejestru wymaganych przez to rozwiązanie. Tworzy również reguł zapory systemu Windows, aby umożliwić agentów do utworzenia połączenia TCP ze sobą. Klucze rejestru utworzony przez skrypt Określ, czy dzienniki debugowania i ścieżka do pliku dzienniki dziennika. Skrypt określa również port TCP agent używany do komunikacji. Wartości te klucze są automatycznie ustawiane przez skrypt. Nie należy zmieniać ręcznie tych kluczy. Otworzyć domyślny port to 8084. Można użyć niestandardowego numeru portu zapewniając numer_portu parametru do skryptu. Użyj tego samego portu na wszystkich komputerach, w którym skrypt jest uruchamiany. 

    >[!NOTE]
    > Skrypt konfiguruje tylko zapory systemu Windows lokalnie. Jeśli masz zapory sieciowej, upewnij się, że umożliwia ruch kierowany do portu TCP używanego przez Monitor wydajności sieci.

    >[!NOTE]
    > Nie trzeba uruchomić skrypt programu EnableRules.ps1 PowerShell monitora punktu końcowego usługi.

    

* **Protokół ICMP**: Jeśli wybierzesz ICMP jako protokół do monitorowania, należy włączyć następujące reguły zapory w celu niezawodnie korzystać z protokołu ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Konfigurowanie rozwiązania 

1. Dodaj rozwiązanie sieci monitora wydajności do swojego obszaru roboczego z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Można też użyć procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md). 
2. Otwórz obszar roboczy analizy dzienników i wybierz **omówienie** kafelka. 
3. Wybierz **monitora wydajności sieci** Kafelek z komunikatem *rozwiązanie wymaga dodatkowej konfiguracji*.

   ![Kafelek monitora wydajności sieci](media/log-analytics-network-performance-monitor/npm-config.png)

4. Na **Instalator** strony, zobaczysz opcję, aby zainstalować agentów Operations Management Suite i konfigurowanie agentów do monitorowania w **typowe ustawienia** widoku. Jak wcześniej wyjaśniono, jeśli zainstalowane i skonfigurowane agentów Operations Management Suite, wybierz **Instalator** widok, aby skonfigurować możliwości chcesz użyć. 

   **Monitor wydajności**: Wybierz protokół do użycia dla transakcji syntetycznych w **domyślne** reguły monitora wydajności, a następnie wybierz **Zapisz i Kontynuuj**. To pole wyboru protokołu zawiera jedynie dla reguły domyślnej generowanych przez system. Należy wybrać protokół jawnie w przypadku tworzenia reguły monitora wydajności. Zawsze można przenieść do **domyślne** reguły ustawienia **monitora wydajności** kartę (wydaje się po zakończeniu konfiguracji dnia 0) i zmiany protokołu później. Jeśli nie chcesz rPerfomance tę funkcję można wyłączyć reguły domyślnej z **domyślne** reguły ustawienia **monitora wydajności** kartę.

   ![Widok Monitora wydajności](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor punktu końcowego usługi**: Ta funkcja udostępnia wbudowane testy wstępnie skonfigurowane do monitorowania połączenia sieciowego do usługi Office 365 i Dynamics 365 z agentów. Wybierz usługi Office 365 i Dynamics 365, które mają być monitorowane przez zaznaczenie pola wyboru obok nich. Wybierz agentów, z których chcesz monitorować, wybierz **dodać agentów**. Jeśli nie chcesz używać tej funkcji lub jeśli chcesz później skonfigurować nie wybierz nic i wybierz **Zapisz i Kontynuuj**.

   ![Monitor punktu końcowego usługi widoku](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor usługi ExpressRoute**: Wybierz **odnajdywanie teraz** do odnajdywania wszystkich ExpressRoute prywatnej komunikacji równorzędnych, które są podłączone do sieci wirtualnych w subskrypcji platformy Azure są połączone z tym obszarem roboczym analizy dzienników. 

   >[!NOTE] 
   > Rozwiązanie umożliwia odnalezienie obecnie tylko ExpressRoute prywatnej komunikacji równorzędnych. 

   >[!NOTE] 
   > Tylko są odnajdywane prywatnej komunikacji równorzędnych, które są podłączone do sieci wirtualnych skojarzone z subskrypcją połączone z tym obszarem roboczym analizy dzienników. Jeśli ExpressRoute jest podłączony do sieci wirtualnych poza subskrypcji połączone z tym obszarem roboczym, należy utworzyć obszaru roboczego analizy dzienników w tych subskrypcjach. Aby monitorować tych komunikacji równorzędnych, należy użyć Monitora wydajności sieci.

   ![Widok monitora usługi ExpressRoute](media/log-analytics-network-performance-monitor/npm-express-route.png)

   Po zakończeniu odnajdywania odnalezionych prywatnej komunikacji równorzędnych są wymienione w tabeli. 

   ![Strona konfiguracji monitora wydajności sieci](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
Do monitorowania tych komunikacji równorzędnych początkowo jest w stanie wyłączenia. Wybierz każdego równorzędna, który chcesz monitorować i konfigurowanie monitorowania dla nich w widoku szczegółów po prawej stronie. Wybierz **zapisać** Aby zapisać konfigurację. Aby dowiedzieć się więcej, zobacz artykuł "ExpressRoute Konfigurowanie monitorowania". 

Po zakończeniu pracy Instalatora trwa 30 minut godzinę do wypełniania danych. Gdy rozwiązanie agregują dane z sieci, zostanie wyświetlony komunikat *rozwiązanie wymaga dodatkowej konfiguracji* w Monitorze wydajności sieci **omówienie** kafelka. Po dane są zbierane i indeksowane, **omówienie** kafelka zmiany i informuje o kondycji Twojej sieci w podsumowaniu. Następnie można edytować monitorowania węzłów, na których Operations Management Suite zostali zainstalowani agenci, a także podsieci wykryte za pomocą środowiska.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edytuj ustawienia monitorowania dla podsieci i węzły 

Wszystkie podsieci z co najmniej jeden agent zainstalowany są wyświetlane na **podsieci** kartę na stronie konfiguracji. 


Aby włączyć lub wyłączyć monitorowanie określonej podsieci:

1. Wybierz lub wyczyść pole wyboru obok pozycji **podsieci Identyfikatora**. Następnie upewnij się, że **na użytek monitorowania** jest wybrane lub wyczyszczone, gdzie to właściwe. Można zaznaczyć lub wyczyścić wielu podsieci. Po wyłączeniu podsieciami, które nie są monitorowane i agenci zostaną zaktualizowane, aby zatrzymać polecenie ping innych agentów. 
2. Wybierz węzły, które mają być monitorowane w określonej podsieci. Wybierz podsieć, z listy, a wymagane węzłów przenoszeniu ich do listy, które zawierają węzły niemonitorowane i monitorowane. Opis niestandardowego można dodać do podsieci.
3. Wybierz **zapisać** Aby zapisać konfigurację. 

#### <a name="choose-nodes-to-monitor"></a>Wybierz węzły do monitorowania

Wszystkie węzły, które zainstalowano na nich agenta są wyświetlane na **węzłów** kartę. 

1. Zaznacz lub usuń węzły, które chcesz monitorować lub zatrzymać monitorowanie. 
2. Wybierz **na użytek monitorowania**, lub wyczyść, zależnie od potrzeb. 
3. Wybierz pozycję **Zapisz**. 


Konfigurowanie funkcji, które chcesz:

- [Monitor wydajności](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Szczegóły danych kolekcji
Aby zbierać informacje o utracie i czas oczekiwania, Monitor wydajności sieci używa TCP SYN SYNACK potwierdzenie uzgadnianie pakietów, w przypadku protokołu TCP. Monitor wydajności sieci używa odpowiedzi ECHA ICMP ECHA protokołu ICMP, po wybraniu ICMP jako protokół. Śledzenie trasy jest również używany do pobierania informacji o topologii.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak dane są zbierane dla monitora wydajności sieci.

| Platforma | Bezpośrednie agenta | Agenta programu System Center Operations Manager | Azure Storage | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Komunikaty ECHA ICMP/uzgodnienia TCP co 5 sekund danych wysyłane co 3 minuty |
 

 
W tym rozwiązaniu zastosowano transakcji syntetycznych do oceny kondycji sieci. Agenci programu Operations Management Suite jest zainstalowany w różnych miejscach pakiety TCP exchange sieci lub echa ICMP ze sobą. Czy agenci używa pakiety TCP lub echa protokołu ICMP, zależy od protokołu, który zostanie wybrany do monitorowania. W procesie agentów Dowiedz się obustronne straty czasu i pakietów, jeśli istnieje. Okresowo każdy agent wykonuje także śledzenie trasy do innych agentów można znaleźć w sieci, należy sprawdzić wszystkie różne trasy. Przy użyciu tych danych, agentów można wywnioskować opóźnienia sieci i rysunki utraty pakietów. Testy są powtarzane co pięć sekund. Dane są agregowane dla około 3 minuty przez agentów przed przesłaniem do usługi analizy dzienników.



>[!NOTE]
> Mimo że agentów komunikują się ze sobą często, nie generują one znaczne obciążenie sieci podczas przeprowadzania testów. Agenci polegać tylko na TCP SYN SYNACK potwierdzenie uzgadnianie pakietów do określenia opóźnienia i utratę. Żadne pakiety danych są wymieniane. W trakcie tego procesu agentów komunikują się ze sobą tylko w razie potrzeby. Topologia komunikacja agenta jest optymalizowane w celu zmniejszenie ruchu w sieci.

## <a name="use-the-solution"></a>Użycie rozwiązania 

### <a name="network-performance-monitor-overview-tile"></a>Kafelek Omówienie narzędzia Monitor wydajności sieci 

Po włączeniu rozwiązania monitora wydajności sieci rozwiązania Kafelek na **omówienie** strony zapewnia szybki przegląd kondycji sieci. 

 ![Kafelek Omówienie narzędzia Monitor wydajności sieci](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Pulpit nawigacyjny sieci monitora wydajności 

* **Najważniejsze zdarzenia kondycji sieci**: Ta strona zawiera listę najnowszych zdarzeń, kondycji i alertów w systemie i czasu ponieważ zdarzenia były aktywne. Zdarzenie kondycji lub alert jest generowany wartość metryki wybrany (utraty, czas oczekiwania, czas odpowiedzi lub użycie przepustowości) monitorowania reguły przekracza wartość progową. 

* **Monitor usługi ExpressRoute**: Ta strona przedstawia podsumowanie kondycji dla różnych połączeń komunikacji równorzędnej ExpressRoute rozwiązania monitorów. **Topologii** kafelka jest wyświetlana liczba ścieżek sieciowych za pośrednictwem obwody usługi ExpressRoute, które są monitorowane w sieci. Wybierz ten Kafelek, aby przejść do **topologii** widoku.

* **Monitor punktu końcowego usługi**: Ta strona przedstawia podsumowanie kondycji różnych testów został utworzony. **Topologii** fragmentu zawiera liczbę punktów końcowych, które są monitorowane. Wybierz ten Kafelek, aby przejść do **topologii** widoku.

* **Monitor wydajności**: Ta strona przedstawia podsumowanie kondycji dla **sieci** łącza i **podsieć** łącza, który monitoruje rozwiązania. **Topologii** kafelka jest wyświetlana liczba ścieżek sieciowych, które są monitorowane w sieci. Wybierz ten Kafelek, aby przejść do **topologii** widoku. 

* **Typowe zapytania**: Ta strona zawiera zbiór zapytania wyszukiwania, które pobierają sieci nieprzetworzone dane monitorowania bezpośrednio. Te zapytania jako punkt początkowy służy do tworzenia zapytań niestandardowych raportowania. 

   ![Pulpit nawigacyjny sieci monitora wydajności](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Przechodzenie do głębokość 

Można wybrać różne łącza na pulpicie nawigacyjnym rozwiązania, aby przejść do szczegółów dół głębiej do wszystkie obszary zainteresowań. Na przykład gdy zostanie wyświetlony alert lub łączy sieciowych w złej kondycji, są wyświetlane na pulpicie nawigacyjnym, zaznacz go, aby zbadać dokładnie. Strona zawiera listę wszystkich łączy podsieci dla określonej sieci łącza. Widać utraty, opóźnienia i stan kondycji każdego łącza podsieci. Można szybko znaleźć powoduje występowanie problemów, które łączy podsieci. Wybierz **wyświetlanie łączy węzłów** wyświetlić wszystkich łączy węzłów łącza podsieci w złej kondycji. Następnie możesz wyświetlić poszczególnych łączy węzła do węzła i łącza węzłów w złej kondycji. 

Wybierz **widoku topologii** do wyświetlania topologii przeskoku przeskoku tras między węzły źródłowe i docelowe. Zła trasy są czerwone. Można wyświetlić opóźnienia przekazanych przez każdego przeskoku, dzięki czemu można szybko zidentyfikować problem do określonej części sieci.

 

### <a name="network-state-recorder-control"></a>Stan sieci Rejestrator formantu

Każdy widok wyświetla migawkę kondycji Twojej sieci w określonym punkcie w czasie. Domyślnie wyświetlany jest stan ostatniej. Na pasku w górnej części strony wyświetlany punkt w czasie, dla którego stan jest wyświetlany. Zaznacz, aby wyświetlić migawki kondycji Twojej sieci podczas poprzedniej **akcje**. Możesz także można włączyć lub wyłączyć automatyczne odświeżanie dla dowolnej strony podczas przeglądania najnowszy stan. 

 ![Rejestrator stanu sieci](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend wykresów 

Na każdym poziomie Drąż w dół można wyświetlić trend dotyczy metryki. Może być utrata, czas oczekiwania, czas odpowiedzi lub użycie przepustowości. Aby zmienić interwał czasu trendu, użyj formant czasu w górnej części wykresu. 

Wykresy trendu Pokaż historycznych perspektywy wydajności metryki wydajności. Niektóre problemy z siecią są charakter przejściowy i są trudne do catch analizując bieżącego stanu sieci. Problemy można szybko surface i zniknąć przed każdy powiadomienia, tylko do pojawić się ponownie w późniejszym czasie, w czasie. Ponadto takie przejściowych problemów może być trudne dla administratorów aplikacji. Problemy często wyświetlane jako niewyjaśniony wzrost krótszy czas odpowiedzi aplikacji, nawet jeśli wszystkie składniki aplikacji wyświetlane bezproblemowe działanie. 

Można łatwo wykryć tego rodzaju problemów, analizując wykresu trendu. Problem jest wyświetlany jako nagłym kolekcji w opóźnieniem sieci lub utraty pakietów. Aby zbadać problem, formant Rejestrator stanu sieci aby wyświetlić migawki sieci i topologii dla tego punktu w czasie, gdy wystąpił problem.

 
![Trend wykresów](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapy topologii 

Monitor wydajności sieci przedstawia topologię przeskoku przeskoku tras między źródłowego i docelowego punktu końcowego na mapy topologii interaktywnego. Zaznacz, aby wyświetlić mapę topologii **topologii** kafelka na pulpicie nawigacyjnym rozwiązania. Można także wybrać **widoku topologii** łącza na stronach Przechodzenie do szczegółów. 

Mapy topologii przedstawia, jak wiele tras należą do zakresu od źródłowy i docelowy i co ścieżek zająć pakietów danych. Opóźnienie przekazanych przez każdego przeskoku sieci jest również widoczne. Wszystkie ścieżki, dla których opóźnienie całkowita ścieżka jest powyżej wartości progowej (ustawiana w odpowiadająca im zasada monitorowania) są wyświetlane na czerwono. 

Po wybraniu węzła lub kursor nad nim na mapy topologii, zobaczysz właściwości węzła, takie jak adres IP i nazwy FQDN. Wybierz przeskoków, aby wyświetlić jego adresu IP. Można zidentyfikować przeskoków sieciowych powodującymi opóźnienia, które przyczynia się po raz pierwszy. Aby filtrować określonej trasy, należy użyć filtrów w okienku akcji zwijane. Aby uprościć topologii sieci, Ukryj pośrednie przeskoków za pomocą suwaka w okienku akcji. Można powiększyć lub pomniejszyć mapy topologii za pomocą kółka myszy. 

Topologia pokazano mapy topologii warstwy 3 i nie zawiera urządzenia warstwy 2 i połączeń. 

 
![Mapy topologii](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Wyszukaj analizy dzienników 

Wszystkie dane, które jest narażonych graficznie za pośrednictwem pulpitu nawigacyjnego monitora wydajności sieci i przechodzenia strony jest również dostępna natywnie w [wyszukiwania analizy dzienników](log-analytics-log-search-new.md). Można wykonywać analizy interakcyjnej danych w repozytorium i skorelować danych z różnych źródeł. Możesz również utworzyć niestandardowe alertów i widoków i eksportowanie danych do programu Excel, usłudze Power BI lub możliwe do udostępnienia łącza.  **Typowych kwerend** obszar na pulpicie nawigacyjnym ma niektóre przydatne zapytań, które służy jako punkt początkowy do tworzenia własnych kwerendy i raporty. 

 

## <a name="provide-feedback"></a>Przekazywanie opinii 

* **UserVoice:** możesz zamieścić pomysłów funkcji Monitora wydajności sieci, które chcesz pracować nad. Odwiedź stronę [strony UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Dołącz do naszych kohorty:** NAS interesuje zawsze o nowych klientów, Dołącz do naszych kohorty. Jako część możesz uzyskać wczesny dostęp do nowych funkcji i możliwości, aby pomóc nam ulepszyć monitora wydajności sieci. Jeśli interesuje Cię dołączenie, wypełnij to [szybkie ankiety](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Kolejne kroki 
Dowiedz się więcej o [monitora wydajności](log-analytics-network-performance-monitor-performance-monitor.md), [Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-performance-monitor.md), i [ExpressRoute Monitor](log-analytics-network-performance-monitor-expressroute.md). 
