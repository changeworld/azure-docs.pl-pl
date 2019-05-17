---
title: Na platformie Azure przy użyciu rozwiązania mapy usługi | Dokumentacja firmy Microsoft
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące wdrażania rozwiązania Service Map w danym środowisku i korzystania z niego w różnych scenariuszach.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2018
ms.author: magoedte
ms.openlocfilehash: 09755922da78a3e856c491c01ce9f34f50063d71
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606508"
---
# <a name="using-service-map-solution-in-azure"></a>Za pomocą rozwiązania Service Map na platformie Azure
Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Dzięki usłudze Service Map można wyświetlać serwery w sposób, w jakich się o nich myśli: jako wzajemnie połączone systemy dostarczające usługi. Usługa ta pokazuje połączenia między serwerami i procesami, opóźnienie połączeń przychodzących i wychodzących oraz porty dla każdej architektury połączonej za pomocą protokołu TCP. Nie jest wymagana żadna konfiguracja z wyjątkiem zainstalowania agenta.

W tym artykule opisano szczegóły dotyczące dołączania i przy użyciu mapy usługi. Aby uzyskać informacje dotyczące konfigurowania rozwiązania Service Map i dołączania agentów, zobacz [konfigurowania rozwiązania Service Map rozwiązanie na platformie Azure]( service-map-configure.md).

>[!NOTE]
>Jeśli masz już wdrożone rozwiązania Service Map, można teraz również wyświetlić map w usłudze Azure Monitor dla maszyn wirtualnych, który zawiera dodatkowe funkcje, aby monitorować wydajność i kondycja maszyny Wirtualnej. Aby dowiedzieć się więcej, zobacz [usługi Azure Monitor dla maszyn wirtualnych — omówienie](../../azure-monitor/insights/vminsights-overview.md).


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-service-map"></a>Włączanie mapy usługi
1. W witrynie Azure portal kliknij pozycję **+ Utwórz zasób**.
2. Na pasku wyszukiwania wpisz **rozwiązania Service Map** i naciśnij klawisz **Enter**.
3. Na stronie wyników wyszukiwania portalu marketplace wybierz **rozwiązania Service Map** z listy.<br><br> ![Wybierz rozwiązanie mapy usługi z wyników wyszukiwania w portalu Azure Marketplace](./media/service-map/marketplace-search-results.png)<br>
4. Na **rozwiązania Service Map** okienko omówienia, przejrzyj szczegóły rozwiązania, a następnie kliknij przycisk **Utwórz** do rozpoczęcia procesu dołączania do obszaru roboczego usługi Log Analytics.<br><br> ![Dołączanie rozwiązania mapy usługi](./media/service-map/service-map-onboard.png).
5. W **skonfiguruj rozwiązanie** okienku wybierz istniejącą lub Utwórz nowy obszar roboczy usługi Log Analytics.  Aby uzyskać więcej informacji na temat tworzenia nowego obszaru roboczego, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../../azure-monitor/learn/quick-create-workspace.md). Po podaniu wymaganych informacji, kliknij przycisk **Utwórz**.  

Podczas weryfikowania informacji i rozwiązanie jest wdrożone, możesz śledzić postęp w sekcji **powiadomienia** z menu. 

Dostęp do rozwiązania Service Map w witrynie Azure portal w obszarze roboczym usługi Log Analytics i wybierz opcję **rozwiązania** z okienka po lewej stronie.<br><br> ![Wybierz opcję rozwiązania w obszarze roboczym](./media/service-map/select-solution-from-workspace.png).<br> Wybierz z listy rozwiązań **ServiceMap(workspaceName)** w rozwiązania Service Map rozwiązania Przegląd kliknij Kafelek podsumowanie mapy usługi.<br><br> ![Kafelek podsumowania rozwiązania Service Map](./media/service-map/service-map-summary-tile.png).

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Przypadki użycia: Wprowadź dział IT przetwarza pamiętać zależności

### <a name="discovery"></a>Odnajdowanie
Usługa Service Map automatycznie tworzy mapę typowych odwołanie zależności między serwerami, procesami i usługami innych firm. Go umożliwia odnalezienie i mapuje wszystkie zależności TCP w celu identyfikowania połączeń Zaskoczenie, zdalne systemów innych firm, których zależysz i zależności do tradycyjnych ciemny obszarów sieci, takich jak usługi Active Directory. Usługa Service Map odnajduje połączenia sieciowe nie powiodło się, które próbujesz zarządzanych systemach, aby ułatwia identyfikowanie potencjalnych błędnej konfiguracji serwera, przerwa w działaniu usługi i problemy z siecią.

### <a name="incident-management"></a>Zarządzanie zdarzeniami
Usługa Service Map, pomaga wyeliminować sytuację wątpliwości podczas określania przyczyn problemów, pokazujący, jak są połączone systemy i wpływu na siebie nawzajem. Oprócz identyfikowania połączenia zakończone niepowodzeniem, pomaga zidentyfikować równoważenia obciążenia nieprawidłowo, Zaskakujące lub nadmierne obciążenie usług krytycznych i nieautoryzowane klientów, takich jak komputery deweloperskie z rozmów na systemy produkcyjne. Przy użyciu przepływów pracy zintegrowane śledzenie zmian, możesz też sprawdzić czy zdarzenia zmiany w komputerze zaplecza lub usługa opisano przyczyny wystąpienia zdarzenia.

### <a name="migration-assurance"></a>Zapewnienie migracji
Za pomocą rozwiązania Service Map, mogą efektywnie planować, Przyspiesz i sprawdzanie poprawności migracji na platformę Azure, co ułatwia, upewnij się, że nic nie pozostawione nie występują awarie Zaskoczenie. Może odnajdywać wszystkie współzależne systemy, które trzeba migrowane razem, oceny konfiguracji systemu oraz wydajności i ustalić, czy na komputerze z uruchomionym systemem nadal działa jako użytkownicy lub kwalifikuje się do likwidacji zamiast migracji. Po zakończeniu przenoszenia można sprawdzić na obciążenie klienta i tożsamości, aby sprawdzić połączenie systemy testowe i klientów. Jeśli definicje planowania i Zapora podsieci występują problemy, połączenia zakończone niepowodzeniem w społeczności maps rozwiązania Service Map punktu do systemów które wymaga łączności.

### <a name="business-continuity"></a>Ciągłość działalności biznesowej
Jeśli używasz usługi Azure Site Recovery i potrzebujesz pomocy definiowanie sekwencji odzyskiwania dla danego środowiska aplikacji, Usługa Service Map może automatycznie dowiesz się, jak systemy opierają się na siebie, aby upewnić się, że plan odzyskiwania jest niezawodne. Wybierając serwera o krytycznym znaczeniu lub grupy, a następnie wyświetlając jego klientów, można zidentyfikować systemów frontonu, które do odzyskania po przywrócona i dostępności serwera. Z drugiej strony sprawdzając krytycznych serwerów zaplecza zależności, można zidentyfikować systemów, które na odzyskanie sprawności zanim zostaną przywrócone z systemami fokus.

### <a name="patch-management"></a>Zarządzanie poprawkami
Usługa Service Map zwiększa użytkowania ocena aktualizacji systemu, pokazując, które inne zespoły i serwery są zależne od usługi, dzięki czemu można powiadomić je z wyprzedzeniem przed walce z systemami stosowania poprawek. Mapy usługi zwiększa również zarządzanie poprawkami, pokazując, czy usługi są dostępne i prawidłowo połączona po są poprawkami i ponownego uruchomienia.

## <a name="mapping-overview"></a>Mapowanie — omówienie
Agenci rozwiązania Service Map zbierać informacje o wszystkich procesów połączenia TCP na serwerze, na którym są one zainstalowane i szczegółowe informacje dotyczące połączeń przychodzących i wychodzących dla każdego procesu.

Z listy w okienku po lewej stronie można wybrać maszyny lub grupy, które mają agentów rozwiązania Service Map wizualizacji ich zależności za pośrednictwem określonego przedziału czasu. Zależności maszyny mapuje fokus na określonym komputerze i pokazują wszystkich maszyn, które są bezpośrednie TCP klientów lub serwerów tej maszyny.  Mapowania maszyny grupy Pokaż zestawy serwerów oraz ich zależności.

![Omówienie rozwiązania Service Map](media/service-map/service-map-overview.png)

Maszyny można rozwijać w mapę, aby pokazać uruchamianie przetwarzania grupy i procesy z aktywnymi połączeniami sieciowymi w wybranym zakresie czasu. Gdy komputer zdalny, z agent rozwiązania Service Map jest rozszerzona, aby wyświetlić szczegóły procesu, wyświetlane są tylko procesy, które komunikują się z maszyną fokus. Po lewej stronie procesy, które łączą się z wskazuje liczbę agentów maszyn frontonu, łączących się z maszyną fokus. Jeśli maszyna fokus jest połączenie z maszyną zaplecza, zawierającej żadni agenci, serwerów zaplecza znajduje się w grupie portu serwera, oraz inne połączenia do tego samego numeru portu.

Domyślnie mapy usługi maps Pokaż ostatnich 30 minut informacji o zależnościach. Za pomocą kontrolek na czas, w lewym górnym rogu, można tworzyć zapytania mapy dla zakresy czasu historycznych maksymalnie jedną godzinę, aby pokazać, jak zależności będzie wyglądał w przeszłości (na przykład podczas zdarzenia lub przed wystąpieniem zmiany). Rozwiązania Service Map, dane są przechowywane przez 30 dni w płatnych obszarów roboczych oraz przez 7 dni w bezpłatnych obszarów roboczych.

## <a name="status-badges-and-border-coloring"></a>Wskaźniki stanu i kolorowanie obramowania
W dolnej części każdego serwera, na mapie, może być lista wskaźniki stanu przekazywania stanu informacji o serwerze. Wskaźniki oznacza, że niektóre istotne informacje dotyczące serwera z jednego z integracji rozwiązań. Klikając pozycję wskaźnika przejście bezpośrednio do szczegółowych informacji o stanie w okienku po prawej stronie. Wskaźniki stanu aktualnie dostępne obejmują alerty, pomoc techniczna, zmiany, zabezpieczeń i aktualizacje.

W zależności od ważności wskaźniki stanu komputera węzła obramowania mogą być kolor czerwony (krytyczna), żółty (ostrzeżenie) lub niebieski (informacyjny). Kolor reprezentuje najpoważniejsze stan dowolnego wskaźniki stanu. Szare obramowanie wskazuje węzeł, który ma nie wskaźniki stanu.

![Wskaźniki stanu](media/service-map/status-badges.png)

## <a name="process-groups"></a>Grupy procesów
Grupa procesów łączyć procesy, które są skojarzone z typowych produktu lub usługi do grupy procesów.  Po rozwinięciu węzła maszyny wyświetli autonomiczny procesów wraz z grupy procesów.  Jeśli wszystkie połączenia przychodzące i wychodzące do procesu w ramach grupy procesu nie powiodło się następnie połączenia jest wyświetlany jako grupy całego procesu nie powiodło się.

## <a name="machine-groups"></a>Grupy maszyn
Grupy maszyn umożliwiają wyświetlanie map skupia się wokół zestaw serwerów, nie tylko jeden można wyświetlić wszystkie elementy członkowskie klastra wielowarstwowej aplikacji lub serwera, w jedną mapę.

Użytkownicy wybierają serwerów, które należą do grupy ze sobą i wybierz nazwę grupy.  Następnie można wyświetlić grupę ze wszystkimi połączenia i procesów ani wyświetlić przy użyciu tylko procesy i połączeń, które bezpośrednio odnoszą się do elementów członkowskich grupy.

![Grupy maszyn](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Tworzenie grupy maszyn
Aby utworzyć grupę, wybierz maszynie lub maszynach, na liście komputerów, a następnie kliknij pozycję **Dodaj do grupy**.

![Utwórz grupę](media/service-map/machine-groups-create.png)

Tam możesz wybrać **Utwórz nową** i nadaj nazwę grupie.

![Nazwa grupy](media/service-map/machine-groups-name.png)

>[!NOTE]
>Grupy maszyn są ograniczone do 10 serwerów.

### <a name="viewing-a-group"></a>Wyświetlanie grupy
Po utworzeniu niektóre grupy, można je wyświetlić, wybierając kartę grupy.

![Karty grupy](media/service-map/machine-groups-tab.png)

Następnie wybierz nazwę grupy, aby wyświetlić mapę dla tej grupy na komputerze.
![Grupy maszyn](media/service-map/machine-group.png) maszyn, które należą do tej grupy są opisane w oficjalnym na mapie.

Rozwijanie grupy spowoduje wyświetlenie listy maszyn, które tworzą grupy maszyn.

![Grupuj maszyny maszyny](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtruj według procesów
Możliwość przełączania widoku mapy między pokazywaniem wszystkich procesów i połączeń w grupie i tylko te, które bezpośrednio odnoszą się do grupy na komputerze.  Widok domyślny to pokazanie wszystkich procesów.  Aby zmienić widok, klikając ikonę filtru powyżej mapy.

![Grupę filtrów](media/service-map/machine-groups-filter.png)

Gdy **wszystkie procesy** jest zaznaczone, mapy obejmie wszystkich procesów i połączeń na wszystkich komputerach w grupie.

![Przetwarza wszystkie grupy maszyn](media/service-map/machine-groups-all.png)

Jeśli zmienisz widok, aby wyświetlić tylko **procesy połączone z grupami**, mapy, zostanie zawężona tylko procesy i połączeń, które są podłączone bezpośrednio do innych maszyn w grupie, tworząc uproszczony widok.

![Grupy maszyn filtrowane procesów](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Dodawanie maszyn do grupy
Aby dodać maszyny do istniejącej grupy, zaznacz pola wyboru obok maszyny, a następnie kliknij przycisk **Dodaj do grupy**.  Następnie wybierz grupę, którą chcesz dodać do tej maszyny.
 
### <a name="removing-machines-from-a-group"></a>Usuwanie maszyny z grupy
Na liście grupy rozwiń nazwę grupy, aby wyświetlić listę maszyn w grupie maszyn.  Następnie kliknij przycisk menu wielokropka obok maszyny, aby usunąć, a następnie wybierz **Usuń**.

![Usuń maszynę z grupy](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Usuwanie lub zmiana nazwy grupy
Kliknij menu wielokropka obok nazwy grupy na liście grup.

![Menu grupy maszyn](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ikony ról
Niektóre procesy pełnią role określonego na maszynach: serwery sieci web, serwery aplikacji, bazy danych i tak dalej. Usługa Service Map oznacza stosowanym procesu i pola, aby ułatwić identyfikację błyskawicznie roli procesu lub odtwarza serwera ikon roli.

| Ikona roli | Opis |
|:--|:--|
| ![Serwer sieci Web](media/service-map/role-web-server.png) | Serwer sieci Web |
| ![Serwer aplikacji](media/service-map/role-application-server.png) | Serwer aplikacji |
| ![Serwer bazy danych](media/service-map/role-database.png) | Serwer bazy danych |
| ![Serwer LDAP](media/service-map/role-ldap.png) | Serwer LDAP |
| ![Serwer SMB](media/service-map/role-smb.png) | Serwer SMB |

![Ikony ról](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Połączenia zakończone niepowodzeniem
Połączenia zakończone niepowodzeniem są wyświetlane w mapach rozwiązania Service Map dla procesów i komputerów, z czerwonym linia przerywana wskazująca, czy system klienta nie może dotrzeć do procesu lub portu. Połączenia zakończone niepowodzeniem są zgłaszane z dowolnego systemu z wdrożonym agent rozwiązania Service Map, w przypadku tego systemu jest próba nawiązania połączenia nie powiodło się. Usługa Service Map mierzy tego procesu przez obserwację gniazda TCP, których nie można ustanowić połączenia. Ten błąd może wynikać z zapory, błędnej konfiguracji klienta, serwera lub usługi zdalnej jest niedostępny.

![Połączenia zakończone niepowodzeniem](media/service-map/failed-connections.png)

Omówienie połączenia zakończone niepowodzeniem mogą pomóc w rozwiązywaniu problemów, sprawdzanie poprawności migracji, analizy zabezpieczeń i architektury ogólny opis. Połączenia zakończone niepowodzeniem są czasami nieszkodliwe, ale często wskazują bezpośrednio do problemu, takie jak środowisko pracy awaryjnej, nagle staje się niedostępny lub dwie warstwy aplikacji nie będzie w stanie komunikować się po migracji do chmury.

## <a name="client-groups"></a>Grupy klientów
Grupy klienta są pola na mapie, które reprezentują komputerów klienckich, które nie mają agenci zależności. Pojedynczą grupę klienta reprezentuje klientów dla poszczególnych proces lub komputera.

![Grupy klientów](media/service-map/client-groups.png)

Aby wyświetlić adresy IP serwerów z grupy klientów, wybierz grupę. Zawartość grupy są wymienione w **właściwości grupy klientów** okienka.

![Właściwości grupy klientów](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Port serwera grup
Port serwera grupy są pola, które reprezentują porty serwera na serwerach, które nie mają agenci zależności. Pole zawiera port serwera i liczbę serwerów przy użyciu połączenia do tego portu. Rozwiń pole, aby wyświetlić poszczególne serwery i połączeń. Jeśli istnieje tylko jeden serwer w polu, znajduje się nazwa lub adres IP.

![Port serwera grup](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu kontekstowe
Klikając przycisk wielokropka (...) w prawym górnym rogu dowolnego serwera Wyświetla menu kontekstowe dla tego serwera.

![Połączenia zakończone niepowodzeniem](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Załaduj mapę serwera
Klikając **Załaduj mapę serwera** spowoduje przejście do nowej mapy za pomocą wybranego serwera jako nową maszynę fokus.

### <a name="show-self-links"></a>Pokaż linki własne
Klikając **Pokaż Self-Links** ponownych rysowaniach węzeł serwera, wraz ze wszystkimi linków do samego siebie, będące połączeń TCP, które rozpoczęcia i zakończenia procesów na serwerze. Jeśli linków do samego siebie są wyświetlane zmiany polecenia menu **Ukryj Self-Links**, dzięki czemu można je wyłączyć.

## <a name="computer-summary"></a>Podsumowanie komputera
**Podsumowanie maszyny** okienko zawiera omówienie systemu operacyjnego serwera, liczby współzależność i danych z innych rozwiązań. Dane te obejmują metryki wydajności, bilety działu usług, śledzenia zmian, zabezpieczeń i aktualizacje.

![Okienko Podsumowanie maszyny](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Właściwości komputera i procesu
Po przejściu mapę mapy usługi, możesz wybrać maszyn i procesy, aby uzyskać dodatkowy kontekst, dotyczące ich właściwości. Maszyn zawierają informacje dotyczące DNS name, IPv4 adresów, Procesora i pamięci pojemności, typ maszyny Wirtualnej, system operacyjny i wersję, ostatnie ponowne uruchomienie czas i identyfikatory ich agentów pakietu OMS i mapy usługi.

![W okienku właściwości maszyny](media/service-map/machine-properties.png)

Szczegóły procesu można zbierać z metadanych systemu operacyjnego dotyczące uruchamiania procesów, takich jak nazwa procesu, opis procesu, nazwę użytkownika i domeny (na Windows), nazwę firmy, nazwę produktu, wersja produktu, katalog roboczy, wiersza polecenia i procesu czas rozpoczęcia.

![W okienku właściwości procesu](media/service-map/process-properties.png)

**Podsumowanie procesu** okienko zawiera dodatkowe informacje o łączności przez proces, w tym jego powiązanych portów, połączenia przychodzące i wychodzące i awarie połączeń.

![Okienko Podsumowanie procesu](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Integracja alertów
Usługa Service Map integruje się z alertów platformy Azure, aby wyświetlić wyzwolone alerty dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikony, jeśli bieżące alerty i **alerty maszyny** okienku są wyświetlane alerty.

![W okienku alertów maszyny](media/service-map/machine-alerts.png)

Aby włączyć mapy usługi do wyświetlania powiązanych alertów, Utwórz regułę alertu, który jest uruchamiany dla określonego komputera. Aby utworzyć odpowiednie alerty:
- Zawiera klauzulę do grupy według komputera (na przykład **komputera interwał 1 minutę**).
- Wybierz alert w oparciu metryki pomiaru.

## <a name="log-events-integration"></a>Integracja z dziennika zdarzeń
Usługa Service Map integruje się z przeszukiwania dzienników, aby wyświetlić liczbę wszystkich zdarzeń dziennika dostępne dla wybranego serwera w wybranym zakresie czasu. Możesz kliknąć każdy wiersz na liście liczby zdarzeń, przejdź do wyszukiwania w dzienniku i wyświetlić zdarzenia osobny dziennik.

![Okienko dziennika zdarzeń maszyny](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Integracja z pomocy technicznej
Integracja mapy usługi przy użyciu łącznika zarządzania usługami IT jest automatyczne, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics. Integracja w rozwiązania Service Map jest oznaczona etykietą "Pomoc techniczna". Aby uzyskać więcej informacji, zobacz [centralnie zarządzać elementy robocze ITSM za pomocą łącznika zarządzania usługami IT](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

**Maszyny pomoc techniczna** okienku są wyświetlane wszystkie zdarzenia zarządzania usługami IT dla wybranego serwera w wybranym zakresie czasu. Serwer wyświetla ikony, jeśli istnieją bieżących elementów i wyświetlane w okienku pomocy technicznej maszyn.

![W okienku pomocy technicznej maszyny](media/service-map/service-desk.png)

Aby otworzyć element w połączonych rozwiązania ITSM, kliknij przycisk **Wyświetl element roboczy**.

Aby wyświetlić szczegóły dotyczące elementu podczas wyszukiwania dziennika, kliknij **Pokaż podczas wyszukiwania dziennika**.
Metryki połączeń są zapisywane w dwóch nowych tabel w usłudze Log Analytics 

## <a name="change-tracking-integration"></a>Integracja śledzenia zmiany
Integracja mapy usługi przy użyciu śledzenia zmian jest automatyczne, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

**Maszyny Change Tracking** okienko zawiera listę wszystkich zmian, przy czym pierwsze najbardziej aktualne, wraz z linkiem do szczegółów przeszukiwania dzienników, aby uzyskać więcej informacji.

![Okienko Change Tracking maszyny](media/service-map/change-tracking.png)

Poniższa ilustracja jest szczegółowy widok zdarzenia Zmianakonfiguracji, który może zostać wyświetlony po wybraniu **Pokaż w usłudze Log Analytics**.

![Zmianakonfiguracji zdarzeń](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Integracja wydajności
**Wydajność maszyny** okienko wyświetla standardowych metryk wydajności dla wybranego serwera. Metryki obejmują wykorzystanie procesora CPU, wykorzystanie pamięci, wysłanych i odebranych bajtów sieci i listę najważniejszych procesów sieci bajtów wysłanych i odebranych.

![Okienko wyników maszyny](media/service-map/machine-performance.png)

Aby wyświetlić dane dotyczące wydajności, może być konieczne [odpowiednich liczników wydajności usługi Log Analytics Włącz](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Liczniki, które chcesz włączyć:

W systemie Windows:
- Procesor(*)\\czas procesora (%)
- Pamięć\\% przydzielonych bajtów w użyciu
- Sieci Adapter(*)\\bajty wysłane/s
- Sieci Adapter(*)\\bajty odebrane/s

W systemie Linux:
- Procesor(*)\\czas procesora (%)
- Memory(*)\\% użycie pamięci
- Sieci Adapter(*)\\bajty wysłane/s
- Sieci Adapter(*)\\bajty odebrane/s

Aby uzyskać dane dotyczące wydajności sieci, musisz również włączyć rozwiązania danych o komunikacji sieciowej w wersji 2.0 w obszarze roboczym.
 
## <a name="security-integration"></a>Integracja z zabezpieczeniami
Mapa usługi integracji z zabezpieczeniami i inspekcją jest automatycznie, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

**Zabezpieczenia maszyny** okienko zawiera dane z rozwiązania zabezpieczenia i inspekcja dla wybranego serwera. Okienka Wyświetla podsumowanie oczekujących bezpieczeństwo serwera w wybranym zakresie czasu. Kliknięcie dowolnego awarii problemy dotyczące zabezpieczeń w dół do przeszukiwania dzienników, aby uzyskać szczegółowe informacje o nich.

![Okienko zabezpieczeń komputera](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Integracja aktualizacji
Integracja mapy usługi przy użyciu rozwiązania Update Management jest automatyczne, gdy oba rozwiązania są włączone i skonfigurowane w obszarze roboczym usługi Log Analytics.

**Aktualizacji maszyn** okienko wyświetla dane z rozwiązania Update Management dla wybranego serwera. Okienko zawiera podsumowanie dowolnych brakujących aktualizacji na serwerze w wybranym zakresie czasu.

![Okienko Change Tracking maszyny](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Rekordy usługi Log Analytics
Mapy usługi komputera i przetwarzanie magazynu danych jest dostępna dla [wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics. Dane te można zastosować do scenariuszy obejmujących planowania migracji, analizy wydajności, wykrywanie i rozwiązywanie problemów z wydajnością na żądanie.

Za godzinę dla każdego komputera unikatowy i procesów, oprócz rekordów, które są generowane, gdy proces lub komputer zostaje uruchomiony lub jest uruchomiona do rozwiązania Service Map, generowany jest jeden rekord. Te rekordy mają właściwości podane w poniższych tabelach. Pola i wartości w zdarzeniach ServiceMapComputer_CL są mapowane na pola zasób maszynę w interfejsie API ServiceMap usługi Azure Resource Manager. Pola i wartości w zdarzeniach ServiceMapProcess_CL są mapowane na pola zasobów procesu w interfejsie API ServiceMap usługi Azure Resource Manager. Pole ResourceName_s zgodne pola Nazwa odpowiedniego zasobu usługi Resource Manager. 

>[!NOTE]
>Gdy funkcje mapy usług wzrosną, te pola mogą ulec zmianie.

Istnieją wewnętrznie generowane właściwości, które służy do identyfikacji procesów unikatowy i komputerów:

- Komputer: Użyj *ResourceId* lub *ResourceName_s* do unikatowej identyfikacji komputera w obszarze roboczym usługi Log Analytics.
- Proces: Użyj *ResourceId* do unikatowej identyfikacji procesów w obrębie obszaru roboczego usługi Log Analytics. *ResourceName_s* jest unikatowa w obrębie kontekstu komputera, na którym proces działa (MachineResourceName_s) 

Ponieważ wiele rekordów może istnieć dla określonego procesu i komputera w określonym okresie, zapytania może zwrócić więcej niż jeden rekord dla tego samego komputera lub procesu. Aby dołączyć tylko najnowsze rekord, należy dodać "| Funkcja deduplikacji ResourceId"zapytania.

### <a name="connections"></a>Połączenia
Metryki połączeń są zapisywane do nowej tabeli w usłudze Log Analytics — VMConnection. Ta tabela zawiera informacje o połączeniach dla maszyny (przychodzące i wychodzące). Metryki połączeń są również udostępniane za pomocą interfejsów API, które zapewniają środki do uzyskania określona Metryka przedziale czasu.  Połączenia protokołu TCP, wynikające z "*zaakceptować*- ing nasłuchiwania gniazda są dla ruchu przychodzącego, a te utworzone przez *połączyć*- ing do danego adresu IP i portu są ruchu wychodzącego. Kierunek połączenie jest reprezentowane przez właściwość "Direction", który może być ustawiony na **dla ruchu przychodzącego** lub **wychodzących**. 

Rekordów w tych tabelach są generowane na podstawie danych przekazywanych przez agenta zależności. Każdy rekord reprezentuje obserwacji w przedziałach czasu jednej minuty. Właściwość TimeGenerated wskazuje początek przedziału czasu. Każdy rekord zawiera informacje w celu zidentyfikowania odpowiednich jednostek, to znaczy, połączenia lub portu, a także metryki skojarzone z tej jednostki. Obecnie jest zgłaszany tylko aktywność sieciową występujący za pomocą protokołu TCP za pośrednictwem protokołu IPv4.

Aby zarządzać, koszt i złożoność, rekordy połączeń nie przedstawiają poszczególnych fizycznych połączeń sieciowych. Wiele połączeń sieci fizycznej są grupowane w logicznej połączenia, które następnie są odzwierciedlane w odpowiedniej tabeli.  Znaczenie, rejestruje w *VMConnection* tabeli reprezentują logiczne grupowanie, a nie poszczególnych fizycznego połączenia, które są przestrzegane. Połączenie z siecią fizyczną współużytkujące taką samą wartość, dla następujących atrybutów dla interwału danego jednej minuty są agregowane w jednym rekordzie logicznych w *VMConnection*. 

| Właściwość | Opis |
|:--|:--|
| `Direction` |Kierunek połączenia, wartość jest *dla ruchu przychodzącego* lub *ruchu wychodzącego* |
| `Machine` |Nazwa FQDN komputera |
| `Process` |Tożsamość procesu lub grupy procesów, inicjowanie/akceptować połączenia |
| `SourceIp` |Adres IP źródła |
| `DestinationIp` |Adres IP miejsca docelowego |
| `DestinationPort` |Numer portu docelowego |
| `Protocol` |Protokół używany dla połączenia.  Wartości *tcp*. |

Aby uwzględnić wpływ grupowania, informacje o liczbie pogrupowanych połączeń fizycznych znajduje się w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
| `LinksEstablished` |Liczba połączeń sieci fizycznej, które zostały utworzone w przedziale czasu raportowania |
| `LinksTerminated` |Liczba połączeń sieci fizycznej, które zostały zakończone raportowania przedziale czasu |
| `LinksFailed` |Liczba połączeń sieci fizycznej, które nie powiodły się w przedziale czasu raportowania. Te informacje są obecnie dostępne tylko dla połączeń wychodzących. |
| `LinksLive` |Liczba połączeń sieci fizycznej, które były otwarte na końcu raportowania przedział czasu|

#### <a name="metrics"></a>Metryki

Oprócz metryki liczbę połączeń informacji na temat ilości danych wysłanych i odebranych w danym połączenie logiczne lub portu sieci znajdują się również w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
| `BytesSent` |Całkowita liczba bajtów wysłanych raportowania przedziale czasu |
| `BytesReceived` |Całkowita liczba bajtów odebranych w przedziale czasu raportowania |
| `Responses` |Liczba odpowiedzi zaobserwowane w przedziale czasu raportowania. 
| `ResponseTimeMax` |Największy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta.|
| `ResponseTimeMin` |Najmniejszy czas odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta.|
| `ResponseTimeSum` |Suma czasów odpowiedzi (w milisekundach) zaobserwowane w przedziale czasu raportowania.  Jeśli brak wartości właściwości jest pusta|

Trzeci typ danych, zgłaszany jest czas odpowiedzi — obiekt wywołujący poświęcić czas oczekiwania na żądanie przesyłane przez połączenie do przetworzenia i przez zdalny punkt końcowy. Czas odpowiedzi, zgłaszane jest oszacowanie czas odpowiedzi true podstawowego protokołu aplikacji. Jest obliczana, przy użyciu algorytmów heurystycznych oparte na obserwacji przepływ danych między źródłowym i docelowym koniec połączenie z siecią fizyczną. Pod względem koncepcyjnym go różni się od czasu ostatniego bajtu żądania pozostawia nadawcy i czas po nadejściu ostatni bajt odpowiedzi powrotu do tego. Te dwa sygnatury czasowe są używane do odróżnić zdarzenia żądań i odpowiedzi dla danego połączenia fizycznych. Różnica między nimi reprezentuje czas odpowiedzi pojedynczego żądania. 

W pierwszej wersji tej funkcji nasze algorytm jest przybliżeniem, która może działać z dowolnym stopniu sukcesu w zależności od rzeczywistej aplikacji protokół dla połączenia określonej sieci. Na przykład bieżącego podejścia działa dobrze w przypadku protokołów odpowiedź na żądanie na podstawie takich jak HTTP (S), ale nie pracować jednokierunkowe lub odpowiednich protokołów opartych na kolejki komunikatów.

Oto niektóre ważne punkty, które należy wziąć pod uwagę:

1. Jeśli proces akceptuje połączenia na ten sam adres IP, ale w wielu interfejsów sieciowych, zostanie zgłoszony oddzielny rekord dla każdego interfejsu. 
2. Rekordy z symbolami wieloznacznymi IP będzie zawierać żadnych działań. Zostaną one dołączone do reprezentowania fakt, że port na maszynie jest otwarty dla ruchu przychodzącego ruchu sieciowego.
3. Aby zmniejszyć poziom szczegółowości i ilość danych, rekordów za pomocą adresu IP z symbolami wieloznacznymi zostaną pominięte po pasującego rekordu (w przypadku tego samego procesu, portów i protokołów) przy użyciu określonego adresu IP. Gdy rekord IP symbol wieloznaczny zostanie pominięty, IsWildcardBind właściwości rekordu z określonego adresu IP ustawi na wartość "True" do wskazania, że port jest uwidaczniany przez każdy interfejs raportowania maszyny.
4. Porty, które są powiązane tylko dla określonego interfejsu mają IsWildcardBind ustawiony na wartość "False".

#### <a name="naming-and-classification"></a>Nazewnictwo i klasyfikacji
Dla wygody adres IP zdalnego punktu końcowego połączenia znajduje się we właściwości RemoteIp. Dla połączeń przychodzących RemoteIp jest taka sama jak SourceIp, natomiast w przypadku połączeń wychodzących, jest taka sama jak DestinationIp. Właściwość RemoteDnsCanonicalNames reprezentuje zgłoszone przez maszynę dla RemoteIp nazwy kanonicznej DNS. Właściwości RemoteDnsQuestions i RemoteClassification są zarezerwowane do użytku w przyszłości. 

#### <a name="geolocation"></a>Geolokalizacja
*VMConnection* zawiera również informacje o geolokalizacji na drugim końcu każdego wybranego rekordu połączenia w następujących właściwości rekordu: 

| Właściwość | Opis |
|:--|:--|
| `RemoteCountry` |Nazwa kraju/regionu, hostingu RemoteIp.  Na przykład *Stanów Zjednoczonych* |
| `RemoteLatitude` |Szerokość geograficzną.  Na przykład *47.68* |
| `RemoteLongitude` |Długość geograficzna geolokalizacji.  Na przykład *-122.12* |

#### <a name="malicious-ip"></a>Złośliwy adres IP
Dla każdej właściwości RemoteIp w *VMConnection* tabeli jest sprawdzana względem zbiór adresów IP przy użyciu znanych złośliwych działań. Jeśli RemoteIp została zidentyfikowana jako szkodliwe następujące właściwości zostaną wypełnione (są puste, jeśli adres IP nie jest uznawane za złośliwe) w następujących właściwości rekordu:

| Właściwość | Opis |
|:--|:--|
| `MaliciousIp` |Adres RemoteIp |
| `IndicatorThreadType` |Wskaźnik zagrożenia wykryte jest jednym z następujących wartości *Botnet*, *C2*, *CryptoMining*, *zakres sieci Darknet*, *przed atakami DDos* , *MaliciousUrl*, *złośliwego oprogramowania*, *wyłudzania informacji*, *Proxy*, *potencjalnie niechciane aplikacje*, *Listy do obejrzenia*.   |
| `Description` |Opis obserwowanych zagrożeń. |
| `TLPLevel` |Poziom Rack (RECENT sygnalizacji ulicznej Protocol) jest jednym ze zdefiniowanymi wartościami *biały*, *zielony*, *żółtą*, *Red*. |
| `Confidence` |Wartości są *0 – 100*. |
| `Severity` |Wartości są *0 – 5*, gdzie *5* jest najpoważniejsze i *0* nie jest poważny wcale. Wartość domyślna to *3*.  |
| `FirstReportedDateTime` |Po raz pierwszy dostawca zgłosił wskaźnika. |
| `LastReportedDateTime` |Czas ostatniego wskaźnika widzianego przez Interflow. |
| `IsActive` |Wskazuje, wskaźniki zostaną dezaktywowane z *True* lub *False* wartość. |
| `ReportReferenceLink` |Zawiera łącza do raportów związanych z danym możliwość obserwowania. |
| `AdditionalInformation` |Udostępnia dodatkowe informacje, jeśli ma to zastosowanie, temat obserwowanych zagrożenia. |

### <a name="servicemapcomputercl-records"></a>Rekordy ServiceMapComputer_CL
Rekordy z typem *ServiceMapComputer_CL* zawierają dane spisu dla serwerów z agentami mapy usługi. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| `ResourceName_s` | Unikatowy identyfikator dla maszyny w obszarze roboczym |
| `ComputerName_s` | Nazwa FQDN komputera |
| `Ipv4Addresses_s` | Lista IPv4 serwera adresów |
| `Ipv6Addresses_s` | Lista IPv6 serwera adresów |
| `DnsNames_s` | Tablica nazw DNS |
| `OperatingSystemFamily_s` | Windows lub Linux |
| `OperatingSystemFullName_s` | Pełna nazwa systemu operacyjnego  |
| `Bitness_s` | Liczba bitów maszyny (32-bitowy lub 64-bitowy)  |
| `PhysicalMemory_d` | Pamięci fizycznej w Megabajtach |
| `Cpus_d` | Liczba procesorów CPU |
| `CpuSpeed_d` | Szybkość Procesora w MHz|
| `VirtualizationState_s` | *Nieznany*, *fizycznych*, *wirtualnego*, *funkcji hypervisor* |
| `VirtualMachineType_s` | *funkcji Hyper-v*, *vmware*i tak dalej |
| `VirtualMachineNativeMachineId_g` | Identyfikator maszyny Wirtualnej, zgodnie z dokonanym przez jego funkcji hypervisor |
| `VirtualMachineName_s` | Nazwa maszyny Wirtualnej |
| `BootTime_t` | W czasie rozruchu |

### <a name="servicemapprocesscl-type-records"></a>Rejestruje typ ServiceMapProcess_CL
Rekordy z typem *ServiceMapProcess_CL* mają dane spisu dla procesy połączone z protokołu TCP na serwerach z agentami mapy usługi. Te rekordy mają właściwości podane w poniższej tabeli:

| Właściwość | Opis |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Unikatowy identyfikator procesu wewnątrz obszaru roboczego |
| `ResourceName_s` | Unikatowy identyfikator procesu na maszynie, na którym jest uruchomiony|
| `MachineResourceName_s` | Nazwa zasobu maszyny |
| `ExecutableName_s` | Nazwa pliku wykonywalnego procesu |
| `StartTime_t` | Czas rozpoczęcia procesu puli |
| `FirstPid_d` | Pierwszy identyfikator PID w puli procesów |
| `Description_s` | Opis procesu |
| `CompanyName_s` | Nazwa firmy |
| `InternalName_s` | Nazwa wewnętrzna |
| `ProductName_s` | Nazwa produktu |
| `ProductVersion_s` | Wersja produktu |
| `FileVersion_s` | Wersja pliku |
| `CommandLine_s` | W wierszu polecenia |
| `ExecutablePath _s` | Ścieżka do pliku wykonywalnego |
| `WorkingDirectory_s` | Katalog roboczy |
| `UserName` | Konta, w którym proces jest wykonywany |
| `UserDomain` | Domeny, w którym proces jest wykonywany |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

### <a name="list-all-known-machines"></a>Listę wszystkich znanych maszyn
ServiceMapComputer_CL | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Wyświetl listę wszystkich zarządzanych komputerów pojemność pamięci fizycznej.
ServiceMapComputer_CL | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId | Projekt PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Nazwa komputera listy, DNS, adresu IP i systemu operacyjnego.
ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Znajdź wszystkie procesy za pomocą "sql" w wierszu polecenia
ServiceMapProcess_CL | gdzie contains_cs CommandLine_s "sql" | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Znajdź maszyny (najnowsze rekordu) według nazwy zasobu
Szukaj w "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" (ServiceMapComputer_CL) | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Znajdź maszyny (najnowsze rekordu) przy użyciu adresu IP
Szukaj w "10.229.243.232" (ServiceMapComputer_CL) | Podsumowanie arg_max(TimeGenerated, *) przez ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listę wszystkich znanych procesów na określonym komputerze
ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>Lista wszystkich komputerów z programem SQL
ServiceMapComputer_CL | gdzie ResourceName_s w ((wyszukiwanie w (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Wyświetla listę wszystkich wersji produktu unikatowy programu curl w mojego centrum danych
ServiceMapProcess_CL | gdzie ExecutableName_s == "zawinięcie" | odrębne ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Utwórz grupę komputerów wszystkich komputerów z systemem CentOS
ServiceMapComputer_CL | gdzie contains_cs OperatingSystemFullName_s "CentOS" | odrębne ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Podsumowanie połączenia wychodzące z grupy maszyn
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
Wszystkie dane serwera, proces i zależności na mapie usługi są dostępne za pośrednictwem [interfejsu API REST mapy usługi](https://docs.microsoft.com/rest/api/servicemap/).

## <a name="diagnostic-and-usage-data"></a>Dane diagnostyczne i dane dotyczące użycia
Firma Microsoft automatycznie zbiera dane dotyczące użycia i wydajności przez korzystanie z usługi mapy usługi. Firma Microsoft używa tych danych do zapewniania i poprawiania jakości, bezpieczeństwa i integralności usługi mapy usługi. Aby zapewnić dokładne i wydajne funkcje do rozwiązywania problemów, dane obejmują informacje o konfiguracji oprogramowania, takie jak system operacyjny i wersję, adres IP, nazwę DNS i nazwę stacji roboczej. Firma Microsoft gromadzi nazwisk, adresów ani innych informacji kontaktowych.

Aby uzyskać więcej informacji na temat zbierania i wykorzystywania danych, zobacz [Microsoft Online Services Privacy Statement](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [dziennikach](../../azure-monitor/log-query/log-query-overview.md) w usłudze Log Analytics, aby pobrać dane, które są zbierane przez rozwiązania Service Map.


## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zobacz [Rozwiązywanie problemów z części dokumentu konfigurowania rozwiązania Service Map]( service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Opinie
Masz opinię dla nas o rozwiązania Service Map lub tej dokumentacji?  Odwiedź nasze [stronę z opiniami użytkowników](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), gdzie możesz proponować funkcje lub głosować się sugestie istniejących.
