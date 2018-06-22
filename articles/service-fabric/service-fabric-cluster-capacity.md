---
title: Planowanie pojemności klastra usługi sieć szkieletowa | Dokumentacja firmy Microsoft
description: Zagadnienia związane z planowaniem pojemności klastra sieci szkieletowej usług. Elementów NodeType, operacje, trwałości i niezawodności warstw
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 4c584f4a-cb1f-400c-b61f-1f797f11c982
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: chackdan
ms.openlocfilehash: a5046a5e3771e95d76bb6edc7987a1e3176abeb0
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309419"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Zagadnienia związane z planowaniem pojemności klastra sieci szkieletowej usług
Wszystkie wdrożenia produkcyjnego planowania pojemności jest ważnym krokiem. Poniżej przedstawiono niektóre elementy, które należy wziąć pod uwagę w ramach tego procesu.

* Liczba typów węzeł klastra musi zaczynać
* Właściwości każdego typu węzła (rozmiar, podstawową, internetowy, liczba maszyn wirtualnych itd.)
* Charakterystyka niezawodności i trwałości klastra

Daj nam krótko Sprawdź każdy z tych elementów.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Liczba typów węzeł klastra musi zaczynać
Najpierw należy ustalić klastra, które tworzysz przechodzi do zastosowania w przypadku.  Jakiego rodzaju aplikacje, które zamierzasz wdrożyć do tego klastra? Jeśli nie jest jasne celowo klastra, możesz najprawdopodobniej nie została jeszcze gotowy do wprowadź wydajność procesu planowania.

Ustal liczbę typy węzeł klastra musi zaczynać.  Każdy typ węzła jest mapowana na zestaw skali maszyny wirtualnej. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Tak decyzji liczba typów węzła zasadniczo zawiera przedstawione poniżej zagadnienia:

* Aplikacja ma wiele usług i ich wymaga jednak publicznego lub Internetem? Typowe aplikacje zawierają usługi frontonu bramy, który odbiera dane wejściowe z klienta i co najmniej jeden zaplecza usług, które komunikują się z usługi frontonu. Dlatego w tym przypadku na końcu mających co najmniej dwa typy węzłów.
* Usługi (wchodzące w skład aplikacji), czy mają potrzeb różnych infrastruktury, takich większa ilość pamięci RAM lub wyższej cykli Procesora? Na przykład Poinformuj nas założono, że aplikacja, która ma zostać wdrożona zawiera usługi frontonu i zaplecza. Usługi frontonu może działać na mniejsze maszyn wirtualnych (na przykład D2 rozmiarów maszyn wirtualnych), które mają porty niezbędne do Internetu.  Usługi zaplecza, jednak jest wymagające obliczenia i do uruchamiania na większych maszyn wirtualnych (o rozmiarów maszyn wirtualnych, takie jak D15 D4, D6,), które nie są internet skierowane.
  
  W tym przykładzie Chociaż można wybrać typu jeden węzeł, umieść wszystkie usługi zaleca się umieszczenie ich w klastrze dwa typy węzłów.  Dzięki temu każdy typ węzła mają różne właściwości, takie jak łączność z Internetem lub rozmiar maszyny Wirtualnej. Liczbę maszyn wirtualnych mogą być skalowane niezależnie, jak również.  
* Ponieważ nie można przewidzieć przyszłe, przejdź z faktów, które znasz i wybierz liczbę typy węzłów, które aplikacje muszą rozpoczynać się nazwą. Można zawsze dodawać i usuwać typy węzłów później. Klaster sieci szkieletowej usług musi mieć co najmniej jeden węzeł typu.

## <a name="the-properties-of-each-node-type"></a>Właściwości każdego typu węzła
**Typu węzła** są widoczne jako odpowiednik ról usług w chmurze. Typy węzłów zdefiniować rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Mapuje każdego typu węzła, który jest zdefiniowany w klastrze usługi sieć szkieletowa [zestaw skali maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Każdy typ węzła jest różne skali ustawić i można skalować w lub w dół niezależnie, mają różne zestawy otwartych portów i metryki pojemności różnych. Aby uzyskać więcej informacji na temat relacji między typami węzła i zestawy skalowania maszyny wirtualnej, jak dla protokołu RDP w jedno wystąpienie, sposobu otwierania nowych portów i tak dalej, zobacz [typy węzłów klastra usługi sieć szkieletowa](service-fabric-cluster-nodetypes.md).

Klastra usługi sieć szkieletowa może składać się z więcej niż jednego typu węzła. W takim przypadku klaster składa się z jednego typu węzła podstawowego i co najmniej jeden typ węzła innej niż podstawowa.

Typ jednego węzła po prostu nie może przekraczać 100 węzłów na zestaw skali maszyny wirtualnej. Należy dodać do osiągnięcia docelowych skali zestawach skali maszyn wirtualnych i skalowania automatycznego nie automagically dodać zestawy skalowania maszyny wirtualnej. Dodanie zestawach skali maszyn wirtualnych w miejscu do klastra na żywo jest trudne zadań i często skutkuje inicjowania obsługi administracyjnej nowych klastrów z typami odpowiedni węzeł udostępniane w czasie tworzenia użytkowników. 

### <a name="primary-node-type"></a>Typ węzła podstawowego

Usługi systemowe platformy Service Fabric (na przykład usługa Menedżera klastra lub usługi składnika Image Store) są umieszczane na typ węzła podstawowego. 

![Zrzut ekranu przedstawiający klastra, która ma dwa typy węzłów][SystemServices]

* **Minimalny rozmiar maszyn wirtualnych** węzeł podstawowy typ jest określany przez **warstwa trwałości** wybierzesz. Warstwa trwałości domyślny jest brązowa. Zobacz [właściwości trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) więcej szczegółów.  
* **Minimalną liczbę maszyn wirtualnych** węzeł podstawowy typ jest określany przez **warstwa niezawodności** wybierzesz. Warstwa niezawodności domyślny jest Silver. Zobacz [właściwości niezawodności klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) więcej szczegółów.  

Za pomocą szablonu usługi Azure Resource Manager skonfigurowano typ węzła podstawowego `isPrimary` atrybutu w obszarze [definicji typu węzła](https://docs.microsoft.com/en-us/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ podstawowy nie węzła

W klastrze z wieloma typami węzła jest jeden typ węzła podstawowego i pozostałe są innych niż podstawowe.

* **Minimalny rozmiar maszyn wirtualnych** węzła innych niż podstawowe typy jest określany przez **warstwa trwałości** wybierzesz. Warstwa trwałości domyślny jest brązowa. Zobacz [właściwości trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) więcej szczegółów.  
* **Minimalną liczbę maszyn wirtualnych** dla typów innych niż podstawowe węzła jest jednym. Jednak należy wybrać ten numer, na podstawie liczby replik aplikacji/usługi, które mają być uruchamiane w tym typem węzła. Po wdrożeniu klastra można zwiększyć liczbę maszyn wirtualnych w typu węzła.

## <a name="the-durability-characteristics-of-the-cluster"></a>Właściwości trwałości klastra
Warstwa trwałości jest służy do wskazania systemowi uprawnienia, które mają maszyn wirtualnych z podstawowej infrastruktury platformy Azure. W polu Typ węzła podstawowego to uprawnienie umożliwia sieci szkieletowej usług wstrzymać wszystkie maszyny Wirtualnej infrastruktury poziomu żądania (takie jak ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej lub migracja maszyny Wirtualnej) wpływ kworum wymagania dotyczące usługi systemowe i stanowych usług. W typach węzła innego niż podstawowy to uprawnienie umożliwia sieci szkieletowej usług wstrzymać maszyny Wirtualnej infrastruktury poziomu żądań (takie jak ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej i migracji maszyn wirtualnych) wpływające na wymagania dotyczące kworum dla stanowych usług.

| Warstwa trwałości  | Wymagana minimalna liczba maszyn wirtualnych | Jednostki SKU obsługiwanej maszynie Wirtualnej                                                                  | Aktualizacje, które należy do Twojej VMSS                               | Aktualizacje i obsługa inicjowane przez platformę Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Złoty             | 5                              | Węzeł pełnej wersji produktu w wersji dedykowanej jednego odbiorcy (na przykład L32s, GS5, G5, DS15_v2, D15_v2) | Mogą być opóźnione aż zatwierdzone przez klaster sieci szkieletowej usług | Można wstrzymać przez 2 godziny na UD, aby umożliwić dodatkowego czasu repliki pozwoli na odzyskanie wcześniejszych błędów |
| Srebrny           | 5                              | Maszyny wirtualne lub pojedynczego rdzenia                                                        | Mogą być opóźnione aż zatwierdzone przez klaster sieci szkieletowej usług | Nie można opóźnić na dłuższy czas                                                    |
| Brązowy           | 1                              | Wszyscy                                                                                | Nie zostanie opóźniony o klastra sieci szkieletowej usług           | Nie można opóźnić na dłuższy czas                                                    |

> [!WARNING]
> Typy węzłów z trwałości brązowa uzyskać _żadnych uprawnień_. Oznacza to, że zadania infrastruktury, które mają wpływ na obciążeń bezstanowych nie zostanie zatrzymana ani opóźnione, który może mieć wpływ obciążeń. Użyj brązowy tylko dla typów węzłów, które są uruchamiane tylko bezstanowe. W przypadku obciążeń produkcyjnych uruchomiona Silver lub powyżej jest zalecane. 
>

**Korzyści wynikające z używania Silver lub złota poziom trwałości**
 
- Zmniejsza liczbę czynności w ramach operacji skalowania (to znaczy dezaktywacji węzła i Usuń ServiceFabricNodeState jest wywoływana automatycznie).
- Zmniejsza ryzyko utraty danych z powodu operacji Zmień inicjowane przez klienta w miejscu SKU maszyny Wirtualnej lub operacji infrastruktury platformy Azure.

**Wady używania Silver lub złota poziom trwałości**
 
- Wdrożenia z skalowania maszyny wirtualnej ustawić i innych powiązanych zasobów platformy Azure mogą być opóźnione, można przekroczyło limit czasu lub mogą zostać zablokowane całkowicie przez problemy w klastrze lub na poziomie infrastruktury. 
- Zwiększa liczbę [zdarzenia cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (na przykład podstawowego zamiany) ze względu na automatyczne deactivations węzła podczas operacji infrastruktury platformy Azure.
- Pobiera węzły poza usługi dla okresów podczas aktualizacji oprogramowania platformy Azure lub konserwacji sprzętu, które są wykonywane działania. Węzły o stanie wyłączona lub wyłączenie może pojawić się podczas tych czynności. Tymczasowo zmniejsza pojemność klastra, ale nie powinny mieć wpływ na dostępność klastra lub aplikacji.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Zalecenia dotyczące użycie Silver lub złota poziom trwałości

Użyj trwałości Silver lub Gold dla wszystkich typów węzłów zawierających usługi stanowej spodziewasz się w skali (zmniejszyć liczbę wystąpień maszyny Wirtualnej) często, i chcesz użyć opóźniony operacje wdrażania i pojemności należy zmniejszyć na rzecz uproszczenia te skalowania w operacje. Scenariusze skalowalnego w poziomie (Dodawanie wystąpień maszyn wirtualnych) nie są odtwarzane w wybranym warstwa trwałości, tylko skali w jest.

### <a name="changing-durability-levels"></a>Zmienianie poziomów trwałości
- Typy węzłów o poziomie trwałości Silver lub złota nie można zmienić na brązowa.
- Uaktualnianie z brązowa Silver lub złota może zająć kilka godzin.
- Zmieniając poziom trwałości, należy zaktualizować je w obu sieci szkieletowej usług Konfiguracja rozszerzenia w zasobie zestaw skali maszyny wirtualnej, a w definicji typu węzła w zasobie klastra sieci szkieletowej usług. Te wartości muszą być zgodne.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Operacyjne zalecenia dla węzła typu, że wybrano trwałości silver lub gold poziomu.

- Zachowaj klastra, a aplikacje dobrej kondycji przez cały czas i upewnij się, że aplikacje odpowiadanie na wszystkie [usługi zdarzenia cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. repliki w kompilacji jest zablokowana) w odpowiednim czasie.
- Przyjmuje bezpieczniejsze sposoby zmiany jednostki SKU maszyny Wirtualnej (skalowanie w górę/dół): Zmiana jednostki SKU maszyny Wirtualnej zestawu skali maszyny wirtualnej jest z założenia niebezpieczna operacja i dlatego należy unikać Jeśli to możliwe. Oto proces można wykonać w celu uniknięcia typowych problemów.
    - **Dla typów innych niż podstawowe węzłów:** zalecane jest, aby utworzyć nowy zestaw skali maszyny wirtualnej, zmodyfikuj ograniczenia umieszczania usług do uwzględnienia nowego typu węzeł/zestaw skali maszyny wirtualnej, a następnie zmniejsz starego wystąpienia zestawu skali maszyny wirtualnej liczba 0, w jednym węźle naraz (jest to aby upewnić się, że usuwania węzłów nie wpływają na niezawodność klastra).
    - **Dla typu węzła podstawowego:** firma Microsoft zaleca, nie należy zmieniać SKU wirtualna tego typu węzła podstawowego. Zmiana typu węzła podstawowego, jednostka SKU nie jest obsługiwane. Jeśli przyczyna dla nowej jednostki SKU pojemności, zaleca się dodawania więcej wystąpień. Jeśli nie jest możliwe, Utwórz nowy klaster i [Przywróć stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie należy przywrócić wszystkie stanu usługi systemu, zostaną ponownie utworzone podczas wdrażania aplikacji do nowego klastra. Jeśli właśnie uruchomionych aplikacji bezstanowych w klastrze, a następnie wszystko, co możesz zrobić to wdrażanie aplikacji do nowego klastra, nie niczego do przywrócenia. Jeśli zdecydujesz się przejść nieobsługiwany trasy i chcesz zmienić jednostki SKU maszyny Wirtualnej, następnie utwórz modyfikacje skali maszyny wirtualnej ustawić definicję modelu w celu odzwierciedlenia nowej jednostki SKU. Jeśli klaster ma tylko jeden węzeł typu, następnie upewnij się, że wszystkie aplikacje stanowe odpowiadanie na wszystkie [usługi zdarzenia cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. repliki w kompilacji jest zablokowana) w odpowiednim czasie, który repliki usługi odbudować czas trwania jest mniej niż pięć minut (poziom trwałości srebrny). 

    > [!WARNING]
    > Zmienia rozmiar jednostki SKU maszyny Wirtualnej dla zestawy skalowania maszyny wirtualnej, który nie działa przynajmniej srebrny trwałości nie jest zalecane. Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjnego danych w miejscu infrastruktury. Co najmniej możliwość opóźnienia lub monitora tę zmianę może to oznaczać, że operacji może spowodować utratę danych dla stanowych usług lub spowodować innych nieprzewidzianych problemów z działaniem, nawet w przypadku obciążeń bezstanowych. 
    > 
    
- Obsługa minimalna liczba pięć węzłów do zestawu skali maszyny wirtualnej, który ma poziom trwałości Gold lub Silver włączone.
- Każdy zestaw poziom trwałości Silver lub Gold skalowania maszyny Wirtualnej musi być zamapowany własny typ węzła w klastrze usługi sieć szkieletowa usług. Mapowanie wiele maszyn wirtualnych zestawy skalowania do jednego węzła typu uniemożliwi koordynację klastra sieci szkieletowej usług i infrastruktury platformy Azure działa prawidłowo.
- Usunąć losowe wystąpień maszyn wirtualnych, nie używaj skalowania zestaw skali maszyny wirtualnej w dół funkcji. Usuwanie losowe wystąpień maszyny Wirtualnej ma możliwości nierówności w ich rozmieszczenie UD i FD wystąpienie maszyny Wirtualnej. Ta niezgodność może negatywnie wpłynąć na możliwość systemów poprawnie załadować równowagi między replik serwisu wystąpień usługi.
- Przy użyciu automatycznego skalowania, następnie reguły tak ustawić skali w (usuwanie wystąpień maszyny Wirtualnej) są wykonywane tylko jeden węzeł naraz. W czasie skalowania więcej niż jedno wystąpienie nie jest bezpieczne.
- Usuwanie lub cofanie przydziału maszyn wirtualnych dla typu węzła podstawowego, można nigdy nie zmniejszyć liczbę przydzielone maszyn wirtualnych poniżej warstwa niezawodności wymaga. Te operacje będą blokowane przez nieokreślony czas w skali ustawić z poziomu trwałości Silver lub Gold.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Właściwości niezawodności klastra
Warstwa niezawodności umożliwia określenie liczby replikami usług systemowych, które mają być uruchamiane w tym klastrze typu węzła podstawowego. Zwiększenie liczby replik, bardziej niezawodne usługi systemowe są w klastrze.  

Warstwa niezawodności może przyjmować następujące wartości:

* Platynowa - uruchamiania usług systemowych z obiektu docelowego zestawu replik liczby 9
* Złota - uruchamiania usług systemowych z elementem docelowym liczba siedmiu zestawu replik
* Srebrny - uruchamiania usług systemowych zestawu z elementem docelowym liczba pięciu replik 
* Brązowy - uruchamiania usług systemowych zestawu z elementem docelowym trzy liczby replik

> [!NOTE]
> Warstwa niezawodności, którą wybierzesz określa minimalną liczbę węzłów, które muszą mieć typu węzła podstawowego. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Zalecenia dotyczące warstwy niezawodności

W przypadku można zwiększyć lub zmniejszyć rozmiar klastra (suma wystąpień maszyn wirtualnych we wszystkich typach węzła), należy zaktualizować niezawodności klastra z jedną warstwę do innego. W ten sposób wyzwala uaktualnienia klastra konieczne zmiany repliki usługi systemu ustaw liczbę. Poczekaj, aż uaktualnianie w toku, aby zakończyć przed wprowadzeniem jakichkolwiek innych zmian do klastra, takich jak dodawanie węzłów.  Można monitorować postępy uaktualniania w narzędziu Service Fabric Explorer lub uruchamiając [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Poniżej przedstawiono zalecenia o wyborze warstwa niezawodności.

| **Rozmiar klastra** | **Warstwa niezawodności** |
| --- | --- |
| 1 |Nie określaj parametru warstwa niezawodności system oblicza ona |
| 3 |Brązowy |
| 5 lub 6|Srebrny |
| 7 lub 8 |Złoty |
| 9 i nowsze |Platynowa |

## <a name="primary-node-type---capacity-guidance"></a>Typ węzła podstawowego — wskazówki dotyczące wydajności

Poniżej przedstawiono wskazówki dotyczące planowania pojemności typu węzła podstawowego:

- **Liczba wystąpień maszyn wirtualnych do uruchomienia żadnych obciążeń produkcyjnych na platformie Azure:** należy określić minimalny rozmiar typu węzła podstawowego 5. 
- **Liczba wystąpień maszyn wirtualnych do uruchamiania testów obciążenia w Azure** można określić rozmiar typu węzła podstawowego minimalną 1 lub 3. Jeden węzeł klastra, uruchamia się przy użyciu specjalnej konfiguracji i dlatego, skalowania poza tego klastra nie jest obsługiwane. Jeden węzeł klastra, nie ma żadnych niezawodności, więc w szablonie usługi Resource Manager można do usuwania nie określenia tej konfiguracji (to ustawienie nie zostanie wartość konfiguracji nie jest wystarczająco dużo). Skonfigurowanie jednego węzła klastra skonfigurowane za pomocą portalu następnie konfiguracja jest automatycznie podjęte obsługę. Jednej do trzech klastrów nie są obsługiwane do uruchamiania obciążeń produkcyjnych. 
- **Maszyna wirtualna SKU:** typu węzła podstawowego jest gdzie uruchamiania usług systemowych, dlatego planowanie SKU maszyny Wirtualnej, możesz wybrać, musi uwzględniać konta ogólną szczytowego obciążenia można umieścić w klastrze. W tym miejscu jest odpowiednio do zilustrowania I znaczenie w tym miejscu — traktować typu węzła podstawowego jako sieci "płuca", jest to, co zapewnia tlenu do Twojej inteligencji, a więc jeśli inteligencji nie można uzyskać wystarczającą ilość tlenu, wystąpi treść. 

Ponieważ wymagana pojemność klastra zależy od obciążenia, który ma zostać uruchomiona w klastrze, firma Microsoft nie udostępnia jakościowe wskazówek dla określonego obciążenia, jednak w tym miejscu jest szerokie wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych: 

- Zalecaną WERSJĘ maszyny Wirtualnej jest standardowe D3 lub standardowy D3_V2 lub równoważne z co najmniej 14 GB lokalny dysk SSD.
- Minimalne obsługiwane użycie SKU maszyny Wirtualnej jest standardowe D1 lub standardowy D1_V2 lub równoważne z co najmniej 14 GB lokalny dysk SSD. 
- Częściowe podstawowej jednostki SKU maszyny Wirtualnej, podobnie jak standardowy A0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowy SKU A1 nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.

> [!WARNING]
> Zmiana rozmiar jednostki SKU maszyny Wirtualnej w klastrze uruchomionych w węźle podstawowym nie jest obecnie obsługiwana. Tak wybierz węzeł podstawowy typ jednostki SKU maszyny Wirtualnej dokładnie, biorąc pod uwagę przyszłych potrzeb pojemności. W tej chwili, jedynym sposobem obsługiwanych, aby przenieść typu węzła podstawowego do nowej wersji maszyny Wirtualnej (lub zmniejszyć rozmiar) jest utworzenie nowego klastra z prawej pojemnością, wdrażanie aplikacji go, a następnie przywróceniu stanu aplikacji (jeśli dotyczy) z [ Najnowsza wersja usługi tworzenia kopii zapasowych](service-fabric-reliable-services-backup-restore.md) została wykonana ze starego klastra. Nie należy przywrócić wszystkie stanu usługi systemu, zostaną ponownie utworzone podczas wdrażania aplikacji do nowego klastra. Jeśli właśnie uruchomionych aplikacji bezstanowych w klastrze, a następnie wszystko, co możesz zrobić to wdrażanie aplikacji do nowego klastra, nie niczego do przywrócenia.
> 

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ węzła non-primary — wskazówki dotyczące pojemności dla obciążeń stanowych

Są to wskazówki dla obciążeń stanowych przy użyciu usługi Service fabric [niezawodnej kolekcje lub reliable Actors](service-fabric-choose-framework.md) działającą w typie podstawowym z systemem innym niż węzeł.

**Liczba wystąpień maszyn wirtualnych:** dla obciążeń produkcyjnych, które nie jest obiektem stanowym, zalecane jest, uruchom je wraz z liczbą repliki minimalna i docelowa 5. Oznacza to, że w stanie stabilności na końcu repliki (z zestawu replik) w każdej domenie błędów i domeny uaktualnień. Pojęcie warstwy niezawodności całego dla typu węzła podstawowego jest możliwość określenia tego ustawienia dla usług systemowych. Tak samą opłatą dotyczy Twoich stanowych usług.

Tak w przypadku obciążeń produkcyjnych minimalna zalecana z systemem innym niż — podstawowy typ rozmiaru węzła wynosi 5, jeśli używasz obciążeń stanowych w nim.

**Maszyna wirtualna SKU:** . to jest typ węzła którym działają usługi aplikacji, dlatego SKU maszyny Wirtualnej wybierz dla niego, należy wziąć pod uwagę obciążenia szczytowego planowane jest umieszczenie w każdym węźle. Wymagana pojemność elementu nodetype, zależy od obciążenia, który ma zostać uruchomiona w klastrze, dlatego firma Microsoft nie może zapewnić jakościowe wskazówek dla określonego obciążenia, jednak w tym miejscu jest szerokie wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych 

- Zalecaną WERSJĘ maszyny Wirtualnej jest standardowe D3 lub standardowy D3_V2 lub równoważne z co najmniej 14 GB lokalny dysk SSD.
- Minimalne obsługiwane użycie SKU maszyny Wirtualnej jest standardowe D1 lub standardowy D1_V2 lub równoważne z co najmniej 14 GB lokalny dysk SSD. 
- Częściowe podstawowej jednostki SKU maszyny Wirtualnej, podobnie jak standardowy A0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowy SKU A1 specjalnie nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ węzła non-primary — wskazówki dotyczące wydajności w przypadku obciążeń bezstanowych

W tych wskazówkach bezstanowe uruchomionych na nodetype innej niż podstawowa.

**Liczba wystąpień maszyn wirtualnych:** dla obciążeń produkcyjnych, które są bezstanowych, rozmiar typu minimalne obsługiwane z systemem innym niż podstawowy węzłami wynosi 2. Dzięki temu można przeprowadzić dwa bezstanowych wystąpienia aplikacji, dzięki czemu usługi na przetrwanie utraty wystąpienia maszyny Wirtualnej. 

**Maszyna wirtualna SKU:** . to jest typ węzła którym działają usługi aplikacji, dlatego SKU maszyny Wirtualnej wybierz dla niego, należy wziąć pod uwagę obciążenia szczytowego planowane jest umieszczenie w każdym węźle. Wymagana pojemność typu węzła zależy od obciążenia, który ma zostać uruchomiona w klastrze, dlatego firma Microsoft nie może zapewnić jakościowe wskazówek dla określonego obciążenia, jednak w tym miejscu jest szerokie wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych 

- Zalecaną WERSJĘ maszyny Wirtualnej jest standardowe D3 lub standardowy D3_V2 lub równoważnej. 
- Minimalna użycie obsługiwanych SKU maszyny Wirtualnej jest standardowe D1 lub standardowy D1_V2 lub równoważnej. 
- Częściowe podstawowej jednostki SKU maszyny Wirtualnej, podobnie jak standardowy A0 nie są obsługiwane w przypadku obciążeń produkcyjnych.
- Standardowy SKU A1 nie jest obsługiwana w przypadku obciążeń produkcyjnych ze względu na wydajność.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Kolejne kroki
Po zakończeniu, planowanie pojemności i konfigurowanie klastra, przeczytaj następujące artykuły:

* [Zabezpieczenia klastra sieci szkieletowej usług](service-fabric-cluster-security.md)
* [Skalowanie klastra sieci szkieletowej usług](service-fabric-cluster-scaling.md)
* [Planowanie odzyskiwania po awarii](service-fabric-disaster-recovery.md)
* [Relacja z elementów NodeType skali maszyny wirtualnej](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
