---
title: Planowanie pojemności klastra sieci szkieletowej usług
description: Zagadnienia dotyczące planowania pojemności klastra sieci szkieletowej usług. Warstwy typów węzłów, operacji, trwałości i niezawodności
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: pepogors
ms.openlocfilehash: 6e60fc10dd7e0eec24de4a089d09d914624dcfbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258917"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Zagadnienia dotyczące planowania pojemności klastra sieci szkieletowej usług
W przypadku wszystkich wdrożeń produkcyjnych planowanie pojemności jest ważnym krokiem. Oto niektóre elementy, które należy wziąć pod uwagę jako część tego procesu.

* Liczba typów węzłów, z które klaster musi się rozpocząć
* Właściwości każdego typu węzła (rozmiar, podstawowy, internetowy, liczba maszyn wirtualnych itp.)
* Charakterystyka niezawodności i trwałości klastra

> [!NOTE]
> Podczas planowania należy minimalnie przejrzeć wszystkie **niedozwolone** wartości zasad uaktualniania. Ma to na celu zapewnienie odpowiedniego ustawienia wartości i ograniczenie spalania klastra później z powodu niezmienionych ustawień konfiguracji systemu. 
> 

Przejrzyjmy pokrótce każdy z tych punktów.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Liczba typów węzłów, z które klaster musi się rozpocząć
Najpierw musisz dowiedzieć się, do czego będzie używany klaster, który tworzysz.  Jakie aplikacje planujesz wdrożyć w tym klastrze? Jeśli nie są jasne, na potrzeby klastra, najprawdopodobniej nie są jeszcze gotowe do wprowadzenia procesu planowania zdolności produkcyjnych.

Ustal liczbę typów węzłów, od które klaster musi zacząć.  Każdy typ węzła jest mapowany na zestaw skalowania maszyny wirtualnej. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. Tak więc decyzja o liczbie typów węzłów zasadniczo sprowadza się do następujących rozważań:

* Czy aplikacja ma wiele usług i czy którykolwiek z nich musi być publiczny lub internetowy? Typowe aplikacje zawierają usługę bramy frontonu, która odbiera dane wejściowe od klienta i co najmniej jedną usługę zaplecza, które komunikują się z usługami frontonu. W takim przypadku masz co najmniej dwa typy węzłów.
* Czy twoje usługi (które składają się na aplikację) mają różne potrzeby infrastrukturalne, takie jak większa pamięć RAM lub wyższe cykle procesora? Na przykład załóżmy, że aplikacja, którą chcesz wdrożyć zawiera usługę frontonalnej i usługę zaplecza. Usługa front-end może działać na mniejszych maszynach wirtualnych (rozmiarach maszyn wirtualnych, takich jak D2), które mają porty otwarte dla Internetu.  Usługa zaplecza jest jednak intensywnie obliczeniowa i musi działać na większych maszynach wirtualnych (o rozmiarach maszyn wirtualnych, takich jak D4, D6, D15), które nie są skierowane do Internetu.
  
  W tym przykładzie, chociaż można zdecydować się umieścić wszystkie usługi na jeden typ węzła, zaleca się umieszczenie ich w klastrze z dwóch typów węzłów.  Dzięki temu każdy typ węzła mają różne właściwości, takie jak łączność z Internetem lub rozmiar maszyny Wirtualnej. Liczba maszyn wirtualnych może być skalowana niezależnie, jak również.  
* Ponieważ nie można przewidzieć przyszłości, przejdź z faktów, które znasz i wybierz liczbę typów węzłów, które aplikacje muszą rozpocząć. Zawsze można dodać lub usunąć typy węzłów później. Klaster sieci szkieletowej usług musi mieć co najmniej jeden typ węzła.

## <a name="the-properties-of-each-node-type"></a>Właściwości każdego typu węzła
**Typ węzła** może być postrzegany jako odpowiednik ról w usługach w chmurze. Typy węzłów definiują rozmiary maszyn wirtualnych, liczbę maszyn wirtualnych i ich właściwości. Każdy typ węzła zdefiniowany w klastrze sieci szkieletowej usług jest mapowany na [zestaw skalowania maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  
Każdy typ węzła jest odrębnym zestawem skalowania i może być skalowany w górę lub w dół niezależnie, mają różne zestawy portów otwarte i mają różne metryki pojemności. Aby uzyskać więcej informacji na temat relacji między typami węzłów i zestawami skalowania maszyn wirtualnych, jak rdp w jednym z wystąpień, jak otwierać nowe porty i tak dalej, zobacz [Typy węzłów klastra sieci szkieletowej usług](service-fabric-cluster-nodetypes.md).

Klaster sieci szkieletowej usług może składać się z więcej niż jednego typu węzła. W takim przypadku klaster składa się z jednego typu węzła podstawowego i jednego lub więcej typów węzłów innych niż podstawowe.

Typ pojedynczego węzła nie może niezawodnie skalować poza 100 węzłów na skalę maszyny wirtualnej ustawioną dla aplikacji SF; osiągnięcie więcej niż 100 węzłów niezawodnie, będzie wymagać dodania dodatkowych zestawów skalowania maszyny wirtualnej.

### <a name="primary-node-type"></a>Typ węzła podstawowego

Usługi systemu sieci szkieletowej usług (na przykład usługa Menedżera klastrów lub usługa Image Store) są umieszczane w typie węzła podstawowego. 

![Zrzut ekranu przedstawiający klaster z dwoma typami węzłów][SystemServices]

* **Minimalny rozmiar maszyn wirtualnych** dla typu węzła podstawowego zależy od **warstwy trwałości,** którą wybierzesz. Domyślną warstwą trwałości jest brąz. Zobacz [charakterystyki trwałości klastra, aby](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) uzyskać więcej informacji.  
* **Minimalna liczba maszyn wirtualnych** dla typu węzła podstawowego jest określana przez warstwę **niezawodności,** którą wybierzesz. Domyślną warstwą niezawodności jest Silver. Zobacz [charakterystyki niezawodności klastra, aby](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) uzyskać więcej informacji.  

Z szablonu usługi Azure Resource Manager typ węzła podstawowego jest konfigurowany z atrybutem `isPrimary` w [definicji typu węzła](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object).

### <a name="non-primary-node-type"></a>Typ węzła innych niż podstawowy

W klastrze z wieloma typami węzłów istnieje jeden typ węzła podstawowego, a pozostałe nie są podstawowe.

* **Minimalny rozmiar maszyn wirtualnych** dla typów węzłów innych niż podstawowe zależy od **warstwy trwałości,** którą wybierzesz. Domyślną warstwą trwałości jest brąz. Aby uzyskać więcej informacji, zobacz [Charakterystyka trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).  
* **Minimalna liczba maszyn wirtualnych** dla typów węzłów innych niż podstawowe jest jeden. Należy jednak wybrać ten numer na podstawie liczby replik aplikacji/usług, które mają być uruchamiane w tym typie węzła. Liczba maszyn wirtualnych w typie węzła można zwiększyć po wdrożeniu klastra.

## <a name="the-durability-characteristics-of-the-cluster"></a>Charakterystyki trwałości klastra
Warstwa trwałości służy do wskazywania systemowi uprawnień, które mają maszyny wirtualne z podstawową infrastrukturą platformy Azure. W typie węzła podstawowego to uprawnienie umożliwia sieci szkieletowej usług wstrzymanie dowolnego żądania infrastruktury na poziomie maszyny Wirtualnej (takiego jak ponowne uruchomienie maszyny Wirtualnej, odzyskiwanie maszyn wirtualnych lub migracja maszyny wirtualnej), które mają wpływ na wymagania kworum dla usług systemowych i usług stanowych. W typach węzłów innych niż podstawowe to uprawnienie umożliwia sieci szkieletowej usług wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny Wirtualnej (takich jak ponowne uruchomienie maszyny Wirtualnej, ponowne zagospodarowanie maszyn wirtualnych i migracja maszyn wirtualnych), które mają wpływ na wymagania kworum dla usług stanowych.

| Poziom trwałości  | Wymagana minimalna liczba maszyn wirtualnych | Obsługiwane jednostki SKU maszyn wirtualnych                                                                  | Aktualizacje, które można wprowadzić do zestawu skalowania maszyny wirtualnej                               | Aktualizacje i konserwacja zainicjowane przez platformę Azure                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Złoty             | 5                              | Jednostki SKU z pełnym węzłem przeznaczone dla jednego klienta (na przykład L32s, GS5, G5, DS15_v2, D15_v2) | Może być opóźniony do momentu zatwierdzenia przez klaster sieci szkieletowej usług | Można wstrzymać na 2 godziny na UD, aby dać replikom dodatkowy czas na odzyskanie danych po wcześniejszych awariach |
| Srebrny           | 5                              | Maszyny wirtualne z jednego rdzenia lub powyżej z co najmniej 50 GB lokalnego SSD                      | Może być opóźniony do momentu zatwierdzenia przez klaster sieci szkieletowej usług | Nie można opóźnić przez dłuższy czas                                                    |
| Brązu           | 1                              | Maszyny wirtualne z co najmniej 50 GB lokalnego ssd                                              | Nie będzie opóźniony przez klaster sieci szkieletowej usług           | Nie można opóźnić przez dłuższy czas                                                    |

> [!WARNING]
> Typy węzłów z brązową trwałością nie uzyskują _żadnych uprawnień._ Oznacza to, że zadania infrastruktury, które mają wpływ na obciążenia stanowe nie zostaną zatrzymane lub opóźnione, co może mieć wpływ na obciążenia. Użyj tylko Brązowe dla typów węzłów, które uruchamiają tylko obciążenia bezstanowe. W przypadku obciążeń produkcyjnych zalecane jest uruchamianie programu Silver lub powyżej. 
> 
> Niezależnie od poziomu trwałości, operacja [alokacji transakcji](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/deallocate) na zestawie skalowania maszyny Wirtualnej spowoduje zniszczenie klastra

**Zalety stosowania poziomów trwałości srebra lub złota**
 
- Zmniejsza liczbę wymaganych kroków w operacji skalowania w (czyli dezaktywacji węzła i Remove-ServiceFabricNodeState jest wywoływana automatycznie).
- Zmniejsza ryzyko utraty danych z powodu zainicjowanej przez klienta operacji zmiany jednostki SKU maszyny Wirtualnej w miejscu lub operacji infrastruktury platformy Azure.

**Wady stosowania poziomów trwałości srebra lub złota**
 
- Wdrożenia do zestawu skalowania maszyny wirtualnej i innych powiązanych zasobów platformy Azure może być opóźnione, może limit czasu lub mogą być całkowicie zablokowane przez problemy w klastrze lub na poziomie infrastruktury. 
- Zwiększa liczbę [zdarzeń cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (na przykład podstawowych swapów) z powodu automatycznych dezaktywacji węzłów podczas operacji infrastruktury platformy Azure.
- Usuwa węzły z usługi na okresy czasu, gdy aktualizacje oprogramowania platformy Azure lub działania konserwacji sprzętu występują. Mogą być widoczne węzły o stanie Wyłączanie/Wyłączone podczas tych działań. Zmniejsza to tymczasowo pojemność klastra, ale nie powinno mieć wpływu na dostępność klastra lub aplikacji.

### <a name="recommendations-for-when-to-use-silver-or-gold-durability-levels"></a>Zalecenia dotyczące stosowania poziomów trwałości srebra lub złota

Użyj silver lub gold trwałość dla wszystkich typów węzłów, które obsługują usługi stanowe, które oczekujesz skalowania w (zmniejszyć liczbę wystąpień maszyn wirtualnych) często i wolisz, że operacje wdrażania są opóźnione i zdolności mają być zmniejszone na rzecz uproszczenia tych skalowania w Operacji. Scenariusze skalowania w poziomie (dodawanie wystąpień maszyn wirtualnych) nie są uwzględniane w wybranym przez użytkownika warstwie trwałości, tylko skala w.

### <a name="changing-durability-levels"></a>Zmiana poziomu trwałości
- Typy węzłów o poziomie trwałości srebra lub złota nie mogą zostać obniżone do brązowego.
- Aktualizacja z brązu na srebro lub złoto może potrwać kilka godzin.
- Podczas zmiany poziomu trwałości należy go zaktualizować zarówno w konfiguracji rozszerzenia sieci szkieletowej usług w zasobie zestawu skalowania maszyny wirtualnej, jak i w definicji typu węzła w zasobie klastra sieci szkieletowej usług. Te wartości muszą być zgodne.

### <a name="operational-recommendations-for-the-node-type-that-you-have-set-to-silver-or-gold-durability-level"></a>Zalecenia operacyjne dla typu węzła, który został ustawiony na poziom trwałości srebra lub złota.

- Utrzymuj klaster i aplikacje w dobrej kondycji przez cały czas i upewnij się, że aplikacje reagują na wszystkie [zdarzenia cyklu życia repliki usługi](service-fabric-reliable-services-lifecycle.md) (takie jak replika w kompilacji jest zablokowana) w odpowiednim czasie.
- Zastosuj bezpieczniejsze sposoby wprowadzania zmian w jednostce SKU maszyny Wirtualnej (Skalowanie w górę/w dół): Zmiana jednostki SKU maszyny wirtualnej zestawu skalowania maszyny wirtualnej wymaga kilku kroków i zagadnień. Oto proces, który można wykonać, aby uniknąć typowych problemów.
    - **W przypadku typów węzłów innych niż podstawowe:** Zaleca się utworzenie nowego zestawu skalowania maszyny wirtualnej, zmodyfikowanie ograniczenia umieszczania usługi w celu uwzględnienia nowego zestawu skalowania maszyny wirtualnej/typu węzła, a następnie zmniejszenie liczby wystąpień starego zestawu skalowania maszyny wirtualnej do zera, jeden węzeł naraz (ma to na celu upewnienie się, że usunięcie węzłów nie ma wpływu na niezawodność klastra).
    - **Dla typu węzła podstawowego:** Jeśli wybrana jednostka SKU maszyny Wirtualnej ma pojemność i chcesz zmienić jednostkę SKU na większą maszynę wirtualną, postępuj zgodnie z naszymi wskazówkami dotyczącymi [skalowania w pionie dla typu węzła podstawowego.](https://docs.microsoft.com/azure/service-fabric/service-fabric-scale-up-node-type) 

- Zachowaj minimalną liczbę pięciu węzłów dla dowolnego zestawu skalowania maszyny wirtualnej, który ma włączony poziom trwałości gold lub silver.
- Każdy zestaw skalowania maszyny wirtualnej z poziomem trwałości Silver lub Gold musi być mapowany na własny typ węzła w klastrze sieci szkieletowej usług. Mapowanie wielu zestawów skalowania maszyny wirtualnej na typ pojedynczego węzła uniemożliwi prawidłowe działanie koordynacji między klastrem sieci szkieletowej usług a infrastrukturą platformy Azure.
- Nie należy usuwać losowych wystąpień maszyn wirtualnych, zawsze należy używać funkcji skalowania w dół zestawu skalowania maszyny wirtualnej. Usunięcie losowych wystąpień maszyn wirtualnych może spowodować powstanie nierównowagi w wystąpieniu maszyny Wirtualnej rozłożone na UD i FD. Ta nierównowaga może niekorzystnie wpłynąć na zdolność systemów do prawidłowego równoważenia obciążenia między wystąpieniami usługi/replikami usługi.
- Jeśli używasz skalowania automatycznego, a następnie ustawić reguły takie, że skalowanie w (usuwanie wystąpień maszyn wirtualnych) są wykonywane tylko jeden węzeł naraz. Skalowanie w dół więcej niż jedno wystąpienie w czasie nie jest bezpieczne.
- W przypadku usuwania lub usuwania alokacji maszyn wirtualnych na typ węzła podstawowego, nigdy nie należy zmniejszać liczby przydzielonych maszyn wirtualnych poniżej poziomu wymaga warstwy niezawodności. Operacje te będą blokowane przez czas nieokreślony w skali zestawu z poziomem trwałości srebra lub złota.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Charakterystyka niezawodności klastra
Warstwa niezawodności służy do ustawiania liczby replik usług systemowych, które mają być uruchamiane w tym klastrze na typ węzła podstawowego. Im większa liczba replik, tym bardziej niezawodne są usługi systemowe w klastrze.  

Warstwa niezawodności może przyjmować następujące wartości:

* Platinum — uruchamianie usług systemowych z liczbą zestawów replik docelowych dziewięciu
* Złoto — uruchamianie usług systemowych z siedmioosobową liczbą zestawów replik docelowych
* Srebro — uruchamianie usług systemowych z docelową liczbą zestawów replik wynoszącą pięć 
* Brązowy — uruchamianie usług systemowych z docelową liczbą zestawów replik trzech

> [!NOTE]
> Wybrana warstwa niezawodności określa minimalną liczbę węzłów, które musi mieć typ węzła podstawowego. 
> 
> 

### <a name="recommendations-for-the-reliability-tier"></a>Zalecenia dotyczące warstwy niezawodności

Po zwiększeniu lub zmniejszeniu rozmiaru klastra (suma wystąpień maszyn wirtualnych we wszystkich typach węzłów) należy zaktualizować niezawodność klastra z jednej warstwy do drugiej. W ten sposób wyzwala uaktualnienia klastra potrzebne do zmiany liczby zestawów replik usług systemowych. Poczekaj na uaktualnienie w toku, aby zakończyć przed wprowadzeniem innych zmian w klastrze, takich jak dodawanie węzłów.  Postęp uaktualnienia w Eksploratorze sieci szkieletowej usług lub przy uruchomieniu [programu Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

Oto zalecenie dotyczące wyboru warstwy niezawodności.  Liczba węzłów źródłowych jest również ustawiona na minimalną liczbę węzłów dla warstwy niezawodności.  Na przykład dla klastra z niezawodnością Gold istnieje 7 węzłów źródłowych.

| **Liczba węzłów klastra** | **Warstwa niezawodności** |
| --- | --- |
| 1 |Nie należy określać parametru warstwy niezawodności, system oblicza go |
| 3 |Brązu |
| 5 lub 6|Srebrny |
| 7 lub 8 |Złoty |
| 9 i więcej |Platinum |

## <a name="primary-node-type---capacity-guidance"></a>Typ węzła podstawowego — wskazówki dotyczące pojemności

Oto wskazówki dotyczące planowania pojemności typu węzła podstawowego:

- **Liczba wystąpień maszyn wirtualnych do uruchomienia dowolnego obciążenia produkcyjnego na platformie Azure:** Należy określić minimalny rozmiar typu węzła podstawowego 5 i warstwę niezawodności silver.  
- **Liczba wystąpień maszyn wirtualnych do uruchamiania obciążeń testowych na platformie Azure** Można określić minimalny rozmiar typu węzła podstawowego 1 lub 3. Klaster jednego węzła, uruchamia się ze specjalną konfiguracją i tak, skalowanie z tego klastra nie jest obsługiwane. Klaster jednego węzła, nie ma niezawodności, a więc w szablonie Menedżera zasobów należy usunąć/nie określić tej konfiguracji (nie ustawienie wartości konfiguracji nie wystarczy). Jeśli skonfigurujesz klaster jednego węzła skonfigurowany za pośrednictwem portalu, konfiguracja zostanie automatycznie załatwina. Klastry jeden i trzy węzły nie są obsługiwane do uruchamiania obciążeń produkcyjnych. 
- **Jednostka SKU maszyny Wirtualnej:** Typ węzła podstawowego jest, gdzie działają usługi systemowe, więc jednostka SKU maszyny Wirtualnej wybrać dla niego, należy wziąć pod uwagę ogólne obciążenie szczytowe, które planujesz umieścić w klastrze. Oto analogia, aby zilustrować to, co mam na myśli - Pomyśl o typie węzła podstawowego jako o "Płucach", to właśnie dostarcza tlen do mózgu, a więc jeśli mózg nie ma wystarczającej ilości tlenu, twoje ciało cierpi. 

Ponieważ zapotrzebowanie na pojemność klastra zależy od obciążenia, które planujesz uruchomić w klastrze, nie możemy zapewnić ci wskazówek jakościowych dotyczących określonego obciążenia, jednak oto ogólne wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych: 

- Zaleca się dedykować klastry podstawowy NodeType do usług systemowych i użyć ograniczeń umieszczania do wdrażania aplikacji do pomocniczych NodeTypes.
- Zalecana jednostka SKU maszyny Wirtualnej to standardowa D2_V2 lub równoważna z co najmniej 50 GB lokalnego identyfikatora SSD.
- Minimalna obsługiwana jednostka SKU maszyny Wirtualnej jest Standard_D2_V3 lub standardowa D1_V2 lub równoważna z co najmniej 50 GB lokalnego ssd. 
- Nasza rekomendacja to minimum 50 GB. W przypadku obciążeń, szczególnie podczas uruchamiania kontenerów systemu Windows, wymagane są większe dyski. 
- Częściowe podstawowe jednostki SKU maszyn wirtualnych, takie jak standard A0, nie są obsługiwane dla obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane dla obciążeń produkcyjnych ze względu na wydajność.
- Maszyny wirtualne o niskim priorytecie nie są obsługiwane.

> [!WARNING]
> Zmiana rozmiaru jednostki SKU maszyny Wirtualnej węzła podstawowego w uruchomionym klastrze jest operacją skalowania i udokumentowaną w dokumentacji [skalowania zestawu skalowania maszyny wirtualnej.](virtual-machine-scale-set-scale-node-type-scale-out.md)

## <a name="non-primary-node-type---capacity-guidance-for-stateful-workloads"></a>Typ węzła innych niż podstawowe — wskazówki dotyczące pojemności dla obciążeń stanowych

Te wskazówki są dla obciążeń stanowych przy użyciu [sieci szkieletowej niezawodne kolekcje lub niezawodne podmioty,](service-fabric-choose-framework.md) które są uruchomione w typie węzła innych niż podstawowe.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są stanowe, zaleca się ich uruchomienie z minimalną i docelową liczbą replik wynoszącą 5. Oznacza to, że w stanie stacjonarnym kończy się z repliki (z zestawu replik) w każdej domenie błędów i domeny uaktualnienia. Cała koncepcja warstwy niezawodności dla typu węzła podstawowego jest sposobem określenia tego ustawienia dla usług systemowych. Tak więc to samo dotyczy usług stanowych, jak również.

Dlatego w przypadku obciążeń produkcyjnych minimalny zalecany rozmiar typu węzła niepodstawowego wynosi 5, jeśli są uruchomione obciążenia stanowe w nim.

**Jednostka SKU maszyny Wirtualnej:** Jest to typ węzła, w którym są uruchomione usługi aplikacji, więc jednostka SKU maszyny Wirtualnej wybrać dla niego, należy wziąć pod uwagę szczyt obciążenia, które planujesz umieścić w każdym węźle. Potrzeby w zakresie pojemności typu węzła są określane przez obciążenie, które ma być uruchamiane w klastrze, więc nie możemy zapewnić ci wskazówek jakościowych dotyczących określonego obciążenia, jednak oto ogólne wskazówki ułatwiające rozpoczęcie pracy

W przypadku obciążeń produkcyjnych 

- Zalecana jednostka SKU maszyny Wirtualnej to standardowa D2_V2 lub równoważna z co najmniej 50 GB lokalnego identyfikatora SSD.
- Minimalna obsługiwana jednostka SKU maszyny Wirtualnej jest Standard_D2_V3 lub standardowa D1_V2 lub równoważna z co najmniej 50 GB lokalnego ssd. 
- Częściowe podstawowe jednostki SKU maszyn wirtualnych, takie jak standard A0, nie są obsługiwane dla obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane dla obciążeń produkcyjnych ze względu na wydajność.

## <a name="non-primary-node-type---capacity-guidance-for-stateless-workloads"></a>Typ węzła innych niż podstawowy — wskazówki dotyczące pojemności dla obciążeń bezstanowych

Ta wskazówka obciążeń bezstanowych, które są uruchomione na typ węzła innych niż podstawowe.

**Liczba wystąpień maszyn wirtualnych:** W przypadku obciążeń produkcyjnych, które są bezstanowe, minimalny obsługiwany rozmiar typu węzła niepodstawowego wynosi 2. Dzięki temu można uruchomić dwa wystąpienia bezstanowe aplikacji i umożliwiając usługi przetrwać utratę wystąpienia maszyny Wirtualnej. 

**Jednostka SKU maszyny Wirtualnej:** Jest to typ węzła, w którym są uruchomione usługi aplikacji, więc jednostka SKU maszyny Wirtualnej wybrać dla niego, należy wziąć pod uwagę szczyt obciążenia, które planujesz umieścić w każdym węźle. Potrzeby pojemności typu węzła zależy od obciążenia, które planujesz uruchomić w klastrze. Nie możemy zapewnić Ci jakościowych wskazówek dotyczących konkretnego obciążenia pracą.  Jednak oto ogólne wskazówki, które pomogą Ci rozpocząć pracę.

W przypadku obciążeń produkcyjnych 

- Zalecana jednostka SKU maszyny Wirtualnej jest standardową D2_V2 lub równoważną. 
- Minimalna obsługiwana jednostka SKU maszyny Wirtualnej jest standardem D1 lub standardem D1_V2 lub równoważnym. 
- Częściowe podstawowe jednostki SKU maszyn wirtualnych, takie jak standard A0, nie są obsługiwane dla obciążeń produkcyjnych.
- Jednostki SKU maszyny wirtualnej serii nie są obsługiwane dla obciążeń produkcyjnych ze względu na wydajność.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Następne kroki
Po zakończeniu planowania zdolności produkcyjnych i skonfigurowaniu klastra przeczytaj następujące informacje:

* [Zabezpieczenia klastra sieci szkieletowej usług](service-fabric-cluster-security.md)
* [Skalowanie klastra sieci szkieletowej usług](service-fabric-cluster-scaling.md)
* [Planowanie odzyskiwania po awarii](service-fabric-disaster-recovery.md)
* [Relacja typów węzłów z zestawem skalowania maszyny wirtualnej](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
