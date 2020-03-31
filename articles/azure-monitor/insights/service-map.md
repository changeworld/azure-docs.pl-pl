---
title: Korzystanie z rozwiązania mapy usług na platformie Azure | Dokumenty firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania mapy usług w twoim środowisku i używania jej w różnych scenariuszach.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: f2f3e84462307f43ffe432fe878476d979f489f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480916"
---
# <a name="using-service-map-solution-in-azure"></a>Korzystanie z rozwiązania Service Map na platformie Azure

Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Dzięki usłudze Service Map można wyświetlać serwery w sposób, w jakich się o nich myśli: jako wzajemnie połączone systemy dostarczające usługi. Usługa Service Map pokazuje połączenia między serwerami i procesami, opóźnienie połączeń przychodzących i wychodzących oraz porty dla każdej architektury połączonej za pomocą protokołu TCP. Nie jest wymagana żadna konfiguracja, wystarczy zainstalować agenta.

W tym artykule opisano szczegóły dołączania i korzystania z mapy usług. Aby uzyskać informacje dotyczące konfigurowania wymagań wstępnych dla tego rozwiązania, zobacz [Włączanie usługi Azure Monitor dla maszyn wirtualnych omówienie](vminsights-enable-overview.md#prerequisites). Podsumowując, potrzebujesz następujących elementów:

* Obszar roboczy usługi Log Analytics, aby włączyć to rozwiązanie.

* Agent usługi Log Analytics zainstalowany na komputerze z systemem Windows lub na serwerze Linux skonfigurowany do raportowania tego samego obszaru roboczego, w który włączono rozwiązanie.

* Agent zależności zainstalowany na komputerze z systemem Windows lub na serwerze Linux.

>[!NOTE]
>Jeśli mapa usługi została już wdrożona, możesz teraz również wyświetlać mapy w usłudze Azure Monitor dla maszyn wirtualnych, która zawiera dodatkowe funkcje monitorowania kondycji i wydajności maszyny wirtualnej. Aby dowiedzieć się więcej, zobacz [Azure Monitor dla maszyn wirtualnych omówienie](../../azure-monitor/insights/vminsights-overview.md). Aby dowiedzieć się więcej o różnicach między rozwiązaniem mapy usług a funkcją mapowanie usługi Azure Monitor dla maszyn wirtualnych, zobacz następujące [często zadawane pytania](../faq.md#azure-monitor-for-vms).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Włącz mapę usługi

1. Włącz rozwiązanie mapy usług z [witryny Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) lub za pomocą procesu opisanego w [Dodaj rozwiązania monitorowania z Galerii rozwiązań.](solutions.md)
1. [Zainstaluj agenta zależności w systemie Windows](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) lub zainstaluj [agenta zależności w systemie Linux na](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) każdym komputerze, na którym chcesz uzyskać dane. Agent Dependency Agent może monitorować połączenia do najbliższych sąsiadów, więc nie jest konieczny na każdym komputerze.

Dostęp do mapy usług w witrynie Azure portal z obszaru roboczego usługi Log Analytics i wybierz opcję **Rozwiązania** z lewego okienka.<br><br> ![Wybierz opcję Rozwiązania w](./media/service-map/select-solution-from-workspace.png)obszarze roboczym .<br> Z listy rozwiązań wybierz **servicemap(workspaceName)** i na stronie Przegląd rozwiązania mapy usług kliknij kafelek podsumowanie mapy usługi.<br><br> ![Kafelek](./media/service-map/service-map-summary-tile.png)podsumowania mapy usługi .

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Przypadki użycia: uświad swoją zależność procesów IT

### <a name="discovery"></a>Odnajdywanie

Mapa usługi automatycznie tworzy wspólną mapę odwołań zależności między serwerami, procesami i usługami innych firm. Odnajduje i mapuje wszystkie zależności TCP, identyfikując połączenia niespodzianka, zdalne systemy innych firm, od których jesteś zależny, i zależności od tradycyjnych ciemnych obszarów sieci, takich jak usługa Active Directory. Mapa usługi wykrywa nieudane połączenia sieciowe, które próbują nawiązać zarządzane systemy, co pomaga zidentyfikować potencjalne błędy konfiguracji serwera, awarię usługi i problemy z siecią.

### <a name="incident-management"></a>Zarządzanie zdarzeniami

Mapa usługi pomaga wyeliminować zgadywanie izolacji problemu, pokazując, jak systemy są połączone i wpływają na siebie nawzajem. Oprócz identyfikowania nieudanych połączeń pomaga zidentyfikować nieprawidłowo skonfigurowane moduły równoważenia obciążenia, zaskakujące lub nadmierne obciążenie usług krytycznych i nieuczciwych klientów, takich jak maszyny deweloperskie rozmawiające z systemami produkcyjnymi. Za pomocą zintegrowanych przepływów pracy z śledzenia zmian, można również zobaczyć, czy zdarzenie zmiany na komputerze zaplecza lub usługi wyjaśnia główną przyczynę zdarzenia.

### <a name="migration-assurance"></a>Zapewnienie migracji

Korzystając z mapy usług, można skutecznie planować, przyspieszać i weryfikować migracje platformy Azure, co pomaga zapewnić, że nic nie zostanie pozostawione i nieoczekiwane awarie nie wystąpią. Można odkryć wszystkie współzależne systemy, które muszą migrować razem, ocenić konfigurację i pojemność systemu oraz określić, czy uruchomiony system nadal obsługuje użytkowników, czy też jest kandydatem do likwidacji zamiast migracji. Po zakończeniu przenoszenia można sprawdzić obciążenie klienta i tożsamość, aby sprawdzić, czy systemy testowe i klienci łączą się. Jeśli problemy z planowaniem podsieci i definicjami zapory występują problemy, nieudane połączenia na mapach mapy usług wskazują systemy wymagające łączności.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej

Jeśli korzystasz z usługi Azure Site Recovery i potrzebujesz pomocy w definiowaniu sekwencji odzyskiwania dla środowiska aplikacji, mapa usługi może automatycznie pokazać, jak systemy polegają na sobie nawzajem, aby upewnić się, że plan odzyskiwania jest niezawodny. Wybierając krytyczny serwer lub grupę i wyświetlając jego klientów, można określić, które systemy frontonu mają zostać odzyskane po przywróceniu i udostępnieniu serwera. Z drugiej strony, patrząc na zależności zaplecza krytycznych serwerów, można zidentyfikować, które systemy do odzyskania przed systemami fokusu zostaną przywrócone.

### <a name="patch-management"></a>Zarządzanie poprawkami

Mapa usługi zwiększa wykorzystanie oceny aktualizacji systemu, pokazując, które inne zespoły i serwery zależą od usługi, dzięki czemu można powiadomić je z wyprzedzeniem, zanim zdejmiesz swoje systemy do poprawek. Mapa usługi zwiększa również zarządzanie poprawkami, pokazując, czy usługi są dostępne i prawidłowo połączone po ich załataniu i ponownym uruchomieniu.

## <a name="mapping-overview"></a>Omówienie mapowania

Agenci mapy usług zbierają informacje o wszystkich procesach połączonych z protokółem TCP na serwerze, na którym są zainstalowane, oraz szczegółowe informacje o połączeniach przychodzących i wychodzących dla każdego procesu.

Z listy w lewym okienku można wybrać maszyny lub grupy, które mają agentów mapy usług, aby wizualizować ich zależności w określonym zakresie czasu. Mapy zależności komputera koncentrują się na określonym komputerze i pokazują wszystkie maszyny, które są bezpośrednimi klientami TCP lub serwerami tego komputera.  Mapy grupy maszyn pokazują zestawy serwerów i ich zależności.

![Omówienie mapy usług](media/service-map/service-map-overview.png)

Maszyny można rozszerzyć na mapie, aby wyświetlić uruchomione grupy procesów i procesy z aktywnymi połączeniami sieciowymi w wybranym zakresie czasu. Gdy komputer zdalny z agentem mapy usług jest rozwinięty, aby wyświetlić szczegóły procesu, wyświetlane są tylko te procesy, które komunikują się z urządzeniem fokusu. Liczba bezagentowych maszyn front-end, które łączą się z maszyną ostrości, jest wskazywana po lewej stronie procesów, z którymi się łączą. Jeśli komputer fokusu nawiązuje połączenie z komputerem zaplecza, który nie ma agenta, serwer zaplecza jest uwzględniony w grupie portów serwera wraz z innymi połączeniami z tym samym numerem portu.

Domyślnie mapy mapy usług pokazują ostatnie 30 minut informacji o zależności. Za pomocą formantów czasu w lewym górnym rogu, można przeszukiwać mapy historycznych zakresów czasu do jednej godziny, aby pokazać, jak zależności wyglądały w przeszłości (na przykład podczas incydentu lub przed zmianą wystąpiła). Dane mapy usługi są przechowywane przez 30 dni w płatnych obszarach roboczych i przez 7 dni w wolnych obszarach roboczych.

## <a name="status-badges-and-border-coloring"></a>Identyfikatory statusu i kolorowanie obramowania

U dołu każdego serwera na mapie może znajdować się lista identyfikatorów stanu przekazujących informacje o stanie serwera. Plakietki wskazują, że istnieją pewne istotne informacje dla serwera z jednej z integracji rozwiązania. Kliknięcie plakietki prowadzi bezpośrednio do szczegółów stanu w prawym okienku. Aktualnie dostępne identyfikatory stanu obejmują alerty, service desk, zmiany, zabezpieczenia i aktualizacje.

W zależności od ważności identyfikatorów stanu obramowania węzłów maszyny mogą być oznaczone kolorem czerwonym (krytycznym), żółtym (ostrzeżenie) lub niebieskim (informacyjnym). Kolor reprezentuje najpoważniejszy stan dowolnej z odznak stanu. Szare obramowanie wskazuje węzeł, który nie ma wskaźników stanu.

![Odznaki statusu](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupy procesów

Grupy procesów łączą procesy skojarzone ze wspólnym produktem lub usługą w grupę procesów.  Po rozwinięciu węzła maszyny będą wyświetlane autonomiczne procesy wraz z grupami procesów.  Jeśli połączenia przychodzące i wychodzące z procesem w grupie procesów nie powiodły się, połączenie jest wyświetlane jako nieudane dla całej grupy procesów.

## <a name="machine-groups"></a>Grupy maszyn

Grupy maszyn umożliwiają wyświetlanie map wyśrodkowanych wokół zestawu serwerów, a nie tylko jednego, dzięki czemu można zobaczyć wszystkich członków wielowarstwowej aplikacji lub klastra serwerów na jednej mapie.

Użytkownicy wybierają serwery należące do grupy razem i wybierają nazwę grupy.  Następnie można wyświetlić grupę ze wszystkimi jej procesami i połączeniami lub wyświetlić ją tylko z procesami i połączeniami, które bezpośrednio odnoszą się do innych członków grupy.

![Grupa maszyn](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Tworzenie grupy maszyn

Aby utworzyć grupę, wybierz odpowiednią maszynę lub maszyny na liście Maszyny i kliknij przycisk **Dodaj do grupy**.

![Tworzenie grupy](media/service-map/machine-groups-create.png)

Tam możesz wybrać **pozycję Utwórz nowy** i nadać grupie nazwę.

![Grupa nazw](media/service-map/machine-groups-name.png)

>[!NOTE]
>Grupy komputerów są ograniczone do 10 serwerów.

### <a name="viewing-a-group"></a>Wyświetlanie grupy

Po utworzeniu niektórych grup możesz je wyświetlić, wybierając kartę Grupy.

![Karta Grupy](media/service-map/machine-groups-tab.png)

Następnie wybierz nazwę grupy, aby wyświetlić mapę dla tej grupy maszyn.
![Grupa](media/service-map/machine-group.png) maszyn Maszyny należące do grupy są opisane na mapie na biało.

Rozszerzenie grupy spowoduje wyświetlenie listy maszyn tworzących grupę maszyn.

![Maszyny grupy maszyn](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrowanie według procesów

Można przełączać widok mapy między wyświetlaniem wszystkich procesów i połączeń w grupie i tylko tych, które bezpośrednio odnoszą się do grupy maszyn.  Domyślnym widokiem jest pokazanie wszystkich procesów.  Widok można zmienić, klikając ikonę filtru nad mapą.

![Grupa filtrów](media/service-map/machine-groups-filter.png)

Po wybraniu **wszystkich procesów** mapa będzie zawierać wszystkie procesy i połączenia na każdej z maszyn w grupie.

![Grupa maszyn wszystkie procesy](media/service-map/machine-groups-all.png)

Jeśli widok zostanie zmienić, aby wyświetlić tylko **procesy połączone z grupą,** mapa zostanie zawężona tylko do tych procesów i połączeń, które są bezpośrednio połączone z innymi komputerami w grupie, tworząc widok uproszczony.

![Procesy filtrowane grupy maszyn](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Dodawanie maszyn do grupy

Aby dodać maszyny do istniejącej grupy, zaznacz pola wyboru obok żądanych maszyn, a następnie kliknij przycisk **Dodaj do grupy**.  Następnie wybierz grupę, do której chcesz dodać maszyny.
 
### <a name="removing-machines-from-a-group"></a>Usuwanie maszyn z grupy

Na liście grup rozwiń nazwę grupy, aby wyświetlić listę maszyn w grupie maszyn.  Następnie kliknij menu wielokropek obok urządzenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

![Usuwanie maszyny z grupy](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Usuwanie lub zmienianie nazwy grupy

Kliknij menu wielokropka obok nazwy grupy na liście grup.

![Menu grupy maszyn](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony ról

Niektóre procesy obsługują określone role na komputerach: serwery sieci web, serwery aplikacji, bazy danych i tak dalej. Mapa usługi adnotuje pola procesów i komputerów z ikonami ról, aby ułatwić identyfikację na pierwszy rzut oka roli, jaką odgrywa proces lub serwer.

| Ikona roli | Opis |
|:--|:--|
| ![Serwer sieci Web](media/service-map/role-web-server.png) | Serwer sieci Web |
| ![Serwer aplikacji](media/service-map/role-application-server.png) | Serwer aplikacji |
| ![Serwer bazy danych](media/service-map/role-database.png) | Serwer bazy danych |
| ![Serwer LDAP](media/service-map/role-ldap.png) | Serwer LDAP |
| ![Serwer SMB](media/service-map/role-smb.png) | Serwer SMB |

![Ikony ról](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Nieudane połączenia

Połączenia nie powiodły się są wyświetlane na mapach mapy usług dla procesów i komputerów, z przerywaną czerwoną linią wskazującą, że system kliencki nie może osiągnąć procesu lub portu. Połączenia, które nie powiodły się, są zgłaszane z dowolnego systemu z wdrożonym agentem mapy usługi, jeśli ten system jest tym, który próbuje połączenia, którego nie powiodło się. Mapa usługi mierzy ten proces, obserwując gniazda TCP, które nie mogą ustanowić połączenia. Ten błąd może wynikać z zapory, błędnej konfiguracji na kliencie lub serwerze lub niedostępnej usługi zdalnej.

![Nieudane połączenia](media/service-map/failed-connections.png)

Zrozumienie połączeń nie powiodło się może pomóc w rozwiązywaniu problemów, sprawdzania poprawności migracji, analizy zabezpieczeń i ogólnej zrozumienia architektury. Połączenia awaryjne są czasami nieszkodliwe, ale często wskazują bezpośrednio na problem, taki jak środowisko trybu failover nagle staje się nieosiągalne lub dwie warstwy aplikacji nie mogą rozmawiać po migracji do chmury.

## <a name="client-groups"></a>Grupy klientów

Grupy klientów są pola na mapie, które reprezentują komputery klienckie, które nie mają agentów zależności. Pojedyncza grupa klientów reprezentuje klientów dla poszczególnych procesów lub komputerów.

![Grupy klientów](media/service-map/client-groups.png)

Aby wyświetlić adresy IP serwerów w grupie klientów, wybierz grupę. Zawartość grupy jest wyświetlana w okienku **Właściwości grupy klientów.**

![Właściwości grupy klientów](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupy portów serwera

Grupy portów serwera to pola reprezentujące porty serwerów na serwerach, które nie mają agentów zależności. Pole zawiera port serwera i liczbę serwerów z połączeniami z tym portem. Rozwiń to pole, aby wyświetlić poszczególne serwery i połączenia. Jeśli w polu znajduje się tylko jeden serwer, nazwa lub adres IP jest wyświetlany.

![Grupy portów serwera](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu Kontekst

Kliknięcie wielokropek (...) w prawym górnym rogu dowolnego serwera powoduje wyświetlenie menu kontekstowego dla tego serwera.

![Nieudane połączenia](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Załaduj mapę serwera

Kliknięcie **przycisku Załaduj mapę serwera** prowadzi do nowej mapy z wybranym serwerem jako nowym komputerem fokusowym.

### <a name="show-self-links"></a>Pokaż łącza samopołączne

Kliknięcie **przycisku Pokaż łącza samopołączeniowe** ponownie rysuje węzeł serwera, w tym wszelkie łącza samopołączeniowe, które są połączeniami TCP, które rozpoczynają się i kończą w procesach na serwerze. Jeśli są wyświetlane łącza samopołączone, polecenie menu zmieni się na **Ukryj łącza samopołączone,** dzięki czemu można je wyłączyć.

## <a name="computer-summary"></a>Podsumowanie komputera

Okienko **Podsumowanie komputera** zawiera omówienie systemu operacyjnego serwera, liczby zależności i danych z innych rozwiązań. Takie dane obejmują metryki wydajności, bilety w punktach obsługi, śledzenie zmian, zabezpieczenia i aktualizacje.

![Okienko Podsumowanie maszyny](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Właściwości komputera i procesu

Podczas nawigacji na mapie mapy usług, można wybrać maszyny i procesy, aby uzyskać dodatkowy kontekst o ich właściwości. Maszyny zawierają informacje o nazwie DNS, adresach IPv4, pojemności procesora i pamięci, typie maszyny Wirtualnej, systemie operacyjnym i wersji, czasie ostatniego ponownego uruchomienia oraz identyfikatorach agentów OMS i Mapy usług.

![Okienko Właściwości maszyny](media/service-map/machine-properties.png)

Możesz zbierać szczegóły procesu z metadanych systemu operacyjnego dotyczących uruchomionych procesów, w tym nazwę procesu, opis procesu, nazwę użytkownika i domenę (w systemie Windows), nazwę firmy, nazwę produktu, wersję produktu, katalog roboczy, wiersz polecenia i proces czasu rozpoczęcia.

![Okienko Właściwości procesu](media/service-map/process-properties.png)

Okienko **Podsumowanie procesu** zawiera dodatkowe informacje o łączności procesu, w tym jego porty powiązane, połączenia przychodzące i wychodzące oraz połączenia nie powiodły się.

![Okienko Podsumowanie procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integracja alertów

Mapa usługi integruje się z alertami platformy Azure, aby wyświetlić odpalone alerty dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikonę, jeśli istnieją bieżące alerty, a okienko **Alerty maszyny** wyświetla listę alertów.

![Okienko Alerty maszyn](media/service-map/machine-alerts.png)

Aby włączyć mapę usług do wyświetlania odpowiednich alertów, utwórz regułę alertu, która jest uruchamiana dla określonego komputera. Aby utworzyć odpowiednie alerty:
- Dołącz klauzulę do grupowania według komputera (na przykład **przez interwał komputera 1 minuta).**
- Wybierz alert na podstawie pomiaru metryki.

## <a name="log-events-integration"></a>Rejestrowanie integracji zdarzeń

Mapa usługi integruje się z wyszukiwaniem dzienników, aby wyświetlić liczbę wszystkich dostępnych zdarzeń dziennika dla wybranego serwera w wybranym zakresie czasu. Możesz kliknąć dowolny wiersz na liście zliczeń zdarzeń, aby przejść do wyszukiwania dziennika i wyświetlić poszczególne zdarzenia dziennika.

![Okienko Zdarzenia dziennika maszyn](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integracja z biurem serwisowym

Integracja mapy usług ze łącznikiem zarządzania usługami IT jest automatyczna, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics. Integracja z mapą usług jest oznaczona jako "Service Desk". Aby uzyskać więcej informacji, zobacz [Centralne zarządzanie elementami roboczymi ITSM przy użyciu łącznika zarządzania usługami IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

W **okienku Biurko usługi maszynowej** jest wyświetlane wszystkie zdarzenia zarządzania usługami IT dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikonę, jeśli istnieją bieżące elementy, a okienko Usługi obsługi komputera wyświetla je z ich listą.

![Okienko Biurko obsługi maszyny](media/service-map/service-desk.png)

Aby otworzyć element w podłączonym rozwiązaniu ITSM, kliknij pozycję **Wyświetl element roboczy**.

Aby wyświetlić szczegóły elementu w wyszukiwaniu dziennika, kliknij pozycję **Pokaż w wyszukiwaniu w dzienniku**.
Metryki połączenia są zapisywane w dwóch nowych tabelach w usłudze Log Analytics 

## <a name="change-tracking-integration"></a>Integracja śledzenia zmian

Integracja mapy usług z śledzeniem zmian jest automatyczna, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

Okienko **Śledzenie zmian maszyn** zawiera listę wszystkich zmian, z najnowszymi pierwszymi, wraz z łączem do szczegółów wyszukiwania dziennika, aby uzyskać dodatkowe szczegóły.

![Okienko śledzenia zmian maszyny](media/service-map/change-tracking.png)

Poniższy obraz przedstawia szczegółowy widok zdarzenia ConfigurationChange, które może być widoczne po wybraniu opcji **Pokaż w usłudze Log Analytics**.

![Zdarzenie ConfigurationChange](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integracja wydajności

W **okienku Wydajność komputera** są wyświetlane standardowe dane dotyczące wydajności dla wybranego serwera. Metryki obejmują wykorzystanie procesora CPU, wykorzystanie pamięci, bajty sieciowe wysyłane i odbierane oraz listę najważniejszych procesów według bajtów sieciowych wysyłanych i odbieranych.

![Okienko Wydajność komputera](media/service-map/machine-performance.png)

Aby wyświetlić dane dotyczące wydajności, może być konieczne [włączenie odpowiednich liczników wydajności usługi Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)  Liczniki, które chcesz włączyć:

W systemie Windows:
- Procesor(*)\\% czasu procesora
- Używane\\bajty popełnione za pomocą % pamięci
- Wysłane bajty\\karty sieciowej(*) na sekundę
- Odebrane bajty\\karty sieciowej(*) na sekundę

W systemie Linux:
- Procesor(*)\\% czasu procesora
- Pamięć(*)\\% używanej pamięci
- Wysłane bajty\\karty sieciowej(*) na sekundę
- Odebrane bajty\\karty sieciowej(*) na sekundę

Aby uzyskać dane o wydajności sieci, musisz również włączyć rozwiązanie Wire Data 2.0 w obszarze roboczym.
 
## <a name="security-integration"></a>Integracja zabezpieczeń

Integracja mapy usług z zabezpieczeniami i inspekcją jest automatyczna, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

Okienko **Zabezpieczenia komputera** zawiera dane z rozwiązania Zabezpieczenia i Inspekcja dla wybranego serwera. Okienko zawiera podsumowanie wszelkich nierozstrzygniętych problemów z zabezpieczeniami serwera w wybranym zakresie czasu. Kliknięcie dowolnego z problemów z zabezpieczeniami przechodzi w dół do wyszukiwania w dzienniku, aby uzyskać szczegółowe informacje o nich.

![Okienko Zabezpieczenia komputera](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integracja aktualizacji

Integracja mapy usługi z zarządzaniem aktualizacjami jest automatyczna, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

W **okienku Aktualizacje komputera** są wyświetlane dane z rozwiązania Zarządzanie aktualizacjami dla wybranego serwera. Okienko zawiera podsumowanie brakujących aktualizacji dla serwera w wybranym zakresie czasu.

![Okienko śledzenia zmian maszyny](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics

Komputer z mapą usługi i dane spisu przetwarzania są dostępne do [wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics. Te dane można zastosować do scenariuszy, które obejmują planowanie migracji, analizę zdolności produkcyjnych, odnajdowanie i rozwiązywanie problemów z wydajnością na żądanie.

Jeden rekord jest generowany na godzinę dla każdego unikatowego komputera i procesu, oprócz rekordów, które są generowane, gdy proces lub komputer jest uruchamiany lub jest wbudowany w mapę usługi. Te rekordy mają właściwości w poniższych tabelach. Pola i wartości w ServiceMapComputer_CL zdarzenia są mapowane do pól zasobu maszyny w interfejsie API usługi ServiceMap Azure Resource Manager. Pola i wartości w ServiceMapProcess_CL zdarzenia są mapowane do pól zasobu Proces w interfejsie API usługi ServiceMap Azure Resource Manager. Pole ResourceName_s jest zgodne z polem nazwy w odpowiednim zasobie Menedżera zasobów. 

>[!NOTE]
>Wraz ze wzrostem elementów mapy usług te pola mogą ulec zmianie.

Istnieją wewnętrznie wygenerowane właściwości, których można używać do identyfikowania unikatowych procesów i komputerów:

- Komputer: użyj *identyfikatora resourceid* lub *ResourceName_s,* aby jednoznacznie zidentyfikować komputer w obszarze roboczym usługi Log Analytics.
- Proces: użyj *identyfikatora resourceid,* aby jednoznacznie zidentyfikować proces w obszarze roboczym usługi Log Analytics. *ResourceName_s* jest unikatowa w kontekście maszyny, na której działa proces (MachineResourceName_s) 

Ponieważ dla określonego procesu i komputera w określonym zakresie czasu może istnieć wiele rekordów, kwerendy mogą zwracać więcej niż jeden rekord dla tego samego komputera lub procesu. Aby dołączyć tylko najnowszy rekord, dodaj "| dedup ResourceId" do kwerendy.

### <a name="connections"></a>Połączenia

Metryki połączeń są zapisywane w nowej tabeli w usłudze Log Analytics — VMConnection. Ta tabela zawiera informacje o połączeniach dla komputera (przychodzące i wychodzące). Metryki połączenia są również udostępniane za pomocą interfejsów API, które zapewniają środki do uzyskania określonej metryki w okresie czasu.  Połączenia TCP wynikające z akceptowania na gnieździe nasłuchiwania są przychodzące, podczas gdy połączenia z danym adresem IP i portem są wychodzące. Kierunek połączenia jest reprezentowany przez właściwość Direction, którą można ustawić na **przychodzącą** lub **wychodzącą**. 

Rekordy w tych tabelach są generowane na podstawie danych zgłoszonych przez agenta zależności. Każdy rekord reprezentuje obserwację w odstępie jednej minuty. TimeGenerated Właściwość wskazuje początek przedziału czasu. Każdy rekord zawiera informacje umożliwiające identyfikację odpowiedniej jednostki, czyli połączenia lub portu, a także metryki skojarzone z tą encją. Obecnie zgłaszana jest tylko aktywność sieciowa występująca przy użyciu protokołu TCP przez protokół IPv4.

Aby zarządzać kosztami i złożonością, rekordy połączeń nie reprezentują pojedynczych fizycznych połączeń sieciowych. Wiele fizycznych połączeń sieciowych jest grupowanych w połączenie logiczne, które jest następnie odzwierciedlane w odpowiedniej tabeli.  Co oznacza, że rekordy w tabeli *VMConnection* reprezentują logiczne grupowanie, a nie poszczególne połączenia fizyczne, które są obserwowane. Fizyczne połączenie sieciowe dzielące tę samą wartość dla następujących atrybutów w danym interwału jednominutowym jest agregowane w jeden rekord logiczny w *vmconnection*. 

| Właściwość | Opis |
|:--|:--|
| `Direction` |Kierunek połączenia, wartość jest *przychodzące* lub *wychodzące* |
| `Machine` |Nazwa FQDN komputera |
| `Process` |Tożsamość procesu lub grup procesów, inicjowanie/akceptowanie połączenia |
| `SourceIp` |Adres IP źródła |
| `DestinationIp` |Adres IP miejsca docelowego |
| `DestinationPort` |Numer portu miejsca docelowego |
| `Protocol` |Protokół używany do połączenia.  Wartości to *tcp*. |

Aby uwzględnić wpływ grupowania, informacje o liczbie zgrupowanych połączeń fizycznych znajdują się w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `LinksEstablished` |Liczba fizycznych połączeń sieciowych, które zostały nawiązane w oknie czasu raportowania |
| `LinksTerminated` |Liczba fizycznych połączeń sieciowych, które zostały zakończone w oknie czasu raportowania |
| `LinksFailed` |Liczba fizycznych połączeń sieciowych, które nie powiodły się w czasie raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
| `LinksLive` |Liczba fizycznych połączeń sieciowych, które były otwarte na końcu okna czasu raportowania|

#### <a name="metrics"></a>Metryki

Oprócz danych liczby połączeń informacje o ilości danych wysyłanych i odbieranych na danym połączeniu logicznym lub porcie sieciowym są również zawarte w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `BytesSent` |Całkowita liczba bajtów wysłanych w okresie raportowania |
| `BytesReceived` |Całkowita liczba bajtów odebranych w okresie raportowania |
| `Responses` |Liczba odpowiedzi zaobserwowanych w okresie raportowania. 
| `ResponseTimeMax` |Największy czas odpowiedzi (milisekundy) obserwowany w oknie czasu raportowania.  Jeśli nie ma wartości, właściwość jest pusta.|
| `ResponseTimeMin` |Najmniejszy czas odpowiedzi (milisekundy) obserwowany w oknie czasu raportowania.  Jeśli nie ma wartości, właściwość jest pusta.|
| `ResponseTimeSum` |Suma wszystkich czasów odpowiedzi (milisekund) obserwowanych w oknie czasu raportowania.  Jeśli nie ma wartości, właściwość jest pusta|

Trzeci typ danych zgłaszanych jest czas odpowiedzi — jak długo obiekt wywołujący spędza oczekiwanie na żądanie wysłane za pomocą połączenia do przetworzenia i odpowiedzi na przez zdalny punkt końcowy. Zgłoszony czas odpowiedzi jest oszacowaniem rzeczywistego czasu odpowiedzi leżącego u podstaw protokołu aplikacji. Jest obliczany przy użyciu heurystyki na podstawie obserwacji przepływu danych między źródłem a docelowym końcem fizycznego połączenia sieciowego. Koncepcyjnie jest to różnica między czasem ostatniego bajtu żądania pozostawia nadawcy, a godziną, kiedy ostatni bajt odpowiedzi powraca do niego. Te dwa sygnatury czasowe są używane do wytyczyć zdarzenia żądania i odpowiedzi na danym połączeniu fizycznym. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W tej pierwszej wersji tej funkcji nasz algorytm jest przybliżeniem, które może działać z różnym powodzeniem w zależności od rzeczywistego protokołu aplikacji używanego dla danego połączenia sieciowego. Na przykład bieżące podejście działa dobrze dla protokołów opartych na odpowiedzi na żądanie, takich jak HTTP(S), ale nie działa z protokołami opartymi na kolejkach jednokierunkowych lub wiadomości.

Oto kilka ważnych kwestii, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia na tym samym adresie IP, ale za pośrednictwem wielu interfejsów sieciowych, zostanie zgłoszony osobny rekord dla każdego interfejsu. 
2. Rekordy z wieloznacznym adresem IP nie będą zawierać żadnych działań. Są one włączone do reprezentowania faktu, że port na komputerze jest otwarty dla ruchu przychodzącego.
3. Aby zmniejszyć szczegółowość i ilość danych, rekordy z wieloznacznym adresem IP zostaną pominięte, gdy istnieje pasujący rekord (dla tego samego procesu, portu i protokołu) z określonym adresem IP. Po pominięciu rekordu IP symboli wieloznacznych właściwość rekordu IsWildcardBind z określonym adresem IP zostanie ustawiona na "True", aby wskazać, że port jest narażony na każdy interfejs komputera raportowania.
4. Porty, które są powiązane tylko w określonym interfejsie mają IsWildcardBind ustawiony na "False".

#### <a name="naming-and-classification"></a>Nazewnictwo i klasyfikacja

Dla wygody adres IP zdalnego końca połączenia znajduje się we właściwości RemoteIp. W przypadku połączeń przychodzących remoteip jest taki sam jak SourceIp, podczas gdy dla połączeń wychodzących jest taki sam jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje nazwy kanoniczne DNS zgłaszane przez komputer dla usługi RemoteIp. Właściwości RemoteDnsQuestions i RemoteClassification są zarezerwowane do wykorzystania w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja

*VMConnection* zawiera również informacje geolokalizacji dla zdalnego końca każdego rekordu połączenia w następujących właściwościach rekordu: 

| Właściwość | Opis |
|:--|:--|
| `RemoteCountry` |Nazwa kraju/regionu obsługującego program RemoteIp.  Na przykład Stany *Zjednoczone* |
| `RemoteLatitude` |Szerokość geograficzna.  Na przykład *47,68* |
| `RemoteLongitude` |Długość geograficzna.  Na przykład *-122.12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP

Każda właściwość RemoteIp w tabeli *VMConnection* jest sprawdzana względem zestawu adresów IP ze znanymi złośliwymi działaniami. Jeśli RemoteIp zostanie zidentyfikowany jako złośliwy, następujące właściwości zostaną wypełnione (są puste, gdy adres IP nie jest uważany za złośliwy) w następujących właściwościach rekordu:

| Właściwość | Opis |
|:--|:--|
| `MaliciousIp` |Adres RemoteIp |
| `IndicatorThreadType` |Wykryto wskaźnik zagrożenia jest jedną z następujących wartości, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
| `Description` |Opis obserwowanego zagrożenia. |
| `TLPLevel` |Poziom protokołu sygnalizacji świetlnej (TLP) jest jedną z zdefiniowanych wartości: *Biały,* *Zielony,* *Bursztynowy,* *Czerwony.* |
| `Confidence` |Wartości to *od 0 do 100*. |
| `Severity` |Wartości to *0 – 5*, gdzie *5* jest najcięższy, a *0* wcale nie jest dotkliwe. Wartość domyślna to *3*.  |
| `FirstReportedDateTime` |Po raz pierwszy dostawca zgłosił wskaźnik. |
| `LastReportedDateTime` |Ostatni raz wskaźnik był widziany przez Interflow. |
| `IsActive` |Wskazuje, że wskaźniki są dezaktywowane wartością *Prawda* lub *Fałda.* |
| `ReportReferenceLink` |Linki do raportów związanych z danym obserwowalny. |
| `AdditionalInformation` |Zawiera dodatkowe informacje, jeśli dotyczy, o obserwowanym zagrożeniu. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL rekordy

Rekordy z typem *ServiceMapComputer_CL* mają dane spisu dla serwerów z agentami mapy usług. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator komputera w obszarze roboczym |
| `ResourceName_s` | Unikatowy identyfikator komputera w obszarze roboczym |
| `ComputerName_s` | Nazwa FQDN komputera |
| `Ipv4Addresses_s` | Lista adresów IPv4 serwera |
| `Ipv6Addresses_s` | Lista adresów IPv6 serwera |
| `DnsNames_s` | Tablica nazw DNS |
| `OperatingSystemFamily_s` | Windows lub Linux |
| `OperatingSystemFullName_s` | Pełna nazwa systemu operacyjnego  |
| `Bitness_s` | Bitness maszyny (32-bitowe lub 64-bitowe)  |
| `PhysicalMemory_d` | Pamięć fizyczna w MB |
| `Cpus_d` | Liczba procesorów |
| `CpuSpeed_d` | Szybkość procesora w MHz|
| `VirtualizationState_s` | *nieznany*, *fizyczny,* *wirtualny*, *hipernadzorcy* |
| `VirtualMachineType_s` | *hyperv*, *vmware*, i tak dalej |
| `VirtualMachineNativeMachineId_g` | Identyfikator maszyny Wirtualnej przypisany przez jego hipernadzorcy |
| `VirtualMachineName_s` | Nazwa maszyny Wirtualnej |
| `BootTime_t` | Czas rozruchu |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL rekordy typu

Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesów połączonych z TCP na serwerach z agentami mapy usług. Te rekordy mają właściwości w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator procesu w obszarze roboczym |
| `ResourceName_s` | Unikatowy identyfikator procesu w obrębie komputera, na którym jest uruchomiony|
| `MachineResourceName_s` | Nazwa zasobu urządzenia |
| `ExecutableName_s` | Nazwa pliku wykonywalnego procesu |
| `StartTime_t` | Czas rozpoczęcia puli procesów |
| `FirstPid_d` | Pierwszy PID w puli procesów |
| `Description_s` | Opis procesu |
| `CompanyName_s` | Nazwa firmy |
| `InternalName_s` | Nazwa wewnętrzna |
| `ProductName_s` | Nazwa produktu |
| `ProductVersion_s` | Wersja produktu |
| `FileVersion_s` | Wersja pliku |
| `CommandLine_s` | Wiersz polecenia |
| `ExecutablePath _s` | Ścieżka do pliku wykonywalnego |
| `WorkingDirectory_s` | Katalog roboczy |
| `UserName` | Konto, na którym wykonywany jest proces |
| `UserDomain` | Domena, w której proces jest wykonywany |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Lista wszystkich znanych maszyn

ServiceMapComputer_CL | podsumuj arg_max(Czasgenerowany, *) według identyfikatora zasobu

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę pojemności pamięci fizycznej wszystkich zarządzanych komputerów.

ServiceMapComputer_CL | podsumuj arg_max(TimeGenerated, *) według Identyfikatora zasobu | PhysicalMemory_d projektowe, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Wyświetl nazwę komputera, dns, ip i system operacyjny.

ServiceMapComputer_CL | podsumuj arg_max(TimeGenerated, *) według Identyfikatora zasobu | ComputerName_s projektowe, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy z "sql" w wierszu polecenia

ServiceMapProcess_CL | gdzie CommandLine_s contains_cs "sql" | podsumuj arg_max(Czasgenerowany, *) według identyfikatora zasobu

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdowanie komputera (najnowszego rekordu) według nazwy zasobu

szukaj w (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | podsumuj arg_max(Czasgenerowany, *) według identyfikatora zasobu

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdowanie komputera (najnowszego rekordu) według adresu IP

wyszukiwanie w (ServiceMapComputer_CL) "10.229.243.232" | podsumuj arg_max(Czasgenerowany, *) według identyfikatora zasobu

### <a name="list-all-known-processes-on-a-specified-machine"></a>Wyświetlanie listy wszystkich znanych procesów na określonym komputerze

ServiceMapProcess_CL | gdzie MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | podsumuj arg_max(Czasgenerowany, *) według identyfikatora zasobu

### <a name="list-all-computers-running-sql"></a>Lista wszystkich komputerów z sql

ServiceMapComputer_CL | gdzie ResourceName_s w ((szukaj w (ServiceMapProcess_CL)\*\*" sql " | odrębne MachineResourceName_s)) | odrębne ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetl listę wszystkich unikatowych wersji produktu curl w moim centrum danych

ServiceMapProcess_CL | gdzie ExecutableName_s == "curl" | odrębne ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Tworzenie grupy komputerów wszystkich komputerów z systemem CentOS

ServiceMapComputer_CL | gdzie OperatingSystemFullName_s contains_cs "CentOS" | odrębne ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumowywanie połączeń wychodzących z grupy komputerów

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>Interfejs API REST

Wszystkie dane serwera, procesu i zależności w mapie usługi są dostępne za pośrednictwem [interfejsu API REST mapy usług.](https://docs.microsoft.com/rest/api/servicemap/)

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dotyczące użytkowania

Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności za pośrednictwem usługi Mapa usługi. Firma Microsoft używa tych danych do dostarczania i poprawy jakości, zabezpieczeń i integralności usługi Mapy usług. Aby zapewnić dokładne i wydajne możliwości rozwiązywania problemów, dane zawierają informacje o konfiguracji oprogramowania, takie jak system operacyjny i wersja, adres IP, nazwa DNS i nazwa stacji roboczej. Firma Microsoft nie gromadzi nazw, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat gromadzenia i używania danych, zobacz [Zasady zachowania poufności informacji dotyczące usług online firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wyszukiwaniach dzienników](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics w celu pobrania danych zebranych przez mapę usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli masz jakiekolwiek problemy z instalacją lub uruchomieniem mapy usług, ta sekcja może Ci pomóc. Jeśli nadal nie możesz rozwiązać problemu, skontaktuj się z pomocą techniczną firmy Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problemy z instalacją agenta zależności

#### <a name="installer-prompts-for-a-reboot"></a>Instalator monituje o ponowne uruchomienie
Agent zależności *zazwyczaj* nie wymaga ponownego uruchomienia po instalacji lub usunięciu. Jednak w niektórych rzadkich przypadkach system Windows Server wymaga ponownego uruchomienia, aby kontynuować instalację. Dzieje się tak, gdy zależność, zwykle biblioteka redystrybucjowa programu Microsoft Visual C++ wymaga ponownego uruchomienia z powodu zablokowanego pliku.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>Zostanie wyświetlony komunikat "Nie można zainstalować agenta zależności: nie można zainstalować bibliotek środowiska wykonawczego programu Visual Studio (kod = [code_number])"

Agent zależności firmy Microsoft jest zbudowany na bibliotekach środowiska wykonawczego programu Microsoft Visual Studio. Jeśli wystąpi problem podczas instalacji bibliotek, zostanie wyświetlony komunikat. 

Instalatorzy biblioteki wykonawczej tworzą dzienniki w folderze %LOCALAPPDATA%\temp. Plik jest `dd_vcredist_arch_yyyymmddhhmmss.log`, gdzie `x86` `amd64` *arch* jest lub *yyyymmddhhmmss* jest data i godzina (zegar 24-godzinny) po utworzeniu dziennika. Dziennik zawiera szczegółowe informacje o problemie, który blokuje instalację.

Może być przydatne najpierw zainstalować [najnowsze biblioteki środowiska wykonawczego.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

W poniższej tabeli wymieniono numery kodowe i sugerowane rozwiązania.

| Code | Opis | Rozwiązanie |
|:--|:--|:--|
| 0x17 | Instalator biblioteki wymaga aktualizacji systemu Windows, która nie została zainstalowana. | Poszukaj w najnowszym dzienniku instalatora biblioteki.<br><br>Jeśli po `Windows8.1-KB2999226-x64.msu` odwołań następuje `Error 0x80240017: Failed to execute MSU package,` wiersz, nie masz wymagań wstępnych do zainstalowania kb2999226. Postępuj zgodnie z instrukcjami w sekcji wymagań wstępnych w [uniwersalnym czasie działania C w](https://support.microsoft.com/kb/2999226) systemie Windows. Aby zainstalować wymagania wstępne, może być konieczne uruchomienie usługi Windows Update i ponowne uruchomienie komputera.<br><br>Ponownie uruchom instalatora agenta zależności firmy Microsoft. |

### <a name="post-installation-issues"></a>Problemy po instalacji

#### <a name="server-doesnt-appear-in-service-map"></a>Serwer nie jest wyświetlany na mapie usługi

Jeśli instalacja agenta zależności powiodła się, ale nie widać komputera w rozwiązaniu mapy usług:
* Czy agent zależności jest pomyślnie zainstalowany? Można to sprawdzić, sprawdzając, czy usługa jest zainstalowana i uruchomiona.<br><br>
**Windows**: Poszukaj usługi o nazwie **Agent zależności firmy Microsoft**.
**Linux**: Poszukaj uruchomionego procesu **microsoft-dependency-agent**.

* Czy jesteś w [warstwie bezpłatnej usługi Log Analytics?](https://azure.microsoft.com/pricing/details/monitor/) Bezpłatny plan pozwala na maksymalnie pięć unikalnych maszyn mapy serwisowej. Wszystkie kolejne maszyny nie będą wyświetlane na mapie usług, nawet jeśli poprzednie pięć nie są już wysyłanie danych.

* Czy twój serwer wysyła dane dziennika i perf do dzienników usługi Azure Monitor? Przejdź do usługi Azure Monitor\Dzienniki i uruchom następującą kwerendę dla swojego komputera: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Czy masz różne wydarzenia w wynikach? Czy dane są najnowsze? Jeśli tak, agent usługi Log Analytics działa poprawnie i komunikuje się z obszarem roboczym. Jeśli nie, sprawdź agenta na komputerze: [Agent analizy dzienników dla systemu Windows rozwiązywania problemów](../platform/agent-windows-troubleshoot.md) lub [agenta analizy dzienników dla systemu Linux rozwiązywania problemów](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Serwer pojawia się w mapie usługi, ale nie ma żadnych procesów

Jeśli komputer jest widoczny na mapie usługi, ale nie ma danych procesowych ani danych połączenia, oznacza to, że agent zależności jest zainstalowany i uruchomiony, ale sterownik jądra nie został załadowany. 

Sprawdź `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` (Windows) `/var/opt/microsoft/dependency-agent/log/service.log file` lub (Linux). Ostatnie wiersze pliku powinny wskazywać, dlaczego jądro nie załadowane. Na przykład jądro może nie być obsługiwane w systemie Linux, jeśli jądro zostanie zaktualizowane.

## <a name="feedback"></a>Opinia

Czy masz dla nas jakieś opinie na temat mapy usług lub tej dokumentacji?  Odwiedź naszą [stronę Głos użytkownika,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)gdzie możesz zaproponować funkcje lub zagłosować na istniejące sugestie.
