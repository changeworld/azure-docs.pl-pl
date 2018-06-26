---
title: Przy użyciu mapy usługi rozwiązania na platformie Azure | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania mapy usługi w danym środowisku i korzystania z niego w różnych scenariuszach.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2018
ms.author: daseidma;bwren
ms.openlocfilehash: 812137a8320634364a7d91fd2e61cd3e9d15fc12
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751432"
---
# <a name="using-service-map-solution-in-azure"></a>Przy użyciu mapy usługi rozwiązania na platformie Azure
Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Z mapy usługi można przeglądać serwery w taki sposób, który należy wziąć pod uwagę ich: jako połączonych systemy, które dostarczają usług krytycznych. Mapy usługi pokazuje połączeń między serwerami, procesów, i portów w dowolnej połączenia TCP architekturze, bez konieczności wykonywania konfiguracyjnych wymaganych innych niż instalację agenta.

W tym artykule opisano dołączania i przy użyciu mapy usługi. Informacje o konfigurowaniu mapy usługi i dołączania agentów, zobacz [mapy usługi konfigurowanie rozwiązania na platformie Azure]( monitoring-service-map-configure.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Włącz mapy usług
1. W portalu Azure kliknij **+ Utwórz zasób**.
2. Na pasku wyszukiwania wpisz **mapy usługi** i naciśnij klawisz **Enter**.
3. Na stronie witryny marketplace wyników wyszukiwania wybierz **mapy usługi** z listy.<br><br> ![Wybierz rozwiązanie mapy usługi z wyniki wyszukiwania portalu Azure Marketplace](./media/monitoring-service-map/marketplace-search-results.png)<br>
4. Na **mapy usługi** okienku przeglądu, przejrzyj szczegóły rozwiązania, a następnie kliknij przycisk **Utwórz** do rozpoczęcia procesu dołączania do swojego obszaru roboczego analizy dzienników.<br><br> ![Zintegrowanego rozwiązania mapy usługi](./media/monitoring-service-map/service-map-onboard.png).
5. W **skonfiguruj rozwiązanie** okienku, wybierz istniejącą lub Utwórz nowy obszar roboczy analizy dzienników.  Aby uzyskać więcej informacji na temat sposobu tworzenia nowego obszaru roboczego, zobacz [Tworzenie obszaru roboczego analizy dzienników w portalu Azure](../log-analytics/log-analytics-quick-create-workspace.md). Po podaniu wymaganych informacji, kliknij przycisk **Utwórz**.  

Informacje została zweryfikowana i rozwiązanie jest wdrożone, można śledzić postęp w obszarze **powiadomienia** z menu. 

Dostęp do mapy usługi w portalu Azure z obszaru roboczego analizy dzienników i wybierz opcję **rozwiązań** w lewym okienku.<br><br> ![Wybierz opcję rozwiązań w obszarze roboczym](./media/monitoring-service-map/select-solution-from-workspace.png).<br> Wybierz z listy rozwiązań **ServiceMap(workspaceName)** w mapy usługi rozwiązania Przegląd kliknij Kafelek podsumowanie mapy usługi.<br><br> ![Kafelek podsumowania mapy usługi](./media/monitoring-service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Przypadki użycia: należy go przetwarza pamiętać zależności

### <a name="discovery"></a>Odnajdowanie
Mapy usług automatycznie tworzy wspólnej mapę zależności odwołania na serwery, procesów i usług innych firm. Odnajduje, a mapuje wszystkie zależności TCP, identyfikowanie niespodziewanego połączenia zdalnego systemów innych firm, które zależą od i zależności do tradycyjnych ciemny obszarów sieci, takich jak usługi Active Directory. Mapa usług odnajduje połączenia sieciowe nie powiodło się, które próbujesz zarządzanych systemach, aby pomaga zidentyfikować potencjalne konfiguracji serwera, awaria usługi i problemy z siecią.

### <a name="incident-management"></a>Zarządzanie zdarzeniami
Mapy usług pozwala wyeliminować czynności izolacji problem przedstawiające, jak połączenia systemów i wpływu na siebie. Oprócz Identyfikacja połączenia nie powiodło się, pomaga zidentyfikować równoważenia obciążenia nieprawidłowo, zaskakująco lub zbyt duże obciążenie usług krytycznych i nieautoryzowane klientów, takich jak komputerach deweloperów rozmowie z systemów produkcyjnych. Przy użyciu zintegrowanego przepływów pracy ze śledzeniem zmienić, możesz też sprawdzić, czy zdarzenie zmiany na maszynie zaplecza lub usługi wyjaśnia głównej przyczyny zdarzenia.

### <a name="migration-assurance"></a>Zapewnienie migracji
Przy użyciu mapy usługi, można efektywnie planowania, przyspieszanie i zweryfikować Azure migracji, które pomaga zapewnić, że nic pozostawione i nie występują awarie niespodziewanego. Umożliwia odnalezienie wszystkich współzależne systemów, które należy migrować ze sobą, oceny konfiguracji systemu oraz wydajności i ustalić, czy na komputerze z uruchomionym systemem nadal działa jako użytkowników lub kwalifikuje się do likwidacji zamiast migracji. Po zakończeniu przenoszenia można sprawdzić na obciążenia klientów i tożsamości, aby sprawdzić połączenie systemy testowe i klientów. Jeśli definicje planowania i zapory podsieci problemy, połączenia nie powiodło się w społeczności maps mapy usługi punktu do systemów, które wymagają łączności.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
Jeśli używasz usługi Azure Site Recovery i musi pomocy definiowanie sekwencji odzyskiwania dla środowiska aplikacji mapy usługi można automatycznie wyświetlić jak systemy opierają się na siebie nawzajem w celu zapewnienia, że plan odzyskiwania jest niezawodne. Wybierając krytyczne serwera lub grupy i wyświetlanie swoich klientów, można zidentyfikować systemów frontonu do odzyskania po serwer jest przywracane i dostępne. Z drugiej strony analizując krytyczne serwerów zaplecza zależności można zidentyfikować systemów do odzyskania, zanim zostaną przywrócone przez systemy fokus.

### <a name="patch-management"></a>Zarządzanie poprawkami
Mapy usług podnosi poziom użytkowania oceny aktualizacji systemu, pokazujące, które innych zespołów i serwery są zależne od usługi, więc można powiadomić je z wyprzedzeniem przed wyłączyć przez systemy w celu wdrożenia poprawki. Mapy usług zwiększa również zarządzanie poprawkami poprzez wyświetlenie, czy usługi są dostępne i poprawnie połączone po zostaną poprawiono i ponownie uruchomione.

## <a name="mapping-overview"></a>Mapowanie — omówienie
Agenci mapy usługi zbieranie informacji na temat wszystkich procesów połączenia TCP na serwerze, na którym jest zainstalowany i szczegóły dotyczące połączeń przychodzących i wychodzących dla każdego procesu.

Na liście w okienku po lewej stronie możesz wybrać maszyny lub grupy, które mają mapy usługi agentów do wizualizacji ich zależności w przedziale czasu. Zależności maszyny mapuje fokus na określonym komputerze i przedstawiają wszystkie maszyny, które są bezpośrednio TCP klientów lub serwerów tej maszynie.  Mapowanie grup maszyny Pokaż zestawy serwerów oraz ich zależności.

![Omówienie mapy usług](media/monitoring-service-map/service-map-overview.png)

Maszyny można skalować w mapę, aby pokazać działanie przetworzenia grup i procesy z aktywnych połączeń sieciowych w trakcie wybranego zakresu czasu. Gdy komputer zdalny przy użyciu mapy usługi agenta jest rozwinięty, aby wyświetlić szczegóły procesu, wyświetlane są tylko procesy, które komunikują się z maszyną fokus. Po lewej stronie procesów, które łączą się z określonej liczby bez wykorzystania agentów maszyn frontonu, które łączą się maszyny fokus. Jeśli maszyna fokus jest połączenie maszyny zaplecza, której nie ma agenta, serwera zaplecza znajduje się w grupy portów serwera, oraz inne połączenia do tego samego numeru portu.

Domyślnie mapy usługi maps zawierają ostatnich 30 minut informacji o zależnościach. Za pomocą formantów czasu, w lewym górnym rogu, można zbadać mapy dla przedziałów czasu historycznych maksymalnie jedną godzinę, aby pokazać, jak zależności wyszukiwanego w przeszłości (na przykład podczas zdarzenia lub zanim nastąpiła zmiana). Dane mapy usługi są przechowywane przez 30 dni roboczych płatną i 7 dni w bezpłatnych obszarów roboczych.

## <a name="status-badges-and-border-coloring"></a>Identyfikatory stanu i kolorowanie obramowania
W dolnej części każdego serwera w planie może być lista identyfikatory stan przekazywania stanu informacji o serwerze. Identyfikatory oznacza, że niektóre istotne informacje dla serwera z jednego z integracji rozwiązania. Klikając pozycję wskaźnika przejście bezpośrednio do szczegółów o stanie w okienku po prawej stronie. Stan aktualnie dostępne identyfikatory obejmują alerty, działu, zmiany, zabezpieczeń i aktualizacje.

W zależności od ważności identyfikatory stan obramowań węzła maszyny mogą być kolorowe czerwony (krytyczna), żółty (ostrzeżenie) lub niebieski (informacyjny). Kolor reprezentuje najpoważniejsze stan dowolnego identyfikatory stanu. Szare obramowanie wskazuje węzła, który ma wskaźników stanu.

![Identyfikatory stanu](media/monitoring-service-map/status-badges.png)

## <a name="process-groups"></a>Grupa procesów
Grupa procesów łączyć procesów, które są skojarzone z typowych produktu lub usługi do grupy procesów.  Po rozwinięciu węzła maszyny wyświetli autonomiczny procesów wraz z grupy procesów.  Jeśli wszystkie połączenia przychodzące i wychodzące do procesu w obrębie grupy procesu nie powiodło się następnie połączenie, jest wyświetlana jako grupy całego procesu nie powiodło się.

## <a name="machine-groups"></a>Grupy na komputerze
Grupy na komputerze umożliwiają wyświetlanie mapy skupia się wokół zestaw serwerów, nie tylko jedną pozwala zobaczyć wszystkie elementy członkowskie klastra wielowarstwowych aplikacji lub serwera, w jedną mapę.

Użytkownicy wybierają serwerów, które należą do grupy ze sobą i wybierz nazwę grupy.  Można następnie wyświetlić grupy wszystkich połączeń i procesy lub wyświetlić tylko procesy i połączeń, które bezpośrednio odnoszą się do elementów członkowskich grupy.

![Grupy na komputerze](media/monitoring-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Tworzenie grupy na komputerze
Aby utworzyć grupę, wybierz maszynę lub maszyn, które mają na maszynach listy i kliknij przycisk **Dodaj do grupy**.

![Tworzenie grupy](media/monitoring-service-map/machine-groups-create.png)

Istnieje, można wybrać **Utwórz nowy** i nadaj nazwę grupie.

![Nazwa grupy](media/monitoring-service-map/machine-groups-name.png)

>[!NOTE]
>Grupy na komputerze są obecnie ograniczone do 10 serwerów, ale planujemy wkrótce zwiększyć ten limit.

### <a name="viewing-a-group"></a>Wyświetlanie grupy
Po utworzeniu niektórych grup, można je wyświetlić, wybierając kartę grupy.

![Karta grup](media/monitoring-service-map/machine-groups-tab.png)

Następnie wybierz nazwę grupy, aby wyświetlić mapy dla tej grupy na komputerze.
![Grupy na komputerze](media/monitoring-service-map/machine-group.png) maszyny, które należą do tej grupy zostały opisane w oficjalnym na mapie.

Rozszerzanie grupa będzie zawierała listę maszyny, które tworzą grupę maszyny.

![Grupy na komputerze maszyny](media/monitoring-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtruj według procesów
Istnieje możliwość przełączania widoku mapy między przedstawiający wszystkie procesy i połączenia w grupie i tylko te, które dotyczą bezpośrednio do grupy na komputerze.  Widok domyślny jest do wyświetlenia wszystkich procesów.  Widok można zmienić, klikając ikonę filtru powyżej mapy.

![Grupy filtru](media/monitoring-service-map/machine-groups-filter.png)

Gdy **wszystkie procesy** jest zaznaczone, mapy uwzględni wszystkie procesy i połączenia na poszczególnych maszyn w grupie.

![Przetwarza wszystkie grupy na komputerze](media/monitoring-service-map/machine-groups-all.png)

Jeśli zmienisz widok, aby pokazać tylko **podłączone grupy procesów**, mapy będzie zawęzić tylko procesy i połączeń, które są podłączone bezpośrednio do innych komputerów w grupie, tworzenie uproszczony widok.

![Procesy filtrowane grupy na komputerze](media/monitoring-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Dodawanie komputerów do grupy
Aby dodać komputery do istniejącej grupy, zaznacz pola obok maszyny, a następnie kliknij przycisk **Dodaj do grupy**.  Następnie wybierz grupę, w której chcesz dodać maszyn.
 
### <a name="removing-machines-from-a-group"></a>Usuwanie urządzenia z grupy
Na liście grupy rozwiń nazwę grupy, aby wyświetlić listę komputerów w grupie maszyny.  Następnie kliknij przycisk menu wielokropka obok maszyny, której chcesz usunąć, a następnie wybierz pozycję **Usuń**.

![Usuń maszynę z grupy](media/monitoring-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Usuwanie lub zmiana nazwy grupy
Kliknij menu wielokropka obok nazwy grupy na liście grupy.

![Maszyna grupy menu](media/monitoring-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony ról
Niektóre procesy obsługi ról określonego na maszynach: serwery sieci web, serwerów aplikacji, bazy danych i tak dalej. Mapa usług oznacza pola procesów i komputera, aby ułatwić identyfikację jeden rzut oka roli procesu lub serwera pełni ikon roli.

| Ikona roli | Opis |
|:--|:--|
| ![Serwer sieci Web](media/monitoring-service-map/role-web-server.png) | Serwer sieci Web |
| ![Serwer aplikacji](media/monitoring-service-map/role-application-server.png) | Serwer aplikacji |
| ![Serwer bazy danych](media/monitoring-service-map/role-database.png) | Serwer bazy danych |
| ![Serwer LDAP](media/monitoring-service-map/role-ldap.png) | Serwer LDAP |
| ![Serwer protokołu SMB](media/monitoring-service-map/role-smb.png) | Serwer protokołu SMB |

![Ikony ról](media/monitoring-service-map/role-icons.png)


## <a name="failed-connections"></a>Połączenia zakończone niepowodzeniem
Nie powiodło się połączeń są wyświetlane w mapy usługi maps dla procesów i komputerów, z czerwonym linię kropkowaną wskazujący, że systemu klienta kończy się niepowodzeniem do procesu lub portu. Połączenia nie powiodło się są zgłaszane z dowolnego systemu przy użyciu wdrożonej agenta mapy usługi, w przypadku tego systemu jest próba połączenia nie powiodło się. Mapa usług mierzy ten proces obserwując gniazda TCP, których nie można ustanowić połączenia. Ten błąd może wystąpić z zapory, błąd konfiguracji klienta lub serwera lub usługi zdalnej jest niedostępna.

![Połączenia zakończone niepowodzeniem](media/monitoring-service-map/failed-connections.png)

Opis połączenia nie powiodło się może pomóc w rozwiązywaniu problemów, weryfikacji migracji, analizowania zabezpieczeń i zrozumienia ogólnej architektury. Czasami jest bezpieczna połączenia nie powiodło się, ale często wskazuje, bezpośrednio do problemu, takich jak środowisko trybu failover, nagle staje się niedostępny lub dwoma warstwami aplikacji nie będą mogli komunikować po migracji chmury.

## <a name="client-groups"></a>Grupy klientów
Grup klienta są pola na mapie, które reprezentują komputery klienckie, które nie mają zależności agentów. Pojedynczej grupy klientów reprezentuje klientów dla poszczególnych procesu lub komputera.

![Grupy klientów](media/monitoring-service-map/client-groups.png)

Aby wyświetlić adresy IP serwerów z grupy klientów, wybierz grupę. Zawartość grupy są wymienione w **właściwości grupy klienta** okienka.

![Właściwości grupy klientów](media/monitoring-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Port serwera grup
Port serwera grupy są pola, które reprezentują portów na serwerach, które nie mają zależności agentów. Pole zawiera port serwera oraz liczbę serwerów z połączeniami do tego portu. Rozwiń pole poszczególnych serwerów i połączeń. Jeśli istnieje tylko jeden serwer w polu, wyświetlana jest nazwa lub adres IP.

![Port serwera grup](media/monitoring-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu kontekstowe
Klikając przycisk wielokropka (...) u góry po prawej dowolnego serwera Wyświetla menu kontekstowego dla tego serwera.

![Połączenia zakończone niepowodzeniem](media/monitoring-service-map/context-menu.png)

### <a name="load-server-map"></a>Obciążenia serwera mapy
Kliknięcie przycisku **obciążenia serwera mapy** przejście do nowej mapy z wybranego serwera jako nową maszynę fokus.

### <a name="show-self-links"></a>Pokaż linki do samego siebie
Kliknięcie przycisku **Pokaż Self-Links** ponownie rysuje węzeł serwera, wraz ze wszystkimi linki do samego siebie, które są połączeń TCP, które rozpoczęcia i zakończenia procesów w ramach serwera. Jeśli linki do samego siebie są wyświetlane zmiany polecenia menu **Ukryj Self-Links**, dzięki czemu można je wyłączyć.

## <a name="computer-summary"></a>Podsumowanie komputera
**Podsumowanie maszyny** okienko zawiera przegląd systemu operacyjnego serwera, liczby zależności i danych z innych rozwiązań. Dane te obejmują metryki wydajności, bilety usług technicznej śledzenia zmian, zabezpieczeń i aktualizacje.

![Okienko Podsumowanie maszyny](media/monitoring-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Właściwości komputera i procesu
Po przejściu mapy mapy usług, możesz wybrać maszyn i procesy w celu uzyskania dodatkowych kontekstu dotyczące ich właściwości. Maszyny zawierają informacje dotyczące DNS nazwy, adresów IPv4, procesora CPU i pamięci, pojemności, typu maszyny Wirtualnej, systemu operacyjnego i wersji, ostatniego ponownego rozruchu czasie i identyfikatory ich agentów OMS i mapy usługi.

![W okienku właściwości maszyny](media/monitoring-service-map/machine-properties.png)

Szczegóły procesu można zbierać z metadanych systemu operacyjnego dotyczące uruchamiania procesów, w tym nazwę procesu, opis procesu, nazwę użytkownika i domeny (w systemie Windows), nazwę firmy, nazwa produktu, wersja produktu, katalog roboczy, wiersza polecenia i procesu Godzina rozpoczęcia.

![W okienku właściwości procesu](media/monitoring-service-map/process-properties.png)

**Podsumowanie procesu** okienko udostępnia dodatkowe informacje o łączności procesu, takie jak jego powiązanych portów, połączeń przychodzących i wychodzących i nie powiodło się połączenia.

![Okienko Podsumowanie procesu](media/monitoring-service-map/process-summary.png)

## <a name="alerts-integration"></a>Integracja alertów
Mapy usług integruje się z alertami Azure do pokazania w wybranym zakresie czasu wypalane alerty dla wybranego serwera. Serwer Wyświetla ikonę, jeśli są aktualne alerty i **alerty maszyny** w okienku wyświetlana lista alertów.

![W okienku alertów maszyny](media/monitoring-service-map/machine-alerts.png)

Aby włączyć mapy usługi do wyświetlenia powiązanych alertów, należy utworzyć zasady alertu wyzwalająca dla określonego komputera. Aby utworzyć odpowiednie alerty:
- Zawiera klauzulę do grupy przez komputer (na przykład **komputera interwał 1 minuta**).
- Wybierz alert w oparciu metryki pomiaru.

## <a name="log-events-integration"></a>Integracja z dziennika zdarzeń
Mapa usług integruje się z dziennika wyszukiwanie, aby wyświetlić liczbę wszystkich zdarzeń dziennika dostępna dla wybranego serwera w wybranym zakresie czasu. Możesz kliknąć każdy wiersz na liście zdarzeń liczników szybkiego dostępu do dziennika wyszukiwania i zobaczyć osobny dziennik zdarzeń.

![Okienko dziennika zdarzeń maszyny](media/monitoring-service-map/log-events.png)

## <a name="service-desk-integration"></a>Integracja usługi technicznej
Mapa usług integracji z łącznika zarządzania usługi IT odbywa się automatycznie, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym analizy dzienników. Integracja usługi mapy etykietą "Działu". Aby uzyskać więcej informacji, zobacz [centralnie zarządzać zarządzanie usługami IT — elementów roboczych za pomocą łącznika zarządzania usługi IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

**Działu maszyny** okienko Wyświetla listę wszystkich zdarzeń zarządzania usługami INFORMATYCZNYMI dla wybranego serwera w wybranym zakresie czasu. Serwer Wyświetla ikonę, jeśli bieżący elementów i wyświetlane w okienku działu maszyny.

![Okienko działu maszyny](media/monitoring-service-map/service-desk.png)

Aby otworzyć element w rozwiązaniu Zarządzanie usługami IT — połączonych, kliknij przycisk **elementu roboczego widoku**.

Aby wyświetlić szczegóły elementu dziennik wyszukiwania, kliknij przycisk **Pokaż dziennik wyszukiwania**.


## <a name="change-tracking-integration"></a>Zmień integracji śledzenia
Mapa usług integracji z śledzenia zmian odbywa się automatycznie, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym analizy dzienników.

**Śledzenia zmian maszyny** okienku są wyświetlane wszystkie zmiany z najnowszych pierwszy, wraz z linkiem do przechodzenia do wyszukiwania dziennika, aby uzyskać dodatkowe szczegóły.

![Okienko śledzenia zmian maszyny](media/monitoring-service-map/change-tracking.png)

Poniższa ilustracja jest szczegółowy widok zdarzenie Zmianakonfiguracji, które można napotkać po wybraniu **Pokaż w analizy dzienników**.

![Zmianakonfiguracji zdarzeń](media/monitoring-service-map/configuration-change-event-01.png)


## <a name="performance-integration"></a>Integracja wydajności
**Wydajność maszyny** okienko przedstawia metryki wydajności standardowe dla wybranego serwera. Metryki obejmują użycie Procesora, wykorzystanie pamięci, sieci bajtów wysłanych i odebranych i listę najważniejszych procesów sieci bajtów wysłanych i odebranych.

![Okienko wyników maszyny](media/monitoring-service-map/machine-performance.png)

Aby wyświetlić dane wydajności, może być konieczne [włączyć odpowiednie liczniki wydajności usługi Analiza dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Liczniki, które ma zostać włączony:

W systemie Windows:
- Procesor(*)\\czas procesora (%)
- Pamięć\\Zadeklarowane bajty w użyciu (%)
- Sieci Adapter(*)\\bajty wysłane/s
- Sieci Adapter(*)\\bajty odebrane/s

W systemie Linux:
- Procesor(*)\\czas procesora (%)
- Memory(*)\\% wykorzystanie pamięci
- Sieci Adapter(*)\\bajty wysłane/s
- Sieci Adapter(*)\\bajty odebrane/s

Aby uzyskać dane dotyczące wydajności sieci, musi również włączono rozwiązania podczas transmisji danych 2.0 w obszarze roboczym.
 
## <a name="security-integration"></a>Integracja z zabezpieczeniami
Mapy usług integracji z zabezpieczeniami i inspekcji odbywa się automatycznie, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym analizy dzienników.

**Zabezpieczeń maszyny** w okienku zostaną wyświetlone dane z rozwiązań zabezpieczeń i inspekcji dla wybranego serwera. W okienku listy podsumowanie oczekujących bezpieczeństwo serwera podczas wybranego zakresu czasu. Kliknięcie dowolnej ćwiczeń problemy dotyczące zabezpieczeń w dół do wyszukiwania dziennika, aby uzyskać szczegółowe informacje o nich.

![Okienko zabezpieczeń komputera](media/monitoring-service-map/machine-security.png)

## <a name="updates-integration"></a>Integracja aktualizacji
Mapa usług integracji z zarządzania aktualizacjami odbywa się automatycznie, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym Anlaytics dziennika.

**Machine aktualizacje** okienku są wyświetlane dane z rozwiązania do zarządzania aktualizacji dla wybranego serwera. Okienko zawiera podsumowanie dowolnych brakujących aktualizacji dla serwera w wybranym zakresie czasu.

![Okienko śledzenia zmian maszyny](media/monitoring-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Dane spisu komputera i procesu mapy usługi są dostępne dla [wyszukiwania](../log-analytics/log-analytics-log-searches.md) w analizy dzienników. Dane te można stosować do scenariuszy obejmujących planowania migracji, analizy pojemności, odnajdywania i rozwiązywanie problemów z wydajnością na żądanie.

Jeden rekord jest generowany na godzinę dla każdego komputera unikatowy i procesów oprócz rekordów, które są generowane, gdy proces lub komputer uruchamia lub jest na dodawanej do mapy usługi. Te rekordy mają właściwości w poniższych tabelach. Pola i wartości ServiceMapComputer_CL mapy zdarzeń do pól zasobów komputera w interfejsie API ServiceMap Azure Resource Manager. Pola i wartości ServiceMapProcess_CL mapy zdarzeń do pól zasobów procesu w interfejsie API ServiceMap Azure Resource Manager. W polu ResourceName_s odpowiada pola Nazwa zasobu usługi Resource Manager. 

>[!NOTE]
>Wzrostem funkcje mapy usługi tych pól mogą ulec zmianie.

Istnieją wewnętrznie generowane właściwości, które służy do identyfikacji procesów unikatowy i komputerami:

- Komputer: Użyj ResourceId lub ResourceName_s do unikatowej identyfikacji komputera w obszarze roboczym analizy dzienników.
- Proces: ResourceId używany do jednoznacznego identyfikowania procesu w obszarze roboczym analizy dzienników. ResourceName_s jest unikatowa w kontekście komputera, na którym proces jest uruchomiony (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym okresie, zapytania mogą zwracać więcej niż jeden rekord dla tego samego komputera lub proces. Aby dołączyć tylko ostatniego rekordu, należy dodać "| Funkcja deduplikacji ResourceId"w zapytaniu.

### <a name="servicemapcomputercl-records"></a>Rejestruje ServiceMapComputer_CL
Rekordy z typem *ServiceMapComputer_CL* dane spisu dla serwerów z agentami mapy usługi. Te rekordy są właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| ResourceName_s | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| ComputerName_s | Nazwa FQDN komputera |
| Ipv4Addresses_s | Lista IPv4 serwera adresów |
| Ipv6Addresses_s | Lista IPv6 serwera adresów |
| DnsNames_s | Tablicę nazw DNS |
| OperatingSystemFamily_s | Systemu Windows lub Linux |
| OperatingSystemFullName_s | Pełna nazwa systemu operacyjnego  |
| Bitness_s | Liczba bitów maszyny (32-bitowy lub 64-bitowe)  |
| PhysicalMemory_d | Fizycznej pamięci w MB |
| Cpus_d | Liczba procesorów |
| CpuSpeed_d | Szybkość Procesora w MHz|
| VirtualizationState_s | *Nieznany*, *fizycznych*, *wirtualnego*, *funkcji hypervisor* |
| VirtualMachineType_s | *funkcji Hyper-v*, *vmware*i tak dalej |
| VirtualMachineNativeMachineId_g | Identyfikator VM przypisany przez jego funkcji hypervisor |
| VirtualMachineName_s | Nazwa maszyny Wirtualnej |
| BootTime_t | Czas rozruchu |



### <a name="servicemapprocesscl-type-records"></a>Rejestruje typ ServiceMapProcess_CL
Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesów połączenia TCP na serwerach z agentami mapy usługi. Te rekordy są właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| Typ | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | Unikatowy identyfikator procesu, w obszarze roboczym |
| ResourceName_s | Unikatowy identyfikator procesu na maszynie, na którym jest uruchomiony|
| MachineResourceName_s | Nazwa zasobu maszyny |
| ExecutableName_s | Nazwa pliku wykonywalnego procesu |
| StartTime_t | Czas rozpoczęcia procesu puli |
| FirstPid_d | Pierwszy identyfikator PID procesu w puli procesów |
| Description_s | Opis procesu |
| CompanyName_s | Nazwa firmy |
| InternalName_s | Nazwa wewnętrzna |
| ProductName_s | Nazwa produktu |
| ProductVersion_s | Wersja produktu |
| FileVersion_s | Wersja pliku |
| CommandLine_s | W wierszu polecenia |
| ExecutablePath _s | Ścieżka do pliku wykonywalnego |
| WorkingDirectory_s | Katalog roboczy |
| UserName | Konta, na którym jest wykonywany proces |
| UserDomain | Domeny, na którym jest wykonywany proces |


## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Wyświetl listę wszystkich maszyn znane
ServiceMapComputer_CL | Podsumuj arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę wszystkich zarządzanych komputerów pojemność pamięci fizycznej.
ServiceMapComputer_CL | Podsumuj arg_max(TimeGenerated, *) przez ResourceId | Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Nazwa komputera listy, DNS, adresu IP i systemu operacyjnego.
ServiceMapComputer_CL | Podsumuj arg_max(TimeGenerated, *) przez ResourceId | Projekt ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy z "sql" w wierszu polecenia
ServiceMapProcess_CL | gdzie contains_cs CommandLine_s "sql" | Podsumuj arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszyny (najnowszych rekord) według nazwy zasobu
Szukaj w "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" (ServiceMapComputer_CL) | Podsumuj arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź maszyny (najnowszych rekord) za pomocą adresu IP
Szukaj w "10.229.243.232" (ServiceMapComputer_CL) | Podsumuj arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Wyświetl listę wszystkich procesów znanych na określonym komputerze
ServiceMapProcess_CL | gdzie MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | Podsumuj arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-all-computers-running-sql"></a>Lista wszystkich komputerów z programem SQL
ServiceMapComputer_CL | gdzie ResourceName_s w ((wyszukiwanie w (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetla listę wszystkich wersji produktu unikatowy curl w mojej centrum danych
ServiceMapProcess_CL | gdzie ExecutableName_s == "curl" | różne ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Tworzenie grupy komputerów wszystkich komputerów z systemem CentOS
ServiceMapComputer_CL | gdzie contains_cs OperatingSystemFullName_s "CentOS" | różne ComputerName_s


## <a name="rest-api"></a>Interfejs API REST
Wszystkie dane serwera, procesów i zależności mapy usług jest dostępna za pośrednictwem [interfejsu API REST mapy usługi](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia
Firma Microsoft automatycznie zbiera dane użycia i wydajności przez korzystanie z usługi mapy usługi. Firma Microsoft używa tych danych do udostępniania i ulepszania jakości, bezpieczeństwa i integralności usługi mapy usługi. Aby zapewnić dokładne i skuteczne funkcje do rozwiązywania problemów, dane obejmują informacje o konfiguracji oprogramowania, na przykład systemu operacyjnego i wersji, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft gromadzi nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i użycia danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [dziennika wyszukiwania](../log-analytics/log-analytics-log-searches.md) w analizy dzienników do pobierania danych zbieranych przez usługę mapy.


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zobacz [Rozwiązywanie problemów z sekcji dokumentu Konfigurowanie mapy usługi]( monitoring-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Opinia
Masz opinię, abyśmy o mapy usługi lub tej dokumentacji?  Można znaleźć w naszych [strony User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), gdzie można sugeruje funkcje lub głosowania zapasową istniejących sugestie.
