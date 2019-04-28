---
title: 'Rozwiązywanie problemów z siecią wirtualną: Azure | Microsoft Docs'
description: Ta strona zawiera standardowej metody testowania wydajności łącze sieci platformy Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883320"
---
# <a name="troubleshooting-network-performance"></a>Rozwiązywanie problemów z wydajnością sieci
## <a name="overview"></a>Omówienie
System Azure oferuje stabilne i szybkie sposoby nawiązywania połączenia z sieci lokalnej na platformie Azure. Metody, takie jak sieci VPN typu lokacja-lokacja i ExpressRoute pomyślnie są używane przez klientów, dużych i małych do prowadzenia działalności na platformie Azure. Ale co się stanie, gdy wydajność nie spełnia Twoje oczekiwania lub poprzednie środowisko? Ten dokument może pomóc standaryzacji sposób testowania i linii bazowych określonego środowiska.

W tym dokumencie przedstawiono, jak można łatwo i spójne przetestować opóźnienie sieci i przepustowość między dwoma hostami. Ten dokument zawiera także kilka porad dotyczących sposobów Przyjrzyj się sieci platformy Azure i pomóc wyizolować problem punktów. Skrypt programu PowerShell i narzędzia omówione wymagają dwa hosty w sieci (na końcu łącza testowana). Jeden host musi być system Windows Server lub pulpit, drugi może być Windows lub Linux. 

>[!NOTE]
>Podejście do rozwiązywania problemów, narzędzi i metod są osobistych preferencji. W tym dokumencie opisano podejście i narzędzia, które często prowadzą. Prawdopodobnie różnią się w danej metody, nie ma problem z różnych podejść do rozwiązywania problemów. Jednak jeśli nie masz podejście ustanowionych w tym dokumencie można ułatwią rozpoczęcie pracy w ścieżce do tworzenia własnych metod, narzędzi i preferencji do rozwiązywania problemów z siecią.
>
>

## <a name="network-components"></a>Składniki sieciowe
Przed zagłębieniem do rozwiązywania problemów, Omówmy niektóre typowe terminy i składników. Tej dyskusji gwarantuje, że firma Microsoft rozważasz dalsze prace nad każdego składnika w łańcuchu end-to-end, umożliwiającym łączność na platformie Azure.
[![1]][1]

Na najwyższym poziomie I opisano trzy domeny routingu sieci głównych;

- sieć platformy Azure (blue chmura po prawej stronie)
- Internetu lub sieci WAN (zielony w chmurze w Centrum)
- (w chmurze brzoskwini po lewej stronie) w sieci firmowej

Patrząc na diagramie, od prawej do lewej, omówimy pokrótce poszczególnych składników:
 - **Maszyna wirtualna** — serwer może mieć wiele kart sieciowych, upewnij się, wszystkie trasy statyczne, trasy domyślne i ustawienia systemu operacyjnego są wysyłania i odbierania ruchu związanego z sposób podejrzewasz, że jest. Każda jednostka SKU maszyny Wirtualnej ma ograniczenie przepustowości. Jeśli używasz mniejsze jednostki SKU maszyny Wirtualnej, ruch sieciowy jest ograniczona przepustowość dostępną dla karty sieciowej. Zazwyczaj korzystam DS5v2 testowania (i następnie usuwaj po zakończeniu testowania, aby zaoszczędzić pieniądze) w celu zapewnienia odpowiedniej przepustowości na maszynie Wirtualnej.
 - **Karta sieciowa** — upewnij się, wiesz, prywatny adres IP, która jest przypisana do karty Sieciowej w danym.
 - **Sieciowa grupa zabezpieczeń z karty Sieciowej** — może być określone sieciowych grup zabezpieczeń, które są stosowane na poziomie karty Sieciowej, upewnij się, zestaw reguł sieciowej grupy zabezpieczeń jest odpowiednia dla ruchu próbujesz przekazać. Na przykład, upewnij się, porty 5201 dotyczące programu Iperf; port 3389 dla protokołu RDP lub 22 dla protokołu SSH jest otwarty, aby zezwolić na ruch testu do przekazania.
 - **Podsieci sieci wirtualnej** -karta sieciowa jest przypisany do określonej podsieci, upewnij się, gdy wiesz, które jednego i reguły skojarzone z tą podsiecią.
 - **Sieciowa grupa zabezpieczeń podsieci** — tak samo, jak można zastosować karty Sieciowej, sieciowe grupy zabezpieczeń w podsieci, jak również. Upewnij się, zestaw reguł sieciowej grupy zabezpieczeń jest odpowiednia dla ruchu, który próbujesz przekazać. (dla ruchu przychodzącego do karty Sieciowej podsieci, w której sieciowej grupy zabezpieczeń stosuje się najpierw, a następnie karty Sieciowej grupy zabezpieczeń sieci, z drugiej strony dla ruchu wychodzącego z maszyny Wirtualnej karty Sieciowej grupy zabezpieczeń sieci stosuje się najpierw, a następnie właśnie sieciowa grupa zabezpieczeń podsieci).
 - **Podsieci trasy zdefiniowanej przez użytkownika** -tras zdefiniowanych przez użytkownika może kierować ruch do przeskoków pośrednich (na przykład zapora lub moduł równoważenia obciążenia). Upewnij się, że wiesz, czy trasa zdefiniowana przez użytkownika w miejscu, aby ruch sieciowy i jeśli więc gdzie ona trafia i do czego tego następny przeskok będzie do ruchu. (na przykład zapora może przekazać część ruchu i zezwalają na inne ruch między dwoma hostami w tej samej).
 - **Podsieci bramy / sieciowej grupy zabezpieczeń / trasy zdefiniowanej przez użytkownika** — podobnie jak podsieci maszyny Wirtualnej, podsieć bramy może mieć sieciowe grupy zabezpieczeń i tras zdefiniowanych przez użytkownika. Upewnij się, że wiesz, jeśli są one dostępne, i jakie skutki mają one na ruch sieciowy.
 - **Bramy sieci wirtualnej (ExpressRoute)** — po włączeniu komunikacji równorzędnej (ExpressRoute) lub sieci VPN nie ma wiele ustawień, które mogą wpłynąć na sposób lub, jeśli ruch trasy. Jeśli masz wiele obwodów usługi ExpressRoute lub tuneli sieci VPN połączony z tą samą bramą sieci wirtualnej, pamiętaj o ustawienia wagi połączenia jako to ustawienie wpływa na stronie połączenia i ma wpływ na ścieżkę potrzebnego ruchu.
 - **Filtr trasy** (niewyświetlany) — filtr tras tylko dotyczy Peering firmy Microsoft w ramach usługi ExpressRoute, ale ma kluczowe znaczenie w celu sprawdzenia, jeśli nie widzisz trasy, o których oczekujesz, że na Peering firmy Microsoft. 

W tym momencie możesz teraz część sieci WAN łącze. Ta domena routingu może być dostawcą usług, sieci WAN firmowej lub Internetu. Wiele przeskoków, technologie i przedsiębiorstw z poniższych linków, może utrudnić nieco rozwiązywać problemy. Często pomocne w procesie wykluczenia platformy Azure i sieci firmowych najpierw zanim przejdziemy do tej kolekcji firm i przeskoków.

Na powyższym diagramie na końcu z lewej strony jest siecią firmową. W zależności od wielkości firmy tej domeny routingu może być kilka urządzeń sieciowych między możesz i sieci WAN lub wielu warstw urządzeń w sieci kampusowej/enterprise.

Biorąc pod uwagę złożoność tych trzech różnych wysokiego poziomu środowiskach sieciowych, często jest optymalna do uruchomienia na krawędziach i spróbuj pokazać, gdzie wydajność jest dobra, a gdy spadku. Takie podejście może pomóc zidentyfikować problem domenę routingu trzy, a następnie skoncentrować się rozwiązywania problemów w tym konkretnym środowisku.

## <a name="tools"></a>Narzędzia
Większości problemów z siecią mogą być analizowane i samodzielnie przy użyciu podstawowych narzędzi, takich jak ping i traceroute. Jest to rzadkie, trzeba go jako analiza pakietów, takich jak program Wireshark szczegółowego. Aby pomóc w rozwiązywaniu problemów, Azure Connectivity Toolkit (AzureCT) został opracowany, aby umieścić niektóre z tych narzędzi w pakiecie łatwe. W przypadku testowania wydajności podoba mi się używać dotyczące programu Iperf; i PSPing. dotyczące programu Iperf; jest powszechnie używane narzędzie i działa na większości systemów operacyjnych. dotyczące programu Iperf; to rozwiązanie dobre dla testów wydajności podstawowych i jest dość łatwy w użyciu. Narzędzie PSPing to narzędzie ping opracowany przez SysInternals. Narzędzie PSPing jest łatwym sposobem wykonania polecenia ping protokołu ICMP i TCP w pojedynczym poleceniu również łatwe w użyciu. Oba te narzędzia są lekkie i "zainstalowano" po prostu przez kopiowanie plików do katalogu na hoście.

Czy mogę został opakowany wszystkich tych narzędzi i metod moduł programu PowerShell (AzureCT), który można zainstalować i używać.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - Azure Connectivity Toolkit
Moduł AzureCT PowerShell ma dwa składniki [testowania dostępności] [ Availability Doc] i [testowania Wydajnościowego][Performance Doc]. Ten dokument jest tylko zaniepokojona testowanie wydajności, więc pozwala skupić się na dwa polecenia wydajności łącza w tym module programu PowerShell.

Istnieją trzy podstawowe kroki, aby użyć ten zestaw narzędzi do testowania Wydajnościowego. (1) Zainstaluj moduł programu PowerShell, (2) zainstaluj pomocnicze dotyczące programu Iperf aplikacji; i PSPing 3) uruchom test wydajności.

1. Instalowanie modułu programu PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    To polecenie pobiera modułu programu PowerShell i instaluje je lokalnie.

2. Instalowanie obsługi aplikacji
    ```powershell
    Install-LinkPerformance
    ```
    To polecenie AzureCT instaluje dotyczące programu Iperf; i PSPing w nowym katalogu "C:\ACTTools", a także otwiera porty zapory Windows, aby umożliwić ICMP i portu 5201 ruchu (dotyczące programu Iperf;).

3. Uruchom test wydajności

    Po pierwsze na zdalnym hoście należy zainstalować i uruchomić dotyczące programu Iperf; w trybie serwera. Upewnij się również hostem zdalnym nasłuchuje na obu 3389 (RDP dla Windows) lub 22 (protokołu SSH Linux) i zezwala na ruch na porcie 5201 dla dotyczące programu Iperf;. Jeśli host zdalny systemu windows, zainstaluj AzureCT i uruchom polecenie LinkPerformance instalacji, aby skonfigurować dotyczące programu Iperf; i reguły zapory wymaganej do rozpoczęcia dotyczące programu Iperf; w trybie serwera pomyślnie. 
    
    Gdy komputer zdalny jest gotowy, Otwórz program PowerShell na komputerze lokalnym, a następnie uruchom test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    To polecenie uruchamia serię współbieżnych obciążenia i testy opóźnienie ułatwia oszacowanie pojemność przepustowości i opóźnienia Link sieci.

4. Przejrzyj dane wyjściowe testów

    Format danych wyjściowych programu PowerShell wygląda podobnie do:

    [![4]][4]

    Szczegółowe wyniki dotyczące programu Iperf; i PSPing testy są w plikach tekstowych poszczególnych w katalogu narzędzi programu AzureCT na "C:\ACTTools."

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli test wydajności nie udostępnia oczekiwanych wyników, ustalenie, dlaczego należy progresywnego krok po kroku proces. Podana liczba składników w ścieżce, to systematyczne podejście ogólnie zapewnia szybsza ścieżka rozwiązania niż skok wokół i potencjalnie niepotrzebnie testowali tego samego wiele razy.

>[!NOTE]
>Scenariusz, w tym miejscu jest problem z wydajnością, nie problem z łącznością. Kroki będzie inny, jeśli ruch nie przechodzi w ogóle.
>
>

Po pierwsze wyzwanie założeń. Twoje oczekiwania jest uzasadnione? Na przykład jeśli obwód usługi ExpressRoute 1 GB/s i 100 ms czas oczekiwania jest nie można się spodziewać pełnej 1 GB/s ruchu, biorąc pod uwagę charakterystyki wydajności protokołu TCP za pośrednictwem łącza duże opóźnienie. Zobacz [odwołuje się do sekcji](#references) dla więcej założeń wydajności są włączone.

Następnie zaleca się, zaczynając od krawędzi między domen routingu i spróbuj ustalić przyczynę problemu do pojedynczej domeny routingu głównych; w sieci firmowej, sieć WAN lub sieci platformy Azure. Użytkownicy często "czarnej" w ścieżce polecenia blame podczas blaming czarne pole jest proste, może być znacznie opóźniona rozpoznawania zwłaszcza, jeśli ten problem jest faktycznie w obszarze zapewnienie możliwości wprowadzania zmian. Upewnij się, że możesz zrobić z należytą starannością przed przekazaniem do dostawcy usług lub usługodawcy internetowego.

Po zidentyfikowaniu głównych domeny routingu, która prawdopodobnie zawiera ten problem, należy utworzyć diagram warstwowy w danym. Na tablicy, Notatnika lub programu Visio jako diagram zawiera konkretny "map bitwy" umożliwia metodyczny podejście do dalszych odizolować problem. Planowanie punktów testowych i zaktualizować mapę, jak wyczyść obszary lub Postaraj się bardziej w miarę postępów testowania.

Teraz, gdy masz diagram, uruchom, aby podzielić sieć na segmenty i zawęzić problem. Dowiedz się, gdzie działa i których nie. Zachowaj przenoszenie punktów testowych do izolowania do problematycznych składnika.

Ponadto nie zapomnij Spójrz na inne warstwy modelu OSI. Ułatwia skoncentrowanie się na sieć i warstwy 1 – 3 (warstwy fizycznej, danych i sieci), ale problemy można też się na warstwie 7 w warstwie aplikacji. Zachowaj otwarty należy pamiętać i sprawdź założeń.

## <a name="advanced-expressroute-troubleshooting"></a>Zaawansowanego rozwiązywania problemów usługi ExpressRoute
Jeśli nie masz pewności, gdzie jest faktycznie granicy chmury, izolowania składników platformy Azure może być trudne. W przypadku usługi ExpressRoute krawędzi jest składnik sieci o nazwie krawędzi Enterprise firmy Microsoft (MSEE). **Korzystając z usługi ExpressRoute**, MSEE jest pierwszy punkt kontaktu do sieci firmy Microsoft i ostatniego przeskoku, pozostawiając sieci firmy Microsoft. Podczas tworzenia obiektu połączenia między bramą sieci wirtualnej i obwód usługi ExpressRoute, możesz sprawiamy, że połączenie do rozwiązania MSEE. Rozpoznawanie MSEE jako pierwszego lub ostatniego przeskok (w zależności od kierunku zamierzasz) ma kluczowe znaczenie dla izolowania problemów z siecią platformy Azure do jednej dowieść, że problem polega na platformie Azure lub dalsze podrzędne w sieci WAN lub w sieci firmowej. 

[![2]][2]

>[!NOTE]
> Należy zauważyć, że rozwiązania MSEE nie znajduje się w chmurze platformy Azure. Usługa ExpressRoute jest faktycznie na granicy sieci firmy Microsoft bez faktycznego na platformie Azure. Po nawiązaniu połączenia z usługą ExpressRoute do MSEE, jesteś podłączony do sieci firmy Microsoft, w tym miejscu możesz wtedy przejść do dowolnych usług w chmurze, takich jak usługi Office 365 (z Peering firmy Microsoft) lub Azure (przy użyciu prywatnych i/lub Peering firmy Microsoft).
>
>

Jeśli połączenie dwóch sieci wirtualnych (sieciami wirtualnymi A i B na diagramie) **tego samego** obwód usługi ExpressRoute, można wykonać szereg testów, aby ustalić przyczynę problemu na platformie Azure (lub udowodnić, że nie jest na platformie Azure)
 
### <a name="test-plan"></a>Plan testu
1. Uruchom test Get LinkPerformance między maszyny VM1 i VM2. Ten test zapewnia wgląd, jeśli ten problem jest lokalny, czy nie. Jeśli ten test generuje dopuszczalnego czasu oczekiwania i wyniki przepustowości, można oznaczyć sieci lokalnej sieci wirtualnej jako dobre.
2. Zakładając, że lokalnej sieci wirtualnej ruch jest dobra, uruchom test Get LinkPerformance między maszyny VM1 i VM3. Ten test sprawdza poprawność połączenia za pośrednictwem sieci firmy Microsoft do rozwiązania MSEE i wróć do platformy Azure. Jeśli ten test generuje dopuszczalnego czasu oczekiwania i wyniki przepustowości, można oznaczyć sieci platformy Azure jako dobre.
3. Jeśli jest wykluczona platformy Azure, można wykonać podobne sekwencji testów w sieci firmowej. Jeśli, również testy również jest czas na współpracę z dostawcy usług lub z poziomu usługodawcy internetowego, aby zdiagnozować połączenie WAN. Przykład: Między dwoma oddziałami firmy lub między działu a serwerem centrum danych, należy uruchomić ten test. W zależności od tego, co w przypadku testowania, Znajdź punkty końcowe (serwery, komputery, itp.), można korzystać z tej ścieżki.

>[!IMPORTANT]
> Należy koniecznie dla każdego testu, możesz oznaczyć porze dnia, gdy uruchamiasz test i zapisać wyniki w jednej lokalizacji (podoba mi się OneNote lub Excel). Każdego przebiegu testu powinien mieć identyczne dane wyjściowe, aby można było porównać dane wynikowe we wszystkich przebiegach testów i ma "otwory" w danych. Spójność między wiele testów jest głównym powodem I Użyj AzureCT do rozwiązywania problemów. Magic nie znajduje się w scenariuszach dokładnie obciążenia, czy mogę uruchomić, ale zamiast tego *magic* jest fakt, że otrzymuję *spójne dane wyjściowe testu i danych* z każdego testu. Rejestrowanie czasu i równoważy spójności danych co jeden raz jest szczególnie przydatne, jeśli później okaże się, że problem dotyczy sporadyczne. Być skrupulatne poprawianie listy z początku kolekcji danych i będzie można uniknąć godzin przetestowanie tych samych scenariuszy (I przedstawiono w ten sposób twardych wielu lat).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problem jest izolowanym, co teraz?
Im więcej może przyczyną problemu jest ustalenie, jednak często dotrzeć do punktu, w których nie można przejść głębiej i dodatkowych z rozwiązywania problemów. Przykład: Zobacz łącze między dostawcą usług, biorąc przeskoki w Europie, ale oczekiwano ścieżki znajduje się w Azji. Ten punkt jest, gdy powinni skontaktować się, aby uzyskać pomoc. Kto możesz zadawać pytania jest zależny od domeny routingu, które samodzielnie ten problem, aby lub jeszcze lepiej, jeśli jest to możliwe zawęzić jej do określonego składnika.

W przypadku problemów z siecią firmową z wewnętrznych działów IT lub dostawcą usług, obsługi sieci, (które mogą być producenta sprzętu) może mieć pomagające w konfiguracji urządzenia lub sprzętu naprawy.

Dla sieci WAN Udostępnianie wyników testów z dostawcy usług lub usługodawca Internetowy może pomóc w ich pracę i uniknąć obejmujące niektóre z tych samych podstaw, które zostały już przetestowane. Jednak nie zostać offended Jeśli chcą zweryfikować wyniki samodzielnie. "Zaufania, ale Sprawdź" jest dobrym motto podczas rozwiązywania problemów w oparciu o wyniki zgłoszonych inne osoby.

Dzięki platformie Azure po wyizolować problem w możliwie szczegółowo jesteś w stanie, nadszedł czas, aby zapoznać się z [dokumentacja usługi Azure Network] [ Network Docs] i następnie nadal w razie [Otwórz bilet pomocy technicznej][Ticket Link].

## <a name="references"></a>Dokumentacja
### <a name="latencybandwidth-expectations"></a>Oczekiwania dotyczące opóźnienia/przepustowości
>[!TIP]
> Opóźnienie geograficznych (milach lub kilometrach) od punktów końcowych, którą testujesz jest największym składnikiem opóźnienia. Gdy zaangażowanych jest opóźnienie sprzętu (składniki fizyczne i wirtualne, liczba przeskoków, itp.), lokalizacji geograficznej okazał się największym składnikiem ogólny czas oczekiwania podczas pracy z połączeń z siecią WAN. Jest również należy pamiętać, że jest odległość fiber Uruchom nie liniową lub odległości mapy drogowej. Jest to bardzo trudne do skorzystaj z dowolnego dokładności. W rezultacie I zazwyczaj w Internecie przy użyciu kalkulatora odległość miasta i wiedzieć, ta metoda jest rażąco niedokładne miary, ale jest wystarczający, aby ustawić ogólne oczekiwania.
>
>

Mam konfiguracji usługi ExpressRoute w Seattle, Waszyngton w Stanach Zjednoczonych. W poniższej tabeli przedstawiono opóźnienia i przepustowości widziałem testowania różnych lokalizacjach platformy Azure. Czy mogę zostały szacowany geograficznej odległości między każdy koniec testu.

Konfiguracja testu:
 - Serwer fizyczny, system Windows Server 2016 z 10 GB/s karty Sieciowej, połączone z obwodem usługi ExpressRoute.
 - Obwód usługi ExpressRoute Premium 10Gbps w lokalizacji identyfikowany za pomocą prywatnej komunikacji równorzędnej włączone.
 - Siecią wirtualną platformy Azure przy użyciu bramy UltraPerformance w wybranym regionie.
 - DS5v2 maszyny Wirtualnej z systemem Windows Server 2016 w sieci wirtualnej. Maszyna wirtualna była nienależących do domeny przyłączony, kompilowany z domyślnego obrazu platformy Azure (nie optymalizacji lub dostosowywania) AzureCT zainstalowane.
 - Wszystkich testów zostało za pomocą polecenia AzureCT Get-LinkPerformance testu obciążenia 5-minutowych dla każdego z sześciu przebiegów testów. Na przykład:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Przepływ danych dla każdego testu, ma obciążenia przepływać z lokalnego serwera fizycznego (dotyczące programu Iperf; klienta w Seattle) do maszyny Wirtualnej platformy Azure (server dotyczące programu Iperf; w liście regionie platformy Azure).
 - Danych kolumny "Opóźnienie" pochodzi z testu obciążeniowego nie (TCP testu opóźnienie bez dotyczące programu Iperf; uruchomione).
 - Danych kolumny "Maksymalna przepustowość" pochodzi z 16 testu obciążeniowego przepływu TCP z rozmiarem okna 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Wyniki opóźnienia/przepustowości
>[!IMPORTANT]
> Te liczby są ogólne tylko dla celów informacyjnych. Opóźnienie ma wpływ wiele czynników, a te wartości są zazwyczaj spójne wraz z upływem czasu, warunków w ramach platformy Azure lub w sieci dostawcy usług mogą przesyłać dane za pośrednictwem różnych ścieżek w dowolnym momencie, w związku z tym opóźnienia i przepustowości może mieć wpływ na. Ogólnie rzecz biorąc wpływ tych zmian nie powodują między nimi istotne różnice.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Lokalizacja|Azure<br/>Region|Szacowane<br/>Distance (km)|Opóźnienie|1 sesji<br/>Przepustowość|Maksimum<br/>Przepustowość|
| Seattle | Zachodnie stany USA 2        |    191 km |   5 ms | 262.0 MB/s |  3.74 Gbits na sekundę |
| Seattle | Zachodnie stany USA          |  1,094 km |  18 ms |  82.3 MB/s |  3.70 Gbits na sekundę |
| Seattle | Środkowe stany USA       |  2,357 km |  40 ms |  38.8 MB/s |  Do 2,55 Gbits/s |
| Seattle | Środkowo-południowe stany USA |  2,877 km |  51 ms |  30.6 MB/s |  2,49 Gbits na sekundę |
| Seattle | Środkowo-północne stany USA |  2,792 km |  55 ms |  27.7 MB/s |  2.19 Gbits na sekundę |
| Seattle | Wschodnie stany USA 2        |  3,769 km |  73 ms |  21.3 MB/s |  1.79 Gbits na sekundę |
| Seattle | Wschodnie stany USA          |  3,699 km |  74 ms |  21.1 MB/s |  1.78 Gbits na sekundę |
| Seattle | Japonia Wschodnia       |  7,705 km | 106 ms |  14.6 MB/s |  1.22 Gbits na sekundę |
| Seattle | Południowe Zjednoczone Królestwo         |  7,708 km | 146 ms |  10.6 MB/s |   896 MB/s |
| Seattle | Europa Zachodnia      |  7,834 km | 153 ms |  10.2 MB/s |   761 MB/s |
| Seattle | Australia Wschodnia   | 12,484 km | 165 ms |   9.4 MB/s |   794 MB/s |
| Seattle | Azja Południowo-Wschodnia   | 12,989 km | 170 ms |   9.2 MB/s |   756 MB/s |
| Seattle | Brazylia Południowa *   | 10,930 km | 189 ms |   8.2 MB/s |   699 MB/s |
| Seattle | Indie Południowe      | 12,918 km | 202 ms |   7.7 MB/s |   634 MB/s |

\* Czas oczekiwania na Brazylii jest dobrym przykładem, gdy liniową odległość znacznie różni się od fiber Uruchom odległości. Czy mogę oczekiwać byłoby w klubie 160 ms opóźnienie, ale jest faktycznie 189 ms. Ta różnica względem Moje oczekiwania może wskazywać na problem z siecią gdzieś, ale najprawdopodobniej czy fiber uruchamianych nie przechodzi do Brazylii prostym i dodatkowe km 1000 lub to podróż do Seattle uzyskiwanie Brazylii.

## <a name="next-steps"></a>Kolejne kroki
1. Pobierz zestaw narzędzi łączność platformy Azure z repozytorium GitHub w [https://aka.ms/AzCT][ACT]
2. Postępuj zgodnie z instrukcjami dotyczącymi [link testowania wydajnościowego][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "składniki sieci platformy azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Rozwiązywanie problemów z usługi ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "środowiska testowego wydajności"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "dane wyjściowe programu PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
