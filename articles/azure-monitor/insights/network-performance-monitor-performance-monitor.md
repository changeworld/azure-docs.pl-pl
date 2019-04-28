---
title: Funkcja monitorowania wydajności w rozwiązania Network Performance Monitor w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: Funkcja monitora wydajności w Monitorze wydajności sieci pomaga monitorować łączność sieciową w różnych punktach w sieci. Można monitorować wdrożeń w chmurze i lokalizacjami lokalnymi, wiele centrów danych i biur oddziałów i kluczowych dla działalności aplikacji wielowarstwowej lub mikrousług.
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
ms.openlocfilehash: de9e6d5c8c6b4dc9652ae64c9a4fd0e38d7a7b87
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109542"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Network Performance Monitor rozwiązania: Monitorowanie wydajności

Funkcja monitorowania wydajności w [rozwiązania Network Performance Monitor](network-performance-monitor.md) pomaga w monitorowaniu połączenia sieciowego w różnych punktach w sieci. Można monitorować wdrożeń w chmurze i lokalizacjami lokalnymi, wiele centrów danych i biur oddziałów i kluczowych dla działalności aplikacji wielowarstwowej lub mikrousług. Za pomocą Monitora wydajności można wykrywać problemy z siecią, zanim użytkownicy zaczną zgłaszać skargi. Najważniejsze zalety są, możesz: 

- Monitorowanie strat i opóźnień w różnych podsieciach oraz ustawianie alertów.
- Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci.
- Rozwiązywanie problemów przejściowych i w momencie sieci, które trudno odtworzyć.
- Ustalanie konkretnych segmentów sieci, czyli odpowiedzialnych za pogorszenie wydajności.
- Monitorowanie kondycji sieci bez potrzeby używania protokołu SNMP.


![Monitor wydajności sieci](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurowanie
Aby otworzyć konfigurację rozwiązania Network Performance Monitor, otwórz [rozwiązania Network Performance Monitor](network-performance-monitor.md)i wybierz **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Tworzenie nowych sieci

Sieć w rozwiązania Network Performance Monitor jest kontenerem logicznym dla podsieci. Pomaga organizować Monitorowanie infrastruktury sieci, zgodnie z potrzebami. Można utworzyć sieć przy użyciu przyjaznej nazwy i dodać podsieci do niego zgodnie z logiki biznesowej. Na przykład można utworzyć sieci o nazwie Londyn i Dodaj wszystkie podsieci w centrum danych w Londynie. Lub można utworzyć sieci o nazwie *ContosoFrontEnd* i dodać do tej sieci wszystkie podsieci o nazwie Contoso, które pełnią fronton aplikacji. Rozwiązanie automatycznie tworzy domyślnej sieci, który zawiera wszystkie wykryte w danym środowisku podsieci. 

Zawsze, gdy użytkownik utworzy sieć podsieci należy dodać do niego. Następnie tej podsieci jest usuwany z domyślnej sieci. Jeśli usuniesz sieci, wszystkie jego podsieci automatycznie powrót do domyślnej sieci. Sieć domyślna działa jako kontener dla wszystkich podsieci, które nie są zawarte w dowolnej sieci zdefiniowanej przez użytkownika. Nie można edytować ani usunąć domyślnej sieci. Zawsze pozostaje w systemie. Można utworzyć dowolną liczbę sieci niestandardowe. W większości przypadków podsieci w Twojej organizacji są rozmieszczone w więcej niż jedną sieć. Utwórz co najmniej jedną sieć, aby zgrupować podsieci dla logiki biznesowej.

Aby utworzyć nową sieć:


1. Wybierz **sieci** kartę.
1. Wybierz **sieci Dodaj**, a następnie wprowadź nazwę sieci i opis. 
2. Wybierz co najmniej jednej podsieci, a następnie wybierz **Dodaj**. 
3. Wybierz **Zapisz** Aby zapisać konfigurację. 


### <a name="create-monitoring-rules"></a>Tworzenie reguły monitorowania 

Monitor wydajności generuje zdarzenia kondycji w przypadku naruszenia progu wydajności połączenia sieciowe między dwiema podsieciami lub między dwiema sieciami. System może automatycznie informacje tych progów. Można również podać progami niestandardowymi. System automatycznie tworzy domyślną regułę, która generuje zdarzenie kondycji zawsze wtedy, gdy stratę lub opóźnienie między jakaś para sieci lub podsieci łączy naruszenia progu system do opanowania. Ten proces pomaga w rozwiązaniu Monitorowanie infrastruktury sieci, dopóki nie utworzono żadnych reguł monitorowania, jawnie. Jeśli domyślna reguła jest włączona, wszystkie węzły wysyłać transakcji syntetycznych wszystkie węzły, które jest włączony dla monitorowania. Reguła domyślna jest przydatne w przypadku małych sieciach. Przykładem jest scenariusz, w którym małej liczby serwerów z systemem mikrousługi i chcesz upewnić się, że wszystkie serwery mają łączność do siebie nawzajem.

>[!NOTE]
> Firma Microsoft zaleca wyłączenie domyślną regułę, a następnie tworzy niestandardowe reguły monitorowania, szczególnie w dużych sieciach, w którym można korzystać z dużą liczbę węzłów do monitorowania. Niestandardowe reguły monitorowania może zmniejszyć ruch generowany przez rozwiązania i pomagają organizować monitorowania sieci.

Utwórz reguły monitorowania zgodnie z logiki biznesowej. Przykładem jest, jeśli chcesz monitorować wydajność na łączność sieciową z dwiema lokacjami pakietu office, aby centrali. Grupa wszystkich podsieci w site1 pakietu office w sieci O1. Następnie zgrupuj wszystkich podsieci w site2 pakietu office w sieci O2. Na koniec grupy wszystkich podsieci w siedzibie głównej w sieci H. Utwórz dwie reguły monitorowania — jeden między O1 i H, a druga między O2 i H. 

Aby utworzyć niestandardowe reguły monitorowania:

1. Wybierz **Dodaj regułę** na **Monitor** kartę, a następnie wprowadź nazwę reguły i opis.
2. Wybierz pary łączy sieciowych lub podsieci do monitorowania z listy. 
3. Wybierz sieć, która zawiera podsieci, z listy rozwijanej sieć. Następnie wybierz podsieci, z listy rozwijanej odpowiednie podsieci. Aby monitorować wszystkie podsieci w połączenia sieciowego, należy zaznaczyć **wszystkie podsieci**. Podobnie wybierz podsieci, które chcesz. Aby wykluczyć monitorowania dla łącza podsieci w szczególności z wybranych, wybierz **dodać wyjątek**. 
4. Wybieranie między ICMP i TCP protokołów do wykonania transakcji syntetycznych. 
5. Jeśli nie chcesz utworzyć zdarzenia dotyczące kondycji dla elementów został wybrany, wyczyść **Włącz monitorowanie kondycji połączeń objętych tą regułą**. 
6. Wybierz opcję monitorowania warunków. Aby ustawić niestandardowe progi dla generowania zdarzenia kondycji, należy wprowadzić wartości progowe. Zawsze, gdy wartość warunku przekroczy próg wybranych dla wybranej sieci lub pary podsieci, jest generowane zdarzenie kondycji. 
7. Wybierz **Zapisz** Aby zapisać konfigurację. 

Po zapisaniu regułę monitorowania tej reguły można zintegrować z zarządzaniem alertami, wybierając **tworzenia alertu**. Reguła alertu jest tworzony automatycznie przy użyciu zapytania wyszukiwania. Inne wymagane parametry są wypełniane automatycznie. Reguła alertu można otrzymywać alertów opartych na wiadomościach e-mail, oprócz istniejące alerty w ramach rozwiązania Network Performance Monitor. Alerty również mogą powodować naprawcze przy użyciu elementów runbook lub mogą oni także integrować z istniejącymi rozwiązaniami do zarządzania usługi przy użyciu elementów webhook. Wybierz **Zarządzanie Alert** Aby edytować ustawienia alertów. 

Teraz można utworzyć więcej reguł monitora wydajności lub przejdź do pulpitu nawigacyjnego rozwiązania, aby użyć funkcji.

### <a name="choose-the-protocol"></a>Wybierz protokół

Rozwiązanie Network Performance Monitor używa transakcji syntetycznych do obliczania metryki wydajności sieci, takie jak czas oczekiwania przed utratą i łącze pakietów. Aby lepiej zrozumieć tę koncepcję, należy wziąć pod uwagę agenta rozwiązania Network Performance Monitor, połączyć jeden z końców połączenia sieciowego. Ten agent rozwiązania Network Performance Monitor wysyła pakiety sondy do agenta rozwiązania Network Performance Monitor drugiego podłączony do końca innej sieci. Drugi agent odpowiada za pomocą pakietów odpowiedzi. Ten proces jest powtarzany kilka razy. Mierząc liczba odpowiedzi i czas potrzebny na odebranie odpowiedzi, pierwszy agent rozwiązania Network Performance Monitor ocenia opóźnienie linku oraz operacjach porzucania pakietów. 

Format, rozmiar i kolejność tych pakietów jest określany przez protokół, który wybierzesz podczas tworzenia reguły monitorowania. Oparte na protokole pakietów, urządzenia sieciowe pośredniego, takich jak routery i przełączniki, może przetwarzać te pakiety inaczej. W związku z tym wybranego protokołu ma wpływ na dokładność wyników. Wybór protokołu określa również, czy należy wykonać wszelkie ręcznie wykonane czynności po wdrożeniu rozwiązania Network Performance Monitor. 

Rozwiązanie Network Performance Monitor zapewnia wybór między protokołów ICMP i TCP do wykonywania transakcji syntetycznych. Jeśli wybierzesz protokołu ICMP, podczas tworzenia reguły transakcji syntetycznych, agenci rozwiązania Network Performance Monitor korzystać komunikatów ECHA ICMP do obliczania opóźnienie sieci i utraty pakietów. Ten sam komunikat, który jest wysyłany przez narzędzie konwencjonalne korzysta z ECHA ICMP. Użycie jako protokołu TCP, rozwiązania Network Performance Monitor agenci wysyłają TCP SYN za pośrednictwem sieci pakietów. W tym kroku następuje po zakończeniu uzgadnianie protokołu TCP, a następnie połączenie zostanie usunięte przy użyciu pakietów RST. 

Przed wybraniem protokół, należy wziąć pod uwagę następujące informacje: 

* **Odnajdywanie wielu tras sieciowych.** Protokół TCP jest bardziej precyzyjne operacje podczas rozpoznawania wiele tras i wymaga mniejszej liczby agentów w każdej podsieci. Na przykład jeden lub dwaj agenci, które używają protokołu TCP, można wykryć wszystkie nadmiarowe ścieżki między podsieciami. Konieczne jest kilku agentów, korzystających z protokołu ICMP w celu uzyskania podobnych wyników. Za pomocą protokołu ICMP, jeśli masz wiele tras między dwiema podsieciami, potrzebujesz więcej niż 5 n agentów w podsieci źródłowych i docelowych.

* **Dokładność wyników.** Routery i przełączniki zwykle można przypisać niższego priorytetu do pakietów ECHA protokołu ICMP w porównaniu do pakietów protokołu TCP. W niektórych sytuacjach po obciążonej urządzeń sieciowych, dane uzyskane przez protokół TCP lepiej odzwierciedla, strat i opóźnień napotykanych przez aplikacje. Dzieje się tak, ponieważ większość ruchu aplikacji przepływu za pośrednictwem protokołu TCP. W takich przypadkach ICMP zapewnia mniej dokładne wyniki, w porównaniu do protokołu TCP. 

* **Konfiguracja zapory.** Protokół TCP wymaga, że pakiety TCP są wysyłane do portu docelowego. Domyślnym portem używanym przez agentów rozwiązania Network Performance Monitor jest port 8084. Po skonfigurowaniu agentów można zmienić port. Upewnij się, że zapór sieci lub reguł Sieciowej grupy zabezpieczeń sieci (na platformie Azure) zezwalają na ruch na porcie. Należy również upewnić się, że konfiguracja zapory lokalnej na komputerach, na których zainstalowano agentów, aby zezwolić na ruch na tym porcie. Skrypty programu PowerShell umożliwiają skonfigurowanie reguł zapory na komputerach z systemem Windows, ale musisz ręcznie Skonfiguruj zaporę, sieci. Z kolei ICMP nie działają przy użyciu portu. W większości przypadków enterprise ruch protokołu ICMP jest dozwolone za pośrednictwem zapór pozwalają na korzystanie z narzędzia diagnostyczne sieci, takich jak narzędzie ping. Jeśli możesz wywołać jedną maszynę z innego, można użyć protokołu ICMP, bez konieczności ręcznego konfigurowania zapory.

>[!NOTE] 
> Niektóre zapory mogą blokować protokołu ICMP, co może prowadzić do retransmisji, które skutkuje dużą liczbę zdarzeń w systemie zarządzania informacjami i zdarzeniami zabezpieczeń. Upewnij się, że protokół, który wybierzesz nie jest blokowany przez zapory sieciowej lub sieciowej grupy zabezpieczeń. W przeciwnym razie rozwiązania Network Performance Monitor nie może monitorować segmencie sieci. Zalecamy użycie protokołu TCP do monitorowania. Użyj protokołu ICMP w scenariuszach, w której nie można użyć protokołu TCP, takie jak czas: 
>
> - Możesz użyć węzłów klienckimi systemami Windows, ponieważ pierwotne gniazda TCP nie jest dozwolone w klientach Windows.
> - Zapory sieciowe lub sieciowej grupy zabezpieczeń blokuje TCP.
> - Nie wiem, jak przełączyć protokołu.

Jeśli została wybrana opcja używania protokołu ICMP, podczas wdrażania, możesz przełączyć TCP w dowolnym momencie poprzez edycję domyślna reguła monitorowania.

1. Przejdź do **wydajność sieci** > **Monitor** > **skonfigurować**   >  **Monitor**. Następnie wybierz pozycję **domyślną regułę**. 
2. Przewiń do **protokołu** sekcji i wybierz protokół, dla którego chcesz użyć. 
3. Wybierz **Zapisz** Aby zastosować ustawienia. 

Nawet jeśli domyślna reguła używa określonego protokołu, można utworzyć nowe reguły z innym protokołem. Możesz nawet utworzyć różne zasady, gdzie niektóre reguły używają protokołu ICMP, a inne korzystają z protokołu TCP. 

## <a name="walkthrough"></a>Przewodnik 

Teraz sprawdźmy proste śledztwa główną przyczynę zdarzenie kondycji.

Na pulpicie nawigacyjnym rozwiązania zdarzenie kondycji pokazuje, że połączenia sieciowego jest w złej kondycji. Aby zbadać problem, wybierz **sieci łączy monitorowanych** kafelka.

Strona szczegółów pokazuje, że **DMZ2 DMZ1** połączenia sieciowego jest w złej kondycji. Wybierz **Wyświetl linki podsieci** dla tego połączenia sieciowego. 


Na stronie szczegółów znajdują się wszystkie łącza podsieci w **DMZ2 DMZ1** połączenia sieciowego. Dla obu łączy podsieci opóźnienie przekroczyła wartość progową, co sprawia, że połączenia sieciowego jest nieprawidłowy. Widać również trendy opóźnienie oba linki podsieci. Użycie wybór czasu kontrolować na wykresie, aby skoncentrować się na zakres wymagany czas. Możesz zobaczyć pora dnia po osiągnięciu szczyt w opóźnienia. Przeszukiwanie dzienników, które później dla tego przedziału czasu w celu zbadania problemu. Wybierz **Wyświetl linki węzłów** dalszych szczegółów. 
 
 ![Strona łącza podsieci](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobnie jak w poprzedniej strony, strony Przechodzenie do szczegółów dla łącza podsieci w szczególności zawiera także jej składników węzła łącza. Można wykonać podobne działania w tym miejscu tak jak w poprzednim kroku. Wybierz **wyświetlanie topologii** Aby wyświetlić topologię między dwoma węzłami. 
 
 ![Strona łącza węzłów](media/network-performance-monitor-performance-monitor/node-links.png) 

Wszystkie ścieżki między dwoma węzłami wybranego na wykresie mapy topologii. Można wizualizować topologii przeskoku przeskoku trasy między dwoma węzłami na mapie topologii. Pozwala ona jasny obraz, o ile istnieją między dwoma węzłami i jakie ścieżki zająć pakiety danych. Wąskie gardła wydajności sieci są wyświetlane w kolorze czerwonym. Aby zlokalizować połączenia uszkodzoną siecią lub urządzenie sieciowe uszkodzony, Przyjrzyj się czerwony elementy na mapie topologii. 

 ![Pulpit nawigacyjny topologii za pomocą mapy topologii](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Możesz przejrzeć utraty, opóźnienia i liczbę przeskoków w każdej ścieżce **akcji** okienka. Użyj paska przewijania, aby wyświetlić szczegóły ścieżki w złej kondycji. Użyj filtrów, aby zaznacz ścieżki o złej kondycji przeskoku jest wykreślany topologii dla wybranej ścieżki. Aby powiększyć lub pomniejszyć mapy topologii, należy użyć kółka myszy. 

Na poniższej ilustracji głównej przyczyny problemów do określonej sekcji sieci są wyświetlane w czerwonym ścieżek i przeskoków. Wybierz węzeł na mapie topologii, aby wyświetlić właściwości węzła, który zawiera adres FQDN i adresu IP. Wybierając skoku zawiera adres IP przeskoku. 
 
![Mapy topologii z wybrane — właściwości węzła](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Kolejne kroki
[Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
