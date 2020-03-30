---
title: Monitor wydajności na Monitorze wydajności sieci
description: Funkcja Monitor wydajności w Monitorze wydajności sieci ułatwia monitorowanie łączności sieciowej w różnych punktach sieci. Można monitorować wdrożenia w chmurze i lokalizacje lokalne, wiele centrów danych i oddziałów oraz aplikacje wielowarstwowe o znaczeniu krytycznym lub mikrousług.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055685"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Rozwiązanie Monitor wydajności sieci: monitorowanie wydajności

Funkcja Monitor wydajności w [Monitorze wydajności sieci](network-performance-monitor.md) ułatwia monitorowanie łączności sieciowej w różnych punktach sieci. Można monitorować wdrożenia w chmurze i lokalizacje lokalne, wiele centrów danych i oddziałów oraz aplikacje wielowarstwowe o znaczeniu krytycznym lub mikrousług. Za pomocą Monitora wydajności można wykryć problemy z siecią, zanim użytkownicy zdali skargę. Najważniejsze zalety są takie, że możesz: 

- Monitoruj utratę i opóźnienia w różnych podsieciach i ustawiaj alerty.
- Monitoruj wszystkie ścieżki (w tym ścieżki nadmiarowe) w sieci.
- Rozwiązywanie problemów z siecią przejściową i punktu w czasie, które są trudne do replikacji.
- Określ określony segment w sieci, który jest odpowiedzialny za wydajność obniżoną.
- Monitoruj kondycję sieci, bez konieczności stosowania biuletynu SNMP.


![Monitor wydajności sieci](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfigurowanie
Aby otworzyć konfigurację Monitora wydajności sieci, otwórz [rozwiązanie Monitor wydajności sieci](network-performance-monitor.md)i wybierz pozycję **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Tworzenie nowych sieci

Sieć w Monitorze wydajności sieci jest kontenerem logicznym dla podsieci. Pomaga zorganizować monitorowanie infrastruktury sieciowej zgodnie z potrzebami. Można utworzyć sieć o przyjaznej nazwie i dodać do niej podsieci zgodnie z logiką biznesową. Można na przykład utworzyć sieć o nazwie Londyn i dodać wszystkie podsieci w londyńskim centrum danych. Można też utworzyć sieć o nazwie *ContosoFrontEnd* i dodać do tej sieci wszystkie podsieci o nazwie Contoso, które obsługują frontołową część aplikacji. Rozwiązanie automatycznie tworzy domyślną sieć, która zawiera wszystkie podsieci wykryte w twoim środowisku. 

Za każdym razem, gdy tworzysz sieć, dodajesz do niej podsieć. Następnie ta podsieć jest usuwana z sieci domyślnej. Jeśli usuniesz sieć, wszystkie jej podsieci zostaną automatycznie zwrócone do sieci domyślnej. Domyślna sieć działa jako kontener dla wszystkich podsieci, które nie są zawarte w żadnej sieci zdefiniowanej przez użytkownika. Nie można edytować ani usuwać sieci domyślnej. Zawsze pozostaje w systemie. Można utworzyć dowolną liczbę sieci niestandardowych. W większości przypadków podsieci w organizacji są rozmieszczone w więcej niż jednej sieci. Utwórz jedną lub więcej sieci, aby zgrupować podsieci dla logiki biznesowej.

Aby utworzyć nową sieć:


1. Wybierz kartę **Sieci.**
1. Wybierz **pozycję Dodaj sieć**, a następnie wprowadź nazwę i opis sieci. 
2. Zaznacz co najmniej jedną podsieci, a następnie wybierz pozycję **Dodaj**. 
3. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. 


### <a name="create-monitoring-rules"></a>Tworzenie reguł monitorowania 

Monitor wydajności generuje zdarzenia kondycji, gdy próg wydajności połączeń sieciowych między dwoma podsieciami lub między dwiema sieciami zostanie naruszony. System może automatycznie nauczyć się tych progów. Można również podać niestandardowe progi. System automatycznie tworzy regułę domyślną, która generuje zdarzenie kondycji, gdy utrata lub opóźnienie między dowolną parą łączy sieciowych lub podsieci narusza próg wyuczony przez system. Ten proces pomaga rozwiązaniu monitorować infrastrukturę sieciową, dopóki nie utworzono żadnych reguł monitorowania jawnie. Jeśli reguła domyślna jest włączona, wszystkie węzły wysyłają transakcje syntetyczne do wszystkich innych węzłów, które zostały włączone do monitorowania. Reguła domyślna jest przydatna w przypadku małych sieci. Przykładem jest scenariusz, w którym masz niewielką liczbę serwerów z mikrousługą i chcesz upewnić się, że wszystkie serwery mają łączność ze sobą.

>[!NOTE]
> Zaleca się wyłączenie reguły domyślnej i utworzenie niestandardowych reguł monitorowania, szczególnie w przypadku dużych sieci, w których do monitorowania jest używana duża liczba węzłów. Niestandardowe reguły monitorowania mogą zmniejszyć ruch generowany przez rozwiązanie i ułatwić organizowanie monitorowania sieci.

Twórz reguły monitorowania zgodnie z logiką biznesową. Przykładem jest, jeśli chcesz monitorować wydajność łączności sieciowej dwóch lokacji biurowych do centrali. Zgrupuj wszystkie podsieci w lokacji biurowej1 w sieci O1. Następnie zgrupuj wszystkie podsieci w lokacji biurowej2 w sieci O2. Na koniec zgrupuj wszystkie podsieci w centrali w sieci H. Utwórz dwie reguły monitorowania — jedną między O1 i H, a drugą między O2 i H. 

Aby utworzyć niestandardowe reguły monitorowania:

1. Wybierz **pozycję Dodaj regułę** na karcie **Monitor** i wprowadź nazwę i opis reguły.
2. Wybierz parę łączy sieciowych lub podsieci, które mają być monitorowane z list. 
3. Wybierz sieć zawierającą żądane podsieci z listy rozwijanej sieci. Następnie wybierz podsieci z listy rozwijanej odpowiedniej podsieci. Jeśli chcesz monitorować wszystkie podsieci w łączu sieciowym, wybierz **opcję Wszystkie podsieci**. Podobnie wybierz inne podsieci, które chcesz. Aby wykluczyć monitorowanie określonych łączy podsieci z dokonanych wyborów, wybierz **opcję Dodaj wyjątek**. 
4. Wybierz między protokołami ICMP i TCP do wykonywania transakcji syntetycznych. 
5. Jeśli nie chcesz tworzyć zdarzeń dotyczących kondycji dla wybranych elementów, wyczyść **pozycję Włącz monitorowanie kondycji w łączach objętych tą regułą**. 
6. Wybierz warunki monitorowania. Aby ustawić niestandardowe progi dla generowania zdarzeń kondycji, wprowadź wartości progowe. Za każdym razem, gdy wartość warunku przekracza wybrany próg dla wybranej pary sieci lub podsieci, generowane jest zdarzenie kondycji. 
7. Wybierz **pozycję Zapisz,** aby zapisać konfigurację. 

Po zapisaniu reguły monitorowania można zintegrować tę regułę z zarządzaniem alertami, wybierając pozycję **Utwórz alert**. Reguła alertu jest tworzona automatycznie za pomocą kwerendy wyszukiwania. Inne wymagane parametry są wypełniane automatycznie. Korzystając z reguły alertów, oprócz istniejących alertów w Monitorze wydajności sieci można otrzymywać alerty oparte na wiadomościach e-mail. Alerty mogą również wyzwalać akcje naprawcze z elementami runbook lub mogą integrować się z istniejącymi rozwiązaniami do zarządzania usługami przy użyciu elementów webhook. Wybierz **pozycję Zarządzaj alertem,** aby edytować ustawienia alertu. 

Teraz można utworzyć więcej reguł Monitora wydajności lub przejść do pulpitu nawigacyjnego rozwiązania, aby korzystać z tej możliwości.

### <a name="choose-the-protocol"></a>Wybierz protokół

Monitor wydajności sieci używa transakcji syntetycznych do obliczania metryk wydajności sieci, takich jak utrata pakietów i opóźnienie łącza. Aby lepiej zrozumieć tę koncepcję, należy wziąć pod uwagę agenta Monitora wydajności sieci podłączonego do jednego końca łącza sieciowego. Ten agent Monitora wydajności sieci wysyła pakiety sond do drugiego agenta Monitora wydajności sieci podłączonego do innego końca sieci. Drugi agent odpowiada pakietami odpowiedzi. Proces ten powtarza się kilka razy. Mierząc liczbę odpowiedzi i czas odbierania każdej odpowiedzi, pierwszy agent Monitora wydajności sieci ocenia opóźnienie łącza i spadek pakietów. 

Format, rozmiar i kolejność tych pakietów zależy od protokołu wybranego podczas tworzenia reguł monitorowania. Na podstawie protokołu pakietów pośrednie urządzenia sieciowe, takie jak routery i przełączniki, mogą przetwarzać te pakiety w różny sposób. W związku z tym wybór protokołu wpływa na dokładność wyników. Wybór protokołu określa również, czy po wdrożeniu rozwiązania Monitor wydajności sieci należy wykonać ręczne kroki. 

Monitor wydajności sieci oferuje wybór między protokołami ICMP i TCP do wykonywania transakcji syntetycznych. Jeśli podczas tworzenia reguły transakcji syntetycznych wybierzesz opcję ICMP, agenci Monitora wydajności sieci używają komunikatów ICMP ECHO do obliczania opóźnienia sieci i utraty pakietów. ICMP ECHO używa tego samego komunikatu, który jest wysyłany przez konwencjonalne narzędzie ping. W przypadku używania protokołu TCP agenci Monitora wydajności sieci wysyłają pakiety TCP SYN przez sieć. Po tym kroku następuje zakończenie uzgadniania TCP, a połączenie jest usuwane przy użyciu pakietów RST. 

Przed wybraniem protokołu należy wziąć pod uwagę następujące informacje: 

* **Odnajdowanie wielu tras sieciowych.** Protokół TCP jest dokładniejszy podczas odnajdywania wielu tras i wymaga mniejszej liczby agentów w każdej podsieci. Na przykład jeden lub dwa agenty korzystające z protokołu TCP mogą odnajdować wszystkie nadmiarowe ścieżki między podsieciami. Potrzebujesz kilku agentów, które używają ICMP, aby osiągnąć podobne wyniki. Za pomocą ICMP, jeśli masz kilka tras między dwiema podsieciami, potrzebujesz więcej niż 5N agentów w podsieci źródłowej lub docelowej.

* **Dokładność wyników.** Routery i przełączniki mają tendencję do przypisywania niższego priorytetu pakietom ICMP ECHO w porównaniu z pakietami TCP. W niektórych sytuacjach, gdy urządzenia sieciowe są mocno obciążone, dane uzyskane przez protokół TCP dokładniej odzwierciedlają utratę i opóźnienie występujące przez aplikacje. Dzieje się tak, ponieważ większość ruchu aplikacji przepływa przez TCP. W takich przypadkach ICMP zapewnia mniej dokładne wyniki w porównaniu do TCP. 

* **Konfiguracja zapory.** Protokół TCP wymaga, aby pakiety TCP były wysyłane do portu docelowego. Domyślnym portem używanym przez agentów Monitora wydajności sieci jest 8084. Port można zmienić podczas konfigurowania agentów. Upewnij się, że zapory sieciowe lub reguły sieciowej grupy zabezpieczeń (na platformie Azure) zezwalają na ruch na porcie. Należy również upewnić się, że zapora lokalna na komputerach, na których są zainstalowane agenci, jest skonfigurowana tak, aby zezwalała na ruch na tym porcie. Skrypty programu PowerShell umożliwiają konfigurowanie reguł zapory na komputerach z systemem Windows, ale należy ręcznie skonfigurować zaporę sieciową. Z kolei ICMP nie działa przy użyciu portu. W większości scenariuszy korporacyjnych ruch ICMP jest dozwolony za pośrednictwem zapór, aby umożliwić korzystanie z narzędzi diagnostyki sieci, takich jak narzędzie ping. Jeśli można pingować jeden komputer z innego, można użyć protokołu ICMP bez konieczności ręcznego konfigurowania zapór.

>[!NOTE] 
> Niektóre zapory mogą blokować ICMP, co może prowadzić do retransmisji, co powoduje dużą liczbę zdarzeń w systemie zarządzania informacjami o zabezpieczeniach i zdarzeniami. Upewnij się, że wybrany protokół nie jest blokowany przez zaporę sieciową lub grupę sieciową. W przeciwnym razie Monitor wydajności sieci nie może monitorować segmentu sieci. Zaleca się używanie protokołu TCP do monitorowania. Użyj protokołu ICMP w scenariuszach, w których nie można używać protokołu TCP, na przykład gdy: 
>
> - Węzły oparte na kliencie systemu Windows są używane, ponieważ nieprzetworzone gniazda TCP nie są dozwolone na klientach systemu Windows.
> - Zapora sieciowa lub sieć sieciowa blokuje protokół TCP.
> - Nie wiesz, jak przełączyć protokół.

Jeśli podczas wdrażania wybrano opcję ICMP, można przełączyć się na protokół TCP w dowolnym momencie, edytując domyślną regułę monitorowania.

1. Przejdź do **monitora** >konfiguracji > > **monitora**wydajności sieci **.** **Monitor** Następnie wybierz pozycję **Reguła domyślna**. 
2. Przewiń do sekcji **Protokół** i wybierz protokół, którego chcesz użyć. 
3. Wybierz **pozycję Zapisz,** aby zastosować to ustawienie. 

Nawet jeśli reguła domyślna używa określonego protokołu, można utworzyć nowe reguły z innym protokołem. Można nawet utworzyć kombinację reguł, w których niektóre reguły używają protokołu ICMP, a inne używają protokołu TCP. 

## <a name="walkthrough"></a>Przewodnik 

Teraz spójrz na proste dochodzenie w sprawie głównej przyczyny zdarzenia zdrowotnego.

Na pulpicie nawigacyjnym rozwiązania zdarzenie kondycji pokazuje, że łącze sieciowe jest w złej kondycji. Aby zbadać problem, wybierz kafelek **Łącza sieciowe monitorowane.**

Na stronie drążenia pokazuje, że łącze **sieciowe DMZ2-DMZ1** jest w złej kondycji. Wybierz **pozycję Wyświetl łącza podsieci** dla tego łącza sieciowego. 


Strona przechodzenia do szczegółów pokazuje wszystkie łącza podsieci w łączu **sieciowym DMZ2-DMZ1.** Dla obu łączy podsieci opóźnienie przekroczyło próg, co sprawia, że łącze sieciowe w złej kondycji. Można również zobaczyć trendy opóźnienia obu łączy podsieci. Użyj formantu wyboru czasu na wykresie, aby skupić się na wymaganym zakresie czasu. Można zobaczyć porę dnia, kiedy opóźnienie osiągnął swój szczyt. Przeszukaj dzienniki później dla tego okresu, aby zbadać problem. Wybierz **pozycję Wyświetl łącza węzłów,** aby przejść do szczegółów. 
 
 ![Strona Łącza podsieci](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Podobnie jak na poprzedniej stronie, strona przechodzenia do szczegółów dla określonego łącza podsieci zawiera listę jego łączy węzłów składowych. W tym miejscu można wykonać podobne akcje, jak w poprzednim kroku. Wybierz **opcję Wyświetl topologię,** aby wyświetlić topologię między dwoma węzłami. 
 
 ![Strona Łącza węzłów](media/network-performance-monitor-performance-monitor/node-links.png) 

Wszystkie ścieżki między dwoma wybranymi węzłami są drukowane na mapie topologii. Można wizualizować topologię przeskoku po przeskoku tras między dwoma węzłami na mapie topologii. Daje jasny obraz liczby tras istnieje między dwoma węzłami i jakie ścieżki mają pakiety danych. Wąskie gardła wydajności sieci są wyświetlane na czerwono. Aby zlokalizować wadliwe połączenie sieciowe lub wadliwe urządzenie sieciowe, spójrz na czerwone elementy na mapie topologii. 

 ![Pulpit nawigacyjny topologii z mapą topologii](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Można przejrzeć utratę, opóźnienie i liczbę przeskoków w każdej ścieżce w okienku **Akcja.** Użyj paska przewijania, aby wyświetlić szczegóły ścieżek w złej kondycji. Użyj filtrów, aby wybrać ścieżki ze złej wydajnością przeskoku, tak aby kreślona była topologia tylko dla wybranych ścieżek. Aby powiększyć lub pomniejszyć mapę topologii, użyj kółka myszy. 

Na poniższej ilustracji główna przyczyna problemów do określonej sekcji sieci są wyświetlane w czerwonych ścieżkach i przeskokach. Wybierz węzeł na mapie topologii, aby wyświetlić właściwości węzła, który zawiera FQDN i adres IP. Wybranie przeskoku pokazuje adres IP przeskoku. 
 
![Mapa topologii z zaznaczonymi właściwościami węzła](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe rekordy danych wydajności sieci.
