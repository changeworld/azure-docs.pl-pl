---
title: Funkcję Monitor wydajności w rozwiązaniu monitora wydajności sieci w Azure Log Analytics | Dokumentacja firmy Microsoft
description: Możliwość monitora wydajności w Monitorze wydajności sieci pomaga monitorować łączność sieciową między różnymi punktami w sieci. Możesz monitorować wdrożenia chmury oraz lokalizacji lokalnej wielu centrów danych i biur oddziałów i kluczowych aplikacji wielowarstwowej lub mikrousług.
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
ms.openlocfilehash: 65497548d0b8066627be25520c28d39491918d09
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Monitor wydajności rozwiązania sieci: monitorowanie wydajności

Możliwość monitora wydajności w [monitora wydajności sieci](log-analytics-network-performance-monitor.md) pomaga w monitorowaniu połączeń sieciowych w różnych punktach w sieci. Możesz monitorować wdrożenia chmury oraz lokalizacji lokalnej wielu centrów danych i biur oddziałów i kluczowych aplikacji wielowarstwowej lub mikrousług. Zanim użytkownicy skarżą się z Monitora wydajności można wykryć problemy z siecią. Możesz ma następujące zalety klucza: 

- Monitorowanie utraty i opóźnienia w różnych podsieciach i Ustaw alerty.
- Monitorowanie wszystkich ścieżek (w tym nadmiarowe ścieżki) w sieci.
- Przejściowych i w momencie Rozwiązywanie problemów z sieci, które są trudne do replikacji.
- Określ określonego segmentu w sieci, która jest odpowiedzialna za pogorszenie wydajności.
- Monitorowanie kondycji sieci, bez konieczności SNMP.


![Monitor wydajności sieci](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurowanie
Aby otworzyć konfigurację monitora wydajności w sieci, otwórz [rozwiązania monitora wydajności sieci](log-analytics-network-performance-monitor.md)i wybierz **Konfiguruj**.

![Konfigurowanie monitora wydajności sieci](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Tworzenie nowych sieci

Sieć w Monitorze wydajności w sieci jest kontenerem logicznym podsieci. Pomaga organizować Monitorowanie infrastruktury sieci, zgodnie z potrzebami. Można utworzyć sieci o przyjaznej nazwie i dodać podsieci do niego według logiki biznesowej. Na przykład można utworzyć sieć o nazwie Londynie i dodać wszystkie podsieci w Londynie centrum danych. Można utworzyć sieci o nazwie *ContosoFrontEnd* i dodać do tej sieci wszystkie podsieci o nazwie Contoso, które pełnią fronton aplikacji. Rozwiązanie automatycznie tworzy domyślnej sieci, zawierającą wszystkie podsieci wykrytych w środowisku. 

Gdy utworzysz sieci, możesz dodać podsieci do niego. Następnie tej podsieci jest usuwany z domyślnej sieci. Jeśli usuniesz sieci, wszystkie jego podsieci automatycznie są zwracane do domyślnej sieci. Domyślnej sieci działa jako kontener dla wszystkich podsieci, które nie są zawarte w każdej sieci zdefiniowanej przez użytkownika. Nie można edytować ani usunąć domyślnej sieci. Zawsze pozostaje w systemie. Można utworzyć dowolną liczbę sieci niestandardowe. W większości przypadków podsieci w organizacji są rozmieszczone w więcej niż jedną sieć. Utwórz co najmniej jednej sieci, aby zgrupować podsieci dla logiki biznesowej.

Aby utworzyć nową sieć:


1. Wybierz **sieci** kartę.
1. Wybierz **sieci Dodaj**, a następnie wprowadź nazwę sieciową i opis. 
2. Wybierz co najmniej jednej podsieci, a następnie wybierz **Dodaj**. 
3. Wybierz **zapisać** Aby zapisać konfigurację. 


### <a name="create-monitoring-rules"></a>Tworzenie reguły monitorowania 

Monitor wydajności generuje zdarzenia kondycji, gdy naruszenia progu wydajności połączeń sieciowych między dwoma podsieci lub między dwiema sieciami. System może automatycznie informacje tych progów. Możesz również zapewnić progami niestandardowymi. System automatycznie tworzy regułę domyślną, która generuje zdarzenie kondycji, w przypadku naruszenia łączy utraty lub opóźnienia między jakiejkolwiek parze sieci lub podsieć próg rozpoznawane przez system. Ten proces pomaga w rozwiązaniu Monitorowanie infrastruktury sieci, dopóki nie utworzono żadnych reguł monitorowania jawnie. Jeśli domyślna reguła jest włączona, wszystkie węzły wysłać transakcji syntetycznych do wszystkich węzłów, które są włączone do monitorowania. Domyślna reguła jest przydatne w przypadku małych sieciach. Przykładem jest scenariusz, w którym masz niewielkiej liczby serwerów z systemem mikrousługi i chce mieć pewność, że wszystkie serwery mają łączność między sobą.

>[!NOTE]
> Zaleca się wyłączenie reguły domyślnej, a następnie utworzyć niestandardowe reguły monitorowania, szczególnie w dużych sieciach, w których są używane dużą liczbę węzłów do monitorowania. Niestandardowe reguły monitorowania można ograniczyć ruch generowany przez rozwiązanie i pomoc można organizować monitorowania sieci.

Tworzenie reguł monitorowania zgodnie z logiki biznesowej. Przykładem jest, jeśli chcesz monitorować wydajność dwóch lokacji office z centralą łączność sieciową. Grupa wszystkich podsieci w site1 pakietu office w sieci O1. Następnie grupy wszystkich podsieci w witryna2 pakietu office w sieci O2. Na koniec grupy wszystkich podsieci w siedzibie głównej w sieci H. utworzyć dwie reguły monitorowania — jeden między O1 i H, a druga między O2 i H. 

Aby utworzyć niestandardowe reguły monitorowania:

1. Wybierz **Dodaj regułę** na **Monitor** , a następnie wprowadzić nazwę reguły i opis.
2. Wybierz pary sieci lub podsieć łącza do monitorowania za pomocą list. 
3. Wybierz sieć, która zawiera podsieci, z listy rozwijanej sieci. Następnie wybierz podsieci z listy rozwijanej odpowiednich podsieci. Jeśli chcesz monitorować wszystkich podsieci w połączenia sieciowego, wybierz **wszystkie podsieci**. Podobnie wybierz podsieci, które mają. Aby wykluczyć, monitorowania łączy określonej podsieci z wybranych, wybierz **dodać wyjątek**. 
4. Wybór między ICMP i TCP protokołów do wykonania transakcji syntetycznych. 
5. Jeśli nie chcesz utworzyć zdarzenia kondycji dla elementów została wybrana, wyczyść **Włącz monitorowanie kondycji łącza objęte regułą**. 
6. Wybierz warunki monitorowania. Aby ustawić progami niestandardowymi generacji kondycji zdarzenie, wprowadź wartości progowe. Zawsze, gdy wartość warunku przekroczy wybrany próg dla wybranej sieci lub parę podsieć, jest generowane zdarzenie kondycji. 
7. Wybierz **zapisać** Aby zapisać konfigurację. 

Po zapisaniu reguły monitorowania tej reguły można zintegrować z zarządzania alertami, wybierając **Tworzenie alertów**. Reguła alertu jest tworzony automatycznie z zapytania wyszukiwania. Inne wymagane parametry są wypełniane automatycznie. Reguła alertu może odbierać opartych na wiadomościach e-mail alertów, oprócz istniejące alerty w Monitorze wydajności sieci. Alerty także wyzwolić czynności zaradczych z elementami runbook, lub ich można zintegrować z istniejącymi rozwiązaniami do zarządzania usługi za pomocą elementów webhook. Wybierz **Zarządzanie Alert** Aby edytować ustawienia alertu. 

Teraz można utworzyć więcej reguł monitora wydajności lub przejdź do pulpitu nawigacyjnego rozwiązania, aby używać funkcji.

### <a name="choose-the-protocol"></a>Wybierz protokół

Monitor wydajności sieci używa transakcji syntetycznych do obliczania metryki wydajności sieci, takich jak opóźnienie utratą i łącze pakietów. Aby lepiej zrozumieć to pojęcie, należy wziąć pod uwagę agent monitora wydajności sieci, połączony z jednym zakończeniu połączenia sieciowego. Ten agent monitora wydajności sieci wysyła pakiety sondy do agenta monitora wydajności sieci drugiego połączenia w innym celu sieci. Druga odpowiedź agenta z pakietów odpowiedzi. Ten proces jest powtarzany kilka razy. Mierząc kod odpowiedzi oraz czas potrzebny na odebranie odpowiedź, czas oczekiwania linku ocenia pierwszego agenta monitora wydajności sieci i porzucania pakietów. 

Format, rozmiar i kolejność tych pakietów jest określana przez protokół, który można wybrać podczas tworzenia reguł monitorowania. Oparte na protokole pakietów, urządzenia sieciowe pośredniego, takich jak routery i przełączniki, może przetworzyć te pakiety inaczej. W rezultacie wybranego protokołu ma wpływ na dokładność wyników. Wybór protokołu określa również, czy wszystkie wymagane ręczne wykonanie czynności należy wykonać po wdrożeniu rozwiązania monitora wydajności sieci. 

Monitor wydajności sieci umożliwia wybór między protokołów ICMP i TCP wykonania transakcji syntetycznych. Jeśli wybierzesz protokołu ICMP, podczas tworzenia reguły transakcji syntetycznych, agenci monitora wydajności sieci umożliwia obliczenie opóźnienie sieci i utraty pakietów komunikatów ECHA protokołu ICMP. Ten sam komunikat wysyłany przez narzędzie ping z konwencjonalnej korzysta z ECHA protokołu ICMP. Użycie jako protokołu TCP, Monitor wydajności sieci agenci wysyłają TCP SYN za pośrednictwem sieci pakietów. W tym kroku następuje wykonania procedury uzgadniania protokołu TCP, a połączenie zostanie usunięte przy użyciu RST pakietów. 

Przed wybraniem protokołu, należy wziąć pod uwagę następujące informacje: 

* **Odnajdywanie wiele tras sieciowych.** Protokół TCP jest dokładniejsze podczas rozpoznawania wiele tras i wymaga mniejszej liczby agentów w każdej podsieci. Na przykład jeden lub dwaj agenci, które używają protokołu TCP umożliwia odnalezienie wszystkich nadmiarowe ścieżki między podsieciami. Należy kilka czynników, korzystających z protokołu ICMP w celu uzyskania wyników podobne. Za pomocą protokołu ICMP, jeśli masz wiele tras między dwiema podsieciami, potrzebujesz więcej niż 5 n agentów w podsieci źródłowe lub docelowe.

* **Dokładność wyników.** Routery i przełączniki zwykle można przypisać niższy priorytet pakietów ECHA protokołu ICMP w porównaniu do pakietów TCP. W niektórych sytuacjach silnie ładowane urządzeń sieciowych, dane uzyskiwane przez protokół TCP dokładniejsze odzwierciedla utraty i opóźnienia wystąpił przez aplikacje. Dzieje się tak, ponieważ większość aplikacji ruch przepływa za pośrednictwem protokołu TCP. W takich przypadkach ICMP zawiera mniej dokładne wyniki w porównaniu do TCP. 

* **Konfiguracja zapory.** Protokół TCP wymaga, aby TCP pakiety są wysyłane do portu docelowego. Domyślny port używany przez Monitor wydajności sieci agentów jest 8084. Po skonfigurowaniu agentów, można zmienić port. Upewnij się, że Twoje zapory sieciowe lub zasad grupy (NSG) zabezpieczeń sieci (na platformie Azure) zezwala na ruch na porcie. Należy również upewnić, że zapory lokalnej na komputerach, na których zainstalowano agentów jest skonfigurowane zezwalająca na ruch na tym porcie. Skrypty programu PowerShell umożliwia konfigurowanie reguł zapory na komputerach z systemem Windows, ale należy ręcznie skonfigurować zapory sieciowej. Z kolei ICMP nie działa przy użyciu portu. W większości przypadków enterprise ruch protokołu ICMP jest dozwolone za pośrednictwem zapór umożliwiają używanie narzędzia diagnostyczne sieci, takie jak narzędzie ping. Jeśli jeden komputer z innej można wywołać, można użyć protokołu ICMP, bez konieczności ręcznego konfigurowania zapory.

>[!NOTE] 
> Niektóre zapory mogą blokować protokołu ICMP, co może prowadzić do retransmisji, który daje w dużej liczby zdarzeń w systemie zarządzania informacji i zdarzeń zabezpieczeń. Upewnij się, że protokół, który wybrano nie jest zablokowany przez zaporę sieci lub grupy NSG. W przeciwnym razie Monitor wydajności sieci nie można monitorować w segmencie sieci. Zaleca się używanie protokołu TCP dla monitorowania. Użyj protokołu ICMP w scenariuszach, w których nie można użyć protokołu TCP, takie jak czas: 
>
> - Możesz użyć węzłów opartą na kliencie systemu Windows, ponieważ gniazd TCP nie są dozwolone w klientów systemu Windows.
> - Zapora sieci lub grupy NSG blokuje TCP.
> - Nie wiadomo jak przejść protokołu.

Jeśli została wybrana opcja do wykorzystania podczas wdrażania protokołu ICMP, możesz przełączyć TCP, aby w dowolnym momencie poprzez edycję domyślna reguła monitorowania.

1. Przejdź do **wydajność sieci** > **Monitor** > **skonfigurować**   >  **Monitor**. Następnie wybierz **domyślną regułę**. 
2. Przewiń do **protokołu** sekcji, a następnie wybierz protokół, którego chcesz używać. 
3. Wybierz **zapisać** Aby zastosować ustawienia. 

Nawet jeśli domyślna reguła używa określonego protokołu, możesz utworzyć nowe reguły z innego protokołu. Można nawet utworzyć różnych zasad, gdy niektóre zasady używa protokołu ICMP i inne TCP. 

## <a name="walkthrough"></a>Przewodnik 

Teraz przyjrzeć się proste badanie przyczynę kondycji zdarzenie.

Na pulpicie nawigacyjnym rozwiązania zdarzenie kondycji pokazuje, że połączenia sieciowego jest zła. Aby zbadać problem, wybierz **łączy sieciowych monitorowanych** kafelka.

Strona przechodzenia wskazuje, że **DMZ2 DMZ1** połączenia sieciowego jest w złej kondycji. Wybierz **wyświetlanie łączy podsieci** dla tego połączenia sieciowego. 


Na stronie przechodzenia znajdują się wszystkich łączy podsieci w **DMZ2 DMZ1** połączenia sieciowego. Dla obu łączy podsieci opóźnienie przekroczeniu progu, co sprawia, że połączenia sieciowego jest zła. Możesz również sprawdzić trendów opóźnienia zarówno łączy podsieci. Użycie kontroli wybór czasu na wykresie, aby skoncentrować się na zakres wymagany czas. Widać porę dnia, gdy czas oczekiwania osiągnięta Szczyt. Wyszukaj w dziennikach później tego okresu, aby zbadać problem. Wybierz **wyświetlanie łączy węzłów** aby przejść dalej. 
 
 ![Strona łącza podsieci](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Podobne do poprzedniej strony, strony Przechodzenie do szczegółów dla łącza określonej podsieci wymieniono łącza składowych węzła. Można wykonywać działania podobne tutaj tak samo jak w poprzednim kroku. Wybierz **widoku topologii** do wyświetlania topologii między dwoma węzłami. 
 
 ![Strona łącza węzła](media/log-analytics-network-performance-monitor/node-links.png) 

Wszystkie ścieżki między dwoma węzłami wybranego na wykresie mapy topologii. Można zwizualizować topologii przeskoku przeskoku tras między dwa węzły na mapie topologii. Umożliwia danych istnieje jak wiele tras między dwa węzły i co ścieżek zająć pakietów danych. Wąskich gardeł wydajności sieci są zaznaczone na czerwono. Aby zlokalizować połączenie sieciowe uszkodzony lub uszkodzenia urządzenia sieciowego, wyświetlanie elementów red na mapy topologii. 

 ![Pulpit nawigacyjny topologii z mapy topologii](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Możesz przejrzeć utraty, opóźnienia i liczby przeskoków w poszczególnych ścieżek w **akcji** okienka. Umożliwia wyświetlenie szczegółów zła ścieżek pasek przewijania. Użyj filtrów, aby zaznaczyć ścieżek o złej kondycji przeskoku, dzięki czemu są kreślone topologii dla wybranej ścieżki. Aby powiększyć lub pomniejszyć mapy topologii, użyj kółka myszy. 

Na poniższej ilustracji głównej przyczyny problemów do określonej sekcji sieci są wyświetlane w czerwonym ścieżki i przeskoków. Wybierz węzeł mapy topologii, aby wyświetlić właściwości tego węzła, który zawiera adres IP i nazwy FQDN. Wybieranie skoku zawiera adres IP przeskoku. 
 
![Mapy topologii z wybrane właściwości węzła](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Kolejne kroki
[Wyszukaj dzienniki](log-analytics-log-searches.md) do wyświetlania rekordów danych wydajności szczegółowe sieci.
