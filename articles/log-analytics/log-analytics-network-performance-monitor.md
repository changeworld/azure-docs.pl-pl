---
title: Sieci monitora wydajności rozwiązania na platformie Azure | Dokumentacja firmy Microsoft
description: Monitor wydajności na platformie Azure sieci pomaga monitorować wydajność Twojej sieci w pobliżu rzeczywistych czas do wykrywania i zlokalizuj wąskich gardeł wydajności sieci.
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
ms.openlocfilehash: 90fd9b09f90d5b209e0eb8cc6996ef7fb1e7e0b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Sieci monitora wydajności rozwiązania na platformie Azure

![Symbol monitora wydajności sieci](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Sieci oparte na chmurze hybrydowe rozwiązanie monitorujące, które pomaga monitorować wydajność sieci między różnymi punktami w infrastrukturze sieci, łączności sieciowej monitor punkty końcowe aplikacji/usługi jest sieci monitora wydajności (NPM) i Monitorowanie wydajności z usługi Azure ExpressRoute.  

NPM wykrywa, takie jak blackholing ruchu, błędy routingu i problemów, które metody monitorowania z konwencjonalnej sieci nie są w stanie wykryć problemy z siecią. Rozwiązanie generuje alerty, powiadamia o progu naruszenia przez łącze sieci zapewnia szybkie wykrycie problemów z wydajnością sieci i lokalizuje źródłem problemu konkretnym segmencie sieci lub urządzenia. 

NPM oferuje trzy szerokie możliwości: 

[Monitor wydajności](log-analytics-network-performance-monitor-performance-monitor.md): Monitor łączność sieciową między lokalizacje wdrożeń lokalnych i w chmurze, wielu centrów danych i biur oddziałów, misji wielowarstwowe aplikacje/micro-obsługi usług krytycznych. Zanim użytkownicy skarżą się z Monitora wydajności można wykryć problemy z siecią.  

[Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-service-endpoint.md): można monitorować łączność z użytkownikom usług interesujących, określić, jakie infrastruktury znajduje się w ścieżce i sieci występują wąskich gardeł. Informacje o awarii przed użytkowników i zobacz dokładnej lokalizacji problemów wzdłuż ścieżki sieciowej. 

Funkcja ta ułatwia wykonywanie http, HTTPS, TCP i ICMP na podstawie testów monitorowania w niemal w czasie rzeczywistym lub w przeszłości dostępności i czas odpowiedzi usługi i udział utraty pakietów i opóźnienie sieci. Z mapy topologii sieci można odizolować spowolnienie sieci, identyfikując miejsc problem, pojawiające się na ścieżce sieciowej z węzła do usługi, z danymi czas oczekiwania na każdym przeskoku. Testy wbudowane monitorowanie połączenie sieciowe z usługi Office 365 i Dynamics CRM bez przeprowadzania wstępnej konfiguracji. Dzięki tej możliwości, można monitorować łączność sieciową takie jak witryny sieci Web, SaaS, PaaS aplikacje, bazy danych SQL, obsługuje punktu końcowego żadnych TCP itp.  

[Monitor usługi ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): monitorować wydajność między oddziałach i Azure i na trasie łączności za pośrednictwem usługi Azure ExpressRoute.  

Więcej informacji o różnych funkcjach obsługiwanych przez [monitora wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) jest dostępna w trybie online.
 

## <a name="set-up-and-configure"></a>Instalowanie i konfigurowanie

### <a name="install-and-configure-agents"></a>Instalowanie i konfigurowanie agentów 

Należy zainstalować agentów na podstawowy proces [połączyć komputery do analizy dzienników](log-analytics-windows-agents.md) i [połączenie programu Operations Manager do analizy dzienników](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Gdzie chcesz zainstalować agentów 

**Monitor wydajności:** zainstalować agentów OMS na co najmniej jeden węzeł podłączony do poszczególnych podsieci, z którego chcesz monitorować łączność sieciową z innych podsieci.  

Aby monitorować połączenia sieciowego, należy zainstalować agentów na oba punkty końcowe tego łącza.  Jeśli nie wiesz o topologii sieci, należy zainstalować agentów na serwerach z krytycznych obciążeń, między którymi chcesz monitorować wydajność sieci. Na przykład jeśli chcesz monitorować połączenie sieciowe między serwerem sieci Web a serwerem SQL, należy zainstalować agenta na obu serwerach. Agenci monitorują łączności sieciowej (linki) między hostami nie hosty samodzielnie. 

**Monitor punktu końcowego usługi:** zainstalować agenta pakietu OMS na każdym węźle, z którego chcesz monitorować łączność sieciową z punktem końcowym usługi. Na przykład jeśli chcesz monitorować łączność sieciową do usługi Office 365 w witrynie office O1 O2 i O3, następnie zainstalować agenta pakietu OMS na co najmniej jeden węzeł w O1, O2 i O3. 

**Monitor usługi ExpressRoute:** zainstalować co najmniej jeden agent pakietu OMS w sieci wirtualnej platformy Azure i co najmniej jednego agenta w podsieci lokalnej, w połączonej za pomocą komunikacji równorzędnej ExpressRoute prywatnych.  

### <a name="configure-oms-agents-for-monitoring"></a>Konfigurowanie agentów OMS do monitorowania  

NPM używa transakcji syntetycznych, aby monitorować wydajność sieci między agentami źródłowym i docelowym. Rozwiązanie dostępne opcje TCP i ICMP jako protokół monitorowania w funkcji Monitora wydajności i Monitor punktu końcowego usługi, natomiast TCP jest używany dla monitora usługi ExpressRoute. Upewnij się, że zapora zezwala na komunikację między agentami OMS używane do monitorowania przy użyciu protokołu wybrany do monitorowania.  

**Protokół TCP:** Jeśli wybrano TCP protokołu służy do monitorowania, należy otworzyć port zapory na agentach, używany dla monitora wydajności i monitora ExpressRoute możliwości, aby upewnić się, że agenci można połączyć ze sobą. Aby to zrobić, uruchom skrypt programu PowerShell EnableRules.ps1 bez żadnych parametrów w oknie power shell z uprawnieniami administracyjnymi.  

Skrypt tworzy kluczy rejestru wymaganych przez to rozwiązanie i tworzy reguł zapory systemu Windows umożliwiają agentów do utworzenia połączenia TCP ze sobą. Klucze rejestru utworzony przez skrypt Określ również, czy do rejestrowania dzienników debugowania i ścieżkę do plików dzienników. Definiuje również port TCP agent używany do komunikacji. Wartości te klucze są automatycznie ustawiane za pomocą skryptu, dlatego nie należy ręcznie zmienić te klucze. Otworzyć domyślny port to 8084. Można użyć niestandardowego numeru portu zapewniając numer_portu parametru do skryptu. Jednak tego samego portu należy używać na wszystkich komputerach, na którym skrypt jest uruchamiane. 

>[!NOTE]
> Skrypt konfiguruje tylko zapory systemu windows lokalnie. Jeśli masz zapory sieciowej, należy sprawdzić, czy zezwala ruch kierowany do portu TCP używany przez NPM 

>[!NOTE]
> Nie trzeba uruchomić skrypt programu EnableRules.ps1 PowerShell monitora punktu końcowego usługi 

 

**Protokół ICMP** — Jeśli wybrano protokołu ICMP, jako protokół dla monitorowania, Włącz poniższe reguły zapory dla niezawodnego przy użyciu protokołu ICMP: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Konfigurowanie rozwiązania 

1. Dodaj rozwiązanie sieci monitora wydajności do swojego obszaru roboczego z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md). 
2. Otwórz obszar roboczy analizy dzienników i kliknij na **omówienie** kafelka.  
3. Kliknij Kafelek zatytułowany **monitora wydajności sieci** z komunikatem *rozwiązanie wymaga dodatkowej konfiguracji*.

    ![Kafelek NPM](media/log-analytics-network-performance-monitor/npm-config.png)

3. Na **Instalator** strony, zobaczysz opcję, aby zainstalować agentów OMS i konfigurowanie agentów do monitorowania w **typowe ustawienia** widoku. Zgodnie z powyższymi wskazówkami, jeśli zostały już zainstalowane i skonfigurowane OMS agentów, następnie kliknij polecenie **Instalator** konfigurowania możliwości planuje się przy użyciu widoku.  

    **Widok Monitora wydajności** — wybierz, jakiego protokołu używanego dla transakcji syntetycznych w domyślnej reguły monitora wydajności i kliknij na zapisać i kontynuować. To pole wyboru protokołu zawiera jedynie dla reguły domyślnej generowanych przez system i należy wybrać protokół jawnie w przypadku tworzenia reguły monitora wydajności. Zawsze można przenieść do domyślnych ustawień reguły na karcie Monitor wydajności (pojawia się po zakończeniu konfiguracji dnia 0) i później zmienić protokołu. W przypadku, gdy nie chcesz rPerfomance tę funkcję, można wyłączyć reguły domyślnej z domyślnych ustawień reguły na karcie Monitor wydajności. 

    ![Konfiguracja programu NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Widok monitora punktu końcowego usługi** — ta funkcja udostępnia wbudowane testy wstępnie skonfigurowane do monitorowania połączenia sieciowego do usługi Office 365 i Dynamcis365 z agentów. Wybierz usługi usługi Office 365 i Dynamcis365, które planuje się monitorowanie, zaznaczając pole wyboru obok nich. Wybierz agentów, z których chcesz monitorować, klikając przycisk Dodaj agentów. Jeśli nie chcesz używać tej funkcji lub chcesz później skonfigurować, można pominąć to i bezpośrednio kliknij **zapisać** i **Kontynuuj** bez wybierania niczego.  

    ![Konfiguracja programu NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Widok monitora ExpressRoute** — polecenie **odnajdywanie teraz** przycisk, aby odnaleźć wszystkie ExpressRoute prywatnej komunikacji równorzędnych, które są podłączone do sieci wirtualnych w subskrypcji platformy Azure są połączone z tym obszarem roboczym analizy dzienników.  


    >[!NOTE] 
    > Rozwiązanie umożliwia odnalezienie obecnie tylko ExpressRoute prywatnej komunikacji równorzędnych. 

    >[!NOTE] 
    > Tylko te prywatnej komunikacji równorzędnych są odnajdywane, które są podłączone do sieci wirtualnych, skojarzone z subskrypcją połączone z tym obszarem roboczym analizy dzienników. Twoje ExpressRoute jest podłączony do sieci wirtualnych poza subskrypcji połączone z tym obszarem roboczym, należy utworzyć obszaru roboczego analizy dzienników w tych subskrypcjach i monitorowanie tych komunikacji równorzędnych za pomocą programu NPM. 

    ![Konfiguracja programu NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Po zakończeniu odnajdywania odnalezionych prywatnej komunikacji równorzędnych są wymienione w tabeli.  

    ![Konfiguracja programu NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    Do monitorowania tych komunikacji równorzędnych początkowo są w stanie wyłączenia. Kliknij każdy równorzędna interesuje monitorowania i skonfigurować monitorowanie ich w widoku szczegółów po prawej stronie (RHS).  Kliknij przycisk, aby zapisać konfigurację zapisywania. Zobacz [ExpressRoute Konfigurowanie monitorowania]() Aby dowiedzieć się więcej.  

    Po zakończeniu instalacji trwa 30 minut godzinę do wypełniania danych. Gdy rozwiązanie jest agregowanie danych z sieci, zobacz *rozwiązanie wymaga dodatkowej konfiguracji* Kafelek Omówienie programu NPM. Gdy dane są zbierane i indeksowane, przeglądu kafelka zmian i informuje o podsumowanie kondycji sieci. Następnie użytkownik może edytować monitorowania w węzłach, na których zostali zainstalowani agenci OMS, a także podsieci wykryte za pomocą środowiska 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edytuj ustawienia monitorowania dla podsieci i węzły 

Wszystkie podsieci z co najmniej jeden agent zainstalowany są wyświetlane na karcie podsieci na stronie konfiguracji. 


Aby włączyć lub wyłączyć monitorowanie określonej podsieci 

1. Zaznacz lub usuń zaznaczenie pola obok **podsieci Identyfikatora** , a następnie upewnij się, że **na użytek monitorowania** jest wybrany lub wyczyszczone, odpowiednio. Można zaznaczyć lub wyczyścić wielu podsieci. Po wyłączeniu podsieciami, które nie są monitorowane, jak agenci zostaną zaktualizowane, aby zatrzymać polecenie ping innych agentów. 
2. Wybierz węzły, które mają być monitorowane w określonej podsieci, wybierając podsieci z listy i przenoszenie wymagane węzłów między listami zawierającego węzły niemonitorowane i monitorowane. Możesz dodać **niestandardowych opis, aby** podsieci. 
3. Kliknij przycisk **zapisać** Aby zapisać konfigurację. 

#### <a name="choose-nodes-to-monitor"></a>Wybierz węzły do monitorowania

Wszystkie węzły, które zainstalowano na nich agenta są wymienione w **węzłów** kartę. 

1. Zaznacz lub usuń węzły, które chcesz monitorować lub zatrzymać monitorowanie. 
2. Kliknij przycisk **na użytek monitorowania**, lub wyczyść, zależnie od potrzeb. 
3. Kliknij pozycję **Zapisz**. 


Skonfiguruj capability(s), które planuje się: 
- Skonfiguruj [monitora wydajności](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Skonfiguruj [Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Skonfiguruj [monitora usługi ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Szczegóły danych kolekcji
Monitor wydajności sieci używa pakietów uzgadnianie TCP SYN SYNACK potwierdzenie po wybraniu TCP i odpowiedzi ECHA ICMP ECHA ICMP po wybraniu ICMP jako protokół zbierać informacje o utracie i opóźnienia. Traceroute jest również używany do pobierania informacji o topologii.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o jak dane są zbierane dla monitora wydajności sieci.

| Platformy | Bezpośrednie agenta | Agenta programu SCOM | Azure Storage | SCOM wymagane? | Dane agenta programu SCOM wysyłane za pośrednictwem grupy zarządzania | Częstotliwość kolekcji |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Komunikaty ECHA ICMP/uzgodnienia TCP co 5 sekund danych wysyłane co 3 minuty |
 

 
W tym rozwiązaniu zastosowano transakcji syntetycznych do oceny kondycji sieci. Agenci OMS zainstalowany w różnych miejscach pakiety TCP exchange sieci lub echa protokołu ICMP (w zależności od protokołu wybrany do monitorowania) ze sobą. W procesie agentów Dowiedz się obustronne straty czasu i pakietów, jeśli istnieje. Okresowo każdy agent wykonuje także śledzenie trasy do innych agentów można znaleźć w sieci, należy sprawdzić wszystkie różne trasy. Przy użyciu tych danych, agentów można wywnioskować opóźnienia sieci i rysunki utraty pakietów. Testy są powtarzane co pięć sekund i dane są agregowane dla trzy minuty przez agentów przed przekazaniem go z usługą analizy dzienników. 



>[!NOTE]
> Mimo że agentów komunikują się ze sobą często, nie generują one znaczne ruchu sieciowego podczas przeprowadzania testów. Agentów polegać tylko na TCP SYN SYNACK potwierdzenie uzgadnianie pakietów do określenia utraty i opóźnienia — żadne dane, które są wymieniane pakietów. W trakcie tego procesu agentów komunikują się ze sobą tylko w razie potrzeby, a topologia komunikacja agenta jest optymalizowane w celu zmniejszenie ruchu w sieci.

## <a name="using-the-solution"></a>Użycie rozwiązania 

### <a name="npm-overview-tile"></a>Kafelka przeglądu NPM 

Po włączeniu rozwiązania monitora wydajności sieci kafelka rozwiązania na stronie Przegląd zapewnia szybki przegląd kondycji sieci. 

 ![NPM kafelka przeglądu](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Pulpit nawigacyjny sieci monitora wydajności 

**Zdarzenia kondycji sieci górnej** strona zawiera listę najnowszych zdarzeń, kondycji i alertów w systemie i czasu, ponieważ zdarzenie zostało aktywne. Zdarzenie kondycji lub alert jest generowany wartość metryki wybrany (utraty, czas oczekiwania, czas odpowiedzi lub użycie przepustowości) monitorowania reguły przekracza wartość progową. 

 **Monitora wydajności** strony zawiera podsumowanie kondycji łączy sieciowych i podsieć łączy monitorowanych przez rozwiązanie. Na kafelku topologii informuje liczba ścieżek sieciowych monitorowane w sieci. Kliknięcie tego kafelka bezpośrednio powoduje przejście do widoku topologii. 

 **Monitor punktu końcowego usługi** strony zawiera podsumowanie kondycji inne testy, które zostały utworzone. Na kafelku topologii informuje o liczbę punktów końcowych monitorowane. Kliknięcie tego kafelka bezpośrednio powoduje przejście do widoku topologii.

 **ExpressRoute Monitor** strony zawiera podsumowanie kondycji z różnych połączeń komunikacji równorzędnej ExpressRoute monitorowanych przez rozwiązanie. Na kafelku topologii informuje liczba ścieżek sieciowych za pośrednictwem circuit(s) ExpressRoute monitorowane w sieci. Kliknięcie tego kafelka bezpośrednio powoduje przejście do widoku topologii.

 **Typowych kwerend** strona zawiera zestaw zapytania wyszukiwania, które pobierają sieci nieprzetworzone dane monitorowania bezpośrednio. Te zapytania można użyć jako punktu wyjścia do tworzenia zapytań niestandardowych raportowania. 

![Pulpit nawigacyjny NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Przechodzenie do głębokość 

Możesz kliknąć linki różnych na pulpicie nawigacyjnym rozwiązania, aby przejść do dół głębiej do wszystkie obszary zainteresowań. Na przykład gdy zostanie wyświetlony alert lub łączy sieciowych w złej kondycji, są wyświetlane na pulpicie nawigacyjnym, kliknięcie go, aby zbadać dokładnie. Zostaje wyświetlona strona, która zawiera listę wszystkich łączy podsieci dla określonej sieci łącza. Możesz wyświetlić stan utraty, opóźnienia i kondycji każdego łącza podsieci i szybko dowiedzieć łączy podsieci, które są przyczyną problemu. Następnie możesz kliknąć **wyświetlanie łączy węzłów** wyświetlić wszystkich łączy węzłów łącza podsieci w złej kondycji. Następnie możesz wyświetlić poszczególnych łączy węzła do węzła i łącza węzłów w złej kondycji. 

Możesz kliknąć **widoku topologii** do wyświetlania topologii przeskoku przeskoku tras między węzły źródłowe i docelowe. Zła trasy są zaznaczone na czerwono i można wyświetlić opóźnienia przekazanych przez każdego przeskoku, dzięki czemu można szybko zidentyfikować problem do określonej części sieci. 

 

### <a name="network-state-recorder"></a>Rejestrator stanu sieci 

Każdy widok wyświetla migawkę kondycji Twojej sieci w określonym punkcie w czasie. Domyślnie wyświetlany jest stan ostatniej. Na pasku w górnej części strony wyświetlany punkt w czasie, dla którego stan jest wyświetlany. Możesz przejść wstecz w czasie i Wyświetl migawki kondycji Twojej sieci, klikając na pasku akcji. Można również włączyć lub wyłączyć automatyczne odświeżanie na każdej stronie podczas przeglądania najnowszy stan. 

 ![Rejestrator stanu sieci](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend wykresów 

Na każdym poziomie Drąż w dół można wyświetlić trend dotyczy metryki — utraty, czas oczekiwania, czas reakcji i użycie przepustowości. Przedział czasu trendu można zmienić za pomocą formantu czasu w górnej części wykresu. 

Wykresy trendu Pokaż historycznych perspektywy wydajności metryki wydajności. Niektóre problemy dotyczące sieci są charakter przejściowy oraz będzie trudne do catch tylko na podstawie bieżącego stanu sieci. Jest to spowodowane problemy można szybko surface i zniknąć przed każdy powiadomienia, tylko do pojawić się ponownie w późniejszym czasie, w czasie. Takie przejściowych problemów również może być trudne dla administratorów aplikacji, ponieważ te problemy często powierzchni wraz ze wzrostem niewyjaśniony czas odpowiedzi aplikacji, nawet jeśli wszystkie składniki aplikacji są wyświetlane bezproblemowe działanie. 

Tego rodzaju problemy mogą łatwo wykryć, analizując wykresu trendu, w której problem pojawia się jako kolekcji nagłym opóźnieniem sieci lub utraty pakietów. Następnie można zbadać problem za pomocą rejestratora stanu sieci wyświetlanie migawki sieci i topologii dla tego punktu w czasie, kiedy miało miejsce problem. 

 
![Trend wykresów](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapy topologii 

NPM przedstawia topologię przeskoku przeskoku tras między źródłowego i docelowego punktu końcowego na mapie interakcyjne topologii. Mapy topologii można wyświetlić, klikając **topologii** kafelka na pulpicie nawigacyjnym rozwiązania lub klikając **widoku topologii** łącza na stronach Przechodzenie do szczegółów.  

Mapy topologii przedstawia, jak wiele tras należą do zakresu od źródłowy i docelowy i co ścieżek zająć pakietów danych. Opóźnienie przekazanych przez każdego przeskoku sieci jest również widoczne. Wszystkie ścieżki, dla których opóźnienie całkowita ścieżka jest powyżej wartości progowej (ustawiana w odpowiadająca im zasada monitorowania) są wyświetlane na czerwono.  

Po kliknięciu węzła lub kursor nad nim mapy topologii, można znaleźć we właściwościach węzła, takie jak adres IP i nazwy FQDN. Kliknij przycisk przeskoków, aby wyświetlić jego adresu IP. Można zidentyfikować przeskoków sieciowych powodującymi opóźnienia jest zamieszczone przez niego po raz pierwszy. Istnieje możliwość filtrowania tras określonego za pomocą filtrów w okienku akcji zwijane. Można również ułatwić topologii sieci, ukrywając przeskoków za pomocą suwaka, w okienku akcji. Możesz powiększania lub out mapy topologii za pomocą kółka myszy. 

Pamiętaj, że topologii pokazano mapy topologii warstwy 3 i nie zawiera warstwy 2 urządzenia i połączeń. 

 
![Mapy topologii](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Dziennik wyszukiwania analityka 

Wszystkie dane, które jest narażonych graficznie za pośrednictwem pulpitu nawigacyjnego programu NPM i przechodzenia strony jest również dostępna natywnie w [wyszukiwania analizy dzienników](log-analytics-log-search-new.md). Można wykonywać analizy interakcyjnej danych w repozytorium, korelowania danych z innych źródeł, tworzyć niestandardowe alerty, tworzenie niestandardowych widoków i eksportować dane do programu Excel, usłudze PowerBI lub możliwe do udostępnienia łącza. Obszar typowych kwerend na pulpicie nawigacyjnym ma niektóre przydatne zapytań, które służy jako punkt początkowy do tworzenia własnych kwerendy i raporty. 

 

## <a name="provide-feedback"></a>Przekazywanie opinii 

**UserVoice** — możesz zamieścić pomysłów funkcji Monitora wydajności sieci, które chcesz pracować nad. Można znaleźć w naszych [strony UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Dołącz do naszych kohorty** -NAS interesuje zawsze o nowych klientów, Dołącz do naszych kohorty. W ramach go uzyskać wczesny dostęp do nowych funkcji i pomóc nam w ulepszaniu monitora wydajności sieci. Jeśli interesuje Cię dołączenie, wypełnienie wychodzący to [szybkie ankiety](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Kolejne kroki 
- Dowiedz się więcej o [monitora wydajności](log-analytics-network-performance-monitor-performance-monitor.md), [Monitor punktu końcowego usługi](log-analytics-network-performance-monitor-performance-monitor.md), i [ExpressRoute Monitor](log-analytics-network-performance-monitor-expressroute.md). 
