---
title: Skalowanie klastra sieci szkieletowej usług Azure
description: Dowiedz się więcej o skalowaniu klastrów sieci szkieletowej usług Azure w górę lub w dół. W miarę jak żądania aplikacji zmieniają się, więc klastry sieci szkieletowej usług mogą być obsługiwane przez te usługi.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: atsenthi
ms.openlocfilehash: 9dd60a5898b648215fc8b26e49a706a7b19dfeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258696"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Skalowanie klastrów sieci szkieletowej usług Azure
Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem. Klastry mogą zawierać potencjalnie tysiące węzłów. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie (zmieniać liczbę węzłów) lub w pionie (zmienić zasoby węzłów).  Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.  W miarę skalowania klastra aplikacje są również skalowane automatycznie.

Dlaczego warto skalować klaster? Wymagania aplikacji zmieniają się wraz z czasem.  Może być konieczne zwiększenie zasobów klastra w celu zaspokojenia zwiększonego obciążenia aplikacji lub ruchu sieciowego lub zmniejszenia zasobów klastra w przypadku spadku popytu.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie i wyskakowanie lub skalowanie w poziomie
Zmienia liczbę węzłów w klastrze.  Gdy nowe węzły dołączą do klastra, [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md) przenosi do nich usługi, które zmniejszają obciążenie istniejących węzłów.  Można również zmniejszyć liczbę węzłów, jeśli zasoby klastra nie są używane wydajnie.  Gdy węzły opuszczają klaster, usługi przenoszą się z tych węzłów i zwiększają obciążenie pozostałych węzłów.  Zmniejszenie liczby węzłów w klastrze uruchomionym na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę używanych maszyn wirtualnych, a nie za obciążenie tych maszyn wirtualnych.  

- Zalety: Nieskończona skala, teoretycznie.  Jeśli aplikacja jest przeznaczona do skalowalności, można włączyć nieograniczony wzrost, dodając więcej węzłów.  Narzędzia w środowiskach w chmurze ułatwiają dodawanie lub usuwanie węzłów, dzięki czemu można łatwo dostosować pojemność i płacić tylko za używane zasoby.  
- Wady: Aplikacje muszą być [zaprojektowane pod kątem skalowalności.](service-fabric-concepts-scalability.md)  Bazy danych aplikacji i trwałość może wymagać dodatkowych prac architektonicznych do skalowania, jak również.  [Niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md) w sieci szkieletowej usług stanowych usług, jednak znacznie ułatwiają skalowanie danych aplikacji.

Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure, którego można używać do wdrażania kolekcji maszyn wirtualnych i zarządzania nią jako zestaw. Każdy typ węzła zdefiniowany w klastrze platformy Azure jest [łączona jako oddzielny zestaw skalowania.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie skalować w lub na zewnątrz niezależnie, mają różne zestawy portów otwarte i może mieć różne metryki pojemności. 

Podczas skalowania klastra platformy Azure należy pamiętać o następujących wskazówkach:
- typy węzłów podstawowych z uruchomionymi obciążeniami produkcyjnymi powinny zawsze mieć co najmniej pięć węzłów.
- niepodstawowego typów węzłów z uruchomionymi stanowymi obciążeniami produkcyjnymi powinny zawsze mieć co najmniej pięć węzłów.
- niepodstawowego typów węzłów z uruchomionymi bezstanowymi obciążeniami produkcyjnymi powinny zawsze mieć dwa lub więcej węzłów.
- Każdy typ węzła [poziomu trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Złota lub Srebra powinien zawsze mieć pięć lub więcej węzłów.
- Nie należy usuwać losowych wystąpień/węzłów maszyn wirtualnych z typu węzła, zawsze należy używać funkcji skalowania w dół zestawu skalowania maszyny wirtualnej. Usunięcie losowych wystąpień maszyn wirtualnych może niekorzystnie wpłynąć na zdolność systemów do prawidłowego równoważenia obciążenia.
- Jeśli używasz reguł skalowania automatycznego, należy ustawić reguły tak, aby skalowanie w (usuwanie wystąpień maszyn wirtualnych) odbywa się jeden węzeł naraz. Skalowanie w dół więcej niż jedno wystąpienie w czasie nie jest bezpieczne.

Ponieważ typy węzłów sieci szkieletowej usług w klastrze są składa się z zestawów skalowania maszyny wirtualnej w wewnętrznej bazy danych, można [skonfigurować reguły automatycznego skalowania lub ręcznie skalować](service-fabric-cluster-scale-up-down.md) każdy typ węzła/zestaw skalowania maszyny wirtualnej.

### <a name="programmatic-scaling"></a>Skalowanie programowe
W wielu scenariuszach [skalowanie klastra ręcznie lub za pomocą reguł skalowania automatycznego](service-fabric-cluster-scale-up-down.md) są dobrymi rozwiązaniami. W przypadku bardziej zaawansowanych scenariuszy mogą one jednak nie być odpowiednie. Potencjalne wady tych podejść obejmują:

- Ręczne skalowanie wymaga zalogowania się i jawnego żądania operacji skalowania. Jeśli operacje skalowania są wymagane często lub w nieprzewidywalnych czasach, to podejście może nie być dobrym rozwiązaniem.
- Gdy reguły automatycznej skali usuwają wystąpienie z zestawu skalowania maszyny wirtualnej, nie usuwają automatycznie wiedzy o tym węźle ze skojarzonego klastra sieci szkieletowej usług, chyba że typ węzła ma poziom trwałości Silver lub Gold. Ponieważ reguły skalowania automatycznego działają na poziomie zestawu skalowania (a nie na poziomie sieci szkieletowej usług), reguły automatycznego skalowania mogą usuwać węzły sieci szkieletowej usług bez konieczności ich zamykania. To usunięcie niegrzecznego węzła pozostawi stan węzła sieci szkieletowej usługi "ghost" po operacjach skalowania w. Osoba (lub usługa) musiałaby okresowo czyścić stan usuniętego węzła w klastrze sieci szkieletowej usług.
- Typ węzła o poziomie trwałości Złota lub Srebra automatycznie czyści usunięte węzły, więc nie jest wymagane żadne dodatkowe oczyszczanie.
- Chociaż istnieje [wiele metryk obsługiwanych](../azure-monitor/platform/autoscale-common-metrics.md) przez reguły skalowania automatycznego, nadal jest to zestaw ograniczony. Jeśli scenariusz wymaga skalowania na podstawie niektórych metryki nie zostały uwzględnione w tym zestawie, a następnie reguły skalowania automatycznego może nie być dobrym rozwiązaniem.

Jak należy podejść skalowanie sieci szkieletowej usług zależy od scenariusza. Jeśli skalowanie jest rzadkością, możliwość ręcznego dodawania lub usuwania węzłów jest prawdopodobnie wystarczająca. W przypadku bardziej złożonych scenariuszy reguły automatycznej skali i zestawy SDK ujawniające możliwość programowania programowania oferują zaawansowane alternatywy.

Istnieją interfejsy API platformy Azure, które umożliwiają aplikacjom programową pracę z zestawami skalowania maszyn wirtualnych i klastrami sieci szkieletowej usług. Jeśli istniejące opcje skalowania automatycznego nie działają w przypadku scenariusza, te interfejsy API umożliwiają implementowanie niestandardowej logiki skalowania. 

Jednym z podejść do implementowania tej funkcji automatycznego skalowania "domowej produkcji" jest dodanie nowej usługi bezstanowej do aplikacji sieci szkieletowej usług w celu zarządzania operacjami skalowania. Tworzenie własnej usługi skalowania zapewnia najwyższy stopień kontroli i możliwości dostosowywania w zachowaniu skalowania aplikacji. Może to być przydatne w scenariuszach wymagających precyzyjnej kontroli nad tym, kiedy i jak aplikacja jest skalowana w lub na zewnątrz. Jednak ta kontrola pochodzi z kompromisu złożoności kodu. Za pomocą tego podejścia oznacza, że należy posiadać kod skalowania, który jest nietrywialny. W ramach `RunAsync` metody usługi zestaw wyzwalaczy może określić, czy wymagane jest skalowanie (w tym sprawdzanie parametrów, takich jak maksymalny rozmiar klastra i skalowanie czasu odnowienia).   

Interfejs API używany do interakcji zestawu zestawów skalowania maszyny wirtualnej (zarówno w celu sprawdzenia bieżącej liczby wystąpień maszyny wirtualnej, jak i jej zmodyfikowania) jest [płynną biblioteką obliczeń usługi Azure Management Compute.](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/) Płynna biblioteka obliczeniowa zapewnia łatwy w użyciu interfejs API do interakcji z zestawami skalowania maszyny wirtualnej.  Aby wchodzić w interakcje z samym klastrem sieci szkieletowej usług, należy użyć [pliku System.FabricClient](/dotnet/api/system.fabric.fabricclient).

Kod skalowania nie trzeba uruchamiać jako usługa w klastrze, aby być skalowane, choć. Oba `IAzure` `FabricClient` i można połączyć się z ich skojarzonych zasobów platformy Azure zdalnie, więc usługa skalowania może łatwo być aplikacją konsoli lub usługi systemu Windows uruchomiony spoza aplikacji sieci szkieletowej usług.

Na podstawie tych ograniczeń można [zaimplementować bardziej dostosowane modele automatycznego skalowania.](service-fabric-cluster-programmatic-scaling.md)

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasoby (procesora CPU, pamięci lub magazynu) węzłów w klastrze.
- Zalety: Architektura oprogramowania i aplikacji pozostaje taka sama.
- Wady: Skala skończona, ponieważ istnieje ograniczenie, ile można zwiększyć zasoby w poszczególnych węzłach. Przestoje, ponieważ należy wykonać maszyny fizyczne lub wirtualne w trybie offline w celu dodania lub usunięcia zasobów.

Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure, którego można używać do wdrażania kolekcji maszyn wirtualnych i zarządzania nią jako zestaw. Każdy typ węzła zdefiniowany w klastrze platformy Azure jest [łączona jako oddzielny zestaw skalowania.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie zarządzać oddzielnie.  Skalowanie typu węzła w górę lub w dół obejmuje zmianę jednostki SKU wystąpień maszyny wirtualnej w zestawie skalowania. 

> [!WARNING]
> Zaleca się, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu zestawu skalowania/węzła, chyba że działa ona z [trwałością silver lub większą.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest destrukcyjną dla danych operacją infrastruktury w miejscu. Bez pewnej możliwości opóźnienia lub monitorowania tej zmiany, jest możliwe, że operacja może spowodować utratę danych dla usług stanowych lub spowodować inne nieprzewidziane problemy operacyjne, nawet dla obciążeń bezstanowych. 
>

Podczas skalowania klastra platformy Azure należy pamiętać o następujących wytycznych:
- Jeśli skalowanie w dół typu węzła podstawowego, nigdy nie należy skalować go w dół więcej niż to, co pozwala na to [warstwa niezawodności.](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)

Proces skalowania typu węzła w górę lub w dół różni się w zależności od tego, czy jest to typ węzła innego niż podstawowy, czy podstawowy.

### <a name="scaling-non-primary-node-types"></a>Skalowanie typów węzłów innych niż podstawowe
Utwórz nowy typ węzła z potrzebnymi zasobami.  Zaktualizuj ograniczenia umieszczania uruchomionych usług, aby uwzględnić nowy typ węzła.  Stopniowo (po jednym na raz), zmniejszyć liczbę wystąpień starego węzła typu wystąpienia liczyć do zera, tak aby nie ma wpływu na niezawodność klastra.  Usługi będą stopniowo migrować do nowego typu węzła, gdy stary typ węzła zostanie wycofany.

### <a name="scaling-the-primary-node-type"></a>Skalowanie typu węzła podstawowego
Zaleca się, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu węzła podstawowego. Jeśli potrzebujesz więcej pojemności klastra, zaleca się dodanie większej liczby wystąpień. 

Jeśli to nie jest możliwe, można utworzyć nowy klaster i [przywrócić stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie trzeba przywracać żadnego stanu usługi systemowej, są one odtworzone podczas wdrażania aplikacji do nowego klastra. Jeśli po prostu uruchomiony bezstanowych aplikacji w klastrze, a następnie wszystko, co robisz, to wdrożyć aplikacje do nowego klastra, nie masz nic do przywrócenia. Jeśli zdecydujesz się przejść nieobsługiconą trasę i chcesz zmienić jednostkę SKU maszyny Wirtualnej, należy wprowadzić zmiany w definicji modelu zestawu skalowania maszyny wirtualnej, aby odzwierciedlić nową jednostkę SKU. Jeśli klaster ma tylko jeden typ węzła, upewnij się, że wszystkie aplikacje stanowe odpowiadają na wszystkie [zdarzenia cyklu życia repliki usługi](service-fabric-reliable-services-lifecycle.md) (takie jak replika w kompilacji jest zablokowany) w odpowiednim czasie i że czas trwania odbudowy repliki usługi jest krótszy niż pięć minut (dla poziomu trwałości silver). 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu płynnego zestawu SDK obliczeń platformy Azure.
* [Skalowanie autonomicznego klastra w lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

