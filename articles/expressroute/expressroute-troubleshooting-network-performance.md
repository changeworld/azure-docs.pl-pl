---
title: 'Rozwiązywanie problemów z wydajnością łącza sieciowego: platforma Azure'
description: Ta strona zawiera standardową metodę testowania wydajności łącza sieciowego platformy Azure.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869650"
---
# <a name="troubleshooting-network-performance"></a>Rozwiązywanie problemów z wydajnością sieci
## <a name="overview"></a>Omówienie
Platforma Azure oferuje stabilne i szybkie sposoby nawiązywania połączenia z sieci lokalnej do platformy Azure. Metody, takie jak połączenie typu lokacja-lokacja między siecią VPN i usługą ExpressRoute, są z sukcesem używane przez klientów, którzy w swoich dużych i małych firmach stosują platformę Azure. Ale co się dzieje, gdy wydajność nie spełnia Twoich oczekiwań lub wcześniejszych doświadczeń? Ten dokument może pomóc w standaryzacji sposobu testowania i planu bazowego określonego środowiska.

W tym dokumencie pokazano, jak można łatwo i konsekwentnie testować opóźnienie sieci i przepustowość między dwoma hostami. Ten dokument zawiera również kilka porad na temat sposobów przyjrzeć się sieci platformy Azure i pomóc wyizolowaniu punktów problemowych. Omówiony skrypt i narzędzia programu PowerShell wymagają dwóch hostów w sieci (na obu stronach testowanego łącza). Jeden host musi być windows server lub pulpit, drugi może być windows lub linux. 

>[!NOTE]
>Podejście do rozwiązywania problemów, narzędzia i metody używane są osobiste preferencje. W tym dokumencie opisano podejście i narzędzia, które często przyjmuję. Twoje podejście prawdopodobnie będzie się różnić, nie ma nic złego w różnych podejściach do rozwiązywania problemów. Jeśli jednak nie masz ustalonego podejścia, ten dokument może rozpocząć pracę na ścieżce do tworzenia własnych metod, narzędzi i preferencji rozwiązywania problemów z siecią.
>
>

## <a name="network-components"></a>Składniki sieci
Przed kopanie do rozwiązywania problemów, omówimy kilka typowych terminów i składników. Ta dyskusja gwarantuje, że myślimy o każdym składniku w łańcuchu end-to-end, który umożliwia łączność na platformie Azure.
![1][1]

Na najwyższym poziomie opisuję trzy główne domeny routingu sieciowego;

- sieci Azure (niebieska chmura po prawej)
- Internet lub WAN (zielona chmura w centrum)
- sieć firmowa (chmura brzoskwini po lewej)

Patrząc na diagram od prawej do lewej, porozmawiajmy krótko o każdym składniku:
 - **Maszyna wirtualna** — serwer może mieć wiele kart sieciowych, upewnij się, że wszystkie trasy statyczne, trasy domyślne i ustawienia systemu operacyjnego wysyłają i odbierają ruch w taki sposób, w jaki uważasz, że jest. Ponadto każda jednostka SKU maszyny Wirtualnej ma ograniczenie przepustowości. Jeśli używasz mniejszej jednostki SKU maszyny Wirtualnej, ruch jest ograniczony przez przepustowość dostępną dla karty sieciowej. Zwykle używam DS5v2 do testowania (a następnie usunąć raz zrobić z testowania, aby zaoszczędzić pieniądze), aby zapewnić odpowiednią przepustowość na maszynie wirtualnej.
 - **Karta sieciowa** — upewnij się, że znasz prywatny adres IP przypisany do danej karty sieciowej.
 - **NsG karty sieciowej** — mogą istnieć określone sieciowe sieciowe stosowane na poziomie karty sieciowej, upewnij się, że zestaw reguł sieciowej sieciowej sieciowej jest odpowiedni dla ruchu, który próbujesz przekazać. Na przykład upewnij się, że porty 5201 dla iPerf, 3389 dla protokołu RDP lub 22 dla SSH są otwarte, aby umożliwić przemijenie ruchu testowego.
 - **Podsieć sieci wirtualnej** — karta sieciowa jest przypisana do określonej podsieci, upewnij się, że wiesz, która z nich i reguły skojarzone z tą podsiecią.
 - **Podsieć NSG** - Podobnie jak karta sieciowa, sieciowe sieciowe mogą być stosowane również w podsieci. Upewnij się, że zestaw reguł nsg jest odpowiedni dla ruchu, który próbujesz przekazać. (dla ruchu przychodzącego do karty sieciowej podsieci NSG stosuje się najpierw, a następnie sieciowej sieciowej karty sieciowej karty sieciowej, odwrotnie dla ruchu wychodzącego z maszyny Wirtualnej sieci NSG stosuje się najpierw następnie podsieci NSG wchodzi w grę).
 - **UDR podsieci** — trasy zdefiniowane przez użytkownika mogą kierować ruch do przeskoku pośredniego (takiego jak zapora lub moduł równoważenia obciążenia). Upewnij się, że wiesz, czy istnieje UDR w miejscu dla ruchu, a jeśli tak, gdzie idzie i co ten następny przeskok zrobi dla ruchu. (na przykład zapora może przekazać część ruchu i odmówić innego ruchu między tymi samymi dwoma hostami).
 - **Podsieć bramy / NSG / UDR** — podobnie jak podsieć maszyny wirtualnej, podsieć bramy może mieć sieci NSG i UDR. Upewnij się, że wiesz, czy są tam i jakie mają wpływ na ruch.
 - **Brama sieci wirtualnej (ExpressRoute)** — po włączeniu komunikacji równorzędnej (usługi ExpressRoute) lub sieci VPN nie ma wielu ustawień, które mogą mieć wpływ na sposób i jeśli trasy ruchu. Jeśli do tej samej bramy sieci wirtualnej jest podłączonych wiele obwodów usługi ExpressRoute lub tuneli sieci VPN, należy pamiętać o ustawieniach wagi połączenia, ponieważ to ustawienie wpływa na preferencje połączenia i wpływa na ścieżkę ruchu.
 - **Filtr trasy** (nie pokazano) — filtr marszruty dotyczy tylko komunikacji równorzędnej firmy Microsoft w usłudze ExpressRoute, ale ma kluczowe znaczenie dla sprawdzenia, czy nie widzisz oczekiwanych tras w programie Microsoft Peering. 

W tym momencie jesteś w części WAN łącza. Ta domena routingu może być dostawcą usług, firmową siecią WAN lub Internetem. Wiele przeskoków, technologii i firm zaangażowanych w te łącza może nieco utrudnić rozwiązywanie problemów. Często najpierw można wykluczyć platformę Azure i sieci firmowe przed przejściem do tej kolekcji firm i przeskoków.

Na poprzednim diagramie po lewej stronie znajduje się sieć firmowa. W zależności od wielkości firmy ta domena routingu może być kilkoma urządzeniami sieciowymi między Tobą a siecią WAN lub wieloma warstwami urządzeń w sieci kampusu/przedsiębiorstwa.

Biorąc pod uwagę złożoność tych trzech różnych środowisk sieciowych wysokiego poziomu, często optymalne jest rozpoczęcie od krawędzi i próba pokazania, gdzie wydajność jest dobra i gdzie się obniża. Takie podejście może pomóc zidentyfikować problem routingu domeny trzech, a następnie skupić rozwiązywania problemów na tym określonym środowisku.

## <a name="tools"></a>Narzędzia
Większość problemów z siecią można analizować i izolować za pomocą podstawowych narzędzi, takich jak ping i traceroute. Rzadko zdarza się, że musisz iść tak głęboko, jak analiza pakietów, takich jak Wireshark. Aby ułatwić rozwiązywanie problemów, opracowano zestaw narzędzi azure connectivity toolkit (AzureCT), aby umieścić niektóre z tych narzędzi w łatwym pakiecie. Do testowania wydajności, lubię używać iPerf i PSPing. iPerf jest powszechnie używanym narzędziem i działa na większości systemów operacyjnych. iPerf jest dobry do podstawowych testów wydajności i jest dość łatwy w użyciu. PSPing jest narzędziem ping opracowanym przez SysInternals. PSPing jest łatwym sposobem wykonywania pingów ICMP i TCP w jednym również łatwym w użyciu poleceniu. Oba te narzędzia są lekkie i są "zainstalowane" po prostu przez radzenie sobie z plikami do katalogu na hoście.

Opakowałem wszystkie te narzędzia i metody w moduł programu PowerShell (AzureCT), który można zainstalować i używać.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT — zestaw narzędzi łączności platformy Azure
Moduł programu AzureCT PowerShell ma dwa składniki [Testowanie dostępności][Availability Doc] i [testowanie wydajności.][Performance Doc] Ten dokument dotyczy tylko testowania wydajności, więc pozwala skupić się na dwóch poleceniach Wydajności łącza w tym module programu PowerShell.

Istnieją trzy podstawowe kroki, aby użyć tego zestawu narzędzi do testowania wydajności. 1) Zainstaluj moduł Programu PowerShell, 2) Zainstaluj aplikacje pomocnicze iPerf i PSPing 3) Uruchom test wydajności.

1. Instalowanie modułu programu PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    To polecenie pobiera moduł programu PowerShell i instaluje go lokalnie.

2. Instalowanie aplikacji pomocniczych
    ```powershell
    Install-LinkPerformance
    ```
    To polecenie AzureCT instaluje iPerf i PSPing w nowym katalogu "C:\ACTTools", otwiera również porty Zapory systemu Windows, aby umożliwić ruch ICMP i port 5201 (iPerf).

3. Uruchom test wydajności

    Najpierw na hoście zdalnym należy zainstalować i uruchomić iPerf w trybie serwera. Upewnij się również, że host zdalny nasłuchuje na 3389 (RDP dla Windows) lub 22 (SSH dla Linuksa) i zezwala na ruch na porcie 5201 dla iPerf. Jeśli host zdalny jest windows, zainstaluj AzureCT i uruchom polecenie Install-LinkPerformance, aby skonfigurować iPerf i reguły zapory potrzebne do pomyślnego uruchomienia iPerf w trybie serwera. 
    
    Gdy komputer zdalny będzie gotowy, otwórz program PowerShell na komputerze lokalnym i rozpocznij test:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    To polecenie uruchamia serię równoczesnych testów obciążenia i opóźnienia, aby oszacować przepustowość i opóźnienie łącza sieciowego.

4. Przegląd wyników testów

    Format wyjściowy programu PowerShell wygląda podobnie do:

    ![4][4]

    Szczegółowe wyniki wszystkich testów iPerf i PSPing znajdują się w poszczególnych plikach tekstowych w katalogu narzędzi AzureCT w "C:\ACTTools".

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli test wydajności nie daje oczekiwanych wyników, dowiedzieć się, dlaczego powinien być procesem krok po kroku progresywne. Biorąc pod uwagę liczbę składników w ścieżce, systematyczne podejście zazwyczaj zapewnia szybszą ścieżkę do rozdzielczości niż skakanie i potencjalnie niepotrzebnie wykonywanie tych samych testów wiele razy.

>[!NOTE]
>W tym scenariuszu jest problem z wydajnością, a nie problem z łącznością. Kroki byłyby inne, gdyby ruch w ogóle nie przechodził.
>
>

Po pierwsze, zakwestionować swoje założenia. Czy twoje oczekiwania są uzasadnione? Na przykład, jeśli masz obwód 1-Gbps ExpressRoute i 100 ms opóźnienia jest nieuzasadnione oczekiwać pełnego 1 Gb/s ruchu, biorąc pod uwagę charakterystykę wydajności TCP przez łącza o dużym opóźnieniu. Zobacz [odwołania sekcji](#references) więcej na temat założeń wydajności.

Następnie polecam rozpoczęcie od krawędzi między domenami routingu i spróbuj wyizolować problem do jednej głównej domeny routingu; sieci firmowej, sieci WAN lub sieci Azure. Ludzie często obwiniają "czarną skrzynkę" na ścieżce, podczas gdy obwinianie czarnej skrzynki jest łatwe do zrobienia, może znacznie opóźnić rozwiązanie, zwłaszcza jeśli problem jest rzeczywiście w obszarze, w którym masz możliwość wprowadzania zmian. Przed przekazaniem należytej staranności należytej staranności należytej staranności.

Po zidentyfikowaniu głównej domeny routingu, która wydaje się zawierać problem, należy utworzyć diagram danego obszaru. Na tablicy, notatniku lub programie Visio jako diagramie znajduje się konkretna "mapa bitwy", aby umożliwić metodyczne podejście do dalszego izolowania problemu. Możesz planować punkty testowe i aktualizować mapę podczas wyczyszczenie obszarów lub kopanie głębiej w miarę postępu testowania.

Teraz, gdy masz diagram, zacznij dzielić sieć na segmenty i zawęź problem. Dowiedz się, gdzie działa, a gdzie nie. Kontynuuj przenoszenie punktów testowych, aby odizolować się do składnika naruszającego.

Nie zapomnij również spojrzeć na inne warstwy modelu OSI. Łatwo jest skupić się na sieci i warstwach 1 - 3 (warstwy fizyczne, dane i sieci), ale problemy mogą być również w warstwie 7 w warstwie aplikacji. Zachowaj otwarty umysł i zweryfikuj założenia.

## <a name="advanced-expressroute-troubleshooting"></a>Zaawansowane rozwiązywanie problemów z programem ExpressRoute
Jeśli nie masz pewności, gdzie faktycznie znajduje się krawędź chmury, izolowanie składników platformy Azure może być wyzwaniem. Gdy używany jest program ExpressRoute, krawędź jest składnikiem sieciowym o nazwie Microsoft Enterprise Edge (MSEE). **Podczas korzystania z usługi ExpressRoute,** MSEE jest pierwszym punktem kontaktu w sieci firmy Microsoft, a ostatni przeskok opuszczania sieci Firmy Microsoft. Podczas tworzenia obiektu połączenia między bramą sieci wirtualnej a obwodem usługi ExpressRoute faktycznie nawiązujesz połączenie z msee. Rozpoznawanie MSEE jako pierwszego lub ostatniego przeskoku (w zależności od kierunku, w którym zmierzasz) ma kluczowe znaczenie dla izolowania problemów z usługą Azure Network, aby udowodnić, że problem występuje na platformie Azure lub dalej w sieci WAN lub sieci firmowej. 

![2][2]

>[!NOTE]
> Należy zauważyć, że MSEE nie jest w chmurze platformy Azure. Usługa ExpressRoute znajduje się na obrzeżach sieci Firmy Microsoft, a nie na platformie Azure. Po połączeniu z usługą ExpressRoute do msee, masz połączenie z siecią firmy Microsoft, stamtąd można przejść do dowolnej usługi w chmurze, takich jak Office 365 (z Microsoft Peering) lub Azure (z prywatnych i / lub Microsoft peering).
>
>

Jeśli dwie sieci wirtualne (sieci wirtualne A i B na diagramie) są połączone z **tym samym** obwodem usługi ExpressRoute, można wykonać serię testów, aby wyizolować problem na platformie Azure (lub udowodnić, że nie ma go na platformie Azure)
 
### <a name="test-plan"></a>Plan badań
1. Uruchom test Get-LinkPerformance między VM1 i VM2. Ten test zapewnia wgląd, czy problem jest lokalny, czy nie. Jeśli ten test daje dopuszczalne wyniki opóźnienia i przepustowości, można oznaczyć lokalną sieć wirtualną jako dobrą.
2. Zakładając, że lokalny ruch w sieci wirtualnej jest dobry, uruchom test Get-LinkPerformance między VM1 i VM3. Ten test wykonuje połączenie za pośrednictwem sieci firmy Microsoft aż do MSEE i z powrotem na platformie Azure. Jeśli ten test daje dopuszczalne wyniki opóźnienia i przepustowości, można oznaczyć sieci platformy Azure jako dobre.
3. Jeśli platforma Azure jest wykluczona, można wykonać podobną sekwencję testów w sieci firmowej. Jeśli to również dobrze sprawdza, nadszedł czas, aby współpracować z usługodawcą lub usługodawcą internetowym w celu zdiagnozowania połączenia WAN. Przykład: Uruchom ten test między dwoma oddziałami firmy lub między biurkiem a serwerem centrum danych. W zależności od tego, co testujesz, znajdź punkty końcowe (serwery, komputery itp.), które mogą wykonywać tę ścieżkę.

>[!IMPORTANT]
> Bardzo ważne jest, aby dla każdego testu oznaczyć godzinę uruchomienia testu i zarejestrować wyniki we wspólnej lokalizacji (lubię onenote lub excel). Każde uruchomienie testu powinien mieć identyczne dane wyjściowe, dzięki czemu można porównać dane wynikowe w przebiegach testów i nie mają "otwory" w danych. Spójność w wielu testach jest głównym powodem, dla którego używam azurect do rozwiązywania problemów. Magia nie jest w dokładnych scenariuszach obciążenia, które uruchamiam, ale *zamiast tego magią* jest fakt, że otrzymuję *spójny test i dane wyjściowe* z każdego testu. Rejestrowanie czasu i posiadanie spójnych danych za każdym razem jest szczególnie przydatne, jeśli później okaże się, że problem jest sporadyczny. Bądź sumienny z gromadzeniem danych z góry, a unikniesz godzin ponownego testowania tych samych scenariuszy (nauczyłem się tego ciężko wiele lat temu).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problem jest odosobniony, co teraz?
Im bardziej możesz wyizolować problem, tym łatwiej jest go naprawić, jednak często docierasz do punktu, w którym nie możesz pójść głębiej lub dalej z rozwiązywaniem problemów. Przykład: zobaczysz link między usługodawcą, który przeskakuje przez Europę, ale oczekiwana ścieżka znajduje się w Azji. W tym momencie należy zwrócić się o pomoc. To, kogo pytasz, zależy od domeny routingu, do której odizolowałeś problem, lub nawet lepiej, jeśli można zawęzić go do określonego składnika.

W przypadku problemów z siecią firmową wewnętrzny dział IT lub usługodawca obsługujący sieć (który może być producentem sprzętu) może być w stanie pomóc w konfiguracji urządzenia lub naprawie sprzętu.

W przypadku sieci WAN udostępnienie wyników testów usługodawcy lub usługodawcy zewnętrznego może pomóc w rozpoczęciu ich pracy i uniknięciu pokrycia niektórych z tych samych badanych już podstaw. Jednak nie obrażaj się, jeśli chcą zweryfikować swoje wyniki. "Zaufaj, ale sprawdź" to dobre motto podczas rozwiązywania problemów na podstawie zgłoszonych wyników innych osób.

Dzięki platformie Azure po wyizolowaniu problemu w miarę możliwości, nadszedł czas, aby przejrzeć [dokumentację sieci platformy Azure,][Network Docs] a następnie, jeśli nadal [potrzebujesz, otwórz bilet pomocy technicznej.][Ticket Link]

## <a name="references"></a>Dokumentacja
### <a name="latencybandwidth-expectations"></a>Oczekiwania dotyczące opóźnienia/przepustowości
>[!TIP]
> Opóźnienie geograficzne (mile lub kilometry) między testuuszymi punktami końcowymi jest zdecydowanie największym składnikiem opóźnienia. Chociaż istnieje opóźnienie sprzętu (składniki fizyczne i wirtualne, liczba przeskoków, itp.), geografia okazała się największym składnikiem ogólnego opóźnienia podczas radzenia sobie z połączeniami WAN. Ważne jest również, aby pamiętać, że odległość jest odległość włókna uruchomić nie prosto linii lub odległość mapy drogowej. Ta odległość jest niezwykle trudna do zdobycia z dowolną dokładnością. W rezultacie zwykle używam kalkulatora odległości miasta w Internecie i wiem, że ta metoda jest rażąco niedokładną miarą, ale wystarczy, aby ustawić ogólne oczekiwania.
>
>

Mam konfigurację usługi ExpressRoute w Seattle w stanie Waszyngton w USA. W poniższej tabeli przedstawiono opóźnienie i przepustowość, które widziałem testowania w różnych lokalizacjach platformy Azure. Oszacowałem odległość geograficzną między każdym końcem testu.

Konfiguracja testu:
 - Serwer fizyczny z systemem Windows Server 2016 z kartą sieciową o przekątnej 10 Gb/s, połączony z obwodem usługi ExpressRoute.
 - Obwód usługi Premium ExpressRoute o przekątnej 10 Gb/s w lokalizacji identyfikowanej z włączoną funkcją prywatnej komunikacji równorzędnej.
 - Sieci wirtualnej platformy Azure z bramą UltraPerformance w określonym regionie.
 - Maszyna wirtualna DS5v2 z systemem Windows Server 2016 w sieci wirtualnej. Maszyna wirtualna została przyłączona do domeny, zbudowana na podstawie domyślnego obrazu platformy Azure (bez optymalizacji lub dostosowywania) z zainstalowaną usługą AzureCT.
 - Wszystkie testy były przy użyciu polecenia AzureCT Get-LinkPerformance z 5-minutowym testem obciążenia dla każdego z sześciu przebiegów testowych. Przykład:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Przepływ danych dla każdego testu miał obciążenie płynące z lokalnego serwera fizycznego (klienta iPerf w Seattle) do maszyny Wirtualnej platformy Azure (serwera iPerf w wymienionym regionie platformy Azure).
 - Dane kolumny "Opóźnienie" pochodzą z testu Brak obciążenia (test opóźnienia TCP bez uruchomienia iPerf).
 - Dane kolumny "Maksymalna przepustowość" pochodzą z testu obciążenia przepływu 16 TCP o rozmiarze okna 1 Mb.

![3][3]

### <a name="latencybandwidth-results"></a>Wyniki opóźnienia/przepustowości
>[!IMPORTANT]
> Liczby te służą wyłącznie do celów ogólnych. Wiele czynników wpływa na opóźnienie i chociaż te wartości są zazwyczaj spójne w czasie, warunki w ramach platformy Azure lub sieci dostawców usług mogą wysyłać ruch za pośrednictwem różnych ścieżek w dowolnym momencie, w związku z czym może to mieć wpływ na opóźnienie i przepustowość. Ogólnie rzecz biorąc, skutki tych zmian nie powodują znaczących różnic.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Lokalizacja|Azure<br/>Region|Szacowany<br/>Odległość (km)|Opóźnienie|1 Sesja<br/>Przepustowość|Maksimum<br/>Przepustowość|
| Seattle | Zachodnie stany USA 2        |    191 km |   5 ms. | 262,0 Mbits/s |  3,74 Glity/s |
| Seattle | Zachodnie stany USA          |  1094 km |  18 ms |  82,3 Mbits/s |  3,70 Glików na sekundę |
| Seattle | Środkowe stany USA       |  2 357 km |  40 ms |  38,8 Mbits/s |  2,55 Glity/s |
| Seattle | Południowo-środkowe stany USA |  2 877 km |  51 ms. |  30,6 Mbits/s |  2,49 Glity/s |
| Seattle | Północno-środkowe stany USA |  2 792 km |  55 ms. |  27,7 Mbits/s |  2,19 Glików na sekundę |
| Seattle | Wschodnie stany USA 2        |  3 769 km |  73 ms. |  21,3 Mbits/s |  1,79 Glity/s |
| Seattle | Wschodnie stany USA          |  3 699 km |  74 ms. |  21,1 Mbits/s |  1,78 Glity/s |
| Seattle | Japonia Wschodnia       |  7 705 km | 106 ms. |  14,6 Mbits/s |  1,22 Glity/s |
| Seattle | Południowe Zjednoczone Królestwo         |  7 708 km | 146 ms. |  10,6 Mbits/s |   896 Mbits/s |
| Seattle | Europa Zachodnia      |  7 834 km | 153 ms. |  10,2 Mbits/s |   761 Mbits/s |
| Seattle | Australia Wschodnia   | 12 484 km | 165 ms. |   9,4 Mbits/s |   794 Mbits/s |
| Seattle | Azja Południowo-Wschodnia   | 12 989 km | 170 ms |   9,2 Mbits/s |   756 Mbits/s |
| Seattle | Brazylia Południowa *   | 10 930 km | 189 ms |   8,2 Mbits/s |   699 Mbits/s |
| Seattle | Indie Południowe      | 12 918 km | 202 ms. |   7,7 Mbits/s |   634 Mbits/s |

\*Opóźnienie do Brazylii jest dobrym przykładem, gdzie odległość linii prostej znacznie różni się od odległości przebiegu włókna. Spodziewam się, że opóźnienie będzie w sąsiedztwie 160 ms, ale jest rzeczywiście 189 ms. Ta różnica w stosunku do moich oczekiwań może wskazywać na problem z siecią gdzieś, ale najprawdopodobniej, że uruchomić włókna nie idzie do Brazylii w linii prostej i ma dodatkowe 1000 km lub tak podróży, aby dostać się do Brazylii z Seattle.

## <a name="next-steps"></a>Następne kroki
1. Pobierz zestaw narzędzi łączności platformy Azure z usługi GitHub pod adresem[https://aka.ms/AzCT][ACT]
2. Postępuj zgodnie z instrukcjami [dotyczącymi testowania wydajności łącza][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Składniki sieci platformy Azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "Rozwiązywanie problemów z usługią ExpressRoute"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Środowisko testowe perf"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "Wyjście programu PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
