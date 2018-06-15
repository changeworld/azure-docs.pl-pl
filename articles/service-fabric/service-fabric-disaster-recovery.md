---
title: Odzyskiwanie po awarii sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Sieć szkieletowa usług Azure oferuje funkcje niezbędne do postępowania w przypadku wszystkich typów awarii. W tym artykule opisano typy awarii, które mogą wystąpić oraz sposób postępowania z nimi.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 295772b70529f79c7a4c135d8ea7c12a1c661fe6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206440"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Odzyskiwanie po awarii w sieci szkieletowej usług Azure
Istotną częścią dostarczanie wysokiej dostępności jest zapewnienie przełączniki wszystkich różnych typów błędów usługi. Jest to szczególnie ważne w przypadku błędów, które są nieplanowane i poza nimi formantu. W tym artykule opisano niektóre często używanych trybów awarii, może być awarii, jeśli nie modelowane i zarządzane prawidłowo. Omówiono także czynniki oraz czynności do wykonania, jeśli mimo wszystko się stało po awarii. Celem jest ograniczenie lub wyeliminowania ryzyko utraty danych lub przestoju, gdy występują awarie, planowane lub w przeciwnym razie wystąpić.

## <a name="avoiding-disaster"></a>Unikanie po awarii
Podstawowym celem usługi sieć szkieletowa jest ułatwienie modelowanie środowiska usług w taki sposób, że błąd typowych nie są awarii. 

Ogólnie występują dwa typy scenariuszy awarii/awarii:

1. Błędy sprzętu lub oprogramowania
2. Błędy operacyjne

### <a name="hardware-and-software-faults"></a>Błędy sprzętu i oprogramowania
Błędy sprzęt i oprogramowanie są nieprzewidywalne. Najprostszym sposobem na przetrwanie błędów działa większej liczby kopii usługi łączone w granicach awarii sprzętu lub oprogramowania. Na przykład jeśli usługa jest uruchomiona tylko na jednej maszynie określonego, awaria tego jedna maszyna jest awarii dla tej usługi. Prostym sposobem uniknięcia tego po awarii jest zapewnienie, że usługa rzeczywiście jest uruchomiona na wielu komputerach. Testowanie jest także niezbędne do zapewnienia, że błąd na jednym komputerze nie spowoduje zakłócenia uruchomionej usługi. Planowanie pojemności zapewnia wystąpienia zastąpienia można tworzyć innym miejscu i że spadek wydajności nie przeciążać pozostałych usług. Tego samego wzorca działa niezależnie od tego, co próbujesz uniknąć awarii. Na przykład. w przypadku obaw o awarii sieci SAN, możesz uruchomić przez wiele sieci SAN. W przypadku obaw o utracie stojak serwerów, możesz uruchomić między wieloma stojakami. Jeśli Obawiamy o utracie centrów danych, usługę należy uruchomić w wielu regionach platformy Azure lub centrów danych. 

Podczas uruchamiania tego rodzaju łączonych tryb, możesz nadal może ulec niektóre typy jednoczesnych awarii, ale jednym i nawet wiele błędów określonego typu (ex: jednej maszyny Wirtualnej lub sieci niepowodzenie link) są realizowane automatycznie (i dlatego nie jest już "awarii"). Usługi Service Fabric zawiera wiele mechanizmy rozszerzania klastra i uchwytów Przywracanie nie powiodło się, węzły i usług z powrotem. Sieć szkieletowa usług umożliwia uruchamianie wielu wystąpień usług w celu uniknięcia tego rodzaju nieplanowanych awarii z zwroty, do rzeczywistych awarii.

Może to być przyczyny, dlaczego uruchomienie wystarczająco duże, aby obejmować za pośrednictwem błędy wdrożenia nie jest możliwe. Na przykład może zająć więcej zasobów sprzętowych, niż chcesz płacić za względem ryzyko awarii. Podczas pracy nad aplikacji rozproszonych, możliwe że przeskoków dodatkową komunikację lub stan replikacji koszty w odległości geograficznej powoduje, że można zaakceptować opóźnienia. Gdzie jest rysowany tego wiersza, różni się dla każdej aplikacji. Błędy oprogramowania w szczególności ten błąd może być usługi, który próbujesz skalowania. W takim przypadku większej liczby kopii nie uniemożliwiają po awarii, ponieważ występuje błąd, są korelowane we wszystkich wystąpieniach.

### <a name="operational-faults"></a>Błędy operacyjne
Nawet wtedy, gdy usługi są łączone na całym świecie z wielu zwolnienia, on nadal występują zdarzenia Fatalne. Na przykład jeśli ktoś przypadkowo ponownie konfiguruje nazwę dns dla usługi lub usunięcia ich ostatecznego. Na przykład załóżmy, że ma stanowe usługi sieć szkieletowa usług, a ktoś przypadkowo usunięty tej usługi. Jeżeli nie istnieje inne środki zaradcze, tej usługi, a wszystkie miała stanu jest obecnie usunięty. Tego rodzaju operacyjne awarii ("Niestety") wymagają różne czynniki ograniczające i kroki odzyskiwania niż regularne nieplanowanych awarii. 

Najlepszym sposobem, aby uniknąć tego rodzaju błędów operacyjne mają
1. ograniczenia operacyjne dostępu do środowiska
2. ściśle inspekcji niebezpieczne operacje
3. nałożyć automatyzacji, ręczne lub poza pasmem, zmiany i weryfikacji określonych zmian w środowisku rzeczywiste przed ich wprowadzania
4. Upewnij się, że destrukcyjnego operacji są "słowo soft". Elastyczne operacje nie zaczynają obowiązywać natychmiast lub może być cofnięte w ramach przedziałów czasu

Usługa Service Fabric realizuje niektórych mechanizmów, aby zapobiec błędom operacyjne, takie jak dostarczanie [opartej na rolach](service-fabric-cluster-security-roles.md) ACL dla operacji klastra. Jednak większość tych błędów operacyjne wymagają działań organizacji i innych systemów. Sieć szkieletowa usług zapewniają mechanizmu dla pozostałych operacyjne błędów, głównie do tworzenia kopii zapasowej i przywracania dla stanowych usług.

## <a name="managing-failures"></a>Zarządzanie błędów
Celem usługi sieć szkieletowa jest prawie zawsze automatyczne zarządzanie błędów. Jednak w celu obsługi niektórych typów błędów, usługi muszą mieć dodatkowy kod. Inne typy błędów powinna _nie_ automatycznie należy rozwiązać kwestie dotyczące bezpieczeństwa i biznesowych powodów ciągłości. 

### <a name="handling-single-failures"></a>Obsługa błędów pojedynczego
Pojedynczymi urządzeniami może zakończyć się niepowodzeniem dla wielu różnych przyczyn. Niektóre z nich są przyczyny sprzętu, takich jak zasilacze i sieci na awarie sprzętowe. Inne błędy są w oprogramowaniu. Obejmują one błędów rzeczywiste systemu operacyjnego i samą usługę. Sieć szkieletowa usług automatycznie wykrywa te typy błędów, łącznie z przypadkami, w którym komputer staje się odizolowany od innych maszyn z powodu problemów z siecią.

Bez względu na typ usługi uruchomione pojedynczego wystąpienia wyniki w przestoju dla tej usługi Jeśli tym pojedynczej kopii kodu nie powiedzie się z jakiegokolwiek powodu. 

Aby umożliwić obsługę dowolnego pojedynczego uszkodzenia, najprostszym operacją, której można wykonywać jest upewnij się, że usług domyślnie uruchamiane na więcej niż jeden węzeł. W przypadku usług bezstanowych można to zrobić za mające `InstanceCount` większą niż 1. Dla stanowych usług minimalne zalecane jest zawsze `TargetReplicaSetSize` i `MinReplicaSetSize` co najmniej 3. Uruchamianie większej liczby kodu usługi kopii gwarantuje, że usługa może obsłużyć automatycznie dowolnego pojedynczego uszkodzenia. 

### <a name="handling-coordinated-failures"></a>Koordynowane Obsługa błędów
Błędy skoordynowany sposób może się zdarzyć klastra wyniku planowane lub awarie nieplanowane infrastruktury i zmiany lub zmiany oprogramowania planowane. Sieć szkieletowa usług modele infrastruktury strefach skoordynowanego awarii jako domen błędów. Obszary, które wystąpią zmiany oprogramowania skoordynowanego są modelowane jako domen uaktualnienia. Więcej informacji o domenach awarii i uaktualniania znajduje się w [tego dokumentu](service-fabric-cluster-resource-manager-cluster-description.md) opisujący Topologia klastra i definicji.

Domyślnie usługa sieć szkieletowa uwzględnia domenach awarii i uaktualniania podczas planowania, gdzie mają być uruchamiane z usługi. Domyślnie usługa sieć szkieletowa próbuje upewnij się, że usługi uruchomione w kilku domenach awarii i uaktualniania, jeśli wprowadzenia zmiany planowane lub nieplanowane usługi są dostępne. 

Na przykład załóżmy, że błąd źródła zasilania powoduje, że stojak maszyn do jednoczesnego niepowodzenia. Z wielu kopii usługi uruchomionej na utratę wielu maszyn uszkodzenie domeny błędów zmieni się w innym tylko przykładem pojedynczego uszkodzenia dla danej usługi. Jest to, dlaczego zarządzanie domen błędów jest istotny dla zapewnienia wysokiej dostępności usług. Gdy usługa sieć szkieletowa usług działających na platformie Azure, automatycznie są zarządzane domen błędów. W innych środowiskach może nie być. Jeśli tworzysz klastrach lokalnie, należy zamapować i poprawnie zaplanować układu domeny błędów.

Domen uaktualnienia są przydatne do modelowania obszary, w którym oprogramowania ma zostać uaktualniony, w tym samym czasie. W związku z tym domen uaktualnienia również często zdefiniuj granice, w którym oprogramowanie jest wyłączona podczas uaktualniania planowane. Uaktualnienia platformy Service Fabric i usług postępuj zgodnie z tym samym modelu. Aby uzyskać więcej informacji na wprowadzanie uaktualnień, domen uaktualnienia i model kondycji sieci szkieletowej usług, która pomaga zapobiegać niezamierzonych zmian wpływających na klastrze i usługi Zobacz tych dokumentów:

 - [Uaktualnianie aplikacji](service-fabric-application-upgrade.md)
 - [Samouczek uaktualnienia aplikacji](service-fabric-application-upgrade-tutorial.md)
 - [Model kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

Można zwizualizować układ przy użyciu mapy klastra w klastrze [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Węzły rozmieszczenie domen błędów w narzędziu Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelowanie obszarów awarii uaktualnień stopniowych, uruchamianie wielu wystąpień kodu usługi i stanu, zasady umieszczania w celu zapewnienia usług Uruchom w domenach awarii i uaktualniania, a monitorowanie kondycji wbudowanych są po prostu **niektórych** funkcji tej usługi Service Fabric zawiera aby zapobiec zwroty do awarii błędy i problemy z działaniem normalnego. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Obsługa jednoczesnych awarii sprzętu lub oprogramowania
Powyżej zajmowaliśmy pojedynczego błędów. Jak widać, są łatwe w obsłudze zarówno bezstanowych i stanowych usług przez przechowywanie większej liczby kopii uruchomionych w domenach awarii i uaktualniania kodu (i stanu). Również możliwe wielu jednoczesnych losowe awarie. Są to bardziej może doprowadzić do rzeczywistego po awarii.


### <a name="random-failures-leading-to-service-failures"></a>Losowe błędy, co może prowadzić do błędów usługi
Załóżmy, że usługa ma `InstanceCount` 5 i kilka węzłów uruchamianie tych wszystkich wystąpień nie powiodło się w tym samym czasie. Sieć szkieletowa usług reaguje automatycznego tworzenia wystąpień zamiany na innych węzłach. Tworzenie zamiany do czasu usługi do jego liczbę wystąpień żądany jest kontynuowane. Inny przykład załóżmy, że wystąpił usługi bezstanowej z `InstanceCount`-1, co oznacza działa we wszystkich węzłach prawidłowy w klastrze. Załóżmy, że niektóre z tych wystąpień były się niepowodzeniem. W takim przypadku sieci szkieletowej usług powiadomienia, usługa nie jest w stanie żądaną i podejmie próbę utworzenia wystąpienia w węzłach, gdy ich brakuje. 

Dla stanowych usług sytuacji zależy od tego, czy usługa utrwalił stanu lub nie. Także od tego, jak wiele replik usługi ma i ile nie powiodło się. Określanie, czy dla usługi stanowej wystąpił awarii i zarządzanie nią jest zgodna z trzech etapów:

1. Określanie, jeśli nastąpiła utrata kworum lub nie
 - Utrata kworum jest dowolnym momencie większość replik usługi stanowej działają w tym samym czasie, w tym podstawowym.
2. Określanie, czy utraty kworum jest stałe
 - W większości przypadków, błędy są przejściowej. Procesy, zostaną ponownie uruchomione, węzły są ponownie uruchamiane maszyny wirtualne są dalszymi, poprawianego partycje sieciowe. Czasami jednak błędy są trwałe. 
    - Dla usługi bez trwałych stanu awarii kworum lub większej liczby replikami wyniki _natychmiast_ w wyniku utraty kworum trwałych. Gdy sieć szkieletowa usług wykryje utraty kworum w stanowe usługi trwałe, natychmiast wykonywany do kroku 3 przez zadeklarowanie dataloss (potencjalnych). Kontynuowaniem dataloss ma sens, ponieważ sieć szkieletowa usług wie, że nie ma żadnych punktu w oczekiwania repliki, aby wrócić, ponieważ nawet jeśli zostały one odzyskane może być pusta.
    - Dla stanowych usług trwałych awarii kworum lub większej liczby replikami powoduje, że sieć szkieletowa usług można uruchomić oczekiwania repliki, aby wrócić i przywróć kworum. To powoduje awarię usług dla każdego _zapisuje_ dotyczy partycji (lub "zestawu replik") usługi. Jednak odczyty nadal może być możliwe gwarancje spójności mniejsze. Domyślna ilość czasu oczekiwania sieci szkieletowej usług dla kworum można przywrócić jest nieograniczony, ponieważ postępowania jest zdarzeniem dataloss (potencjalnych) i posiada inne ryzyka. Zastępowanie domyślnego `QuorumLossWaitDuration` wartość jest możliwe, ale nie jest zalecane. Zamiast tego w tej chwili wszystkich działań należy przywrócić dół replik. Wymaga to dostarczają węzłów, które są w dół do tworzenia kopii zapasowych i zapewnienie, że ich Zainstaluj przechowywania do lokalnego trwały stan dysków. Utrata kworum jest spowodowany przez niepowodzenie procesu, Service Fabric automatycznie próbuje ponownie procesy i uruchom ponownie replik zawartych w nich. W przypadku niepowodzenia usługi sieć szkieletowa Raporty kondycji błędy. Jeśli te mogą zostać rozwiązane następnie repliki zwykle powrotu. Czasami jednak repliki nie może zostać przywrócone. Na przykład dyski nie wszystkie powiodło się lub maszyny fizyczne zniszczone jakiś sposób. W takich przypadkach teraz zostały zdarzenia utraty kworum trwałych. Mówić sieci szkieletowej usług, aby zakończyć oczekiwanie na dół repliki, aby wrócić, administrator klastra musi ustalić które partycje, których dotyczy usługi i wywołać `Repair-ServiceFabricPartition -PartitionId` lub ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` interfejsu API.  Ten interfejs API umożliwia określanie Identyfikatora partycji można przenieść poza QuorumLoss i do potencjalnych dataloss.

> [!NOTE]
> Jest _nigdy nie_ bezpiecznie korzystać tego interfejsu API innych niż w sposób docelowych z określonymi partycjami. 
>

3. Określenie, czy nastąpiła utrata danych rzeczywistych i przywracanie z kopii zapasowej
  - Gdy usługa sieć szkieletowa wywołuje `OnDataLossAsync` metody jest zawsze z powodu _podejrzane_ dataloss. Sieć szkieletowa usług gwarantuje, że to wywołanie jest dostarczany do _najlepsze_ pozostałe repliki. Jest to, niezależnie od repliki nastąpił postęp w większości. Powód zawsze mówi _podejrzane_ dataloss jest repliką pozostałych faktycznie może wszystkich stanów w tej samej jako serwera podstawowego, jak podczas spadek. Ponieważ bez tego stanu do porównania jej nie ma żadnych dobrze dla sieci szkieletowej usług lub operatorów się upewnić. W tym momencie usługi sieć szkieletowa również wie, że repliki programu nie będzie można ponownie. Która jest wysyłane w przypadku zatrzymania oczekiwanie na utratę kworum rozwiąże się samoistnie decyzji. Najlepsze rozwiązanie dla usługi jest zwykle Zablokuj i poczekaj, aż określonych interwencji administratora. To samo co Typowa implementacja `OnDataLossAsync` Wykonaj metodę?
  - Po pierwsze, dziennika `OnDataLossAsync` zostało wyzwolone i szybko włączyć wszystkie niezbędne alertach.
   - Zwykle w tym punkcie, aby wstrzymać i poczekaj, aż dalszych decyzji i działania ręczne, które mają być podjęte. To dlatego nawet jeśli kopie zapasowe są dostępne może muszą być przygotowane. Na przykład jeśli dwóch różnych usług koordynuje informacje, tych kopii zapasowych może być konieczne można zmodyfikować w celu zapewnienia po przywróceniu się coś, co informacje te dwie usługi najważniejsze informacje spójne. 
  - Często jest również inne dane telemetryczne ani spalin z usługi. Te metadane mogą być zawarte w innych usługach lub w dziennikach. Można te informacje potrzebne do ustalenia, czy wystąpiły wszystkie wywołania odbierane i przetwarzane w podstawowej, które nie były widoczne w kopii zapasowej lub replikowane do tej określonej repliki. Te muszą być odtwarzany lub dodane do kopii zapasowej, zanim będzie możliwe przywrócenie.  
   - Porównania stanu repliki pozostałe na zawarte w żadnej kopii zapasowej, które są dostępne. Jeśli przy użyciu kolekcji niezawodnej sieci szkieletowej usług, a następnie istnieją narzędzia i przetwarza dostępne tak, opisana [w tym artykule](service-fabric-reliable-services-backup-restore.md). Celem jest czy stan w obrębie repliki jest wystarczająca, lub również jakiego kopia zapasowa może nie istnieć.
  - Po zakończeniu porównanie i potrzeby Przywracanie zostało ukończone, kodu usługi powinna zwrócić wartość true, jeśli wprowadzono zmiany stanu. Jeśli replika została najlepiej dostępnych kopii stanu i nie wprowadził zmian, zwracany jest false. Wartość TRUE wskazuje, że jakaś _innych_ pozostałych repliki mogą być niezgodne z tym kontem. Będzie można porzucić i ponownie skompilowany od tej repliki. Wartość FAŁSZ oznacza, że nie wprowadzono żadnych zmian stanu, więc innych replik można zachować ich. 

Jest bardzo ważny, że autorów usługi rozwiązaniem potencjalne dataloss i scenariuszy awarii, przed usług kiedykolwiek są wdrażane w środowisku produkcyjnym. Aby chronić przed możliwością dataloss, ważne jest, aby okresowo [Utwórz kopię zapasową stanu](service-fabric-reliable-services-backup-restore.md) dowolnego stanowych usług do magazynu geograficznie nadmiarowego. Należy również, że masz możliwość przywrócenia. Ponieważ kopie zapasowe wielu różnych usług są wykonywane w różnym czasie, należy się upewnić, po przywróceniu usługi ma spójny widok siebie nawzajem. Rozważmy na przykład sytuacji, w której jedna usługa generuje numer i zapisze go, a następnie wysyła go do innej usługi, które także są przechowywane. Po przywróceniu może się okazać, że usługa drugi ma numer, ale pierwszy ma, ponieważ jest on kopii zapasowej nie zawiera tej operacji.

Jeśli możesz dowiedzieć się, że pozostałe repliki są za małe, aby kontynuować w scenariuszu dataloss i nie można odtworzyć stanu usługi z telemetrii lub spalin, częstotliwości kopii zapasowych określa Twoje najlepsze możliwe cel punktu odzyskiwania należy (RPO). Sieć szkieletowa usług oferuje wiele narzędzi do testowania różnych scenariuszy awarii, w tym stałe kworum i dataloss wymagające Przywracanie z kopii zapasowej. Te scenariusze są dołączane jako część usługi sieć szkieletowa kontroli narzędzi, zarządzane przez usługę analiza błędów. Więcej informacji dotyczących tych narzędzi i wzorce jest dostępna [tutaj](service-fabric-testability-overview.md). 

> [!NOTE]
> Usługi systemu można również pogorszyć utraty kworum wpływu dla danej usługi. Na przykład utrata kworum w usłudze nazewnictwa ma wpływ na rozpoznawania nazw, natomiast utrata kworum w usłudze Menedżer trybu failover blokowanie tworzenia nowych usług i pracy w trybie Failover. Gdy usługa sieć szkieletowa usług systemowych wykonują te same czynności, jak usług do zarządzania stanem, nie zaleca czy należy podjąć próbę przenieść je poza utraty kworum i do potencjalnych dataloss. Zaleca się zamiast tego [poszukuje](service-fabric-support.md) ustalenie rozwiązania, która jest skierowana do konkretnej sytuacji.  Zazwyczaj jest po prostu odczekać, aż powrócić dół replik.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Dostępność klastra sieci szkieletowej usług
Ogólnie rzecz biorąc samego klastra usługi sieć szkieletowa jest bardzo rozproszone środowisko z żadnego pojedynczego punktu awarii. Błąd dowolnego pojedynczego węzła nie spowoduje dostępności lub niezawodnością dla klastra, przede wszystkim, ponieważ usługi systemowe platformy Service Fabric postępuj zgodnie z wytycznymi tego samego, wydanego wcześniej: są zawsze uruchamiane z co najmniej trzech repliki domyślnie, a tych usług systemowych, które są bezstanowych Uruchom we wszystkich węzłach. Pełni rozpowszechnianych podstawowej warstwy wykrywania sieci szkieletowej usług sieci i niepowodzenie. Większość usług systemu może zostać również przebudowany z metadanych w klastrze lub wiedzieć, jak zsynchronizować ich stan w innych miejscach. Dostępność klastra można złamane, gdy usługi systemowe pobranie do kworum utraty sytuacji takich jak opisane powyżej. W takim przypadku nie można wykonywania pewnych operacji w klastrze, takich jak uruchomienie uaktualnienia lub wdrażania nowych usług, ale samego klastra jest nadal uruchomiony. Usług na już uruchomiona zostanie będą nadal działać w tych warunkach, o ile nie wymagają one zapisy w usługach system do dalszego działania. Na przykład jeśli menedżera trybu Failover jest w wyniku utraty kworum wszystkie usługi będzie nadal działać, ale wszystkie usługi, które nie są nie będzie mógł automatycznie uruchomiony ponownie, ponieważ wymaga to włączenia menedżera trybu Failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Awarii centrum danych lub region platformy Azure
W rzadkich przypadkach jest tymczasowo niedostępny z powodu utraty zasilania lub połączenia sieciowego może stać się centrum danych fizycznych. W takich przypadkach swoich klastrów platformy Service Fabric i usług w centrum danych lub regionu Azure będą niedostępne. Jednak _danych jest zachowywana_. Dla klastrów działających na platformie Azure, możesz przeglądać na aktualizacje na awarie [strony stanu platformy Azure][azure-status-dashboard]. W przypadku bardzo mało prawdopodobne, że centrum danych fizycznych częściowo lub całkowicie zniszczenia żadnych klastrów sieci szkieletowej usług hostowanych lub usług zawartych w nich mogą zostać utracone. W tym wszelkie stanu kopii zapasowej nie poza tym centrum danych lub regionu.

Ma dwa różne strategie pozostałych stałych lub utrzymujących awarii jednego centrum danych lub regionu. 

1. Uruchom oddzielne klastry sieci szkieletowej usług w wielu regionach takich i korzystanie z mechanizmu dla trybu failover i powrotu po awarii między tych środowisk. Tego rodzaju wielu klastrów aktywny aktywny lub aktywny / pasywny model wymaga dodatkowego kodu zarządzanie i operacje. To wymaga również, że koordynacji kopii zapasowych z usług w jednym centrum danych lub regionu, aby były dostępne w innych centrów danych lub regionach, gdy dla jednego nie powiedzie się. 
2. Uruchom jednego klastra usługi sieć szkieletowa, obejmującej wiele centrów danych lub regionach. Minimalnej obsługiwanej konfiguracji dla tego jest trzech centrów danych lub regionach. Zalecana liczba regionów lub centrów danych wynosi pięć. Wymaga to bardziej złożona Topologia klastra. Zaletą tego modelu jest jednak czy awarii jednego centrum danych lub regionu jest konwertowany na normalne błąd w danych po awarii. Te błędy są obsługiwane przez te mechanizmy, które działają w przypadku klastrów w pojedynczym regionie. Domen błędów domen uaktualnienia i reguły umieszczania usługi sieć szkieletowa upewnij się, że obciążenia są dystrybuowane, tak aby ich tolerować błędy normalnego. Aby uzyskać więcej informacji dotyczących zasad, które mogą pomóc działać usługi, w tym typie klastra, przeczytaj na [zasad umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Losowe błędy, co może prowadzić do awarii klastra
Sieć szkieletowa usług korzysta z koncepcji węzłów inicjatora. Są to węzły, które zachować dostępność klastra źródłowego. Te węzły pomoc, aby upewnić się, że gdy klaster będzie pozostawał się poprzez ustanowienie dzierżawy na innych węzłach i służących jako tiebreakers podczas niektóre rodzaje awarie sieci. Jeśli losowe awarie usuwania większości węzłów inicjatora w klastrze i nie zostaną one zwrócone, klaster zostaje automatycznie zamknięty. Na platformie Azure, są automatycznie zarządzane węzłów inicjatora: są rozłożone dostępne domenach awarii i uaktualniania, a jeśli jeden węzeł zostanie usunięty z klastra kolejnego zostaną utworzone w jego miejsce. 

Zarówno w przypadku autonomicznych klastrów sieci szkieletowej usług, jak i Azure "Podstawowy typ węzła" jest uruchamiana ziarna. Podczas definiowania typu węzła podstawowego, usługi sieć szkieletowa automatycznie skorzystają z liczby węzłów, tworząc do węzłów inicjatora 9 i 9 replik każdej usług systemowych. Jeśli zestaw losowe awarie przyjmuje się większość tych replik usługi systemu jednocześnie, usług systemu wprowadź utraty kworum, możemy opisanych powyżej. W przypadku utraty Większość węzłów inicjatora klastra zostanie zamknięty wkrótce po.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak można symulować różne błędy, przy użyciu [framework testowania](service-fabric-testability-overview.md)
- Przeczytaj inne zasoby odzyskiwania po awarii i wysokiej dostępności. Firma Microsoft udostępniła dużej liczby wskazówek na następujące tematy. Gdy niektóre z tych dokumentów odwołują się do określonych technik do użycia w innych produktów, zawierają wiele ogólne najlepsze rozwiązania, które można zastosować w tym kontekście sieci szkieletowej usług:
  - [Lista kontrolna dotycząca dostępności](../best-practices-availability-checklist.md)
  - [Wykonywanie wyszczególniania odzyskiwania po awarii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Odzyskiwanie po awarii i wysoką dostępność aplikacji Azure][dr-ha-guide]
- Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
