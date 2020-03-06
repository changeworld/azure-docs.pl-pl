---
title: Odzyskiwanie po awarii Service Fabric platformy Azure
description: Usługa Azure Service Fabric oferuje funkcje niezbędne do obsługi wszystkich typów awarii. W tym artykule opisano typy awarii, które mogą wystąpić, oraz sposób postępowania z nimi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f9bde0f81dc364aaa09dc9763f2014d83f992371
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298297"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Odzyskiwanie po awarii na platformie Azure Service Fabric
Krytyczna część dostarczania wysokiej dostępności zapewnia, że usługi mogą przetrwać wszystkie różne typy awarii. Jest to szczególnie ważne w przypadku nieplanowanych awarii i poza formantem. W tym artykule opisano niektóre typowe tryby awarii, które mogą być awariami, jeśli nie są poprawnie modelowane i zarządzane. Omawia również środki zaradcze i akcje, które należy podjąć w przypadku wystąpienia awarii. Celem jest ograniczenie lub wyeliminowanie ryzyka przestoju lub utraty danych, gdy występują błędy, planowane lub w inny sposób.

## <a name="avoiding-disaster"></a>Uniknięcie awarii
Podstawowym celem Service Fabric jest ułatwienie modelowania środowiska i usług w taki sposób, że typowe typy awarii nie są awariami. 

Ogólnie istnieją dwa typy scenariuszy awarii/awarii:

1. Błędy sprzętu lub oprogramowania
2. Błędy operacyjne

### <a name="hardware-and-software-faults"></a>Błędy sprzętu i oprogramowania
Błędy sprzętu i oprogramowania są nieprzewidywalne. Najprostszym sposobem przeżycia błędów jest uruchomienie większej liczby kopii usługi w granicach sprzętu lub oprogramowania. Na przykład, jeśli usługa jest uruchomiona tylko na jednej konkretnej maszynie, awaria tej usługi jest taka sama. Prostym sposobem na uniknięcie tego awarii jest upewnienie się, że usługa jest rzeczywiście uruchomiona na wielu komputerach. Testowanie jest również konieczne, aby upewnić się, że awaria jednego komputera nie zakłóca działania uruchomionej usługi. Planowanie wydajności gwarantuje, że wystąpienie zastępcze może zostać utworzone w innym miejscu, a zmniejszenie wydajności nie powoduje przeciążenia pozostałych usług. Ten sam wzorzec działa niezależnie od tego, co próbujesz uniknąć awarii. Na przykład. Jeśli obawiasz się awarii sieci SAN, możesz uruchomić wiele sieci SAN. Jeśli chodzi o utratę stojaka serwerów, możesz uruchamiać wiele stojaków. W przypadku martwisz się o utracie centrów danych usługa powinna działać w wielu regionach platformy Azure lub centrach danych. 

W przypadku działania w tym typie trybu łączonego nadal podlegają pewne typy jednoczesnych awarii, ale pojedyncza i nawet wiele błędów określonego typu (np. awaria pojedynczej maszyny wirtualnej lub łącza sieciowego) są automatycznie obsługiwane (i nie jest już "Katastrofa"). Service Fabric zapewnia wiele mechanizmów rozszerzania klastra i obsługi z powrotem węzłów i usług zakończonych niepowodzeniem. Service Fabric również umożliwia uruchamianie wielu wystąpień usług w celu uniknięcia nieplanowanych awarii w przypadku awarii.

Może się okazać, że wdrożenie wystarczająco duże, aby przekroczyć awarie nie jest możliwe. Na przykład może to potrwać więcej zasobów sprzętowych, niż jest to konieczne, w odniesieniu do szansy niepowodzeń. W przypadku aplikacji rozproszonych może być, że dodatkowe przeskoki komunikacyjne lub koszty replikacji stanu na odległość geograficzną powodują nieakceptowalne opóźnienia. Miejsce, w którym ten wiersz jest rysowany, różni się w zależności od aplikacji. W przypadku błędów oprogramowania w odniesieniu do błędu może to być usługa, którą próbujesz skalować. W takim przypadku kopie nie uniemożliwiają awarii, ponieważ warunek błędu jest skorelowany we wszystkich wystąpieniach.

### <a name="operational-faults"></a>Błędy operacyjne
Nawet jeśli Twoja usługa jest położona na całym świecie z wieloma nadmiarowośćmi, nadal mogą wystąpić zdarzenia katastrofalne. Na przykład jeśli ktoś przypadkowo ponownie skonfiguruje nazwę DNS usługi lub usunie ją z prawej strony. Załóżmy na przykład, że masz usługę stanową Service Fabric, a ktoś przypadkowo usunął tę usługę. O ile nie istnieje jakieś inne środki zaradcze, ta usługa i cały stan, który miał zostać już usunięty. Te typy katastrof operacyjnych ("Niestety") wymagają różnych środków zaradczych i czynności związanych z odzyskiwaniem niż regularne nieplanowane błędy. 

Najlepszym sposobem na uniknięcie tego typu błędów operacyjnych jest
1. Ograniczanie dostępu operacyjnego do środowiska
2. ścisła Inspekcja operacji niebezpiecznych
3. narzucanie automatyzacji, zapobieganie ręcznym lub poza pasmem, a także sprawdzanie konkretnych zmian w środowisku rzeczywistym przed ich przekazaniem
4. Upewnij się, że operacje niszczące są "miękkie". Operacje nietrwałe nie zaczynają obowiązywać natychmiast lub mogą być cofnięte w pewnym przedziale czasu

Service Fabric zapewnia pewne mechanizmy uniemożliwiające awarie operacyjne, takie jak zapewnienie kontroli dostępu [opartej na rolach](service-fabric-cluster-security-roles.md) dla operacji klastra. Większość z tych usterek operacyjnych wymaga jednak wysiłków organizacyjnych i innych systemów. Service Fabric zapewnia jakiś mechanizm do pogłębienia błędów operacyjnych, a mianowicie tworzenie kopii zapasowych i przywracanie dla usług stanowych.

## <a name="managing-failures"></a>Zarządzanie błędami
Celem Service Fabric jest niemal zawsze automatyczne zarządzanie błędami. Jednak w celu obsługi niektórych typów błędów usługi muszą mieć dodatkowy kod. Inne typy niepowodzeń _nie_ powinny być automatycznie rozwiązywane ze względu na bezpieczeństwo i przyczyny zachowania ciągłości działania firmy. 

### <a name="handling-single-failures"></a>Obsługa pojedynczych błędów
Pojedyncze maszyny mogą kończyć się niepowodzeniem w przypadku wszystkich różnych przyczyn. Niektóre z nich to przyczyny sprzętowe, takie jak zasilacze i awarie sprzętu sieciowego. Inne błędy są w oprogramowaniu. Obejmują one błędy rzeczywistego systemu operacyjnego i samej usługi. Service Fabric automatycznie wykrywa te typy błędów, w tym przypadki, w których maszyna zostanie odizolowana od innych maszyn z powodu problemów z siecią.

Niezależnie od typu usługi uruchomienie pojedynczego wystąpienia powoduje przestoje dla tej usługi, jeśli jedna kopia kodu nie powiedzie się z jakiegokolwiek powodu. 

W celu obsługi dowolnego pojedynczego błędu najprostszą czynnością jest upewnienie się, że usługi działają domyślnie w więcej niż jednym węźle. W przypadku usług bezstanowych można to osiągnąć za pomocą `InstanceCount` większej niż 1. W przypadku usług stanowych minimalnym zaleceniem jest zawsze `TargetReplicaSetSize` i `MinReplicaSetSize` co najmniej 3. Uruchomienie większej liczby kopii kodu usługi zapewnia, że usługa może obsłużyć pojedynczy błąd automatycznie. 

### <a name="handling-coordinated-failures"></a>Obsługa niepowodzeń koordynowanych
Skoordynowane awarie mogą wystąpić w klastrze z powodu planowanych lub nieplanowanych błędów infrastruktury oraz zmian lub planowanych zmian oprogramowania. Service Fabric modeleje strefy infrastruktury, które napotykają skoordynowane błędy jako domeny błędów. Obszary, które będą powodować skoordynowane zmiany oprogramowania są modelowane jako domeny uaktualnienia. Więcej informacji o domenach błędów i uaktualnień znajduje się w [tym dokumencie](service-fabric-cluster-resource-manager-cluster-description.md) zawierającym opis topologii klastra i definicji.

Domyślnie Service Fabric traktuje domeny błędów i uaktualniania podczas planowania, gdzie usługi powinny działać. Domyślnie program Service Fabric podejmuje próbę upewnienia się, że usługi działają w kilku domenach błędów i uaktualnień, tak aby usługi były nadal dostępne. 

Załóżmy na przykład, że awaria źródła prądu spowoduje niepowodzenie awarii stojaka maszyn. W przypadku wielu kopii usługi, na których działa utrata wielu maszyn w przypadku awarii domeny błędów, zostanie zaakceptowana tylko inny przykład pojedynczego błędu dla danej usługi. Dlatego Zarządzanie domenami błędów ma kluczowe znaczenie dla zapewnienia wysokiej dostępności usług. W przypadku uruchamiania Service Fabric na platformie Azure domeny błędów są zarządzane automatycznie. W innych środowiskach mogą one nie być. Jeśli tworzysz własne klastry lokalnie, pamiętaj, aby prawidłowo zmapować i zaplanować układ domeny błędów.

Domeny uaktualnień są przydatne w przypadku obszarów modelowania, w których oprogramowanie ma być uaktualniane w tym samym czasie. Z tego powodu domeny uaktualnienia często definiują granice, w których oprogramowanie jest wyłączane podczas planowanych uaktualnień. Uaktualnienia obu Service Fabric i usług są zgodne z tym samym modelem. Aby uzyskać więcej informacji na temat uaktualnień stopniowych, domen uaktualnienia i modelu kondycji Service Fabric, który pomaga zapobiegać niezamierzonym zmianom wpływającym na klaster i usługę, zobacz następujące dokumenty:

 - [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
 - [Samouczek dotyczący uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md)
 - [Model kondycji Service Fabric](service-fabric-health-introduction.md)

Możesz wizualizować układ klastra przy użyciu mapy klastra podanej w [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

Węzły ![rozłożone w różnych domenach błędów Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelowanie obszarów awarii, uaktualnień stopniowych, uruchamianie wielu wystąpień kodu i stanu usługi, reguły umieszczania w celu zapewnienia, że usługi działają w domenach błędów i uaktualnień, a wbudowane monitorowanie kondycji to tylko **niektóre** funkcje, które Service Fabric zapewnia, aby zachować normalne problemy z działaniem i awarie przed włączeniem awarii. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Obsługa jednoczesnych awarii sprzętu lub oprogramowania
Więcej informacji na temat pojedynczych awarii. Jak widać, są łatwe w obsłudze zarówno dla usług bezstanowych, jak i stanowych, co pozwala zachować większą liczbę kopii kodu (i stanu), które działają w domenach błędów i uaktualnień. Może również wystąpić wiele jednoczesnych błędów losowych. Jest to bardziej podobne do rzeczywistego katastrofy.


### <a name="random-failures-leading-to-service-failures"></a>Błędy losowe prowadzące do błędów usługi

#### <a name="stateless-services"></a>Usługi bezstanowe
`InstanceCount` dla usługi wskazuje żądaną liczbę wystąpień, które muszą być uruchomione. Gdy dowolne (lub wszystkie) wystąpienia zakończą się niepowodzeniem, Service Fabric reaguje, automatycznie tworząc wystąpienia zastępcze w innych węzłach. Service Fabric kontynuuje tworzenie zamian, dopóki usługa nie zostanie przywrócona do żądanej liczby wystąpień.
Innym przykładem, jeśli usługa bezstanowa ma `InstanceCount`-1, co oznacza, że jedno wystąpienie powinno być uruchomione na każdym węźle w klastrze. Jeśli niektóre z tych wystąpień zakończył się niepowodzeniem, Service Fabric wykryje, że usługa nie jest w żądanym stanie i podejmie próbę utworzenia wystąpień w węzłach, w których się znajdują.

#### <a name="stateful-services"></a>Usługi stanowe
Istnieją dwa typy usług stanowych:
1. Stanowy z utrwalonym stanem
2. Stanowy z nieutrwalonym stanem (stan jest przechowywany w pamięci)

Odzyskiwanie stanu usługi stanowej jest zależne od typu usługi stanowej oraz liczby replik, których dotyczy usługa i ile nie powiodło się.

Co to jest kworum?
W usłudze stanowej dane przychodzące są replikowane między replikami (podstawowymi i ActiveSecondary). Jeśli większość replik odbierze dane, dane są uznawane za zatwierdzone kworum (dla 5 replik, 3 to _kworum_). Oznacza to, że w dowolnym momencie jest gwarantowane, że będzie co najmniej kworum replik z najnowszymi danymi. Jeśli replika nie powiedzie się (wypowiedzenie 2 z 5 replik zakończyło się niepowodzeniem), możemy użyć wartości kworum, aby obliczyć, czy można odzyskać (ponieważ pozostałe 3 z 5 replik nadal są dostępne).

Co to jest utrata kworum?
Gdy kworum replik nie powiedzie się, partycja jest zadeklarowana jako w stanie utraty kworum. Załóżmy, że partycja ma 5 replik, co oznacza, że co najmniej 3 gwarantuje pełne dane. Jeśli kworum (3 out) replik nie powiedzie się, Service Fabric nie można ustalić, czy pozostałe repliki (2 out 5) mają wystarczającą ilość danych do przywrócenia partycji.

Określenie, czy wystąpił błąd usługi stanowej i zarządzanie nią następuje po trzech etapach:

1. Określanie, czy nastąpiła utrata kworum
    - Utrata kworum jest zadeklarowana, gdy większość replik stanowych usług nie działa w tym samym czasie.
2. Ustalanie, czy utrata kworum jest stała, czy nie
   - W większości przypadków błędy są przejściowe. Procesy są ponownie uruchamiane, węzły zostaną ponownie uruchomione, maszyny wirtualne zostaną zaleczone. Czasami niepowodzenia są trwałe. 
     - W przypadku usług bez trwałego uszkodzenia kworum lub większej liczby replik _natychmiast_ po utracie trwałych kworum. Gdy Service Fabric wykrywa utratę kworum w nietrwałej usłudze stanowej, natychmiast przejdzie do kroku 3 przez zadeklarowanie (potencjalne) utraty danych. W efekcie utraty danych jest to przydatne, ponieważ Service Fabric wie, że nie ma żadnego punktu oczekiwania na powrót z replik, ponieważ nawet jeśli zostały odzyskane z powodu nieutrwalonego charakteru usługi, dane zostały utracone.
     - W przypadku stałych usług trwałych awaria kworum lub więcej replik powoduje, że Service Fabric czekać, aż repliki powróciją i spowodują przywrócenie kworum. Powoduje to awarię usługi dla wszelkich _zapisów_ na partycji, której to dotyczy (lub "zestawu replik") usługi. Jednak odczyty mogą być nadal możliwe z obniżonymi gwarancjami spójności. Domyślna ilość czasu, jaką Service Fabric czeka na przywrócenie kworum, jest nieskończona, ponieważ trwa to (potencjalne) zdarzenie utraty danych i przenosi inne zagrożenia.
3. Określanie, czy wystąpiła rzeczywista utrata danych i przywracanie z kopii zapasowych
   
   Gdy Service Fabric wywołuje metodę `OnDataLossAsync`, jest zawsze spowodowane _podejrzaną_ utratą danych. Service Fabric zapewnia, że to wywołanie jest dostarczane do _najlepszej_ pozostałej repliki. Jest to niezależna replika. Z tego powodu zawsze mówimy, że _podejrzewana_ utrata danych polega na tym, że pozostała replika faktycznie ma ten sam stan, co w przypadku, gdy wystąpił problem. Jednak bez tego stanu, aby porównać go z, nie istnieje dobry sposób na Service Fabric lub operatorów, do których należy wiedzieć. W tym momencie Service Fabric również wie, że inne repliki nie zostaną wycofane. To była decyzja podjęta po zatrzymaniu oczekiwania na naprawienie przez chwilę utraty kworum. Najlepszym sposobem działania usługi jest zazwyczaj zawieszanie się i oczekiwanie na konkretną interwencję administracyjną. Co robią typowe implementacje `OnDataLossAsync` metod?
   1. Najpierw należy uruchomić dziennik, który `OnDataLossAsync` został wyzwolony, i wyłączyć wszelkie niezbędne Alerty administracyjne.
   1. Zwykle w tym momencie można wstrzymywać i czekać na dalsze decyzje i akcje wykonywane ręcznie. Jest to spowodowane tym, że nawet jeśli dostępne są kopie zapasowe, może być konieczne ich przygotowanie. Na przykład jeśli dwie różne usługi koordynują informacje, te kopie zapasowe mogą być konieczne do zmodyfikowania, aby upewnić się, że po przywróceniu następuje zgodność informacji tych dwóch usług. 
   1. Często istnieje również inne dane telemetryczne lub wydechowe z usługi. Te metadane mogą być zawarte w innych usługach lub w dziennikach. Te informacje mogą być używane do określenia, czy zostały odebrane jakiekolwiek wywołania i przetworzone na podstawowym poziomie, które nie były obecne w kopii zapasowej lub zreplikowane do tej określonej repliki. Może być konieczne ponowne odtworzenie lub dodanie do kopii zapasowej, zanim będzie możliwe przywrócenie.  
   1. Porównania stanu pozostałej repliki z zawartą w wszystkich dostępnych kopiach zapasowych. W przypadku korzystania z Service Fabric niezawodnych kolekcji dostępne są narzędzia i procesy, które zostały opisane w [tym artykule](service-fabric-reliable-services-backup-restore.md). Celem jest sprawdzenie, czy stan w replice jest wystarczający, czy też to, co może być brak kopii zapasowej.
   1. Po zakończeniu porównywania i w razie potrzeby przywracania kod usługi powinien zwrócić wartość true, jeśli wprowadzono jakiekolwiek zmiany stanu. Jeśli replika ustaliła, że była to Najlepsza dostępna kopia stanu i nie wprowadziła żadnych zmian, zwróć wartość false. Wartość true wskazuje, że wszystkie _Pozostałe_ repliki mogą teraz być niespójne z tym elementem. Zostaną one porzucone i ponownie skompilowane z tej repliki. Wartość false wskazuje, że nie zostały wprowadzone żadne zmiany stanu, więc inne repliki mogą zachować te informacje. 

Jest to istotne znaczenie dla autorów usług, które mogą powodować utratę danych i scenariusze błędów, zanim usługi są kiedykolwiek wdrożone w środowisku produkcyjnym. Aby chronić przed utratą danych, ważne jest okresowe [wykonywanie kopii zapasowej stanu](service-fabric-reliable-services-backup-restore.md) wszystkich usług stanowych w magazynie geograficznie nadmiarowym. Należy również upewnić się, że można go przywrócić. Ponieważ kopie zapasowe wielu różnych usług są wykonywane w różnych okresach, należy się upewnić, że po przywróceniu usługi będą mieć spójny widok. Rozważmy na przykład sytuację, w której jedna usługa generuje liczbę i zapisuje ją, a następnie wysyła ją do innej usługi, która również ją przechowuje. Po przywróceniu można stwierdzić, że druga usługa ma numer, ale nie jest to pierwsze, ponieważ jego kopia zapasowa nie obejmuje tej operacji.

Jeśli okaże się, że pozostałe repliki są niewystarczające do kontynuowania działania w scenariuszu utraty danych i nie można odtworzyć stanu usługi z telemetrii lub wydechu, częstotliwość wykonywania kopii zapasowych określa najlepszy możliwy cel punktu odzyskiwania (RPO). Service Fabric udostępnia wiele narzędzi do testowania różnych scenariuszy awarii, w tym trwałego kworum i utraty danych wymagających przywrócenia z kopii zapasowej. Te scenariusze są dostępne jako część narzędzi do testowania Service Fabric, zarządzane przez usługę błędów. Więcej informacji na temat tych narzędzi i wzorców można znaleźć [tutaj](service-fabric-testability-overview.md). 

> [!NOTE]
> Usługi systemowe mogą również mieć negatywny wpływ na kworum, z zachowaniem specyficznym dla danej usługi. Na przykład utrata kworum w usłudze nazw ma wpływ na rozpoznawanie nazw, podczas gdy usługa Menedżer trybu failover blokuje nowe tworzenie usługi i przełączanie w tryb failover. Chociaż usługi systemowe Service Fabric są zgodne z tym samym wzorcem, co usługi do zarządzania stanami, nie zaleca się wyłączania ich z utraty kworum i utraty danych. Zaleca się [wyszukanie pomocy technicznej](service-fabric-support.md) w celu ustalenia rozwiązania, które jest przeznaczone do konkretnej sytuacji.  Zwykle zaleca się po prostu poczekanie, aż repliki w dół zostaną zwrócone.
>

#### <a name="troubleshooting-quorum-loss"></a>Rozwiązywanie problemów z utratą kworum

Repliki mogą sporadycznie kończyć się niepowodzeniem z powodu przejściowego błędu. Poczekaj przez jakiś czas, ponieważ Service Fabric podejmie próbę ich przełączenia. Jeśli repliki nie były dłużej niż oczekiwany czas trwania, wykonaj poniższe kroki rozwiązywania problemów.
- Repliki mogą ulec awarii. Sprawdź raporty kondycji na poziomie repliki i dzienniki aplikacji. Zbieraj Zrzuty awaryjne i podejmuj działania niezbędne do odzyskania.
- Proces repliki mógł przestać odpowiadać. Sprawdź dzienniki aplikacji, aby to sprawdzić. Zbierz zrzut procesu, a następnie Kasuj proces nieodpowiadający. Service Fabric utworzy proces wymiany i podejmie próbę przywrócenia repliki.
- Węzły obsługujące repliki mogą być wyłączone. Uruchom ponownie źródłową maszynę wirtualną, aby przenieść węzły w górę.

Mogą wystąpić sytuacje, w których nie jest możliwe odzyskanie replik na przykład z powodu niepowodzenia dysków lub nieodpowiadających maszynom. W takich przypadkach Service Fabric musi zostać pożądany, aby nie czekać na odzyskanie repliki.
NIE należy używać tych metod, jeśli potencjalna utrata danych nie jest akceptowalna do przełączenia usługi do trybu online, w takim przypadku należy wykonać wszystkie działania w kierunku odzyskiwania maszyn fizycznych.

Następujące kroki mogą skutkować utratą danych — należy pamiętać o następujących kwestiach:
   
> [!NOTE]
> _Nie_ można bezpiecznie używać tych metod innych niż w celu dla konkretnych partycji. 
>

- Użyj interfejsu API `Repair-ServiceFabricPartition -PartitionId` lub `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`. Ten interfejs API umożliwia określenie identyfikatora partycji do odzyskania z QuorumLoss z potencjalną utratą danych.
- Jeśli klaster napotyka częste błędy, powodując, że usługi przechodzą w stan utraty kworum, a potencjalną _utratą danych jest akceptowalna_, określenie odpowiedniej wartości [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) może pomóc w autoodzyskiwaniu usługi. Service Fabric będzie czekać na podaną QuorumLossWaitDuration (domyślnie nieskończone) przed wykonaniem odzyskiwania. Ta metoda _nie jest zalecana_ , ponieważ może to spowodować nieoczekiwane straty danych.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostępność klastra Service Fabric
Ogólnie mówiąc, sam klaster Service Fabric jest wysoce rozproszonym środowiskiem bez pojedynczych punktów awarii. Awaria jednego z węzłów nie powoduje problemów z dostępnością lub niezawodnością klastra, głównie ponieważ usługi systemu Service Fabric są zgodne z tymi samymi wskazówkami: zawsze są domyślnie uruchamiane z co najmniej trzema replikami i tymi systemami usługi, które nie są uruchamiane bezstanowo na wszystkich węzłach. Podstawowe warstwy Service Fabric sieci i wykrywania błędów są w pełni rozproszone. Większość usług systemowych można odbudować z metadanych w klastrze lub wiedzieć, jak ponownie synchronizować ich stan z innych miejsc. Dostępność klastra może zostać naruszona, jeśli usługi systemowe zaczną korzystać z sytuacji utraty kworum, jak opisano powyżej. W takich przypadkach może nie być możliwe wykonywanie niektórych operacji w klastrze, takich jak uruchamianie uaktualnienia lub wdrażanie nowych usług, ale sam klaster jest nadal w użyciu. Usługi na już uruchomione będą nadal działać w tych warunkach, chyba że wymagają zapisu w usługach systemowych, aby kontynuować działanie. Jeśli na przykład Menedżer trybu failover jest w stanie utraty kworum, wszystkie usługi będą nadal działać, ale wszystkie usługi, które nie powiodą się, nie będą mogły zostać automatycznie ponownie uruchomione, ponieważ wymaga to zaangażowania Menedżer trybu failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Błędy w centrum danych lub regionie platformy Azure
W rzadkich przypadkach fizyczne centrum danych może stać się tymczasowo niedostępne ze względu na utratę mocy lub łączności sieciowej. W takich przypadkach klastry Service Fabric i usługi w tym centrum danych lub regionie platformy Azure będą niedostępne. _Dane są jednak zachowywane_. W przypadku klastrów działających na platformie Azure można wyświetlić aktualizacje na [stronie stanu platformy Azure][azure-status-dashboard]. W bardzo mało prawdopodobnym przypadku, gdy fizyczne centrum danych jest częściowo lub w pełni niszczone, mogą zostać utracone wszystkie klastry Service Fabric hostowane w tym miejscu lub zawarte w nich usługi. Obejmuje to wszystkie Stany, których kopie zapasowe nie są tworzone poza tym centrum danych lub regionem.

Istnieją dwie różne strategie do obsłużenia trwałej lub długotrwałej awarii jednego centrum danych lub regionu. 

1. Uruchom oddzielne klastry Service Fabric w wielu takich regionach i użyj pewnego mechanizmu przełączania do trybu failover i powrotu po awarii między tymi środowiskami. To sortowanie modelu "aktywny-aktywny" i "aktywny-pasywny" wymaga dodatkowego kodu zarządzania i operacji. Wymaga to również koordynacji kopii zapasowych z usług w jednym centrum danych lub regionie, dzięki czemu są one dostępne w innych centrach danych lub regionach, gdy jeden z nich kończy się niepowodzeniem. 
2. Uruchom pojedynczy klaster Service Fabric, który obejmuje wiele centrów danych lub regionów. Minimalna obsługiwana konfiguracja dla tego programu to trzy centra danych lub regiony. Zalecana liczba regionów lub centrów danych wynosi pięć. Wymaga to bardziej złożonej topologii klastra. Zaletą tego modelu jest jednak, że awaria jednego centrum danych lub regionu jest konwertowana z awarii na normalną awarię. Te błędy mogą być obsługiwane przez mechanizmy, które działają w przypadku klastrów w obrębie jednego regionu. Domeny błędów, domeny uaktualnień i reguły umieszczania Service Fabric zapewniają, że obciążenia są dystrybuowane w taki sposób, aby tolerowali normalne awarie. Aby uzyskać więcej informacji na temat zasad, które mogą pomóc w obsłudze usług w tym typie klastra, zapoznaj się z [zasadami umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Błędy losowe prowadzące do awarii klastra
Service Fabric ma koncepcję węzłów inicjatora. Są to węzły, które utrzymują dostępność bazowego klastra. Te węzły pomagają zapewnić, że klaster pozostanie w ramach ustanowienia dzierżaw z innymi węzłami i służy jako tiebreakers w przypadku niektórych rodzajów awarii sieci. Jeśli błędy losowe usuwają większość węzłów inicjatora w klastrze i nie zostaną przywrócone, wówczas pierścień Federacji klastra zostanie zwinięty, gdy utracisz kworum węzła inicjatora i klaster ulegnie awarii. Na platformie Azure dostawca zasobów Service Fabric zarządza Service Fabric konfiguracjami klastra i domyślnie dystrybuuje węzły inicjatora w ramach podstawowego typu węzła i domen uaktualnienia. Jeśli podstawowy NodeType jest oznaczony jako trwałość Srebrna lub złota, po usunięciu węzła inicjatora w podstawowym NodeType lub ręcznym usunięciu węzła inicjatora klaster podejmie próbę podwyższenia poziomu innego węzła, który nie jest inicjatorem, z podstawowego NodeType dostępnego pojemność i zakończy się niepowodzeniem, jeśli masz mniejszą ilość dostępnej pojemności niż wymaga poziomu niezawodności klastra dla typu węzła podstawowego.

W przypadku autonomicznych klastrów Service Fabric i platformy Azure "typ węzła podstawowego" to taki, który uruchamia nasiona. Podczas definiowania typu węzła podstawowego Service Fabric automatycznie będzie korzystać z liczby udostępnionych węzłów przez utworzenie do 9 węzłów inicjatora i 7 replik każdej usługi systemowej. Jeśli zestaw losowych błędów przeprowadzi większość tych replik usługi systemowej jednocześnie, usługi systemowe spowodują utratę kworum, jak opisano powyżej. Jeśli większość węzłów inicjatora zostanie utracona, klaster zostanie zamknięty wkrótce.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak symulować różne błędy przy użyciu [struktury testowania](service-fabric-testability-overview.md)
- Zapoznaj się z innymi zasobami odzyskiwania po awarii i wysokiej dostępności. Firma Microsoft opublikowała na tych tematach dużą liczbę wskazówek. Niektóre z tych dokumentów odnoszą się do określonych technik korzystania z innych produktów, ale zawierają wiele ogólnych najlepszych rozwiązań, które można zastosować również w kontekście Service Fabric:
  - [Lista kontrolna dotycząca dostępności](/azure/architecture/checklist/resiliency-per-service)
  - [Przechodzenie do szczegółów odzyskiwania po awarii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność][dr-ha-guide]
- Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
