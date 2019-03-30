---
title: Planowanie pojemności klastra usługi Service Fabric | Dokumentacja firmy Microsoft
description: Zagadnienia dotyczące planowania pojemności klastra usługi Service Fabric. Elementy NodeType, operacje, trwałości i niezawodności
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: chackdan
ms.openlocfilehash: bd76658c939496f27bf3751060c18d17968acd15
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667348"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Zagadnienia dotyczące planowania pojemności klastra usługi Service Fabric
Dla wszystkich wdrożeń produkcyjnych planowania pojemności jest ważnym krokiem. Poniżej przedstawiono niektóre elementy, które należy wziąć pod uwagę jako część tego procesu.

* Liczba typów węzłów, które klaster musi zaczynać
* Właściwości każdego typu węzła (rozmiar, podstawowy, połączenie z Internetem, liczba maszyn wirtualnych itd.)
* Charakterystyka niezawodności i trwałości klastra

> [!NOTE]
> Co najmniej należy przejrzeć wszystkie **niedozwolone** uaktualnienia wartości zasad podczas planowania. Jest tak, aby upewnić się, odpowiednio ustawić wartości i eliminowanie nagrywania klastra później ze względu na ustawienia konfiguracji systemu nie będzie zmieniana. 
> 

Daj nam chwilę Przejrzyj każdy z tych elementów.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Liczba typów węzłów, które klaster musi zaczynać
Najpierw należy ustalić, co się dzieje służący do klastra, którą tworzysz.  Jakiego rodzaju aplikacje, które planujesz wdrożyć do tego klastra? Jeśli nie jesteś wyczyść celowo klastra, użytkownik najprawdopodobniej nie została jeszcze gotowe do wprowadź wydajność procesu planowania.

Ustanów liczba typów węzłów, które klaster musi zaczynać.  Każdy typ węzła jest mapowany do zestawu skalowania maszyn wirtualnych. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Dlatego decyzja liczba typów węzłów zasadniczo sprowadza się do następujących kwestiach:

* Aplikacja ma wiele usług i czy któryś z nich muszą być publiczne lub połączone z Internetem? Typowe aplikacje zawierają usługi frontonu bramy, który odbiera dane wejściowe z klienta i jeden lub więcej usług zaplecza, które komunikują się przy użyciu usługi frontonu. Dlatego w takim przypadku na końcu zawierające co najmniej dwa typy węzłów.
* Usługi (wchodzące w skład aplikacji), czy mają potrzeb różnych infrastruktury, takich jak więcej pamięci RAM lub cykle procesora CPU wyższa? Na przykład Daj nam założono, że aplikacja, którą chcesz wdrożyć zawiera usługi frontonu i zaplecza. Usługa frontonu, można uruchomić na mniejszych maszynach wirtualnych (rozmiary maszyn wirtualnych na przykład D2), które mają otwarte porty niezbędne do Internetu.  Usługi zaplecza, natomiast dużych obliczeń i musi uruchomić na większych maszyn wirtualnych (przy użyciu rozmiarów maszyn wirtualnych, takich jak D15 D4, D6,), nie będących internet skierowany.
  
  W tym przykładzie mimo że można zdecydować umieścić wszystkie usługi na jeden węzeł typu, zalecane jest umieszczenie ich w klastrze z dwóch typów węzłów.  Dzięki temu każdy typ węzła różne właściwości, takie jak łączność z Internetem lub rozmiar maszyny Wirtualnej. Liczba maszyn wirtualnych można skalować niezależnie, jak również.  
* Ponieważ nie można przewidzieć przyszłości, zawsze pod ręką fakty, które znasz i wybrać liczbę typów węzłów, których aplikacje muszą zaczynać. Zawsze można dodawać lub później usunąć typy węzłów. Klaster usługi Service Fabric musi mieć co najmniej jeden węzeł typu.

## <a name="the-properties-of-each-node-type"></a>Właściwości każdego typu węzła
**Typ węzła** może być traktowany jako równoważne do ról w usługach w chmurze. Typy węzłów definiują rozmiarów maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Każdy typ węzła, który jest zdefiniowany w klastrze usługi Service Fabric mapuje [zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Każdy typ węzła jest różne skalowania zestawu i może być skalowana w lub dół niezależnie od siebie, mają różne zestawy otwartych portów i mieć różne metryki pojemności. Aby uzyskać więcej informacji na temat relacji między typy węzłów i zestawy skalowania maszyn wirtualnych, jak nawiązać połączenie RDP z jednym z wystąpień, sposób otwierania nowych portów i tak, zobacz [typy węzłów klastra usługi Service Fabric](service-fabric-cluster-nodetypes.md).

Klaster usługi Service Fabric może zawierać więcej niż jeden typ węzła. W takim przypadku klaster składa się z jednego typu węzła podstawowego i jeden lub więcej typów węzłów innych niż podstawowe.

Typ jednego węzła nie niezawodne skalowanie ponad 100 węzłów na zestaw aplikacji SF; skalowania maszyn wirtualnych osiągnięcia niezawodnie większa niż 100 węzłów, będą wymagać Dodawanie dodatkowych zestawów skalowania.

### <a name="primary-node-type"></a>Typ węzła podstawowego

Usługi systemowe Service Fabric (na przykład usługa Menedżera klastra lub obraz Store service) są umieszczane na podstawowy typ węzła. 

![Zrzut ekranu przedstawiający klaster, który ma dwa typy węzłów][SystemServices]

* **Minimalny rozmiar maszyn wirtualnych** węzła podstawowego typu jest określona przez **warstwa trwałości** wybierzesz. Domyślna warstwa trwałości jest brązowa. Zobacz [charakterystyki trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Aby uzyskać więcej informacji.  
* **Minimalnej liczbie maszyn wirtualnych** węzła podstawowego typu jest określona przez **warstwy niezawodności** wybierzesz. Warstwa niezawodności domyślny jest Silver. Zobacz [charakterystyka niezawodności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) Aby uzyskać więcej informacji.  

Za pomocą szablonu usługi Azure Resource Manager, podstawowy typ węzła jest skonfigurowany przy użyciu `isPrimary` atrybutu w ramach [definicji typu węzła](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ węzła podstawowego bez

W klastrze z wieloma typami węzła jest jeden typ węzła podstawowego, a pozostałe są inne niż podstawowe.

* **Minimalny rozmiar maszyn wirtualnych** węzłów innych niż podstawowe typy jest określona przez **warstwa trwałości** wybierzesz. Domyślna warstwa trwałości jest brązowa. Aby uzyskać więcej informacji, zobacz [charakterystyki trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* **Minimalnej liczbie maszyn wirtualnych** typy węzłów innych niż podstawowe jest jednym. Jednakże należy wybrać tę liczbę, na podstawie liczby replik aplikacji/usługi, które mają być uruchamiane w tego typu węzła. Po wdrożeniu klastra można zwiększyć liczbę maszyn wirtualnych w typ węzła.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakterystyka niezawodności klastra
Warstwa trwałości jest używany do wskazania systemowi uprawnienia, których maszyn wirtualnych z podstawową infrastrukturą platformy Azure. W typie podstawowym węzłem to uprawnienie pozwala usłudze Service Fabric wstrzymać wszelkie wyświetlania żądania z poziomu infrastruktury maszyny Wirtualnej (na przykład ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej lub migracji maszyny Wirtualnej) mieć wpływ na wymagania dotyczące kworum dla usług systemu i usługi stanowej. Typy węzłów innych niż podstawowe to uprawnienie umożliwia usługi Service Fabric można wstrzymać maszyny Wirtualnej infrastruktury poziomu żądań (na przykład ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej i migracji maszyn wirtualnych), które wpływają na wymagania dotyczące kworum dla usług stanowych.

| Warstwa trwałości  | Wymagana minimalna liczba maszyn wirtualnych | Jednostek SKU obsługiwanych maszyn wirtualnych                                                                  | Aktualizacje wprowadzone w zestawie skalowania maszyn wirtualnych                               | Aktualizowanie i konserwowanie zainicjowane przez platformę Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Złoty             | 5                              | Jednostki SKU węzła pełnej dedykowanego pojedynczemu klientowi (na przykład L32s GS5, 5. generacji, DS15_v2, D15_v2) | Może być opóźniony, dopóki nie zatwierdzone przez klaster usługi Service Fabric | Może być wstrzymana przez 2 godziny na UD, aby umożliwić dodatkowy czas dla repliki, aby odzyskać sprawność po wcześniejszej awarii |
| Srebrny           | 5                              | Maszyny wirtualne lub pojedynczego rdzenia                                                        | Może być opóźniony, dopóki nie zatwierdzone przez klaster usługi Service Fabric | Nie może być opóźniona na dłuższy czas                                                    |
| Brązowy           | 1                              | Wszyscy                                                                                | Nie będą opóźnione przez klaster usługi Service Fabric           | Nie może być opóźniona na dłuższy czas                                                    |

> [!WARNING]
> Typy węzłów z trwałością brązowa uzyskać _brakiem uprawnień_. Oznacza to, że zadania infrastruktury, które wpływają na obciążeń bezstanowych będzie nie można zatrzymać lub opóźnione, co może mieć wpływ na Twoich obciążeń. Tylko brązowa na użytek typy węzłów, które są uruchamiane tylko na obciążeniach bezstanowych. W przypadku obciążeń produkcyjnych systemem Silver lub powyżej jest zalecane. 
> 
> Niezależnie od tego, dowolny poziom trwałości [dezalokacji](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) operacji na zestawie skalowania maszyn wirtualnych zniszcz klaster

**Korzyści wynikające z używania poziomów niezawodności na poziomie Silver lub Gold**
 
- Zmniejsza liczbę czynności w ramach operacji skalowania (czyli dezaktywacja węzła i Usuń ServiceFabricNodeState nosi nazwę automatycznie).
- Zmniejsza ryzyko utraty danych z powodu operacji Zmień inicjowane przez klienta w miejscu jednostki SKU maszyny Wirtualnej lub operacji infrastruktury platformy Azure.

**Wady używania poziomów niezawodności na poziomie Silver lub Gold**
 
- Wdrożeń w celu skalowania maszyn wirtualnych ustawione i inne powiązane zasoby platformy Azure może być opóźniony, przekroczyło limit czasu można i może zostać zablokowany w całości na problemy w klastrze lub na poziomie infrastruktury. 
- Zwiększa liczbę [zdarzenia cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (na przykład podstawowego wymiany) ze względu na automatyczne deactivations węzła podczas wykonywania operacji w infrastrukturze platformy Azure.
- Pobiera węzły na zewnątrz usługi przez czas podczas aktualizacji oprogramowania platformy Azure lub konserwacji sprzętu, które są wykonywane działania. Węzły o stanie wyłączona lub wyłączenie mogą pojawić się podczas tych działań. Tymczasowo zmniejsza pojemność klastra, ale nie wpływa na dostępność klastra lub aplikacji.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Zalecenia dotyczące używania poziomów niezawodności na poziomie Silver lub Gold

Użyj niezawodności na poziomie Silver lub Gold dla wszystkich typów węzłów hostujących usługi stanowe spodziewasz się skalowania na zewnątrz (zmniejszyć liczbę wystąpień maszyn wirtualnych) często i chcesz użyć, czy operacje wdrażania zostać opóźnione i pojemność obniżenie na rzecz uproszczenie tych skalowania w operacje. Scenariusze skalowalnego w poziomie (Dodawanie wystąpień maszyn wirtualnych) nie są odtwarzane w dowolnie warstwa trwałości, tylko skalowania w jest.

### <a name="changing-durability-levels"></a>Zmiana poziomów trwałości
- Typy węzłów z poziomu niezawodności na poziomie Silver lub Gold, nie można obniżyć na brązowy.
- Uaktualnianie z brązowa na poziomie Silver lub Gold może potrwać kilka godzin.
- Zmieniając poziom trwałości, pamiętaj zaktualizować go w obu usługi Service Fabric konfiguracji rozszerzenia w zasobu zestawu skalowania maszyny wirtualnej, a w definicji typu węzła w zasobie klastra usługi Service Fabric. Te wartości muszą być zgodne.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Operational zalecenia dla węzła typu, że zostało ustawione na trwałości silver lub gold poziomu.

- Zachowuj klastra i aplikacje dobrej kondycji przez cały czas i upewnij się, że aplikacje będą odpowiadać na wszystkich [obsługi zdarzeń cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. replika w kompilacji jest zablokowana) w odpowiednim czasie.
- Przyjęcie bezpieczniejsze sposobów na jednostki SKU maszyny Wirtualnej zmiany (skalowania w górę/w dół): Zmiana jednostki SKU maszyny Wirtualnej zestawu skalowania maszyn wirtualnych jest operacją niebezpieczne i dlatego należy unikać, jeśli jest to możliwe. Poniżej przedstawiono ten proces można wykonać, aby uniknąć typowych problemów.
    - **Dla typów innych niż podstawowe węzła:** Zalecane jest, możesz utworzyć nowego zestawu skalowania maszyn wirtualnych, Modyfikowanie ograniczenia do uwzględnienia nowego typu węzeł/zestaw skali maszyny wirtualnej, a następnie ograniczyć stare maszyn wirtualnych skalowania zestawu liczbę wystąpień na zero, jeden węzeł w danym momencie (to jest zapewnienie na umieszczania usługi Czy usunięcie węzłów wpłynąć na niezawodność klastra).
    - **Dla typu węzła podstawowego:** Nasze zalecenie, to nie należy zmieniać jednostki SKU maszyny Wirtualnej typu węzła podstawowego. Zmiana typu węzła podstawowego, który jednostka SKU nie jest obsługiwana. Jeśli przyczyna nowej jednostki SKU pojemności, zaleca się dodanie większej liczby wystąpień. Jeśli to nie jest to możliwe, Utwórz nowy klaster i [Przywróć stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie należy przywrócić wszystkie stanu usługi systemu, zostaną ponownie utworzone podczas wdrażania aplikacji do nowego klastra. Jeśli używasz aplikacji bezstanowych w klastrze, należy wdrożyć aplikacji do nowego klastra.  Masz nie ma niczego do przywrócenia. Jeśli zdecydujesz się przejść nieobsługiwany trasy i chcesz zmienić jednostki SKU maszyny Wirtualnej, następnie modyfikacje upewnij skalowania maszyn wirtualnych Ustaw definicję modelu w celu odzwierciedlenia nowej jednostki SKU. Jeśli klaster zawiera tylko jeden węzeł typu, upewnij się, że wszystkie aplikacje stanowe będą odpowiadać na wszystkich [obsługi zdarzeń cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. replika w kompilacji jest zablokowana) w odpowiednim czasie, który repliki usługi ponownie skompiluj czas trwania wynosi mniej niż pięciu minut (poziom trwałości Silver). 
    
- Obsługa minimalna liczba pięć węzłów do zestawu skalowania maszyn wirtualnych, który ma poziom trwałości Gold i Silver włączone.
- Każdy zestaw skalowania maszyn wirtualnych z poziomu niezawodności na poziomie Silver lub Gold musi być mapowane na swój własny typ węzła w klastrze usługi Service Fabric. Mapowanie wielu zestawów skalowania maszyn wirtualnych do jednego węzła typu uniemożliwi koordynacji między klastrem usługi Service Fabric i infrastruktury platformy Azure działa prawidłowo.
- Usuń losowe wystąpień maszyn wirtualnych, nie zawsze używaj skalowania zestawu skalowania maszyn wirtualnych szczegółów funkcji. Usuwanie losowe wystąpień maszyn wirtualnych ma możliwość tworzenia nierównowagi w wystąpieniu maszyny Wirtualnej, rozkładają się na UD i FD. Ten brak równowagi może negatywnie wpłynąć na możliwość systemów poprawnie załadować saldo między repliki wystąpienia/usługi service.
- W przypadku korzystania ze skalowania automatycznego, następnie ustawić reguły taki sposób, że skalowanie do wewnątrz (usuwania wystąpień maszyn wirtualnych) są wykonywane tylko jeden węzeł w danym momencie. Skalowanie w dół więcej niż jedno wystąpienie w danym momencie nie jest bezpieczne.
- Usuwanie lub cofnięcie przydziału maszyny wirtualne na podstawowy typ węzła, nigdy nie należy zmniejszyć liczbę maszyn wirtualnych z alokacją poniżej wymaga warstwy niezawodności. Te operacje będą blokowane przez czas nieokreślony w zestaw skalowania z poziomu niezawodności na poziomie Silver lub Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Charakterystyka niezawodności klastra
Aby ustawić liczbę replik usług systemowych, które mają być uruchamiane w tym klastrze na podstawowy typ węzła jest używana warstwa niezawodności. Zwiększenie liczby replik, bardziej niezawodne usługi systemowe znajdują się w klastrze.  

Warstwa niezawodności może przyjmować następujące wartości:

* Platinum - uruchamiania usług systemu z obiektem docelowym elementu zestawu replik liczba z 7
* Złoty - uruchamiania usług systemu z obiektem docelowym elementu zestawu replik liczba z 7
* Silver - uruchamiania usług systemu z obiektem docelowym elementu zestawu replik łącznej liczby 5 
* Brązowy - uruchamiania usług systemu z obiektem docelowym elementu zestawu replik trzykrotnego

> [!NOTE]
> Warstwa niezawodności, które wybierzesz określa minimalna liczba węzłów, który musi być typu węzła podstawowego. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Zalecenia dotyczące warstwy niezawodności

Zwiększ lub Zmniejsz rozmiar klastra (suma wystąpień maszyn wirtualnych we wszystkich typach węzła), należy zaktualizować niezawodności klastra z jednej warstwy do innej. W ten sposób jest wyzwalana Uaktualnianie klastra potrzebne zmiany repliki usług systemu ustaw liczbę. Poczekaj, aż do uaktualnienia w toku, które należy wykonać przed wprowadzeniem jakichkolwiek innych zmian w klastrze, takich jak dodawanie węzłów.  Możesz monitorować postępy uaktualniania w narzędziu Service Fabric Explorer lub uruchamiając [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Poniżej przedstawiono zalecenia na temat wybierania warstwy niezawodności.  Liczba węzłów inicjatora jest również ustawiona na minimalna liczba węzłów dla warstwy niezawodności.  Na przykład w przypadku klastra z niezawodnością Gold istnieją 7 węzły obiektów początkowych.

| **Liczba węzłów klastra** | **Warstwa niezawodności** |
| --- | --- |
| 1 |Nie określaj parametru warstwy niezawodności, system oblicza wartość |
| 3 |Brązowy |
| 5 lub 6|Srebrny |
| 7 lub 8 |Złoty |
| 9 i nowsze |Platynowy |

## <a name="primary-node-type---capacity-guidance"></a>Typ węzła podstawowego — wskazówki dotyczące wydajności

Poniżej przedstawiono wskazówki dotyczące planowania pojemności typu węzła podstawowego:

- **Liczba wystąpień maszyn wirtualnych do uruchamiania dowolnych obciążeń produkcyjnych na platformie Azure:** Należy określić minimalny rozmiar typu węzła podstawowego 5 i warstwy niezawodności Silver.  
- **Liczba wystąpień maszyn wirtualnych do uruchamiania testów obciążenia na platformie Azure** można określić rozmiar typu węzła podstawowego minimalnej 1 i 3. Jeden węzeł klastra, jest uruchamiana przy użyciu specjalnej konfiguracji, a tym samym, skalowanie poza tego klastra nie jest obsługiwane. Jeden węzeł klastra, ma nie niezawodności, a więc w szablonie usługi Resource Manager, należy usunąć/nie określić tę konfigurację (to ustawienie nie zostanie wartość konfiguracji nie jest wystarczająco dużo). Jeśli jeden węzeł klastra, skonfigurować za pośrednictwem portalu zostanie skonfigurowany, następnie konfiguracji są automatycznie wykonywane. Jednego do trzech klastrów nie są obsługiwane dla obciążeń produkcyjnych. 
- **JEDNOSTKA SKU MASZYNY WIRTUALNEJ:** Typu węzła podstawowego jest gdzie uruchamiania usług systemowych, więc jednostki SKU maszyny Wirtualnej, możesz wybrać, musi take pod uwagę ogólną szczytowego obciążenia należy zaplanować umieścić w klastrze. W tym miejscu jest odpowiednio do zilustrowania co mam na myśli tutaj — traktować typu węzła podstawowego jako swojej "płuca", jest to, co zapewnia tlenu do Twoich myśli, a więc jeśli mózg nie uzyska wystarczającą ilość tlenu, niska treści wywołania. 

Ponieważ wymagana pojemność klastra jest określana przez obciążenie, które mają być uruchamiane w klastrze, firma Microsoft nie może zapewnić jakościowe wskazówki dla określonego obciążenia, jednak Oto szerokiego wskazówek ułatwiających rozpoczęcie

W przypadku obciążeń produkcyjnych: 

- Zaleca się przydzielenie dedykowanego klastrów podstawowego elementu NodeType usług systemowych i ograniczeniami dotyczącymi umieszczania Użyj wdrożyć aplikację do dodatkowej elementy NodeType.
- Zalecane jednostki SKU maszyny Wirtualnej jest standardowa D3 lub standardowa D3_V2 lub równoważny z co najmniej 14 GB lokalny dysk SSD.
- Minimalne użycie obsługiwane jednostki SKU maszyny Wirtualnej jest standardowa D1 lub D1_V2 standardowy lub równoważny z co najmniej 14 GB lokalny dysk SSD. 
- Lokalny dysk SSD 14 GB stanowi wymaganie minimalne. Nasze zalecenie jest co najmniej 50 GB. Dla obciążeń, szczególnie podczas korzystania z kontenerów Windows większe dyski są wymagane. 
- Częściowe podstawowych wersji SKU maszyn wirtualnych, takich jak standard_a0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowa A1 jednostka SKU nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.
- Maszyny wirtualne o niskim priorytecie nie są obsługiwane.

> [!WARNING]
> Zmiana węzła podstawowego rozmiar jednostki SKU maszyny Wirtualnej w klastrze uruchomiona operacja skalowania jest i udokumentowane w artykule [zestawu skalowania maszyn wirtualnych skalowania w poziomie](virtual-machine-scale-set-scale-node-type-scale-out.md) dokumentacji.

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ węzła podstawowego bez — wskazówki dotyczące wydajności, w przypadku obciążeń stanowych

Ten przewodnik jest przeznaczony dla obciążeń stanowych przy użyciu usługi Service fabric [elementów reliable collections lub elementów reliable Actors](service-fabric-choose-framework.md) uruchomionym typu innego niż podstawowy węzła.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są stanowe zalecane jest, uruchamiaj je przy użyciu minimalnej i docelowej liczby replik 5. Oznacza to, że w stanie stabilności na końcu repliki (z zestawu replik) w każdej domenie błędów i domeny uaktualnień. Pojęcie warstwy niezawodności całego dla typu węzła podstawowego jest sposobem określania tego ustawienia dla usług systemowych. Dlatego takie samo założenie ma zastosowanie do usług stanowych w także.

Tak w przypadku obciążeń produkcyjnych minimalna zalecana innych niż - węzła podstawowego typu rozmiar jest 5, jeśli są to obciążenia stanowe są uruchomione w nim.

**JEDNOSTKA SKU MASZYNY WIRTUALNEJ:** Jest to typ węzła których są uruchomione usługi aplikacji, aby jednostki SKU maszyny Wirtualnej możesz wybrać, należy wziąć pod uwagę szczytowego obciążenia, które planujesz umieszczenie w każdym węźle. Wymagana pojemność typu węzła, zależy od obciążenia, które mają być uruchamiane w klastrze, więc nie możemy zapewnić, że jakościowe wskazówki dla określonego obciążenia, jednak Oto szerokiego wskazówek ułatwiających rozpoczęcie

W przypadku obciążeń produkcyjnych 

- Zalecane jednostki SKU maszyny Wirtualnej jest standardowa D3 lub standardowa D3_V2 lub równoważny z co najmniej 14 GB lokalny dysk SSD.
- Minimalne użycie obsługiwane jednostki SKU maszyny Wirtualnej jest standardowa D1 lub D1_V2 standardowy lub równoważny z co najmniej 14 GB lokalny dysk SSD. 
- Częściowe podstawowych wersji SKU maszyn wirtualnych, takich jak standard_a0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowa A1 jednostka SKU nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ węzła podstawowego bez — wskazówki dotyczące wydajności, w przypadku obciążeń bezstanowych

Niniejsze wskazówki bezstanowych obciążeń uruchomionych na typ węzła innego niż podstawowy.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są bezstanowe rozmiar typu minimalne obsługiwane bez - węzła podstawowego to 2. Dzięki temu można przeprowadzić dwa wystąpienia bezstanowe aplikacji, i zezwolenie usłudze przetrwać utraty wystąpienia maszyny Wirtualnej. 

**JEDNOSTKA SKU MASZYNY WIRTUALNEJ:** Jest to typ węzła których są uruchomione usługi aplikacji, aby jednostki SKU maszyny Wirtualnej możesz wybrać, należy wziąć pod uwagę szczytowego obciążenia, które planujesz umieszczenie w każdym węźle. Wymagana pojemność typu węzła jest określany przez obciążenie, które mają być uruchamiane w klastrze. Firma Microsoft nie udostępnia użytkownikowi jakościowe wskazówki dotyczące określonego obciążenia.  Jednak w tym miejscu jest szeroka wskazówki, aby pomóc Ci rozpocząć pracę.

W przypadku obciążeń produkcyjnych 

- Zalecane jednostki SKU maszyny Wirtualnej jest standardowa D3 lub standardowa D3_V2 lub równoważnej. 
- Minimalne użycie obsługiwane jednostki SKU maszyny Wirtualnej jest standardowa D1 lub standardowa D1_V2 lub równoważnej. 
- Częściowe podstawowych wersji SKU maszyn wirtualnych, takich jak standard_a0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowa A1 jednostka SKU nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Kolejne kroki
Zakończ, planowania pojemności i skonfigurować klaster, przeczytaj następujące:

* [Zabezpieczenia klastra usługi Service Fabric](service-fabric-cluster-security.md)
* [Skalowanie klastra usługi Service Fabric](service-fabric-cluster-scaling.md)
* [Planowanie odzyskiwania po awarii](service-fabric-disaster-recovery.md)
* [Ustaw relację elementy NodeType skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
