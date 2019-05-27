---
title: Często zadawane pytania dotyczące usługi Microsoft Azure Service Fabric | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Service Fabric i odpowiedzi
services: service-fabric
documentationcenter: .net
author: chackdan
manager: chackdan
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: 0bd8a7d403ad1fe0f7abb15356cc9c90ed6b3f02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153830"
---
# <a name="commonly-asked-service-fabric-questions"></a>Często zadawane pytania na temat usługi Service Fabric

Istnieje wiele często zadawane pytania o możliwościach usługi Service Fabric i jak powinna być używana. W tym dokumencie opisano wiele z tych często zadawane pytania i odpowiedzi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Konfiguracja klastra i zarządzanie

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Jak czy mogę wycofać certyfikatu klastra usługi Service Fabric?

Wycofywanie jakiegokolwiek uaktualnienia do aplikacji wymaga wykrywanie awarii kondycji przed kworum klastra usługi Service Fabric, Twoje zatwierdzenie zmiany; zatwierdzone zmiany tylko może być przeniesiona do przodu. Inżynierem eskalacji za pośrednictwem usług obsługi klienta, może być wymagane w celu odzyskania klastra, jeśli wprowadzono niemonitorowane istotną zmianę certyfikatu.  [Uaktualnianie aplikacji usługi Service Fabric](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) stosuje [parametry uaktualniania aplikacji](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master), i oferuje zero promise uaktualnienia przestojów.  Następujących zalecanych aplikacji monitorowanych tryb uaktualniania, automatyczne postęp za pomocą domeny aktualizacji opiera się na kontrole kondycji, przekazując, stopniowe wstecz automatycznie, jeśli aktualizacja usługi domyślnej nie powiedzie się.
 
Jeśli klaster nadal polega na wykorzystaniu klasycznego właściwości odcisk palca certyfikatu w szablonie usługi Resource Manager, zaleca się możesz [klastra zmiany z odcisk palca certyfikatu na nazwę pospolitą](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), aby korzystać z nowoczesnych wpisów tajnych Funkcje zarządzania.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Można utworzyć klastra obejmującego wiele regionów systemu Azure lub własnego centrum danych?

Tak. 

Podstawowe technologie klastra usługi Service Fabric może służyć do łączenia maszyn, w dowolnym miejscu na świecie, tak długo, jak długo mają łączność sieciową między sobą. Jednak kompilowanie i uruchamianie taki klaster może być skomplikowane.

Jeśli interesuje Cię w tym scenariuszu, firma Microsoft zachęca do przekazywania w kontakcie albo za pośrednictwem [liście problemów GitHub sieci szkieletowej usługi](https://github.com/azure/service-fabric-issues) lub za pośrednictwem przedstawicielem pomocy technicznej w celu uzyskania dodatkowych wskazówek. Zespół usługi Service Fabric działa na zapewnienie dodatkowego wyjaśnienia, wskazówki i zalecenia dotyczące tego scenariusza. 

Oto kilka rzeczy, które warto przemyśleć: 

1. Zasób klastra usługi Service Fabric na platformie Azure jest regionalnych obecnie zestawy skalowania maszyn wirtualnych, czy klaster działa w oparciu. Oznacza to, że awarii regionalnej możesz utracić możliwość zarządzania klastrem za pomocą usługi Azure Resource Manager lub witryny Azure portal. Można to zrobić, mimo że w klastrze jest uruchomiona i będzie można bezpośrednio korzystać z niego. Ponadto Azure już dziś nie oferuje możliwość jednej sieci wirtualnej, który jest używany w wielu regionach. Oznacza to, że klaster obejmujący wiele regionów na platformie Azure wymaga [publicznych adresów IP dla każdej maszyny Wirtualnej w zestawach skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) lub [bram Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Te opcje sieci mają wpływ różne kosztów, wydajności i niektóre stopień projektowania aplikacji, tak więc analizy i planowania jest wymagana przed stałego takie środowisko.
2. Konserwacji, zarządzanie i monitorowanie tych komputerów może stać się skomplikowane, zwłaszcza w przypadku, gdy łączone na _typy_ środowisk, takich między różnych dostawców w chmurze lub między zasobami lokalnymi a platformą Azure. Należy uważać, aby upewnić się, że uaktualnień, monitorowania, zarządzania i Diagnostyka są zrozumiałe dla klastra i aplikacji przed uruchomieniem obciążeń produkcyjnych w środowisku usługi. Jeśli masz już doświadczenie rozwiązania tych problemów, na platformie Azure lub w ramach własnymi centrami danych, następnie jest prawdopodobne, że te tego samego rozwiązania może być stosowana podczas kompilowania lub uruchomiony klaster usługi Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Czy węzłów usługi Service Fabric automatycznie otrzymywać aktualizacje systemu operacyjnego?

Możesz użyć [maszyn wirtualnych skalowania Ustaw automatycznej aktualizacji systemu operacyjnego obrazu](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) ogólnie dostępną funkcję już dziś.

W przypadku klastrów, które nie są uruchamiane na platformie Azure, mamy [podane aplikacji](service-fabric-patch-orchestration-application.md) do poprawiania systemów operacyjnych znajdują się pod węzłami usługi Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Czy można używać zestawów skalowania dużych maszyn wirtualnych, w moim klastrze SF? 

**Krótka odpowiedź** — nie. 

**Czas odpowiedzi** — mimo że zestawy skalowania dużych maszyn wirtualnych pozwalają na skalowanie maszynę wirtualną do 1000 wystąpień maszyn wirtualnych zestawu skalowania, robi to przy użyciu grup umieszczania (PGA). Odporność domenach i uaktualnienia domeny są spójne tylko w ramach umieszczania grupy usługi Service fabric używa domenami błędów i domenach uaktualniania podjąć decyzje dotyczące umieszczenia wystąpień replik/usługi usługi. Ponieważ domenami błędów i domenach uaktualniania są porównywalne tylko w ramach grupy umieszczania, SF nie można go używać. Na przykład, jeśli maszyna VM1 w PG1 topologii FD = 0, a VM9 w grupy PG2 ma topologii FD = 4, nie oznacza to, że maszyna VM1 i VM2 znajdują się na dwóch różnych stojakach sprzętu, dlatego SF nie można użyć wartości FD w tym przypadku podjąć decyzje dotyczące umieszczenia.

Obecnie nie istnieją inne problemy z dużych zestawów skalowania, takich jak brak 4 poziomu obciążenia równoważenia pomocy technicznej. Można znaleźć [szczegółowe informacje na temat duże zestawy skalowania](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Co to jest minimalny rozmiar klastra usługi Service Fabric? Dlaczego nie może być mniejsze?

Minimalny rozmiar obsługiwany dla obciążeń produkcyjnych klastra usługi Service Fabric jest pięć węzłów. W przypadku scenariuszy deweloperskich obsługujemy jeden węzeł (zoptymalizowany pod kątem obsługi szybkie prace projektowe w programie Visual Studio) i klastry pięć węzłów.

Firma Microsoft wymaga produkcyjnego klastra dla co najmniej 5 węzłów z trzech następujących powodów:
1. Nawet wtedy, gdy nie użytkownika usługi są uruchomione, klaster usługi Service Fabric uruchamia zestaw usług stanowych systemu, w tym usługi nazewnictwa i Menedżer trybu failover. Te usługi systemu są niezbędne, aby klaster mógł działać.
2. Firma Microsoft zawsze umieszczaj jednej repliki usługi na węzeł, dlatego rozmiar klastra jest górny limit liczby replik przez usługę (faktycznie partycji).
3. Ponieważ uaktualnienie klastra zostanie wyświetlone co najmniej jeden węzeł w dół, chcemy mieć bufor o co najmniej jeden węzeł, w związku z tym, chcemy, aby mieć co najmniej dwa węzły klastra produkcyjnego *dodatkowo* do absolutnego minimum. Absolutnego minimum jest rozmiarem kworum dana usługa system, co zostało opisane poniżej.  

Chcemy, aby klaster ma być dostępny w przypadku równoczesnej awarii dwóch węzłów. W przypadku klastra usługi Service Fabric mają być dostępne usługi systemowe muszą być dostępne. Usługi stanowe systemu, takie jak nazewnictwa i usługi Menedżer trybu failover, śledzenie, jakie usługi zostały wdrożone w klastrze i gdzie są obecnie obsługiwane, są zależne od wysoki poziom spójności. Silnej spójności, z kolei jest zależny od możliwość uzyskania *kworum* wszelkich aktualizacji danego stanu, które z tych usług, gdzie kworum reprezentuje strict większość repliki (N/2 + 1) dla danej usługi. Dlatego jeśli chcemy być odporny na jednoczesne utratę dwóch węzłów (tym samym jednoczesnych utraty dwie repliki usługi system), firma Microsoft musi mieć wartość ClusterSize - QuorumSize > = 2, co zmusza minimalny rozmiar do pięciu osób. Aby zobaczyć, które należy wziąć pod uwagę klaster ma węzły N wiąże się z replik N usługa systemowa — jeden w każdym węźle. Rozmiar kworum usługi system jest (N/2 + 1). Powyższe nierówności wygląda N - (N/2 + 1) > = 2. Istnieją dwa przypadki, które należy rozważyć: gdy N jest parzysta, a N jest nieparzysta. Jeśli N jest parzysta, załóżmy, że N = 2\*m gdzie m > = 1, nierówności wygląda 2\*m - (2\*m/2 + 1) > = 2 lub m > = 3. Co najmniej n to 6 i to osiągnąć, kiedy m = 3. Z drugiej strony, jeśli N jest nieparzysta, powiedz N = 2\*m + 1 gdzie m > = 1, nierówności wygląda 2\*m + 1 – ((2\*m + 1) / 2 + 1) > = 2 lub 2\*m + 1 – (m + 1) > = 2 lub m > = 2. Co najmniej n wynosi 5, a to osiągnąć, kiedy m = 2. W związku między wszystkie wartości N, które spełniają kryteria nierówności wartość ClusterSize - QuorumSize > = 2, wartość minimalna to 5.

Uwaga: w argumencie powyżej, które firma Microsoft ma zakłada, że każdy węzeł repliki usługi systemu, dlatego rozmiar kworum jest obliczana na podstawie liczby węzłów w klastrze. Jednakże, zmieniając *TargetReplicaSetSize* firma Microsoft może spowodować, że rozmiar kworum mniej niż (N / 2 + 1) który może stworzyć wrażenie, że mogliśmy mają mniejszy niż 5 węzłów klastra i nadal masz 2 dodatkowe węzły rozmiar kworum. Na przykład 4 węzłami klastra, jeśli ustawimy TargetReplicaSetSize na 3, rozmiar kworum, w oparciu o TargetReplicaSetSize jest (3/2 + 1) lub 2, dlatego mamy wartość ClusterSize - QuorumSize 4-2 = > = 2. Jednak firma Microsoft nie gwarantuje, że usługa system będzie w lub powyżej kworum możemy utraty jakiejkolwiek parze węzłów jednocześnie, może to być dwa węzły stracimy zostały hostingu dwie repliki, dzięki czemu usługa systemowa zostaną umieszczone w utraciła kworum (o pojedynczą replikę w lewo) ND staną się niedostępne.

W tle Przeanalizujmy niektóre konfiguracje klastra możliwe:

**Jeden węzeł**: Ta opcja zapewnia ona wysokiej dostępności od utraty jednego węzła do jakiegokolwiek powodu oznacza utraty całego klastra.

**Z uwzględnieniem dwóch węzłów**: kworum dla usług wdrożonych w dwóch węzłów (N = 2) 2 (2/2 + 1 = 2). Pojedynczą replikę zostanie utracony, jest niemożliwe utworzyć kworum. Wykonywanie uaktualnienia usługi AD. tymczasowego wyłączania repliki, nie jest przydatne w konfiguracji.

**Trzy węzły**: z trzema węzłami (N = 3) wymagane do utworzenia kworum jest nadal z uwzględnieniem dwóch węzłów (3/2 + 1 = 2). Oznacza to, że możesz utracić oddzielnego węzła i nadal utrzymania kworum, ale równoczesnej awarii dwóch węzłów będą miały usług systemowych do utraciła kworum i spowoduje, że klaster, aby stać się niedostępne.

**Cztery węzły**: w przypadku czterech węzłów (N = 4) wymagane do utworzenia kworum jest trzy węzły (4/2 + 1 = 3). Oznacza to, że możesz utracić oddzielnego węzła i nadal utrzymania kworum, ale równoczesnej awarii dwóch węzłów będą miały usług systemowych do utraciła kworum i spowoduje, że klaster, aby stać się niedostępne.

**Pięć węzłów**: z pięcioma węzłami (N = 5) wymagane do utworzenia kworum jest nadal trzy węzły (+ 1, 5/2 = 3). Oznacza to, można utracić z uwzględnieniem dwóch węzłów, w tym samym czasie i nadal utrzymuje kworum dla usług systemowych.

Dla obciążeń produkcyjnych użytkownik musi być odporny na równoczesnej awarii co najmniej dwa węzły (na przykład jeden z powodu uaktualnienia klastra, jeden z innych powodów), więc pięć węzłów są wymagane.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Można wyłączyć Mój klaster w nocy/weekendy w celu obniżenia kosztów?

Rzecz biorąc — nie. Usługi Service Fabric stanie są przechowywane na dyskach lokalnych, tymczasowych, co oznacza, że jeśli maszyna wirtualna zostanie przeniesiona do innego hosta, dane nie przenoszą się z nim. W normalnych operacji, który nie jest problemem jako nowy węzeł zostanie przełączony w tryb aktualne przez inne węzły. Jednak jeśli zatrzymać wszystkie węzły, a następnie uruchom je ponownie później, ma znaczący możliwość, że większość węzłów Rozpocznij w nowym hostom oraz marki systemu nie można odzyskać.

Jeśli chcesz utworzyć klastry na potrzeby testowania aplikacji przed wdrożeniem, zaleca się dynamiczne tworzenie tych klastrów w ramach Twojej [potok ciągłej integracji/ciągłego wdrażania](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak uaktualnić System operacyjny (na przykład z programu Windows Server 2012 do systemu Windows Server 2016)?

Podczas gdy pracujemy nad udoskonalony interfejs użytkownika, już dziś, ponosisz odpowiedzialność za uaktualnienia. Należy uaktualnić obrazu systemu operacyjnego na maszynach wirtualnych z jednego klastra maszyny Wirtualnej w danym momencie. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Czy można zaszyfrować dołączonych dysków z danymi w typ węzła klastra (zestaw skalowania maszyn wirtualnych)?
Tak.  Aby uzyskać więcej informacji, zobacz [utworzyć klaster z dołączonymi dyskami danych](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [szyfrowanie dysków (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), i [szyfrowanie dysków (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>W typie węzła klastra (zestaw skalowania maszyn wirtualnych) można używać maszyn wirtualnych o niskim priorytecie?
Nie. Maszyny wirtualne o niskim priorytecie nie są obsługiwane. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Co to są katalogów i procesy, wymagających do wykluczenia podczas uruchamiania programu antywirusowego w moim klastrze?

| **Antywirusowe wykluczone katalogi** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Antywirusowe wykluczone procesy** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Jak Moja aplikacja może wykonać uwierzytelnienie do magazynu kluczy do pobrania wpisów tajnych?
Oznacza, że dla aplikacji, aby uzyskać poświadczenia dla uwierzytelniania do magazynu kluczy są następujące:

A. Podczas kompilacji/pakowania zadania aplikacji można ściągnąć certyfikatu do pakietu danych aplikacji SF i służy do uwierzytelniania do magazynu kluczy.
B. Dla hostów z włączoną tożsamością usługi Zarządzanej zestaw skalowania maszyn wirtualnych, możesz tworzyć proste SetupEntryPoint programu PowerShell dla aplikacji SF można pobrać [token dostępu z punktu końcowego MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token), a następnie [pobrać Wpisy tajne z magazynu kluczy](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Projekt aplikacji

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Co to jest najlepszym sposobem na zapytania o dane w partycjach Reliable Collection?

Elementy Reliable collections to zazwyczaj [partycjonowane](service-fabric-concepts-partitioning.md) umożliwia skalowanie w poziomie na większą wydajność i przepływność. Oznacza to, że stan dla danej usługi może zostać rozmieszczone w dziesiątek, jak i setek maszyn. Do wykonania operacji za pośrednictwem tego całego zestawu danych, możesz skorzystać z kilku opcji:

- Tworzenie usługi, który odpytuje wszystkich partycji innej usługi, aby pobrać wymagane dane.
- Utwórz usługę, które mogą odbierać dane z wszystkich partycji innej usługi.
- Okresowe wypychanie danych z każdej z tych usług do zewnętrznego magazynu. To podejście jest tylko jeśli zapytań, które wykonujesz nie są częścią podstawowej logiki biznesowej.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Co to jest najlepszym sposobem na zapytania o dane w mojej aktorów?

Aktorzy mają być niezależnych zespołów stanu i mocy obliczeniowej, dlatego nie zaleca się do wykonywania zapytań o szerokim stanu aktora w czasie wykonywania. W przypadku konieczności zapytania dla pełnego zestawu stanu aktora, należy rozważyć albo:

- Zastępowanie usługi aktora stanowych usług reliable services, tak aby liczba sieci żądań zbieranie wszystkich danych z liczbę uczestników liczby partycji w usłudze.
- Projektowanie swojej aktorów okresowe wypychanie ich stanu do magazynu zewnętrznego, łatwiejsze podczas wykonywania zapytań. Jako powyżej, to podejście jest tylko możliwe, jeśli wykonujesz zapytań nie są wymagane dla zachowania w czasie wykonywania.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Jak dużo danych można przechowywać w niezawodnej kolekcji?

Usług Reliable services są zazwyczaj podzielona na partycje, więc wielkość, które można przechowywać jest ograniczona tylko liczba maszyn, które masz w klastrze, a ilość pamięci dostępnej na tych komputerach.

Na przykład załóżmy, że zostały reliable collection w usłudze przy użyciu 100 partycji i replik 3 przechowywania obiektów, które średni o rozmiarze 1 kb. Teraz załóżmy, że masz klastra 10 maszyny z 16gb pamięci na maszynę. Dla uproszczenia, a także konserwatywne, przyjęto założenie, że system operacyjny i usług systemowych, środowisko uruchomieniowe usługi Service Fabric i usługi wykorzystywać 6gb, pozostawiając dostępnych dla poszczególnych komputerów 10gb lub 100 gb dla klastra.

Pamiętając o tym, że każdy obiekt musi być przechowywany trzy razy (jedną podstawową i dwie repliki), czy masz wystarczającą ilość pamięci dla około 35 milionów obiektów w kolekcji podczas działania pełnego wykorzystania możliwości. Firma Microsoft zaleca jednak jest odporny na jednoczesne utraty domeny błędów i domeny uaktualnienia, który reprezentuje około 1/3 wydajności i może zmniejszyć liczbę do około 23 milionów.

Pamiętaj, że to obliczenie przyjęto następujące założenia:

- To około jednolity rozkład danych między partycjami, lub że zgłaszasz metryk obciążenia Menedżer zasobów klastra. Domyślnie Usługa Service Fabric ładuje salda na podstawie liczby replik. W poprzednim przykładzie, która spowodowałaby 10 replik podstawowych, a 20 replik pomocniczych w każdym węźle w klastrze. To działa dobrze w przypadku obciążenia, który jest równomiernie rozłożona na partycje. Jeśli obciążenie nie jest jeszcze, możesz zgłaszać obciążenia tak, aby usługi Resource Manager można ze sobą pakietu repliki mniejszy i umożliwić większych repliki, aby zużywać więcej pamięci w węźle poszczególnych.

- Czy usługi reliable service w danym jest tylko jeden stan przechowywania w klastrze. Ponieważ można wdrożyć wiele usług w klastrze, należy zachować ostrożność, zasobów, każdy z nich musi uruchomić i zarządzać jego stan.

- Że sam klaster jest rosnący lub nie zmniejszania. Jeśli dodasz więcej maszyn, Usługa Service Fabric będzie ponowne zrównoważenie repliki wykorzystanie dodatkowej pojemności, dopóki liczba maszyn przekracza liczbę partycji w usłudze, ponieważ poszczególnych repliki nie mogą rozciągać się maszyn. Z kolei jeśli rozmiar klastra można zmniejszyć przez usunięcie maszyn, repliki są pakowane precyzyjniejsze i mieć mniej ogólnej wydajności.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Jak dużo danych można przechowywać w Aktor?

Podobnie jak w przypadku usług reliable services, ilości danych, które mogą być przechowywane w usłudze aktora jest ograniczona tylko całkowitego miejsca na dysku i dostępnej pamięci między węzłami w klastrze. Jednak poszczególnych uczestników są najbardziej efektywne, gdy są one używane do hermetyzacji niewielką ilość stanu i skojarzonej logiki biznesowej. Zgodnie z ogólną zasadą Aktor poszczególnych powinien mieć stan, który jest mierzony w kilobajtach.

## <a name="other-questions"></a>Inne pytania

### <a name="how-does-service-fabric-relate-to-containers"></a>Jaki jest związek między usługi Service Fabric kontenery?

Kontenery oferują prosty sposób pakietu usług i ich zależności, tak, aby uruchomić spójne we wszystkich środowiskach i mogą działać w sposób izolowany na jednym komputerze. Usługa Service Fabric umożliwia wdrażanie usług i zarządzania nimi, w tym [usług, które zostały spakowane w kontenerze](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Czy planujesz typu open-source Service Fabric

Mamy open source części usługi Service Fabric ([usług reliable services w ramach](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [framework elementów reliable actors](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [bibliotek Integracja platformy ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [ Narzędzie Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), i [interfejsu wiersza polecenia usługi Service Fabric](https://github.com/Azure/service-fabric-cli)) w witrynie GitHub i zaakceptuj kod wniesiony przez społeczność w tych projektach. 

Firma Microsoft [zapowiedziała niedawno](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) że planujemy typu open-source środowisko uruchomieniowe usługi Service Fabric. W tym momencie mamy [repozytorium usługi Service Fabric](https://github.com/Microsoft/service-fabric/) nawet w usłudze GitHub z systemem Linux tworzyć i testować narzędzia, oznacza to, można sklonować repozytorium, tworzyć usługi Service Fabric dla systemu Linux, uruchom podstawowe testy, zgłaszanie problemów i przesyłać żądania ściągnięcia. Firma Microsoft dokłada wszelkich starań można pobrać Windows środowisko budowania poddane migracji, wraz z kompletnego środowiska ciągłej integracji.

Postępuj zgodnie z [blog usługi Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) Aby uzyskać więcej informacji, zgodnie z ich ogłoszeniem na one.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [podstawowe pojęcia dotyczące usługi Service Fabric](service-fabric-technical-overview.md) i [najlepsze praktyki](service-fabric-best-practices-overview.md) lodowa concepts](service-fabric-technical-overview.md) sieci szkieletowej i [najlepsze rozwiązania](service-fabric-best-practices-overview.md)
