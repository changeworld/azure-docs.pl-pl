---
title: Rozwiązywanie problemów z wydajnością sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera standardowej metody testowania wydajności łącze sieci platformy Azure.
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
ms.locfileid: "27319148"
---
# <a name="troubleshooting-network-performance"></a>Rozwiązywanie problemów z wydajnością sieci
## <a name="overview"></a>Przegląd
Platforma Azure udostępnia stabilny i szybkie sposobów łączenia się z sieci lokalnej na platformie Azure. Metod, takich jak sieci VPN typu lokacja-lokacja i ExpressRoute pomyślnie są używane przez klientów, duże i małe do uruchamiania ich firm na platformie Azure. Ale co się dzieje, gdy wydajność nie spełnia Twoje oczekiwania lub doświadczenia? Ten dokument może pomóc normalizacji sposób testowania i linii bazowych określonego środowiska.

Ten dokument przedstawia, jak można łatwo i spójnej przetestować opóźnienia sieci i przepustowość między dwoma hostami. Ten dokument zawiera również pewne wskazówki dotyczące sposobów Spójrz na sieć platformy Azure i pomoc, aby odizolować punktów problem. Skrypt programu PowerShell i narzędzia omówione wymagają dwa hosty w sieci (na końcu łącza testowane). Jednego hosta musi być systemu Windows Server lub pulpit, innych systemu Windows lub Linux. 

>[!NOTE]
>Podejście do rozwiązywania problemów, narzędzia i metody używane są indywidualnych preferencji. Ten dokument zawiera opis podejście i narzędzia, które często wykonać. Swoje podejście, prawdopodobnie będą się różnić, nie ma problem z różne podejścia do rozwiązywania problemów. Jednak jeśli nie masz nawiązane podejście, ten dokument może ułatwiające rozpoczęcie pracy w ścieżce do tworzenia własnych metod, narzędzi i preferencje dotyczące rozwiązywania problemów z siecią.
>
>

## <a name="network-components"></a>Składniki sieciowe
Przed tu przeszukuje do rozwiązywania problemów, omówimy niektóre typowe warunki i składniki. Rozważania gwarantuje, że firma Microsoft nam o każdym składniku w łańcuchu end-to-end umożliwiającą łączność na platformie Azure.
[![1]][1]

Na najwyższym poziomie I opisano trzy główne sieci domeny routingu;

- sieć platformy Azure (blue chmura po prawej stronie)
- Internet lub sieć WAN (zielony chmury w Centrum)
- Sieci firmy (chmura brzoskwini po lewej stronie)

Spojrzenie na diagramie od prawej do lewej, omówimy krótko poszczególnych składników:
 - **Maszyna wirtualna** — serwer może mieć wiele kart sieciowych, upewnij się, wszelkie trasy statyczne, trasy domyślne oraz ustawień systemu operacyjnego są wysyłanie i odbieranie ruchu sposób podejrzewasz, że jest. Jednostka SKU każda maszyna wirtualna ma ograniczenie przepustowości. Jeśli używasz mniejszych SKU wirtualna ruchu jest ograniczona przepustowość dostępną dla karty sieciowej. Zwykle używać DS5v2 testowanie (i następnie usunięcie wykonywane raz z testowaniem zaoszczędzić) w celu zapewnienia odpowiedniej przepustowości w Maszynie wirtualnej.
 - **Karta sieciowa** — upewnij się, wiadomo, prywatnego adresu IP, do którego przypisano z kartą sieciową.
 - **Karta sieciowa NSG** — może być określonych grup NSG stosowana na poziomie karty Sieciowej, upewnij się, zestaw reguł NSG jest odpowiednia dla ruchu próbujesz przekazać. Na przykład Sprawdź porty 5201 dotyczące programu Iperf; 3389 protokołu RDP lub 22 dla protokołu SSH są otwarte, aby zezwolić na ruch testu do przekazania.
 - **Podsieć sieci wirtualnej** — karta sieciowa jest przypisany do określonej podsieci, upewnij się, wiesz, jakiego jedno i zasady skojarzone z tej podsieci.
 - **NSG podsieci** — podobnie jak karta sieciowa, grupy NSG można zastosować w tej podsieci, jak również. Upewnij się, zestaw reguł NSG jest odpowiednia dla ruchu, który próbujesz przekazać. (dla ruchu przychodzącego z kartą sieciową podsieci, w której grupa NSG stosowana najpierw, a następnie NSG karty Sieciowej, i odwrotnie dla ruchu wychodzącego z maszyny Wirtualnej karty Sieciowej NSG stosuje najpierw, a następnie NSG podsieci wejścia play).
 - **Podsieci przez** -trasy zdefiniowane przez użytkownika może kierować ruch do przeskoków pośrednich (na przykład zapory albo funkcji równoważenia obciążenia). Upewnij się, że wiesz, jeśli jest przez w ruchu na, jeśli więc gdzie ona trafia oraz do ruchu będzie czego ten następnego przeskoku. (na przykład zapora może przekazać część ruchu i odmowy ruch między tym samym dwa hosty).
 - **Podsieć bramy / NSG / przez** — podobnie jak podsieci maszyny Wirtualnej podsieć bramy może mieć grup NSG i Udr. Upewnij się, że wiesz, jeśli są dostępne i jaki wpływ mają one na ruchu.
 - **Brama sieci wirtualnej (ExpressRoute)** — po włączeniu komunikacji równorzędnej (ExpressRoute) lub sieci VPN nie ma wiele ustawień, które mogą wpłynąć na sposób lub, jeśli ruch trasy. Jeśli masz wiele obwody usługi ExpressRoute lub tuneli VPN podłączone do tej samej bramy sieci wirtualnej, należy zwrócić uwagę ustawień wagi połączenia jako to ustawienie ma wpływ na stronie połączeń i ma wpływ na ścieżkę, którą ma ruchu.
 - **Trasy filtru** (nie) - filtr tras tylko dotyczy Peering firmy Microsoft na ExpressRoute, ale jest szczególnie ważne, aby sprawdzić, jeśli nie jest wyświetlane trasy spodziewasz się na Peering firmy Microsoft. 

W tym momencie możesz teraz część łącza sieci WAN. Tej domeny routingu może być dostawcą usług, sieci WAN firmowej lub Internetu. Wiele przeskoków, technologii i przedsiębiorstw z tych łączy może utrudnić nieco rozwiązywać problemy. Często pracy do reguły zarówno Azure, jak i sieci firmowej najpierw przed przeskakiwanie do tej kolekcji firm i przeskoków.

Na powyższym diagramie na lewym jest sieci firmowej. W zależności od wielkości firmy tej domeny routingu może być kilka urządzeń sieciowych między możesz i WAN lub wielu warstw urządzeń w sieci firmy/enterprise.

Podana złożoności tych trzech różnych wysokiego poziomu środowisk sieci, często jest optymalna można uruchomić na krawędziach i spróbuj Pokaż wydajności w przypadku dobrej i gdzie powoduje spadek. Takie podejście może pomóc zidentyfikować problem domenę routingu trzy, a następnie skoncentrować się Rozwiązywanie problemów w tym w danym środowisku.

## <a name="tools"></a>Narzędzia
Większości problemów z siecią można analizować i samodzielnie przy użyciu podstawowe narzędzia, takie jak ping i traceroute. Jest rzadko, trzeba go jako analiza pakietów, takie jak Wireshark głębokość. Aby pomóc w rozwiązywaniu problemów, Toolkit łączności Azure (AzureCT) został opracowany, aby umieścić niektóre z tych narzędzi w pakiecie łatwe. Do testowania wydajności, I ma być używana dotyczące programu Iperf; i narzędzia PSPing. dotyczące programu Iperf; to powszechnie używane narzędzie i uruchamia na większości systemów operacyjnych. dotyczące programu Iperf; jest dobry dla testów wydajności podstawowe i jest dość łatwe w użyciu. Narzędzia PSPing to narzędzie ping opracowane przez SysInternals. Narzędzia PSPing to prosty sposób w celu wykonania polecenia ping protokołu ICMP i TCP w jedno polecenie także łatwy w użyciu. Oba te narzędzia są lekkie i "zainstalowano" po prostu przez kopiowanie plików do katalogu na hoście.

I została opakowana wszystkich tych narzędzi i metod do modułu środowiska PowerShell (AzureCT), który można zainstalować i używać.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - Toolkit Azure łączności
Moduł AzureCT PowerShell ma dwa składniki [testowania dostępności] [ Availability Doc] i [testowania wydajności][Performance Doc]. Ten dokument jest tylko dane testowania wydajności, dlatego pozwala skupić się na dwóch poleceń Link wydajności ten moduł programu PowerShell.

Istnieją trzy podstawowe kroki, aby użyć tego zestawu narzędzi do testowania wydajności. 1) Zainstaluj moduł programu PowerShell, 2) zainstaluj obsługi aplikacji dotyczące programu Iperf; i narzędzia PSPing 3) Uruchamianie testu wydajności.

1. Instalowanie modułu programu PowerShell

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    To polecenie pobiera modułu programu PowerShell i instaluje je lokalnie.

2. Instalowanie obsługi aplikacji
    ```powershell
    Install-LinkPerformance
    ```
    To polecenie AzureCT instaluje dotyczące programu Iperf; oraz narzędzia PSPing w nowym katalogu "C:\ACTTools", zostanie otwarte także porty zapory systemu Windows, aby umożliwić ICMP i portu 5201 ruchu (dotyczące programu Iperf;).

3. Uruchamianie testu wydajności

    Najpierw na zdalnym hoście musisz zainstalować i uruchomić dotyczące programu Iperf; w trybie serwera. Upewnij się również hosta zdalnego nasłuchuje albo 3389 (RDP dla systemu Windows) lub 22 (SSH w systemie Linux), dzięki czemu ruch na porcie 5201 dla dotyczące programu Iperf;. Jeśli host zdalny systemu windows, zainstaluj AzureCT i uruchom polecenie LinkPerformance instalacji, aby skonfigurować dotyczące programu Iperf; i reguły zapory wymaganej do rozpoczęcia dotyczące programu Iperf; pomyślnie w trybie serwera. 
    
    Gdy komputer zdalny jest gotowy, Otwórz program PowerShell na komputerze lokalnym i uruchomienia testu:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    To polecenie uruchamia serię równoczesnych obciążenia i testy opóźnienia, aby oszacować przepustowość i opóźnienia łącze sieci.

4. Przejrzyj dane wyjściowe testów

    Format danych wyjściowych programu PowerShell jest podobny do:

    [![4]][4]

    Szczegółowe wyniki dotyczące programu Iperf; i testy narzędzia PSPing znajdują się w tekst poszczególnych plików w katalogu narzędzia AzureCT na "C:\ACTTools."

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli test wydajności nie jest nadanie oczekiwanych rezultatów, ustaleniem, dlatego należy progresywnego krok po kroku proces. W ścieżce, biorąc pod uwagę liczbę składników, systematyczne podejście zwykle zapewnia szybsze ścieżkę do rozpoznawania niż przeskakiwanie wokół i potencjalnie niepotrzebnie ten sam testowania wiele razy.

>[!NOTE]
>Scenariusz, w tym miejscu jest problemu z wydajnością, nie problemem z łącznością. Kroki będą różne, jeśli ruch nie został w ogóle przekazywanie.
>
>

Po pierwsze żądanie założeń. Twoje oczekiwania jest uzasadnione? Na przykład jeśli masz obwodu ExpressRoute 1 GB i 100 ms opóźnienia jest nie można się spodziewać pełnej 1 GB/s ruchu podane charakterystyki wydajności protokołu TCP za pośrednictwem łącza duże opóźnienie. Zobacz [odwołuje się do sekcji](#references) dla więcej założenia na wydajność.

Następnie zaleca się uruchomienie na krawędziach między domenami routingu i spróbuj określić problem do pojedynczej domeny routingu głównych; Sieci firmowej, sieci WAN lub sieć platformy Azure. Użytkownicy często winy "czarne pole" w ścieżce podczas blaming czarne pole to łatwo zrobić, może być znacznie opóźniona rozpoznawania zwłaszcza, jeśli ten problem jest rzeczywiście w obszarze możliwości wprowadzania zmian. Upewnij się, czy z powodu starannością przed przekazaniem do dostawcy usług lub usługodawcy internetowego.

Po wyłaniają głównych domeny routingu, który wygląda jak zawiera problem, należy utworzyć diagram obszaru zagrożona. W tablicy, Notatnika lub programu Visio jako diagram zawiera konkretny "bitwy mapy" umożliwia metodyczny podejście do dalszego isolate problem. Planowanie punktów testowych i zaktualizuj mapy, zgodnie z wyczyść obszary lub lepszy Testowanie trakcie dig.

Teraz, gdy masz diagram Uruchom, aby podzielić sieć na segmenty i zawęzić problem. Dowiedz się, gdy działa i których nie. Zachowaj przenoszenie punkty testowych do izolowania do składnika ataku.

Ponadto nie zapomnij przyjrzeć się inne warstwy modelu OSI. Łatwo skoncentrować się na sieć i warstwy 1 – 3 (warstwy fizycznej, danych i sieci), ale problemy mogą również być aktywne w warstwy 7 w warstwie aplikacji. Zachowaj Otwórz zdanie i sprawdź założeń.

## <a name="advanced-expressroute-troubleshooting"></a>Zaawansowane rozwiązywanie problemów ExpressRoute
Jeśli nie masz pewności, gdzie jest rzeczywiście krawędzi chmury, izolowanie składniki platformy Azure może być wyzwaniem. Jeśli używane są połączenia ExpressRoute, krawędzi jest składnik sieci o nazwie krawędzi przedsiębiorstwa firmy Microsoft (MSEE). **Korzystając z usługi ExpressRoute**, MSEE jest pierwszy punkt kontaktu w sieci firmy Microsoft, a ostatniego przeskoku, pozostawiając sieci firmy Microsoft. Podczas tworzenia obiektu połączenia między bramą sieci wirtualnej i obwodem usługi ExpressRoute, faktycznie nawiązywania połączenia do MSEE. Rozpoznawanie MSEE jako pierwszego lub ostatniego przeskoków (w zależności od kierunku ma) odgrywa kluczową rolę do izolowania problemów z siecią Azure albo udowodnić, że problem polega na platformie Azure lub bardziej szczegółowo w dół w sieci WAN lub sieci firmowej. 

[![2]][2]

>[!NOTE]
> Należy zauważyć, że MSEE nie jest w chmurze Azure. ExpressRoute rzeczywiście znajduje się na granicy sieci firmy Microsoft w rzeczywistości w systemie Azure. Po nawiązaniu połączenia z usługi ExpressRoute, aby MSEE, jest podłączony do sieci firmy Microsoft, z tego miejsca można następnie przejść do żadnej z usług w chmurze, takie jak usługi Office 365 (ze Peering firmy Microsoft) lub Azure (za pomocą prywatnego i/lub Peering firmy Microsoft).
>
>

Jeśli dwie sieci wirtualnych (sieciami wirtualnymi A i B na diagramie) są połączone z **tego samego** obwodu ExpressRoute, można wykonywać serii testów, aby ustalić przyczynę problemu na platformie Azure (lub potwierdzenia nie znajduje się w usłudze Azure)
 
### <a name="test-plan"></a>Plan testu
1. Uruchom test Get-LinkPerformance między VM1 i maszyny VM2. Ten test zapewnia wgląd w, jeśli ten problem jest lokalny, czy nie. Jeśli ten test tworzy akceptowalnego czasu oczekiwania i wyniki przepustowości, można oznaczyć sieci lokalnej sieci wirtualnej jako dobra.
2. Zakładając, że lokalnej sieci wirtualnej ruch jest dobra, uruchom test Get-LinkPerformance między VM1 i VM3. Ten test sprawdza połączenie za pośrednictwem sieci firmy Microsoft do MSEE z powrotem do usługi Azure. Jeśli ten test tworzy akceptowalnego czasu oczekiwania i wyniki przepustowości, można oznaczyć sieć platformy Azure jako dobra.
3. Jeśli wykluczenia platformy Azure, można wykonać podobne sekwencji testów w sieci firmowej. Jeśli który również sprawdza również, to czas na współpracę z dostawcy usług lub usługodawcy internetowego, aby zdiagnozować połączenie WAN. Przykład: Uruchomić ten test między dwoma oddziałami firmy lub między biurku i serwer centrum danych. W zależności od tego, co w przypadku testowania, Znajdź punkty końcowe (serwery, komputery, itp.) który wykonywania tej ścieżki.

>[!IMPORTANT]
> Jest krytyczny, że dla każdego z testów oznaczyć godzinę uruchomienia testu i zapisać wyniki w jednej lokalizacji (podoba OneNote lub Excel). Każdy test powinien mieć identyczne dane wyjściowe można dane wynikowe umożliwia porównanie różnych uruchomień testów, a nie są "dziury" w danych. Zachowania spójności w wielu testów jest podstawowym powodem AzureCT można używać do rozwiązywania problemów. Magiczna nie jest w scenariuszach dokładne obciążenia uruchomić, ale zamiast tego *magic* jest fakt, że otrzymuję *spójne testu i danych wyjściowych* każdego testu. Podczas rejestrowania i o spójności danych co jeden raz jest szczególnie przydatne, jeśli później okaże się, że problem dotyczy sporadyczne. Być większego z góry kolekcji danych i będzie uniknąć godzin ponowne tych samych operacji (I przedstawiono w ten sposób twardych wielu lat).
>
>

## <a name="the-problem-is-isolated-now-what"></a>Problem jest izolowanym, co teraz?
Im bardziej można odizolować problem łatwiej jest ustalenie, jednak często osiągnięciu punktu, której nie można przejść głębiej i dodatkowych z rozwiązywania problemów. Przykład: Zobacz łącze między dostawcą usług biorąc przeskoków za pośrednictwem Europy, ale oczekiwano ścieżki są rejestrowane w Azji. Ten punkt jest, gdy powinna osiągnąć, aby uzyskać pomoc. Kto zadajesz jest zależny od domeny routingu, które izolowane problem do lub lepszą nawet jeśli jesteś w stanie zawęzić jej do określonego składnika.

Występują problemy z siecią firmową wewnętrzny w dziale IT lub dostawcy usług obsługującego sieci (które mogą być producenta sprzętu) można ułatwić konfigurację urządzenia lub naprawiania sprzętu.

Dla sieci WAN Udostępnianie wyników badania u dostawcy usług lub usługodawca Internetowy może pomóc powinien zostać uruchomiony i uniknąć obejmujące niektóre z tej samej ziemi, które już przetestowaniu. Jednak nie można offended Jeśli chcą zweryfikować wyniki samodzielnie. "Zaufania, ale Sprawdź" jest dobrym motto podczas rozwiązywania problemów w oparciu o wyniki zgłoszone innych osób.

Przy użyciu platformy Azure, po wyizolować problem dotyczący tyle szczegółów możesz, nadszedł czas na Przejrzyj [dokumentacji sieci Azure] [ Network Docs] , a następnie Jeśli nadal potrzebna [Otwórz bilet pomocy technicznej][Ticket Link].

## <a name="references"></a>Dokumentacja
### <a name="latencybandwidth-expectations"></a>Czas oczekiwania/przepustowość oczekiwania
>[!TIP]
> Geograficzne opóźnienia (milach lub kilometrach) od punktów końcowych, które w przypadku testowania jest składnik największy opóźnienia. Podczas zaangażowanych jest opóźnienia sprzętu (składniki fizyczne i wirtualne, liczba przeskoków, itp.), geography okazał się największym składnikiem ogólną opóźnienia podczas pracy nad połączeń z siecią WAN. Jest również pamiętać, że odległość odległość fiber Uruchom nie liniową lub odległości mapy drogowej. Jest to bardzo trudne do uzyskać z dowolnego dokładności. W związku z tym zazwyczaj w Internecie przy użyciu kalkulatora odległość mieście i wiedzieć, ta metoda jest rażąco niedokładne miary, ale jest wystarczająca do ustawienia oczekiwania ogólne.
>
>

Teraz już wiem konfiguracji usługi ExpressRoute w Seattle, Washington w USA. W poniższej tabeli przedstawiono opóźnienia i przepustowości I był wyświetlany testowanie do różnych lokalizacji platformy Azure. I zostały szacowany geograficzne odległość między każdego końca testu.

Testuj ustawienia:
 - Fizycznego serwera z systemem Windows Server 2016 z 10 GB/s karty Sieciowej, połączony z obwodem usługi ExpressRoute.
 - Obwód Premium ExpressRoute 10 GB/s w lokalizacji o prywatnej komunikacji równorzędnej włączone.
 - Sieć wirtualną platformy Azure z bramą UltraPerformance w wybranym regionie.
 - Maszyna wirtualna DS5v2, systemem Windows Server 2016 sieci wirtualnej. Maszyny Wirtualnej został domeny z systemem innym niż sprzężone, skompilowanych z domyślnego obrazu platformy Azure (nie optymalizacji lub dostosowania) za pomocą AzureCT zainstalowane.
 - Wszystkich testów został za pomocą polecenia AzureCT Get-LinkPerformance z testu obciążenia 5-minutowy dla każdej z sześciu uruchomień testów. Na przykład:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Przepływ danych dla każdego z testów ma obciążenia wynikających z lokalnego serwera fizycznego (dotyczące programu Iperf; klienta w Seattle) do maszyny Wirtualnej platformy Azure (server dotyczące programu Iperf; w wymienionych region platformy Azure).
 - Danych kolumny "Opóźnienie" pochodzi z testu obciążenia nie (TCP testu opóźnienia bez dotyczące programu Iperf; uruchomiona).
 - "Maksymalna przepustowość" danych kolumny pochodzi z 16 testu obciążenia przepływu TCP o rozmiarze okna 1 Mb.

[![3]][3]

### <a name="latencybandwidth-results"></a>Wyniki opóźnienia/przepustowość
>[!IMPORTANT]
> Numery te są tylko ogólne odwołania. Czas oczekiwania ma wpływ wiele czynników, a te wartości są zazwyczaj spójne w czasie, warunków w ramach platformy Azure lub w sieci dostawcy usług mogą przesyłać dane za pośrednictwem innej ścieżki w dowolnym momencie, w związku z tym opóźnienia i przepustowości mogą mieć wpływ. Ogólnie rzecz biorąc wpływu tych zmian nie powodują istotne różnice.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Lokalizacja|Azure<br/>Region|Szacowane<br/>Odległość (km)|Opóźnienie|1 sesji<br/>Przepustowość|Maksimum<br/>Przepustowość|
| Seattle | Zachodnie stany USA 2        |    191 km |   5 ms | 262.0 MB/s |  3.74 Gbits na sekundę | 21
| Seattle | Zachodnie stany USA          |  1,094 km |  18 ms |  82.3 MB/s |  3.70 Gbits na sekundę | 20
| Seattle | Środkowe stany USA       |  2,357 km |  40 ms |  38.8 MB/s |  Do 2,55 Gbits/s | 17
| Seattle | Środkowo-południowe stany USA |  2,877 km |  51 ms |  30.6 MB/s |  2,49 Gbits na sekundę | 19
| Seattle | Środkowo-północne stany USA |  2,792 km |  55 ms |  27.7 MB/s |  2.19 Gbits na sekundę | 18
| Seattle | Wschodnie stany USA 2        |  3,769 km |  73 ms |  21.3 MB/s |  1.79 Gbits na sekundę | 16
| Seattle | Wschodnie stany USA          |  3,699 km |  74 ms |  21.1 MB/s |  1.78 Gbits na sekundę | 15
| Seattle | Japonia Wschodnia       |  7,705 km | 106 ms |  14.6 MB/s |  1.22 Gbits na sekundę | 28
| Seattle | Południowe Zjednoczone Królestwo         |  7,708 km | 146 ms |  10.6 MB/s |   896 MB/s | 24
| Seattle | Europa Zachodnia      |  7,834 km | 153 ms |  10.2 MB/s |   761 MB/s | 23
| Seattle | Australia Wschodnia   | 12,484 km | 165 ms |   9.4 MB/s |   794 MB/s | 26
| Seattle | Azja Południowo-Wschodnia   | 12,989 km | 170 ms |   9.2 MB/s |   756 MB/s | 25
| Seattle | Brazylia Południowa *   | 10,930 km | 189 ms |   8.2 MB/s |   699 MB/s | 22
| Seattle | Indie Południowe      | 12,918 km | 202 ms |   7.7 MB/s |   634 MB/s | 27

\*Opóźnienie Brazylia jest dobrym przykładem, gdy odległość liniową znacznie różni się od fiber Uruchom odległości. Można oczekiwać byłoby w okolicy 160 ms opóźnienie, ale jest rzeczywiście 189 ms. Ta różnica względem Moje oczekiwania może wskazywać na problem z siecią gdzieś, ale najprawdopodobniej czy uruchomić fiber nie przechodzi Brazylia prostym i ma dodatkowe km 1000 lub celu podróży, aby uzyskać Brazylia z Seattle.

## <a name="next-steps"></a>Kolejne kroki
1. Pobierz zestaw narzędzi platformy Azure łączności z serwisu GitHub w [http://aka.ms/AzCT][ACT]
2. Postępuj zgodnie z instrukcjami dotyczącymi [link testowania wydajności][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "składniki sieci platformy azure"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute Rozwiązywanie problemów"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "środowiska testowego wydajności"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "dane wyjściowe programu PowerShell"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT











