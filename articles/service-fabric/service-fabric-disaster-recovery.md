---
title: Odzyskiwanie po awarii w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Usługa Azure Service Fabric udostępnia funkcje niezbędne do czynienia ze wszystkimi typami awarii. W tym artykule opisano typy awarii, które mogą wystąpić i radzenia sobie z nimi.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7153a6ed4a91e59eea936f1e17d827a40bb99371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60948520"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Odzyskiwanie po awarii w usłudze Azure Service Fabric
Kluczową częścią dostarczania wysokiej dostępności jest zapewnienie, że usługi mogą przetrwać różne rodzaje błędów. Jest to szczególnie ważne w przypadku awarii, znajdujących się niezaplanowane i poza Twoją kontrolą. W tym artykule opisano niektóre typowe trybów awarii, które może być awarii, w przeciwnym razie modelowane i poprawnie zarządzać. Omówiono także środki zaradcze i akcje do wykonania w przypadku awarii mimo to się stało. Celem jest ograniczenie lub wyeliminowania ryzyka przestoju lub utraty danych, gdy wystąpią błędy, planowane lub w przeciwnym razie wystąpić.

## <a name="avoiding-disaster"></a>Unikanie po awarii
Usługi Service Fabric podstawowym celem jest ułatwienie modelami, środowiska i usługi w taki sposób, że typowe typy błędów nie są awarii. 

Ogólnie rzecz biorąc, istnieją dwa typy scenariuszy awarii/błąd:

1. Błędy sprzętowe lub programowe
2. Błędy operacyjne

### <a name="hardware-and-software-faults"></a>Błędy sprzętu i oprogramowania
Błędy sprzętu i oprogramowania są nieprzewidywalne. Najprostszym sposobem na przetrwanie błędów działa większej liczby kopii usługa była dostępna w granicach błędów sprzętu lub oprogramowania. Na przykład jeśli usługa jest uruchomiona tylko na jednym komputerze określonym, awarii jednej maszyny jest awarii dla tej usługi. Prostym sposobem uniknięcia tego po awarii jest zapewnienie, że usługa jest uruchomione na wielu komputerach. Testowanie jest również upewnić się, że błąd na jednym komputerze nie przerwać uruchomioną usługę. Planowanie pojemności zapewnia wystąpienia zastąpienie mogą być tworzone gdzie indziej i że spadek wydajności nie przeciążać pozostałych usług. Tego samego wzorca działa niezależnie od tego, próbuje uniknąć awarii. Na przykład. Jeśli interesujące Cię awarii za pośrednictwem sieci SAN, można uruchomić między wieloma sieciami SAN. Jeśli interesujące Cię utraty stojak serwerów, możesz uruchomić między wieloma stojakami. Martwisz utratą centrów danych, usługi powinien uruchamiać w wielu regionach platformy Azure lub centrów danych. 

Podczas pracy w tego rodzaju tryb łączone, jesteś nadal podlega procesowi niektóre rodzaje jednoczesne awarie, ale jednym i nawet utworzyć wiele błędów określonego typu (np: pojedynczej maszyny Wirtualnej lub sieci niepowodzenie link) automatycznie są obsługiwane (i dlatego nie jest już "Odzyskiwanie po awarii"). Usługa Service Fabric udostępnia wiele mechanizmów dotyczące rozszerzania klastra i obsługuje Przywracanie nie powiodło się, węzły i wróć do usługi. Usługa Service Fabric umożliwia również uruchomione wiele wystąpień usługi, aby uniknąć tego rodzaju nieplanowanych awarii z zwroty do rzeczywistej awarii.

Może to być powodów dlaczego uruchomiona wystarczająco duży, aby obejmować błędy wdrożenia nie jest możliwe. Na przykład może zająć więcej zasobów sprzętowych, niż chcesz zapłacić za względem ryzyko wystąpienia awarii. Podczas pracy z aplikacji rozproszonych, może to być, że przeskoków dodatkowej komunikacji lub replikacji w stanie koszty w odległości geograficznej powoduje, że niedopuszczalne opóźnienia. Gdy ta linia jest rysowana różni się dla każdej aplikacji. Błędy oprogramowania w szczególności ten błąd może być usługi, który chcesz skalować. W tym przypadku większej liczby kopii nie uniemożliwiają po awarii, ponieważ warunek błędu są skorelowane we wszystkich wystąpieniach.

### <a name="operational-faults"></a>Błędy operacyjne
Nawet wtedy, gdy usługi są łączone na całym świecie przy użyciu wielu nadmiarowość, nadal możesz skorzystać katastrofalne zdarzenia. Na przykład, jeśli ktoś przypadkowo ponownie konfiguruje nazwę dns dla usługi lub usuwa je od razu wykupić. Na przykład załóżmy, że trzeba było stanowej usługi Service Fabric i przypadkowo usunięto tej usługi. Jeżeli nie istnieje inne środki zaradcze, usługi i cały stan miał jest stała się teraz. Tego rodzaju operacyjne awarii ("Niestety") wymaga różne czynniki ograniczające i kroki odzyskiwania niż regularne nieplanowanych awarii. 

Najlepszym sposobem, aby uniknąć tego rodzaju błędy operacyjne mają
1. ograniczenia operacyjne dostępu do środowiska
2. niebezpieczne operacje ściśle inspekcji
3. nałożyć automatyzacji, ręcznie lub poza pasmem, zmiany i zweryfikować zmiany przed rzeczywistym środowisku przed ich realizacja
4. Upewnij się, że operacje destrukcyjne "elastyczne". Operacje nietrwałego nie zaczynają obowiązywać natychmiast, lub może być cofnięte w niektórych przedziale czasu

Usługa Service Fabric udostępnia kilka mechanizmów zapobiec błędom operacyjne, takie jak dostarczanie [opartej na rolach](service-fabric-cluster-security-roles.md) dostęp do kontroli dla operacji klastra. Jednak większość te błędy operacyjne wymaga wysiłki w organizacji oraz innych systemów. Usługa Service Fabric zapewnić pewien mechanizm dla pozostałych błędy operacyjne, głównie do tworzenia kopii zapasowej i przywracania dla usług stanowych.

## <a name="managing-failures"></a>Zarządzanie błędów
Celem usługi Service Fabric jest prawie zawsze automatyczne zarządzanie błędów. Jednak w celu obsługi niektórych rodzajów błędów, usługi muszą mieć dodatkowego kodu. Inne rodzaje błędów powinna _nie_ automatycznie rozwiązany z powodów ciągłości bezpieczeństwa i biznesowych. 

### <a name="handling-single-failures"></a>Obsługa błędów jednym
Pojedynczych maszyn może się nie powieść z wielu różnych powodów. Niektóre z nich są przyczyny sprzętu, takich jak zasilacze i sieć awarie sprzętowe. Inne błędy są w oprogramowaniu. Obejmują one błędy rzeczywiste systemu operacyjnego i samą usługę. Usługa Service Fabric automatycznie wykrywa te rodzaje błędów, łącznie z przypadkami, w którym komputer staje się odizolowane od innych maszyn z powodu problemów z siecią.

Niezależnie od używanego typu usługi uruchomione pojedyncze wystąpienie wyniki w przerwy w działaniu usługi w przypadku tego pojedynczej kopii kodu nie powiedzie się z jakiegokolwiek powodu. 

Aby umożliwić obsługę dowolnego pojedynczego uszkodzenia, najprostszym rzeczą, jaką można zrobić to upewnij się, czy usług na więcej niż jeden węzeł są domyślnie uruchamiane. W przypadku usług bezstanowych można to osiągnąć przez `InstanceCount` większą niż 1. W przypadku usług stanowych minimalna zawsze zalecany jest `TargetReplicaSetSize` i `MinReplicaSetSize` co najmniej 3. Uruchamianie większej liczby kopii kodu usługi gwarantuje, że usługa może obsługiwać automatycznie dowolnego pojedynczego uszkodzenia. 

### <a name="handling-coordinated-failures"></a>Koordynowane obsługi błędów
Błędy skoordynowanego może nastąpić w klastra z powodu planowanych lub awarii niezaplanowane infrastruktury i zmiany lub zmiany oprogramowania planowane. Usługa Service Fabric modeli strefy infrastruktury, które środowisko skoordynowanego błędy jako domen błędów. Obszary, które zostaną objęci zmianami skoordynowanego oprogramowania są modelowane jako domeny uaktualnienia. Aby uzyskać więcej informacji o domenach błędów i uaktualnień [w tym dokumencie](service-fabric-cluster-resource-manager-cluster-description.md) opisującą Topologia klastra i definicji.

Domyślnie Usługa Service Fabric uwzględnia domenach błędów i uaktualnień, podczas planowania, gdzie należy uruchamiać usług. Domyślnie Usługa Service Fabric próbuje upewnij się, że usługi uruchomione w kilku domenach domenach błędów i uaktualnień, jeśli planowane lub nieplanowane zmiany warstw usług pozostają dostępne. 

Na przykład załóżmy, że błąd źródła zasilania spowoduje, że stojak maszyn w celu jednoczesnego niepowodzenia. Wiele kopii usługi uruchomione utratę wielu maszyn w błąd domen błędów jest przekształcany po prostu inny przykład pojedynczego uszkodzenia dla danej usługi. Jest to, dlaczego zarządzanie domenami błędów ma krytyczne znaczenie dla zapewnienia wysokiej dostępności usług. Podczas uruchamiania usługi Service Fabric na platformie Azure, domeny błędów odbywa się automatycznie. W innych środowiskach może nie być. Jeśli tworzysz klastrów w środowisku lokalnym, należy poprawnie plan układu domeny błędów i mapy.

Domeny uaktualnień są przydatne do modelowania obszary, gdzie oprogramowanie ma zostać uaktualniony, w tym samym czasie. W związku z tym domen uaktualnienia również często definiują granice, w którym oprogramowanie jest zajęta w dół podczas planowanych aktualizacji. Uaktualnianie usługi Service Fabric i usługi postępuj zgodnie z tego samego modelu. Aby uzyskać więcej informacji na stopniowego uaktualnienia domen uaktualnienia i model kondycji usługi Service Fabric, który pomaga zapobiegać niezamierzone zmiany wpływające na klaster i usługi Zobacz te dokumenty:

 - [Uaktualnianie aplikacji](service-fabric-application-upgrade.md)
 - [Samouczek dotyczący uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md)
 - [Model kondycji sieci szkieletowej usług](service-fabric-health-introduction.md)

Można wizualizować układu przy użyciu mapy klastra w klastrze [narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![Węzły są rozmieszczone w domenach błędów w narzędziu Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelowanie obszarów błędów i uaktualnień stopniowych, uruchamiając wiele wystąpień elementu kodu usługi, a stan, zasady umieszczania w celu zapewnienia usług uruchamiania różnych domenach błędów i uaktualnień i monitorowanie kondycji wbudowane są po prostu **niektóre** programu Funkcje, które Usługa Service Fabric udostępnia, aby zapobiec zwroty do awarii normalne problemy operacyjne i błędów. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Obsługa jednoczesne awarie sprzętowe lub programowe
Powyżej Rozmawialiśmy o jednym błędów. Jak widać, są łatwe w obsłudze dla usług stanowych i bezstanowych, po prostu, przechowując większej liczby kopii kod (i stanu), działające na różnych domenach błędów i uaktualnień. Wiele jednoczesnych awarii losowe może też być. Są to bardziej może doprowadzić do rzeczywistej awarii.


### <a name="random-failures-leading-to-service-failures"></a>Błędy losowe, co prowadzi do awarii usługi
Załóżmy, że usługa miał `InstanceCount` 5 i kilka węzłów uruchamianie tych wszystkich wystąpień nie powiodło się w tym samym czasie. Usługa Service Fabric odpowiada przez automatyczne tworzenie wystąpień zastąpienia w innych węzłach. Tworzenie zamiany, aż usługa jest powrót do ich liczba wystąpień żądany jest kontynuowane. Inny przykład: Załóżmy, że wystąpił bezstanowej usługi za pomocą `InstanceCount`-1, co oznacza działa we wszystkich węzłach prawidłowy w klastrze. Załóżmy, że niektóre z tych wystąpień są nie powiedzie się. W tym przypadku usługi Service Fabric powiadomienia, usługa nie jest w stanie żądaną i podejmie próbę utworzenia wystąpienia w węzłach, gdzie ich brakuje. 

Dla usług stanowych sytuacja zależy od tego, czy usługa zawiera utrwalone stanu, czy nie. Także od tego, jak wiele replik ma usługi i ile nie powiodła się. Określanie, czy w przypadku usługi stanowej wystąpił awarii i zarządzania nią następujące trzy etapy:

1. Określanie, jeśli nastąpiła utrata kworum lub nie
   - Utrata kworum jest dowolnym momencie większość replik stanowej usługi nie działają w tym samym czasie, w tym podstawowego.
2. Określanie, czy utraciła kworum jest stały, czy nie
   - W większości przypadków, błędy są przejściowy. Procesy zostaną ponownie uruchomione, węzły zostaną ponownie uruchomione maszyny wirtualne są dalszymi, poprawianie partycje sieci. Czasami jednak błędy są trwałe. 
     - Dla usług bez utrwalone stanu niepowodzenia kworum lub większej liczby replik wyniki _natychmiast_ utraciła kworum stałe. Gdy Usługa Service Fabric wykrywa utraciła kworum w stanowej usłudze nietrwałe, natychmiast przechodzi do kroku 3 przez zadeklarowanie () utraty danych. Kontynuowanie danych utraty sens, ponieważ usługa Service Fabric wie, że żaden punkt oczekujący replik możesz wrócić, ponieważ nawet wtedy, gdy zostały one odzyskane może być pusta.
     - Przypadku trwałych usług stanowych błąd kworum lub większej liczby replik spowoduje, że Usługa Service Fabric rozpocząć oczekiwanie na repliki, aby wrócić i przywróć kworum. Skutkuje to przerwa w działaniu usługi dla każdego _zapisuje_ dotyczy partycji (lub "zestawu replik") usługi. Jednak odczyty mogą nadal możliwe gwarancje spójności mniejsze. Wartość domyślna czasu, przez jaki usługa Service Fabric czeka kworum można przywrócić jest nieograniczony, ponieważ postępowanie jest (potencjalnych) zdarzenia utraty danych i niesie ze sobą innych zagrożeń. Zastąpienie domyślnej `QuorumLossWaitDuration` wartość jest możliwe, ale nie jest zalecane. Zamiast tego w tej chwili wszelkich wysiłków należy przywrócić dół replik. Wymaga to wprowadzenia węzły, które działają z kopii zapasowej i zapewnienie, że ich ponownie zainstalować dysków, w których przechowywane lokalne trwały stan. Utrata kworum jest spowodowany przez błąd procesu, usługi Service Fabric automatycznie próbuje odtworzyć procesy i ponownie uruchom replik w nich znajdują. W przypadku niepowodzenia usługi Service Fabric zgłasza błędy kondycji. Jeśli te mogą być rozwiązane następnie repliki są zazwyczaj dostarczane ponownie. Czasami jednak replik nie mogą zostać przywrócone. Na przykład dysków nie wszystkie powiodło się lub maszyny fizyczne zniszczone jakiś sposób. W takich przypadkach mamy zdarzenia utraty stałe kworum. Aby poinformować usługę Service Fabric, aby zakończyć oczekiwanie na dół repliki, aby wrócić, administrator klastra należy określić, które partycje, których dotyczy usług i wywołać `Repair-ServiceFabricPartition -PartitionId` lub `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` interfejsu API.  Ten interfejs API umożliwia określanie Identyfikatora partycji można przenieść poza QuorumLoss oraz potencjalnych dataloss.

   > [!NOTE]
   > Jest _nigdy nie_ bezpiecznie używać tego interfejsu API w innych niż w ukierunkowany sposób na określone partycje. 
   >

3. Określenie, czy nastąpiła utrata danych rzeczywistych i przywracanie z kopii zapasowej
   - Kiedy Usługa Service Fabric wywołuje `OnDataLossAsync` metody, zawsze jest z powodu _podejrzenie_ utraty danych. Usługa Service Fabric zapewnia, że to wywołanie jest dostarczane do _najlepsze_ pozostałe repliki. Jest to, niezależnie od repliki podejścia biznesowego uczyniło większość postępu. Przyczyna zawsze mówimy _podejrzenie_ utrata danych jest, że możliwe jest, pozostałe repliki faktycznie zawiera wszystkich stanów w tej samej, tak jak podstawowy, gdy spadek. Jednak bez tego stanu do porównania, aby nie ma dobrej możliwości dla usługi Service Fabric lub operatorów się upewnić. W tym momencie usługi Service Fabric również wie, że pozostałe repliki nie zostaną wprowadzone ponownie. To było decyzji wprowadzonych podczas oczekiwania na utratę kworum rozwiąże się samoistnie zatrzymania. Najlepszy plan działania usługi jest zwykle zablokować i poczekaj na określonych interwencji administratora. Dlatego działanie Typowa implementacja metody `OnDataLossAsync` metoda zrobić?
   - Po pierwsze, dziennika `OnDataLossAsync` została wyzwolona i szybko włączyć wszystkie niezbędne alerty administracyjne.
   - Zazwyczaj na tym etapie, aby wstrzymać, a następnie poczekaj, aż dalszych decyzji i działania ręczne, które należy podjąć. Jest to spowodowane nawet, jeśli są dostępne kopie zapasowe muszą może być przygotowane. Na przykład jeśli dwa różne usługi koordynuje informacje, kopie zapasowe może być konieczne można zmodyfikować w celu zapewnienia po przywróceniu się informacji najważniejsze tych dwóch usług, informacje o spójne. 
   - Często jest również niektórych danych telemetrycznych ani spalin z usługi. Te metadane mogą znajdować się w innych usługach lub w dziennikach. Te informacje można potrzebne do określenia, czy wystąpiły wszelkie wywołania odbierane i przetwarzane na podstawowy, który nie był obecny w kopii zapasowej lub replikowane do tej konkretnej repliki. Te muszą być powtórzone lub dodawane do kopii zapasowej przed Przywracanie jest możliwe.  
   - Porównania stan pozostałe repliki znajdującej się w żadnej kopii zapasowej, które są dostępne. Jeśli przy użyciu usługi Service Fabric elementów reliable collections, istnieją narzędzia i procesy dostępne, aby to zrobić, opisane w [w tym artykule](service-fabric-reliable-services-backup-restore.md). Celem jest, aby wyświetlić stan w obrębie repliki jest wystarczająca, czy też jakie kopii zapasowej może brakować.
   - Po zakończeniu porównywania i przywracanie zostało ukończone, kod usługi w razie potrzeby powinna zwrócić wartość true, jeśli wprowadzono zmiany stanu. Jeżeli replika była najlepiej dostępna kopia stanu i wprowadzone żadne zmiany, zostanie zwrócona wartość false. Wartość TRUE wskazuje, że wszelkie _innych_ pozostałe repliki mogą być niezgodne z niej. Będzie można usunięty i ponownie skompilowany od tej repliki. Wartość false wskazuje, że nie wprowadzono żadnych zmian stanu, pozostałe repliki umożliwia zachowanie Agencji. 

Kluczowe znaczenie ma autorzy usługi rozwiązaniem potencjalnych scenariuszy utraty i niepowodzeń danych, zanim usługi nigdy nie są wdrażane w środowisku produkcyjnym. Aby chronić przed możliwością utraty danych, jest ważne, aby okresowo [Utwórz kopię zapasową stanu](service-fabric-reliable-services-backup-restore.md) dowolnych usług stanowych w magazynie geograficznie nadmiarowym. Upewnij się że użytkownik może go przywrócić. Ponieważ kopie zapasowe wielu różnych usług są pobierane w różnym czasie, musisz upewnij się, że po przywróceniu usługi spójny widok siebie nawzajem. Rozważmy na przykład sytuacji, gdzie jedna usługa generuje numer i zapisuje go, a następnie wysyła je do innej usługi, która również są przechowywane. Po przywróceniu może się okazać, że usługa drugi ma numer, ale ma pierwszy, ponieważ jest on kopii zapasowej nie zawiera tej operacji.

Jeśli możesz dowiedzieć się, że pozostałe repliki są niewystarczające do kontynuowania pracy w przypadku utraty danych, a nie można odtworzyć stan usługi z telemetrii lub spalin, częstotliwość tworzenia kopii zapasowych określa swoje najważniejsze cel punktu odzyskiwania to możliwe (RPO). Usługa Service Fabric udostępnia wiele narzędzi do testowania różnych scenariuszy awarii, w tym trwałe kworum i utraty danych, wymagające przywracania z kopii zapasowej. Te scenariusze są dołączane jako część narzędzi testowania usługi Service Fabric zarządza usługa analizy błędów. Więcej informacji na temat tych narzędzi i wzorców jest dostępna [tutaj](service-fabric-testability-overview.md). 

> [!NOTE]
> Usługi systemowe również mogą występować utraciła kworum bez wpływu na są specyficzne dla danej usługi. Na przykład utraciła kworum w usłudze nazewnictwa ma wpływ na rozpoznawania nazw, natomiast utrata kworum w usłudze Menedżer trybu failover blokuje tworzenia nowych usług i pracy w trybie Failover. Gdy usługi systemowe Service Fabric postępuj zgodnie z tego samego wzorca co w przypadku usług do zarządzania stanem, nie jest zalecane, należy podjąć próbę przenieść je poza utraciła kworum oraz ryzyko utraty danych. Zalecane jest zamiast tego [poszukuje](service-fabric-support.md) ustalenie rozwiązania, który jest przeznaczony do konkretnej sytuacji.  Zazwyczaj jest po prostu odczekać, aż zwracają dół replik.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Dostępność klastra usługi Service Fabric
Ogólnie rzecz biorąc sam klaster usługi Service Fabric to bardzo rozproszone środowisko z żadnego pojedynczego punktu awarii. Błąd dowolnego pojedynczego węzła nie spowoduje dostępności lub niezawodnością dla klastra, przede wszystkim, ponieważ usługi systemowe Service Fabric, wykonaj te same wytyczne podany wcześniej: one domyślne, a te systemu są zawsze uruchamiane z co najmniej trzema replikami usługi, które są bezstanowe uruchamiania we wszystkich węzłach. W pełni rozpowszechnianych niższych warstwach usługi Service Fabric sieci i Niepowodzenie wykrywania. Większość usług systemu może zostać ponownie skompilowany od metadanych w klastrze lub wiesz, jak ponowną synchronizację ich stan w innych miejscach. Dostępność klastra można złamane w sytuacji, gdy usługi systemowe zagłębieniem kworum utraty sytuacjach takich jak opisane powyżej. W takich przypadkach nie można wykonywania pewnych operacji, takie jak uruchomienie uaktualnienia lub wdrażaniem nowych usług w klastrze, ale sam klaster jest nadal uruchomiony. Usługi na już uruchomione, będą nadal działać w tych warunkach, o ile nie wymagają one operacje zapisu usługi systemowe, aby kontynuować działanie. Na przykład jeśli Menedżer trybu Failover jest utraciła kworum wszystkie usługi będą nadal działać, ale wszystkie usługi, które nie spełniają nie będzie można automatycznie ponownie uruchomiony, ponieważ wymaga to udziału Menedżer trybu Failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Awarii centrum danych lub regionu platformy Azure
W rzadkich przypadkach centrum danych fizycznych może stać się tymczasowo niedostępne z powodu utraty łączności sieciowej lub zasilania. W takich przypadkach Twoje klastrów usługi Service Fabric i usługi w danym centrum danych lub regionu platformy Azure będą niedostępne. Jednak _Twoje dane są zachowywane_. W przypadku klastrów działających na platformie Azure, można wyświetlać aktualizacje na awarie na [stronie stanu platformy Azure][azure-status-dashboard]. W wypadku bardzo mało prawdopodobne, że centrum danych fizycznych jest częściowo lub całkowicie zniszczone, wszystkie klastry usługi Service Fabric hostowany ma lub usług w nich mogą zostać utracone. Obejmuje to stan nie kopii zapasowej poza tym centrum danych lub regionu.

Ma dwa różne strategie pozostałych błąd stałe lub stałą w jednym centrum danych lub regionu. 

1. Uruchom osobne klastry usługi Service Fabric w wielu regionach w takich i korzystanie z mechanizmu w trybie failover i powrotu po awarii między tymi środowiskami. Tego rodzaju wielu klastrów active-active lub aktywny / pasywny model wymaga dodatkowego kodu zarządzanie i operacje. To wymaga również, że koordynacji tworzenia kopii zapasowych z usług w jednym centrum danych lub regionu tak, że są one dostępne w innych centrach danych lub regionach, gdy dla jednego zakończy się niepowodzeniem. 
2. Uruchom jeden klaster usługi Service Fabric, obejmującej wielu centrów danych lub regionach. Minimalnej obsługiwanej konfiguracji dla tego jest trzech centrów danych lub regionach. Zalecana liczba regionów lub centrów danych wynosi pięć. Wymaga to bardziej złożona Topologia klastra. Zaletą tego modelu jest jednak, czy niepowodzenie jednego centrum danych lub regionu jest konwertowana po awarii na normalne awarii. Te błędy mogą być obsługiwane przez mechanizmów, które działają w przypadku klastrów w jednym regionie. Domeny błędów, domen uaktualnienia i reguły umieszczania usługi Service Fabric upewnij się, że obciążenia są dystrybuowane tak, aby ich tolerują błędy normalny. Aby uzyskać więcej informacji na temat zasad, które mogą pomóc świadczyć usługi w tym typie klastra, zapoznaj się [zasady umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Losowe awarie, co prowadzi do awarii klastra
Usługa Service Fabric korzysta z koncepcji węzły obiektów początkowych. Są to węzły, które zachować dostępność klastra źródłowego. Te węzły pomóc upewnić się, że klaster będzie pozostawał się poprzez ustanowienie dzierżawy z innymi węzłami i służy jako tiebreakers podczas niektórych rodzajów błędów sieci. Jeśli błędy losowe usunięty większość węzły obiektów początkowych w klastrze, a ich on przełączony z powrotem, pierścień Federacji usługi klastra zwija jako utraty kworum węzła inicjatora i klastra kończy się niepowodzeniem. Na platformie Azure dostawcy zasobów usługi Service Fabric zarządza konfiguracje klastra usługi Service Fabric i domyślnie dystrybuuje węzły obiektów początkowych w różnych domenach błędów i uaktualnień domenach typu węzła podstawowego; Jeśli podstawowy element nodetype, jest oznaczana niezawodności na poziomie Silver lub Gold, po usunięciu węzła inicjatora, skalowanie w swojej podstawowego elementu nodetype lub ręczne usunięcie węzła inicjatora klastra spróbuje podwyższyć poziom innego węzła bez inicjatora z podstawowego elementu nodetype dostępne pojemność i zakończy się niepowodzeniem, jeśli masz mniej dostępnej pojemności, niż poziom niezawodności klastra wymaga typu węzła podstawowego.

W autonomicznych klastrów usługi Service Fabric i platformie Azure "Podstawowy typ węzła" jest uruchamiana, ziarna. Podczas definiowania typu węzła podstawowego, Usługa Service Fabric będzie automatycznie zalet liczbę węzłów, tworząc maksymalnie 9 węzły obiektów początkowych i 7 replik wszystkich usług systemu. Jeśli zestaw losowo występować usterki zajmuje się większość tych repliki usługi systemu jednocześnie, usług systemu wprowadź utraciła kworum, możemy opisany powyżej. W przypadku utraty większość węzły obiektów początkowych w klastrze zostanie zamknięty wkrótce po.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się, jak symulowanie różnych błędów przy użyciu [framework testowalności](service-fabric-testability-overview.md)
- Przeczytaj inne zasoby odzyskiwania po awarii i wysoka dostępność. Firma Microsoft opublikowała dużej liczby wskazówek na następujące tematy. Podczas gdy niektóre z tych dokumentów odwołują się do określonych technik stosowanych w innych produktów, zawierają wiele ogólne najlepsze rozwiązania, które można zastosować w kontekście usługi Service Fabric oraz:
  - [Lista kontrolna dotycząca dostępności](../best-practices-availability-checklist.md)
  - [Wykonywanie próbnego odzyskiwania po awarii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Odzyskiwanie po awarii i wysoka dostępność dla aplikacji platformy Azure][dr-ha-guide]
- Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
