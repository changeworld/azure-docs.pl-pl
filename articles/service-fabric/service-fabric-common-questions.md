---
title: Typowe pytania dotyczące sieci szkieletowej usług Platformy Microsoft Azure
description: Często zadawane pytania dotyczące sieci szkieletowej usług, w tym możliwości, przypadki użycia i typowe scenariusze.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254884"
---
# <a name="commonly-asked-service-fabric-questions"></a>Często zadawane pytania dotyczące usługi Service Fabric

Istnieje wiele często zadawane pytania dotyczące tego, co sieci szkieletowej usług może zrobić i jak należy go używać. Niniejszy dokument obejmuje wiele z tych często zadawanych pytań i ich odpowiedzi.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Konfiguracja klastra i zarządzanie nim

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Jak wycofać certyfikat klastra sieci szkieletowej usług?

Wycofywanie dowolnego uaktualnienia do aplikacji wymaga wykrywania awarii kondycji przed kworum klastra sieci szkieletowej usług zatwierdzania zmiany; zatwierdzone zmiany można tylko przewinąć do przodu. Inżynier eskalacji za pośrednictwem obsługi klienta, może być wymagane do odzyskania klastra, jeśli wprowadzono niemonitorowane zmiany certyfikatu zerwania.  [Uaktualnienie aplikacji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) stosuje [parametry uaktualniania aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)i zapewnia zerową obietnicę aktualizacji przestojów.  Zgodnie z naszym zalecanym trybem monitorowania uaktualnienia aplikacji, automatyczny postęp za pośrednictwem domen aktualizacji jest oparty na przekazywaniu kontroli kondycji, wycofywanie automatycznie, jeśli aktualizacja usługi domyślnej nie powiedzie się.
 
Jeśli klaster nadal korzysta z klasycznej właściwości Odcisk palca certyfikatu w szablonie Menedżera zasobów, zaleca się [zmianę klastra z odcisku palca certyfikatu na nazwę pospolitą,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)aby wykorzystać nowoczesne funkcje zarządzania wpisami tajnymi.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Czy można utworzyć klaster, który obejmuje wiele regionów platformy Azure lub własne centra danych?

Tak. 

Podstawowa technologia klastrowania sieci szkieletowej usług może służyć do łączenia maszyn działających w dowolnym miejscu na świecie, o ile mają one łączność sieciową ze sobą. Jednak tworzenie i uruchamianie takiego klastra może być skomplikowane.

Jeśli jesteś zainteresowany tym scenariuszem, zachęcamy do skontaktowania się za pośrednictwem [listy problemów GitHub sieci szkieletowej usług](https://github.com/azure/service-fabric-issues) lub za pośrednictwem przedstawiciela pomocy technicznej w celu uzyskania dodatkowych wskazówek. Zespół sieci szkieletowej usług pracuje nad zapewnieniem dodatkowej przejrzystości, wskazówek i zaleceń dotyczących tego scenariusza. 

Kilka rzeczy do rozważenia: 

1. Zasób klastra sieci szkieletowej usług na platformie Azure jest obecnie regionalny, podobnie jak zestawy skalowania maszyny wirtualnej, na których jest zbudowany klaster. Oznacza to, że w przypadku awarii regionalnej może utracić możliwość zarządzania klastrem za pośrednictwem usługi Azure Resource Manager lub witryny Azure portal. Może się tak zdarzyć, nawet jeśli klaster pozostaje uruchomiony i można będzie wchodzić z nim bezpośrednio w interakcję. Ponadto platforma Azure obecnie nie oferuje możliwość jednej sieci wirtualnej, która jest użyteczna w różnych regionach. Oznacza to, że klaster wieloregionowy na platformie Azure wymaga publicznych [adresów IP dla każdej maszyny Wirtualnej w zestawach skalowania maszyny Wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) lub [bramy sieci VPN platformy Azure.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Te opcje sieci mają różny wpływ na koszty, wydajność i do pewnego stopnia projektowanie aplikacji, więc wymagana jest dokładna analiza i planowanie przed stanem takiego środowiska.
2. Konserwacja, zarządzanie i monitorowanie tych maszyn może stać się skomplikowane, zwłaszcza gdy obejmuje _między typami_ środowisk, takich jak między różnymi dostawcami chmury lub między zasobami lokalnymi i platformy Azure. Należy dopilnować, aby przed uruchomieniem obciążeń produkcyjnych w takim środowisku należy zadbać o to, aby uaktualnienia, monitorowanie, zarządzanie i diagnostyka były zrozumiałe zarówno dla klastra, jak i aplikacji. Jeśli masz już doświadczenie w rozwiązywaniu tych problemów na platformie Azure lub w ramach własnych centrów danych, prawdopodobnie te same rozwiązania mogą być stosowane podczas tworzenia lub uruchamiania klastra sieci szkieletowej usług. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Czy węzły sieci szkieletowej usług automatycznie otrzymują aktualizacje systemu operacyjnego?

Można użyć funkcji [automatyczna aktualizacja obrazu](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) systemu operacyjnego virtual machine Set dzisiaj.

Dla klastrów, które NIE są uruchamiane na platformie Azure, [udostępniliśmy aplikację](service-fabric-patch-orchestration-application.md) do poprawki systemów operacyjnych pod węzłami sieci szkieletowej usług.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Czy mogę używać dużych zestawów skalowania maszyn wirtualnych w klastrze SF? 

**Krótka odpowiedź** - Nie. 

**Długa odpowiedź** — mimo że zestawy skalowania dużej maszyny wirtualnej umożliwiają skalowanie skalowania maszyny wirtualnej skonfigurowanej do 1000 wystąpień maszyn wirtualnych, robi to za pomocą grup umieszczania (PGs). Domeny błędów (FD) i domeny uaktualnienia (UD) są spójne tylko w obrębie sieci szkieletowej usługi grupy miejsc docelowych, która używa plików FD i UD do podejmowania decyzji dotyczących umieszczania replik usług/wystąpień usługi. Ponieważ identyfikatory FD i UD są porównywalne tylko w obrębie grupy miejsc docelowych, SF nie może jej używać. Na przykład Jeśli VM1 w PG1 ma topologię FD = 0 i VM9 w PG2 ma topologii FD = 4, nie oznacza, że VM1 i VM2 są na dwóch różnych stojaków sprzętowych, w związku z tym SF nie może używać wartości FD w tym przypadku do podejmowania decyzji o umieszczeniu.

Istnieją inne problemy z dużych zestawów skalowania maszyny wirtualnej obecnie, takich jak brak poziomu 4 Obsługa równoważenia obciążenia. Szczegółowe [informacje na temat zestawów na dużą skalę](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) można znaleźć w



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Jaki jest minimalny rozmiar klastra sieci szkieletowej usług? Dlaczego nie może być mniejszy?

Minimalny obsługiwany rozmiar klastra sieci szkieletowej z uruchomionymi obciążeniami produkcyjnymi to pięć węzłów. W przypadku scenariuszy deweloperskich obsługujemy jeden węzeł (zoptymalizowany pod kątem szybkiego tworzenia w programie Visual Studio) i klastry pięciu węzłów.

Wymagamy klastra produkcyjnego mieć co najmniej 5 węzłów z następujących trzech powodów:
1. Nawet wtedy, gdy nie są uruchomione żadne usługi użytkownika, klaster sieci szkieletowej usług uruchamia zestaw stanowych usług systemowych, w tym usługę nazewnictwa i usługę menedżera trybu failover. Te usługi systemowe są niezbędne do klastra, aby pozostać operacyjnym.
2. Zawsze umieszczamy jedną replikę usługi na węzeł, więc rozmiar klastra jest górną granicą liczby replik, które może mieć usługa (w rzeczywistości partycja).
3. Ponieważ uaktualnienie klastra spowoduje obniżenie co najmniej jednego węzła, chcemy mieć bufor co najmniej jeden węzeł, w związku z tym chcemy klastra produkcyjnego mieć co najmniej dwa węzły *oprócz* minimum gołe. Minimum to rozmiar kworum usługi systemowej, jak wyjaśniono poniżej.  

Chcemy, aby klaster był dostępny w obliczu jednoczesnej awarii dwóch węzłów. Aby klaster sieci szkieletowej usług był dostępny, usługi systemowe muszą być dostępne. Stanowe usługi systemowe, takie jak usługa nazewnictwa i usługa menedżera trybu failover, które śledzą, jakie usługi zostały wdrożone w klastrze i gdzie są obecnie hostowane, zależą od ścisłej spójności. Ta silna spójność z kolei zależy od możliwości uzyskania *kworum* dla danej aktualizacji stanu tych usług, gdzie kworum reprezentuje ścisłą większość replik (N/2 +1) dla danej usługi. Tak więc, jeśli chcemy być odporne na jednoczesne utraty dwóch węzłów (w ten sposób jednoczesna utrata dwóch replik usługi systemowej), musimy mieć ClusterSize - KworumSize >= 2, co wymusza minimalny rozmiar do pięciu. Aby to zobaczyć, należy wziąć pod uwagę klaster ma węzły N i istnieją repliki N usługi systemowej - po jednym w każdym węźle. Rozmiar kworum dla usługi systemowej jest (N/2 + 1). Powyższa nierówność wygląda jak N - (N/2 + 1) >= 2. Istnieją dwa przypadki do rozważenia: kiedy N jest parzysta i gdy N jest dziwne. Jeśli N jest parzyste,\*powiedzmy N = 2 m, gdzie m\*>= 1, nierówność wygląda jak 2 m - (2\*m/2 + 1) >= 2 lub m >= 3. Minimum dla N wynosi 6 i to jest osiągane, gdy m = 3. Z drugiej strony, jeśli N jest nieparzyste, powiedzmy N =\*2 m+1, gdzie\*m >= 1, nierówność wygląda jak 2 m+1 - ( (2\*m+1)/2 + 1 ) >= 2 lub 2\*m+1 - (m+1) >= 2 lub m >= 2. Minimum dla N wynosi 5 i to jest osiągane, gdy m = 2. W związku z tym wśród wszystkich wartości N, które spełniają nierówności ClusterSize - KworumSize >= 2, minimum wynosi 5.

Uwaga: w powyższym argumencie przyjęliśmy założenie, że każdy węzeł ma replikę usługi systemowej, w związku z czym rozmiar kworum jest obliczany na podstawie liczby węzłów w klastrze. Jednak zmieniając *TargetReplicaSetSize* możemy zrobić rozmiar kworum mniej niż (N/2 + 1), co może sprawiać wrażenie, że możemy mieć klaster mniejszy niż 5 węzłów i nadal mają 2 dodatkowe węzły powyżej rozmiaru kworum. Na przykład w klastrze 4 węzłów, jeśli ustawimy Rozmiar TargetReplicaSetSize na 3, rozmiar kworum oparty na TargetReplicaSetSize jest (3/2 + 1) lub 2, w związku z tym mamy ClusterSize - QuorumSize = 4-2 >= 2. Nie możemy jednak zagwarantować, że usługa systemowa będzie na poziomie kworum lub powyżej, jeśli stracimy dowolną parę węzłów jednocześnie, może to oznaczać, że dwa węzły, które utraciliśmy, były hostowane przez dwie repliki, więc usługa systemowa przejdzie do utraty kworum (mając tylko jedną replikę) i staną się niedostępne.

W tym kontekście przyjrzyjmy się niektórym możliwym konfiguracjom klastra:

**Jeden węzeł:** ta opcja nie zapewnia wysokiej dostępności, ponieważ utrata pojedynczego węzła z jakiegokolwiek powodu oznacza utratę całego klastra.

**Dwa węzły:** kworum dla usługi wdrożonej w dwóch węzłach (N = 2) wynosi 2 (2/2 + 1 = 2). Po utracie pojedynczej repliki nie można utworzyć kworum. Ponieważ wykonanie uaktualnienia usługi wymaga tymczasowego likwidacji repliki, nie jest to przydatna konfiguracja.

**Trzy węzły:** z trzema węzłami (N=3), wymaganie utworzenia kworum jest nadal dwa węzły (3/2 + 1 = 2). Oznacza to, że można utracić pojedynczy węzeł i nadal utrzymywać kworum, ale jednoczesna awaria dwóch węzłów spowoduje, że usługi systemowe ulegną utracie kworum i spowoduje, że klaster stanie się niedostępny.

**Cztery węzły:** z czterema węzłami (N=4), wymaganie utworzenia kworum to trzy węzły (4/2 + 1 = 3). Oznacza to, że można utracić pojedynczy węzeł i nadal utrzymywać kworum, ale jednoczesna awaria dwóch węzłów spowoduje, że usługi systemowe ulegną utracie kworum i spowoduje, że klaster stanie się niedostępny.

**Pięć węzłów:** z pięcioma węzłami (N=5), wymaganie utworzenia kworum jest nadal trzy węzły (5/2 + 1 = 3). Oznacza to, że można utracić dwa węzły w tym samym czasie i nadal obsługiwać kworum dla usług systemowych.

W przypadku obciążeń produkcyjnych musi być odporna na jednoczesną awarię co najmniej dwóch węzłów (na przykład jeden z powodu uaktualnienia klastra, jeden z innych powodów), więc wymagane jest pięć węzłów.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Czy mogę wyłączyć klaster w nocy/weekendy, aby zaoszczędzić koszty?

Ogólnie rzecz biorąc — nie. Sieć szkieletowa usług przechowuje stan na dyskach lokalnych, tymczasowych, co oznacza, że jeśli maszyna wirtualna zostanie przeniesiona do innego hosta, dane nie są z nią przenoszone. W normalnej pracy nie jest to problem, ponieważ nowy węzeł jest przyuwiany na bieżąco przez inne węzły. Jeśli jednak zatrzymasz wszystkie węzły i uruchomisz je ponownie później, istnieje duże prawdopodobieństwo, że większość węzłów rozpocznie się na nowych hostach i sprawi, że system nie będzie mógł odzyskać.

Jeśli chcesz utworzyć klastry do testowania aplikacji przed jej wdrożeniem, zaleca się dynamiczne tworzenie tych klastrów w ramach [ciągłego potoku integracji/ciągłego wdrażania.](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak uaktualnić system operacyjny (na przykład z systemu Windows Server 2012 do systemu Windows Server 2016)?

Podczas gdy pracujemy nad lepszym doświadczeniem, dziś jesteś odpowiedzialny za uaktualnienie. Należy uaktualnić obraz systemu operacyjnego na maszynach wirtualnych klastra jednej maszyny wirtualnej naraz. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Czy można szyfrować dołączone dyski danych w typie węzła klastra (zestaw skalowania maszyny wirtualnej)?
Tak.  Aby uzyskać więcej informacji, zobacz [Tworzenie klastra z dołączonymi dyskami danych](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) i [szyfrowanie dysków platformy Azure dla zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Czy można używać maszyn wirtualnych o niskim priorytecie w typie węzła klastra (zestaw skalowania maszyny wirtualnej)?
Nie. Maszyny wirtualne o niskim priorytecie nie są obsługiwane. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Jakie katalogi i procesy należy wykluczyć podczas uruchamiania programu antywirusowego w klastrze?

| **Katalogi wykluczonych z programu antywirusowego** |
| --- |
| Pliki programów\Sieci szkieletowej usług firmy Microsoft |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Procesy wykluczone z programu antywirusowego** |
| --- |
| Plik Fabric.exe |
| Plik FabricHost.exe |
| Program FabricInstallerService.exe |
| Plik FabricSetup.exe |
| Plik FabricDeployer.exe |
| Plik ImageBuilder.exe |
| Plik FabricGateway.exe |
| Plik FabricDCA.exe |
| Plik FabricFAS.exe |
| Plik FabricUOS.exe |
| Plik FabricRM.exe |
| PlikStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>W jaki sposób moja aplikacja może uwierzytelnić się w keyvault, aby uzyskać wpisy tajne?
Następujące są środki dla aplikacji, aby uzyskać poświadczenia do uwierzytelniania do KeyVault:

A. Podczas zadania kompilacji/pakowania aplikacji można wyciągnąć certyfikat do pakietu danych aplikacji SF i użyć go do uwierzytelnienia w funkcji KeyVault.
B. W przypadku hostów obsługujących zestaw skalowania maszyny wirtualnej msi można opracować prosty program PowerShell SetupEntryPoint dla aplikacji SF, aby uzyskać [token dostępu z punktu końcowego MSI,](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)a następnie pobrać [wpisy tajne z funkcji KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Projekt aplikacji

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Jaki jest najlepszy sposób, aby zbadać dane na partycjach niezawodnej kolekcji?

Niezawodne kolekcje są zazwyczaj [podzielone na partycje,](service-fabric-concepts-partitioning.md) aby włączyć skalowanie w poziomie w celu zwiększenia wydajności i przepływności. Oznacza to, że stan danej usługi może być rozłożony na dziesiątki lub setki maszyn. Aby wykonać operacje za cały zestaw danych, masz kilka opcji:

- Utwórz usługę, która wysyła zapytanie do wszystkich partycji innej usługi, aby wyciągnąć wymagane dane.
- Utwórz usługę, która może odbierać dane ze wszystkich partycji innej usługi.
- Okresowo wypychaj dane z każdej usługi do magazynu zewnętrznego. Takie podejście jest odpowiednie tylko wtedy, gdy kwerendy, które wykonujesz, nie są częścią podstawowej logiki biznesowej, ponieważ dane magazynu zewnętrznego będą przestarzałe.
- Alternatywnie należy przechowywać dane, które muszą obsługiwać wykonywanie zapytań we wszystkich rekordach bezpośrednio w magazynie danych, a nie w niezawodnej kolekcji. Eliminuje to problem ze starych danych, ale nie pozwala na zalety niezawodnych kolekcji, które mają być wykorzystywane.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Jaki jest najlepszy sposób wykonywania zapytań o dane między moimi aktorami?

Aktorzy są przeznaczone do niezależnych jednostek stanu i obliczeń, więc nie jest zalecane wykonywanie szerokich zapytań o stanie aktora w czasie wykonywania. Jeśli masz potrzebę kwerendy w całym zestawie stanu aktora, należy wziąć pod uwagę albo:

- Zastępowanie usług aktora stanowymi niezawodnymi usługami, dzięki czemu liczba żądań sieciowych do zbierania wszystkich danych od liczby podmiotów do liczby partycji w usłudze.
- Projektowanie podmiotów okresowo wypychać ich stan do magazynu zewnętrznego w celu łatwiejszego wykonywania zapytań. Jak powyżej, to podejście jest opłacalne tylko wtedy, gdy kwerendy, które wykonujesz nie są wymagane dla zachowania środowiska wykonawczego.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Ile danych można przechowywać w niezawodnej kolekcji?

Niezawodne usługi są zazwyczaj podzielone na partycje, więc ilość, którą można przechowywać, jest ograniczona tylko przez liczbę komputerów w klastrze i ilość pamięci dostępnej na tych komputerach.

Na przykład załóżmy, że masz niezawodną kolekcję w usłudze ze 100 partycjami i 3 replikami, przechowując obiekty o średniej wielkości 1 kb. Załóżmy teraz, że masz 10 klastra maszyn z 16gb pamięci na komputerze. Dla uproszczenia i być konserwatywnym, załóżmy, że system operacyjny i usługi systemowe, środowisko uruchomieniowe sieci szkieletowej usług i usługi zużywają 6 gb tego, pozostawiając 10 gb dostępne na komputerze lub 100 GB dla klastra.

Mając na uwadze, że każdy obiekt musi być przechowywany trzy razy (jeden podstawowy i dwie repliki), trzeba będzie wystarczającej ilości pamięci dla około 35 milionów obiektów w kolekcji podczas pracy z pełną pojemnością. Jednak zaleca się odporność na jednoczesną utratę domeny awarii i domeny uaktualnienia, która reprezentuje około 1/3 pojemności i zmniejszyłaby liczbę do około 23 milionów.

Należy zauważyć, że w obliczeniach przyjęto również:

- Że dystrybucja danych między partycjami jest w przybliżeniu jednolite lub że raportujesz metryki obciążenia do Menedżera zasobów klastra. Domyślnie sieci szkieletowej usług ładuje równowagę na podstawie liczby replik. W poprzednim przykładzie, który umieściłby 10 replik podstawowych i 20 replik pomocniczych w każdym węźle w klastrze. To działa dobrze dla obciążenia, które jest równomiernie rozłożone na partycje. Jeśli obciążenie nie jest nawet, należy zgłosić obciążenia, tak aby Menedżer zasobów można spakować mniejsze repliki razem i umożliwić większe repliki zużywają więcej pamięci w poszczególnych węzłach.

- Że niezawodna usługa, o których mowa jest tylko jeden stan przechowywania w klastrze. Ponieważ można wdrożyć wiele usług do klastra, należy pamiętać o zasobach, które każdy musi uruchomić i zarządzać jego stan.

- Że sam klaster nie rośnie lub kurczy się. Jeśli dodasz więcej komputerów, sieci szkieletowej usług będzie równoważyć repliki, aby wykorzystać dodatkową pojemność, dopóki liczba maszyn przekroczy liczbę partycji w usłudze, ponieważ pojedyncza replika nie może obejmować maszyn. Z drugiej strony, jeśli zmniejszysz rozmiar klastra przez usunięcie maszyn, repliki są bardziej szczelnie zapakowane i mają mniejszą ogólną pojemność.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Ile danych mogę przechowywać u aktora?

Podobnie jak w razie usług niezawodnych, ilość danych, które można przechowywać w usłudze aktora jest ograniczona tylko przez całkowitą ilość miejsca na dysku i pamięci dostępnej w węzłach w klastrze. Jednak poszczególne podmioty są najbardziej skuteczne, gdy są one używane do hermetyzacji niewielką ilość stanu i skojarzonej logiki biznesowej. Zgodnie z ogólną zasadą pojedynczy aktor powinien mieć stan, który jest mierzony w kilobajtach.

## <a name="other-questions"></a>Inne pytania

### <a name="how-does-service-fabric-relate-to-containers"></a>W jaki sposób usługa Service Fabric odnosi się do kontenerów?

Kontenery oferują prosty sposób pakowania usług i ich zależności, tak aby działały spójnie we wszystkich środowiskach i mogą działać w sposób izolowany na jednym komputerze. Usługa Sieci szkieletowej oferuje sposób wdrażania i zarządzania usługami, w tym [usługami, które zostały spakowane w kontenerze.](service-fabric-containers-overview.md)

### <a name="are-you-planning-to-open-source-service-fabric"></a>Czy planujesz otworzyć sieci szkieletowej usług typu open source?

Mamy otwarte części sieci szkieletowej usług[(ramy niezawodnych usług,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [ramy wiarygodnych aktorów,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [ASP.NET biblioteki integracji Core,](https://github.com/Azure/service-fabric-aspnetcore) [Eksplorator sieci szkieletowej usług](https://github.com/Azure/service-fabric-explorer)i sieć [wytwarzania sieci szkieletowej usług)](https://github.com/Azure/service-fabric-cli)na GitHub i akceptujemy wkład społeczności do tych projektów. 

[Niedawno ogłosiliśmy,](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) że planujemy open-source środowisko uruchomieniowe sieci szkieletowej usług. W tym momencie mamy [repozytorium sieci szkieletowej usług](https://github.com/Microsoft/service-fabric/) na GitHub z narzędziami kompilacji i testowania systemu Linux, co oznacza, że można sklonować repozytorium, budować sieci szkieletowe usług dla systemu Linux, uruchamiać podstawowe testy, otwierać problemy i przesyłać żądania ściągania. Ciężko pracujemy nad migracją środowiska kompilacji systemu Windows wraz z kompletnym środowiskiem ciągłej integracji.

Postępuj zgodnie z [blogiem sieci szkieletowej usług,](https://blogs.msdn.microsoft.com/azureservicefabric/) aby uzyskać więcej informacji w miarę ich ogłaszania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [podstawowych pojęciach](service-fabric-technical-overview.md) i najlepszych praktykach sieci [szkieletowej usług](service-fabric-best-practices-overview.md)
