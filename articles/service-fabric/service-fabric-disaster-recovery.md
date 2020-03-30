---
title: Odzyskiwanie po awarii sieci szkieletowej usługi Azure
description: Usługa Azure Service Fabric oferuje możliwości radzenia sobie z awariami. W tym artykule opisano typy awarii, które mogą wystąpić i jak sobie z nimi radzić.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371651"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Odzyskiwanie po awarii w sieci szkieletowej usług Azure
Kluczową częścią dostarczania wysokiej dostępności jest zapewnienie, że usługi mogą przetrwać wszystkie rodzaje awarii. Jest to szczególnie ważne w przypadku awarii, które są nieplanowane i poza kontrolą. 

W tym artykule opisano niektóre typowe tryby awarii, które mogą być awarie, jeśli nie są poprawnie modelowane i zarządzane. Omówiono również środki zaradcze i działania do podjęcia, jeśli w każdym razie dojdzie do katastrofy. Celem jest ograniczenie lub wyeliminowanie ryzyka przestojów lub utraty danych w przypadku wystąpienia awarii, planowanych lub innych.

## <a name="avoiding-disaster"></a>Unikanie katastrofy
Głównym celem usługi Azure Service Fabric jest pomoc w modelowania środowiska i usług w taki sposób, że typowe typy awarii nie są awariami. 

Ogólnie rzecz biorąc istnieją dwa typy scenariuszy awarii/awarii:
- Usterki sprzętu i oprogramowania
- Usterki operacyjne

### <a name="hardware-and-software-faults"></a>Usterki sprzętu i oprogramowania
Usterki sprzętu i oprogramowania są nieprzewidywalne. Najprostszym sposobem na przetrwanie błędów jest uruchomienie większej liczby kopii usługi w granicach błędów sprzętu lub oprogramowania. 

Na przykład jeśli usługa jest uruchomiona tylko na jednym komputerze, awaria tego jednego komputera jest katastrofą dla tej usługi. Prostym sposobem uniknięcia tej awarii jest zapewnienie, że usługa jest uruchomiona na wielu komputerach. Testowanie jest również konieczne, aby upewnić się, że awaria jednej maszyny nie zakłóca uruchomionej usługi. Planowanie pojemności zapewnia, że wystąpienie zastępowania można utworzyć w innym miejscu i że zmniejszenie pojemności nie przeciąża pozostałych usług. 

Ten sam wzorzec działa niezależnie od tego, co próbujesz uniknąć awarii. Na przykład jeśli obawiasz się awarii sieci SAN, można uruchomić przez wiele sieci SAN. Jeśli obawiasz się utraty stojaka serwerów, napotkasz wiele stojaków. Jeśli obawiasz się utraty centrów danych, usługa powinna działać w wielu regionach platformy Azure, w wielu strefach dostępności platformy Azure lub we własnych centrach danych. 

Gdy usługa jest łączony z wieloma wystąpieniami fizycznymi (maszyny, stojaki, centra danych, regiony), nadal naliczasz pewne typy równoczesnych awarii. Ale pojedyncze, a nawet wiele awarii określonego typu (na przykład pojedyncza maszyna wirtualna lub łącze sieciowe nie działa) są automatycznie obsługiwane i dlatego nie są już "katastrofą". 

Sieć szkieletowa usług udostępnia mechanizmy rozszerzania klastra i obsługuje przynosząc węzły i usługi nie powiodło się z powrotem. Usługa Service Fabric umożliwia również uruchamianie wielu wystąpień usług, aby zapobiec nieplanowanym awariom przekształcania się w rzeczywiste awarie.

Może być powody, dla których uruchamianie wdrożenia wystarczająco duże, aby obejmować błędy nie jest możliwe. Na przykład może to zająć więcej zasobów sprzętowych niż jesteś gotów zapłacić za w stosunku do szansy awarii. W przypadku aplikacji rozproszonych dodatkowe przeskoki komunikacyjne lub koszty replikacji stanu na odległość geograficzną mogą powodować niedopuszczalne opóźnienia. W przypadku rysowania tej linii różni się dla każdej aplikacji. 

W przypadku błędów oprogramowania w szczególności błąd może znajdować się w usłudze, którą próbujesz skalować. W takim przypadku więcej kopii nie zapobiec awarii, ponieważ warunek awarii jest skorelowany we wszystkich wystąpieniach.

### <a name="operational-faults"></a>Usterki operacyjne
Nawet jeśli usługa jest łączony na całym świecie z wielu zwolnień, nadal może wystąpić katastrofalne zdarzenia. Na przykład ktoś może przypadkowo ponownie skonfigurować nazwę DNS usługi lub usunąć ją wprost. 

Na przykład załóżmy, że masz stanową usługę sieci szkieletowej usług i ktoś przypadkowo usunął tę usługę. Chyba że istnieje inne środki zaradcze, że usługa i cały stan, który miał już zniknął. Tego typu awarie operacyjne ("oops") wymagają różnych środków zaradczych i kroków odzyskiwania niż zwykłe nieplanowane awarie. 

Najlepszym sposobem uniknięcia tego typu usterek operacyjnych są:
- Ograniczanie dostępu operacyjnego do środowiska.
- Ściśle kontroluj niebezpieczne operacje.
- Narzuć automatyzację, zapobiegaj zmianom ręcznym lub poza pasmem i weryfikuj określone zmiany w środowisku przed ich uchwaleniem.
- Upewnij się, że destrukcyjne operacje są "miękkie". Operacje miękkie nie są skuteczne natychmiast lub można je cofnąć w określonym przedziale czasu.

Sieci szkieletowej usług udostępnia mechanizmy, aby zapobiec błędom operacyjnym, takich jak zapewnienie kontroli dostępu [na podstawie ról](service-fabric-cluster-security-roles.md) dla operacji klastra. Jednak większość z tych usterek operacyjnych wymaga wysiłków organizacyjnych i innych systemów. Usługa Fabric zapewnia mechanizmy przetrwania błędów operacyjnych, w szczególności [tworzenia kopii zapasowych i przywracania dla usług stanowych.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Zarządzanie awariami
Celem sieci szkieletowej usług jest automatyczne zarządzanie błędami. Ale do obsługi niektórych typów błędów, usługi muszą mieć dodatkowy kod. Inne rodzaje awarii _nie_ powinny być automatycznie rozwiązywane ze względów bezpieczeństwa i ciągłości działania. 

### <a name="handling-single-failures"></a>Obsługa pojedynczych awarii
Pojedyncze maszyny mogą zakończyć się niepowodzeniem z różnych powodów. Czasami jest to przyczyna sprzętu, takich jak zasilacze i awarie sprzętu sieciowego. Inne awarie są w oprogramowaniu. Należą do nich awarie systemu operacyjnego i samej usługi. Sieć szkieletowa usług automatycznie wykrywa tego typu awarie, w tym przypadki, w których maszyna zostaje odizolowana od innych komputerów z powodu problemów z siecią.

Niezależnie od typu usługi, uruchomienie pojedynczego wystąpienia powoduje przestoje dla tej usługi, jeśli ta pojedyncza kopia kodu nie powiedzie się z jakiegokolwiek powodu. 

Aby obsłużyć każdy pojedynczy błąd, najprostszą rzeczą, jaką można zrobić, to upewnić się, że usługi są domyślnie uruchamiane w więcej niż jednym węźle. W przypadku usług bezstanowych upewnij się, że `InstanceCount` jest większa niż 1. W przypadku usług stanowych minimalne `TargetReplicaSetSize` `MinReplicaSetSize` zalecenie jest to, że i są ustawione na 3. Uruchamianie większej liczby kopii kodu usługi zapewnia, że usługa może automatycznie obsługiwać pojedyncze awarie. 

### <a name="handling-coordinated-failures"></a>Obsługa skoordynowanych awarii
Skoordynowane awarie w klastrze mogą być spowodowane planowanymi lub nieplanowanymi awariami i zmianami w infrastrukturze lub planowanymi zmianami oprogramowania. Usługa Sieci szkieletowej modeluje strefy infrastruktury, w których występują skoordynowane awarie jako *domeny błędów.* Obszary, w których będą wprowadzane skoordynowane zmiany w oprogramowaniu, są modelowane jako *domeny uaktualnień.* Aby uzyskać więcej informacji na temat domen błędów, domen uaktualniania i topologii klastrów, zobacz [Opisywanie klastra sieci szkieletowej usług przy użyciu Menedżera zasobów klastra](service-fabric-cluster-resource-manager-cluster-description.md).

Domyślnie sieci szkieletowej usług uwzględnia domeny błędów i uaktualniania podczas planowania, gdzie usługi powinny działać. Domyślnie sieć szkieletowa usług próbuje upewnić się, że usługi działają w kilku domenach błędów i uaktualniania, dzięki czemu w przypadku planowanych lub nieplanowanych zmian usługi pozostaną dostępne. 

Załóżmy na przykład, że awaria źródła zasilania powoduje, że wszystkie maszyny w stelażu zawodzą jednocześnie. Po uruchomieniu wielu kopii usługi utrata wielu maszyn w awarii domeny błędów zamienia się w kolejny przykład pojedynczej awarii usługi. Dlatego zarządzanie domenami błędów i uaktualniania ma kluczowe znaczenie dla zapewnienia wysokiej dostępności usług. 

Gdy korzystasz z sieci szkieletowej usług na platformie Azure, domeny błędów i domeny uaktualnień są zarządzane automatycznie. W innych środowiskach mogą nie być. Jeśli budujesz własne klastry lokalnie, należy poprawnie mapować i planować układ domeny błędów.

Domeny uaktualnienia są przydatne w obszarach modelowania, w których oprogramowanie zostanie uaktualnione w tym samym czasie. Z tego powodu domeny uaktualnienia często definiują również granice, w których oprogramowanie jest łajdasane podczas planowanych uaktualnień. Uaktualnienia sieci szkieletowej usług i usług wykonaj ten sam model. Aby uzyskać więcej informacji na temat uaktualnień stopniowych, domen uaktualniania i modelu kondycji sieci szkieletowej usług, który pomaga zapobiegać niezamierzonym zmianom wpływającym na klaster i usługę, zobacz:

 - [Uaktualnienie aplikacji](service-fabric-application-upgrade.md)
 - [Samouczek uaktualniania aplikacji](service-fabric-application-upgrade-tutorial.md)
 - [Model kondycji sieci szkieletowej usługi](service-fabric-health-introduction.md)

Układ klastra można wizualizować za pomocą mapy klastra dostępnej w [Eksploratorze sieci szkieletowej usług:](service-fabric-visualizing-your-cluster.md)

<center>

![Węzły rozłożone na domeny błędów w Eksploratorze sieci szkieletowej usług][sfx-cluster-map]
</center>

> [!NOTE]
> Modelowanie obszarów awarii, uaktualnień stopniowych, uruchamianie wielu wystąpień kodu usługi i stanu, reguły umieszczania, aby upewnić się, że usługi działają w domenach błędów i uaktualnień oraz wbudowane monitorowanie kondycji to tylko *niektóre* z funkcji, które zapewnia sieci szkieletowej usług, aby zachować normalne problemy operacyjne i awarie przed przekształceniem się w awarie. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Obsługa jednoczesnych awarii sprzętu lub oprogramowania
Mówiliśmy o pojedynczych porażkach. Jak widać, są one łatwe w obsłudze dla usług bezstanowych i stanowych tylko przez zachowanie większej liczby kopii kodu (i stanu) działających w domenach błędów i uaktualnienia. 

Wiele jednoczesnych awarii losowych może również się zdarzyć. Są one bardziej prawdopodobne, aby doprowadzić do przestoju lub rzeczywistej awarii.


#### <a name="stateless-services"></a>Usługi bezstanowe
Liczba wystąpień dla usługi bezstanowej wskazuje żądaną liczbę wystąpień, które muszą być uruchomione. Gdy dowolne (lub wszystkie) wystąpienia nie powiodą się, usługa Service Fabric odpowiada, automatycznie tworząc wystąpienia zastępcze w innych węzłach. Sieć szkieletowa usług kontynuuje tworzenie zamienników, dopóki usługa nie powróci do żądanej liczby wystąpień.

Załóżmy na przykład, że `InstanceCount` usługa bezstanowa ma wartość -1. Ta wartość oznacza, że jedno wystąpienie powinno być uruchomione na każdym węźle w klastrze. Jeśli niektóre z tych wystąpień nie powiedzie się, sieć szkieletowa usług wykryje, że usługa nie jest w żądanym stanie i spróbuje utworzyć wystąpienia w węzłach, w których ich brakuje.

#### <a name="stateful-services"></a>Usługi stanowe
Istnieją dwa typy usług stanowych:
- Stanowy ze stanem utrzymywanym.
- Stanowy ze stanem nieutrzymywalnym. (Stan jest przechowywany w pamięci).

Odzyskiwanie po awarii usługi stanowej zależy od typu usługi stanowej, ile replik miała usługa i ile replik nie powiodło się.

W usłudze stanowej przychodzące dane są replikowane między replikami (podstawowymi i dowolnymi aktywnymi pomocniczymi). Jeśli większość replik odbiera dane, dane są uznawane za zatwierdzone *kworum.* (Dla pięciu replik trzy będą kworum.) Oznacza to, że w dowolnym momencie będzie co najmniej kworum replik z najnowszymi danymi. Jeśli repliki nie powiedzmy (powiedzmy dwa na pięć), możemy użyć wartości kworum, aby obliczyć, czy możemy odzyskać. (Ponieważ pozostałe trzy z pięciu replik są nadal w górę, jest gwarantowane, że co najmniej jedna replika będzie miała pełne dane.)

Gdy kworum replik nie powiedzie się, partycja jest zadeklarowana jako w stanie *utraty kworum.* Załóżmy, że partycja ma pięć replik, co oznacza, że co najmniej trzy są gwarantowane, aby mieć pełne dane. Jeśli kworum (trzy na pięć) replik nie powiedzie się, sieci szkieletowej usług nie można określić, czy pozostałe repliki (dwa na pięć) mają wystarczającą ilość danych, aby przywrócić partycję. W przypadkach, gdy usługa Service Fabric wykrywa utratę kworum, jej domyślnym zachowaniem jest zapobieganie dodatkowym zapisom na partycji, deklarowanie utraty kworum i oczekiwanie na przywrócenie kworum replik.

Określanie, czy wystąpiła awaria dla usługi stanowej, a następnie zarządzanie nią następuje po trzech etapach:

1. Określanie, czy nastąpiła utrata kworum, czy nie.
   
   Utrata kworum jest zadeklarowana, gdy większość replik usługi stanowej są w dół w tym samym czasie.
2. Określanie, czy utrata kworum jest trwała, czy nie.
   
   W większości przypadków awarie są przejściowe. Procesy są uruchamiane ponownie, węzły są ponownie uruchamiane, maszyny wirtualne są ponownie uruchamiane, a partycje sieciowe leczą. Czasami jednak awarie są trwałe. Czy błędy są trwałe, czy nie zależy od tego, czy usługa stanowa utrzymuje swój stan lub czy przechowuje go tylko w pamięci: 
   
   - W przypadku usług bez stanu utrwalonych awaria kworum lub więcej replik _powoduje natychmiast_ trwałą utratę kworum. Gdy usługa Service Fabric wykryje utratę kworum w stanowej usłudze nietrwałej, natychmiast przechodzi do kroku 3, deklarując (potencjalną) utratę danych. Przejście do utraty danych ma sens, ponieważ sieci szkieletowej usług wie, że nie ma sensu czekać na repliki wrócić. Nawet jeśli odzyskają, dane zostaną utracone z powodu nieutrwalonych charakter usługi.
   - W przypadku usług trwałych stanowych błąd kworum lub więcej replik powoduje, że sieci szkieletowej usług czekać na repliki wrócić i przywrócić kworum. Powoduje to awarię usługi dla wszystkich _zapisów_ do partycji, których dotyczy problem (lub "zestaw replik") usługi. Jednak odczyty mogą być nadal możliwe przy ograniczeniu gwarancji spójności. Domyślna ilość czasu oczekiwania sieci szkieletowej usług na przywrócenie kworum jest *nieskończona,* ponieważ postępowanie jest (potencjalnym) zdarzeniem utraty danych i niesie ze sobą inne ryzyko. Oznacza to, że sieci szkieletowej usług nie przejdzie do następnego kroku, chyba że administrator podejmie działania w celu zadeklarowania utraty danych.
3. Określanie, czy dane zostały utracone, i przywracanie z kopii zapasowych.

   Jeśli utrata kworum została zadeklarowana (automatycznie lub za pomocą akcji administracyjnej), sieci szkieletowej usług i usługi przejść do określenia, czy dane zostały faktycznie utracone. W tym momencie sieci szkieletowej usług wie również, że inne repliki nie wracają. Taka była decyzja podjęta, kiedy przestaliśmy czekać na utratę kworum, aby rozwiązać się sam. Najlepszym sposobem działania dla usługi jest zwykle zamrożenie i oczekiwanie na konkretną interwencję administracyjną.
   
   Gdy usługa Service `OnDataLossAsync` Fabric wywołuje metodę, zawsze z powodu _podejrzenia_ utraty danych. Sieci szkieletowej usług zapewnia, że to wywołanie jest dostarczany do _najlepszej_ repliki pozostałych. Jest to niezależnie od tego, która replika poczyniła największe postępy. 
   
   Powodem, dla którego zawsze mówimy, że _podejrzewana_ utrata danych jest to, że jest możliwe, że pozostała replika ma cały ten sam stan, co podstawowa, gdy kworum zostało utracone. Jednak bez tego stanu, aby porównać go do, nie ma dobrego sposobu dla sieci szkieletowej usług lub operatorów wiedzieć na pewno.     
   
   Więc co robi typowa `OnDataLossAsync` implementacja metody zrobić?
   1. Dzienniki implementacji, który `OnDataLossAsync` został wyzwolony i odpala wszelkie niezbędne alerty administracyjne.
   1. Zazwyczaj implementacja wstrzymuje się i czeka na dalsze decyzje i ręczne działania, które należy podjąć. Dzieje się tak, ponieważ nawet jeśli kopie zapasowe są dostępne, mogą wymagać przygotowania. 
   
      Na przykład jeśli dwie różne usługi koordynują informacje, te kopie zapasowe mogą wymagać zmodyfikowania, aby upewnić się, że po wykonaniu przywracania informacje, na których zależy tym dwóm usługom, są spójne. 
   1. Często istnieje kilka innych danych telemetrycznych lub wydechu z usługi. Te metadane mogą być zawarte w innych usługach lub w dziennikach. Te informacje mogą być używane w razie potrzeby, aby ustalić, czy były jakieś wywołania odebrane i przetworzone w podstawowym, które nie były obecne w kopii zapasowej lub replikowane do tej konkretnej repliki. Te wywołania mogą wymagać odtworzenia lub dodania do kopii zapasowej, zanim przywrócenie jest możliwe.  
   1. Implementacja porównuje stan pozostałej repliki z stanem zawartym w dostępnych kopiach zapasowych. Jeśli używasz kolekcji niezawodne sieci szkieletowej usług, istnieją [narzędzia i procesy](service-fabric-reliable-services-backup-restore.md) dostępne w tym celu. Celem jest, aby sprawdzić, czy stan w replice jest wystarczająca i zobaczyć, co kopia zapasowa może brakować.
   1. Po zakończeniu porównania i po zakończeniu przywracania (jeśli to konieczne), kod usługi powinien zwrócić **wartość true,** jeśli wprowadzono zmiany stanu. Jeśli replika ustaliła, że była najlepszą dostępną kopią stanu i nie wnieśła żadnych zmian, kod zwraca **false**. 
   
      Wartość **true** wskazuje, że wszystkie _inne_ pozostałe repliki mogą być teraz niezgodne z tym. Zostaną one usunięte i przebudowany z tej repliki. Wartość **false** wskazuje, że nie wprowadzono żadnych zmian stanu, więc inne repliki mogą zachować to, co mają. 

Jest niezwykle ważne, że autorzy usług praktyce potencjalnych scenariuszy utraty danych i awarii przed usługi są wdrażane w produkcji. Aby chronić przed możliwością utraty danych, należy okresowo [tworzyć kopii zapasowej stanu](service-fabric-reliable-services-backup-restore.md) dowolnej usługi stanowej do magazynu geograficznie nadmiarowego. 

Należy również upewnić się, że masz możliwość przywrócenia stanu. Ponieważ kopie zapasowe wielu różnych usług są podejmowane w różnym czasie, należy upewnić się, że po przywracaniu usługi mają spójny widok siebie nawzajem. 

Rozważmy na przykład sytuację, w której jedna usługa generuje liczbę i przechowuje ją, a następnie wysyła ją do innej usługi, która również ją przechowuje. Po przywróceniu może się okazać, że druga usługa ma numer, ale pierwsza nie, ponieważ jego kopia zapasowa nie zawiera tej operacji.

Jeśli okaże się, że pozostałe repliki są niewystarczające, aby kontynuować w scenariuszu utraty danych i nie można odtworzyć stan usługi z telemetrii lub wydechu, częstotliwość kopii zapasowych określa najlepszy możliwy cel punktu odzyskiwania (RPO). Sieci szkieletowej usług zawiera wiele narzędzi do testowania różnych scenariuszy awarii, w tym trwałe kworum i utraty danych, która wymaga przywrócenia z kopii zapasowej. Te scenariusze są uwzględniane jako część narzędzi testowalności w sieci szkieletowej usług, zarządzane przez usługę analizy błędów. Aby uzyskać więcej informacji na temat tych narzędzi i wzorców, zobacz [Wprowadzenie do usługi analizy błędów](service-fabric-testability-overview.md). 

> [!NOTE]
> Usługi systemowe mogą również ponieść straty kworum. Wpływ jest specyficzny dla danej usługi. Na przykład utrata kworum w usłudze nazewnictwa wpływa na rozpoznawanie nazw, podczas gdy utrata kworum w usłudze Menedżer trybu failover blokuje tworzenie nowych usług i tryb failover. 
> 
> Usługi systemu sieci szkieletowej usług wykonaj ten sam wzorzec co usługi zarządzania stanem, ale nie zaleca się, aby spróbować przenieść je z utraty kworum i do potencjalnej utraty danych. Zamiast tego zaleca się, aby [szukać pomocy technicznej,](service-fabric-support.md) aby znaleźć rozwiązanie, które jest ukierunkowane na sytuację. Zwykle lepiej jest po prostu poczekać, aż repliki w dół powróci.
>

#### <a name="troubleshooting-quorum-loss"></a>Rozwiązywanie problemów z utratą kworum

Repliki mogą być w dół sporadycznie z powodu błędu przejściowego. Poczekaj na jakiś czas, jak sieci szkieletowej usług próbuje je przywołać. Jeśli repliki były w dół przez więcej niż oczekiwany czas trwania, wykonaj następujące akcje rozwiązywania problemów:
- Repliki mogą ulec awarii. Sprawdź raporty kondycji na poziomie repliki i dzienniki aplikacji. Zbieraj zrzuty awaryjne i podejmij niezbędne działania, aby odzyskać.
- Proces repliki mógł przestać odpowiadać. Sprawdź dzienniki aplikacji, aby to sprawdzić. Zbieranie zrzutów procesów, a następnie zatrzymanie procesu nieodpowiada. Sieć szkieletowa usług utworzy proces zastępowania i spróbuje przywrócić replikę.
- Węzły obsługujące repliki mogą być w dół. Uruchom ponownie podstawową maszynę wirtualną, aby podnieść węzły.

Czasami odzyskanie replik może nie być możliwe. Na przykład dyski nie powiodły się lub komputery fizycznie nie reagują. W takich przypadkach sieci szkieletowej usług należy poinformować, aby nie czekać na odzyskiwanie repliki.

*Nie* należy używać tych metod, jeśli potencjalna utrata danych jest nie do przyjęcia, aby przenieść usługę do trybu online. W takim przypadku należy dołomić wszelkie wysiłki w kierunku odzyskania maszyn fizycznych.

Następujące akcje mogą spowodować utratę danych. Sprawdź, zanim za nimi zastosujesz.
   
> [!NOTE]
> _Nigdy nie_ jest bezpieczne, aby używać tych metod innych niż w sposób ukierunkowany względem określonych partycji. 
>

- Użyj `Repair-ServiceFabricPartition -PartitionId` interfejsu `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API lub. Ten interfejs API umożliwia określenie identyfikatora partycji, aby przenieść się z utraty kworum i do potencjalnej utraty danych.
- Jeśli klaster napotka częste błędy, które powodują, że usługi, aby przejść do stanu utraty kworum i potencjalnej _utraty danych jest dopuszczalne,_ określając odpowiednią wartość [QuorumLossWaitDuration](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) może pomóc usługi automatycznie odzyskać. Sieci szkieletowej usług `QuorumLossWaitDuration` będzie czekać na pod warunkiem wartość (domyślnie jest nieskończona) przed wykonaniem odzyskiwania. *Nie* zaleca się tej metody, ponieważ może to spowodować nieoczekiwane straty danych.

## <a name="availability-of-the-service-fabric-cluster"></a>Dostępność klastra sieci szkieletowej usług
Ogólnie rzecz biorąc klaster sieci szkieletowej usług jest wysoce rozproszonym środowiskiem bez pojedynczych punktów awarii. Awaria jednego węzła nie spowoduje problemów z dostępnością lub niezawodnością klastra, głównie dlatego, że usługi systemu sieci szkieletowej usług są zgodne z tymi samymi wytycznymi podanymi wcześniej. Oznacza to, że zawsze są one domyślnie uruchamiane z co najmniej trzema replikami, a usługi systemowe, które są bezstanowe, są uruchamiane we wszystkich węzłach. 

Podstawowe warstwy sieci szkieletowej usług i wykrywania awarii są w pełni rozproszone. Większość usług systemowych można przebudować z metadanych w klastrze lub wiedzieć, jak ponownie zsynchronizować ich stan z innych miejsc. Dostępność klastra może zostać naruszona, jeśli usługi systemowe dostaną się do sytuacji utraty kworum, takich jak opisane wcześniej. W takich przypadkach może nie być w stanie wykonać niektórych operacji w klastrze (takich jak rozpoczęcie uaktualnienia lub wdrażania nowych usług), ale sam klaster jest nadal w górę. 

Usługi w uruchomionym klastrze będą działać w tych warunkach, chyba że wymagają zapisów w usługach systemowych, aby kontynuować działanie. Na przykład jeśli Menedżer trybu failover jest w utracie kworum, wszystkie usługi będą nadal działać. Ale wszystkie usługi, które nie powiodą się, nie będzie można automatycznie uruchomić ponownie, ponieważ wymaga to zaangażowania Menedżera trybu failover. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Awarie centrum danych lub regionu platformy Azure
W rzadkich przypadkach fizyczne centrum danych może stać się tymczasowo niedostępne z powodu utraty zasilania lub łączności sieciowej. W takich przypadkach klastry i usługi sieci szkieletowej usług w tym centrum danych lub regionie platformy Azure będą niedostępne. Jednak _dane są zachowywane_. 

W przypadku klastrów uruchomionych na platformie Azure można wyświetlać aktualizacje dotyczące awarii na [stronie stanu platformy Azure][azure-status-dashboard]. W bardzo mało prawdopodobnym przypadku, gdy fizyczne centrum danych jest częściowo lub całkowicie zniszczone, wszystkie klastry sieci szkieletowej usług hostowane tam lub usługi znajdujące się w nich mogą zostać utracone. Ta strata obejmuje każdy stan, którego kopia zapasowa nie została utworzona poza tym centrum danych lub regionu.

Istnieją dwie różne strategie przetrwania trwałej lub trwałej awarii pojedynczego centrum danych lub regionu: 

- Uruchom oddzielne klastry sieci szkieletowej usług w wielu takich regionach i użyj mechanizmu pracy awaryjnej i powrotu po awarii między tymi środowiskami. Ten rodzaj modelu aktywnego/aktywnego lub aktywnego/pasywnego wielu klastrów wymaga dodatkowego kodu zarządzania i operacji. Ten model wymaga również koordynacji kopii zapasowych z usług w jednym centrum danych lub regionie, dzięki czemu są one dostępne w innych centrach danych lub regionach, gdy jeden nie powiedzie się. 
- Uruchom pojedynczy klaster sieci szkieletowej usług, który obejmuje wiele centrów danych lub regionów. Minimalna obsługiwana konfiguracja dla tej strategii to trzy centra danych lub regiony. Zalecana liczba regionów lub centrów danych wynosi pięć. 
  
  Ten model wymaga bardziej złożonej topologii klastra. Jednak zaletą jest to, że awaria jednego centrum danych lub regionu jest konwertowana z awarii na normalny błąd. Te błędy mogą być obsługiwane przez mechanizmy, które działają dla klastrów w jednym regionie. Domeny błędów, domeny uaktualnienia i reguły umieszczania sieci szkieletowej usług zapewniają, że obciążenia są dystrybuowane, dzięki czemu tolerują normalne awarie. 
  
  Aby uzyskać więcej informacji na temat zasad, które mogą pomóc w obsłudze usług w tego typu klastrze, zobacz [Zasady umieszczania usług sieci szkieletowej usług](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-that-lead-to-cluster-failures"></a>Losowe błędy, które prowadzą do awarii klastra
Usługa Fabric ma pojęcie *węzłów źródłowych*. Są to węzły, które utrzymują dostępność klastra źródłowego. 

Węzły źródłowe pomagają zapewnić, że klaster pozostaje w górę, ustanawiając dzierżawy z innymi węzłami i służąc jako tiebreakers podczas niektórych rodzajów awarii. Jeśli losowe błędy usuwają większość węzłów źródłowych w klastrze i nie są one szybko przywracane, klaster zostanie automatycznie zamknięty. Klaster kończy się niepowodzeniem. 

Na platformie Azure dostawca zasobów sieci szkieletowej usług zarządza konfiguracjami klastra sieci szkieletowej usług. Domyślnie Dostawca zasobów dystrybuuje węzły źródłowe między domenami błędów i uaktualniania dla *typu węzła podstawowego*. Jeśli typ węzła podstawowego jest oznaczony jako Trwałość Silver lub Gold, po usunięciu węzła źródłowego (przez skalowanie w typie węzła podstawowego lub ręcznie usunięcie go), klaster spróbuje podwyższyć kursor innego węzła innego nieujawniającego z dostępnej pojemności węzła podstawowego. Ta próba zakończy się niepowodzeniem, jeśli masz mniej dostępnej pojemności niż wymaga tego poziom niezawodności klastra dla typu węzła podstawowego.

W obu autonomicznych klastrów sieci szkieletowej usług i platformy Azure typ węzła podstawowego jest tym, który uruchamia nasiona. Podczas definiowania typu węzła podstawowego sieci szkieletowej usług automatycznie będzie korzystać z liczby węzłów dostarczonych przez utworzenie maksymalnie dziewięciu węzłów źródłowych i siedem replik każdej usługi systemowej. Jeśli zestaw losowych błędów wyjmuje większość tych replik jednocześnie, usługi systemowe wejdą w utratę kworum. Jeśli większość węzłów źródłowych zostaną utracone, klaster zostanie zamknięty wkrótce po.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak symulować różne błędy przy użyciu [struktury testowalności.](service-fabric-testability-overview.md)
- Przeczytaj inne zasoby odzyskiwania po awarii i wysokiej dostępności. Firma Microsoft opublikowała wiele wskazówek dotyczących tych tematów. Chociaż niektóre z tych zasobów odnoszą się do określonych technik do użycia w innych produktach, zawierają one wiele ogólnych najlepszych rozwiązań, które można zastosować w kontekście sieci szkieletowej usług:
  - [Lista kontrolna dotycząca dostępności](/azure/architecture/checklist/resiliency-per-service)
  - [Wykonywanie ćwiczenia odzyskiwania po awarii](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Odzyskiwanie aplikacji platformy Azure po awarii i ich wysoka dostępność][dr-ha-guide]
- Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
