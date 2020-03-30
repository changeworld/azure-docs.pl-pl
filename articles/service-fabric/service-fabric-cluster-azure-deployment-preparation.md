---
title: Planowanie wdrożenia klastra sieci szkieletowej usługi Azure
description: Dowiedz się więcej o planowaniu i przygotowywaniu do wdrożenia klastra sieci szkieletowej produkcyjnych w produkcji na platformie Azure.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193480"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planowanie i przygotowanie do wdrożenia klastra

Planowanie i przygotowanie do wdrożenia klastra produkcyjnego jest bardzo ważne.  Istnieje wiele czynników, które należy wziąć pod uwagę.  W tym artykule otrzymasz od kroków przygotowywania wdrożenia klastra.

## <a name="read-the-best-practices-information"></a>Zapoznaj się z informacjami o najlepszych praktykach
Aby pomyślnie zarządzać aplikacjami i klastrami sieci szkieletowej usług Azure, istnieją operacje, które zdecydowanie zalecamy wykonanie w celu optymalizacji niezawodności środowiska produkcyjnego.  Aby uzyskać więcej informacji, zobacz [Najlepsze wskazówki dotyczące aplikacji sieci szkieletowej usług i klastra](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Wybierz system operacyjny dla klastra
Sieci szkieletowej usług umożliwia tworzenie klastrów sieci szkieletowej usług na dowolnej maszynie wirtualnej lub komputerach z systemem Windows Server lub Linux.  Przed wdrożeniem klastra należy wybrać system operacyjny: Windows lub Linux.  Każdy węzeł (maszyna wirtualna) w klastrze działa w tym samym systemie operacyjnym, nie można mieszać maszyn wirtualnych z systemem Windows i Linux w tym samym klastrze.

## <a name="capacity-planning"></a>Planowanie pojemności
W przypadku wszystkich wdrożeń produkcyjnych planowanie pojemności jest ważnym krokiem. Poniżej przedstawiono niektóre zagadnienia, które należy uwzględnić w ramach tego procesu.

* Początkowa liczba typów węzłów dla klastra 
* Właściwości każdego typu węzła (rozmiar, liczba wystąpień, podstawowy, internetowy, liczba maszyn wirtualnych itp.)
* Charakterystyka niezawodności i trwałości klastra

### <a name="select-the-initial-number-of-node-types"></a>Wybieranie początkowej liczby typów węzłów
Najpierw musisz dowiedzieć się, do czego będzie używany klaster, który tworzysz. Jakie aplikacje planujesz wdrożyć w tym klastrze? Czy aplikacja ma wiele usług i czy którykolwiek z nich musi być publiczny lub internetowy? Czy twoje usługi (które składają się na aplikację) mają różne potrzeby infrastrukturalne, takie jak większa pamięć RAM lub wyższe cykle procesora? Klaster sieci szkieletowej usług może składać się z więcej niż jednego typu węzła: typu węzła podstawowego i jednego lub więcej typów węzłów innych niż podstawowe. Każdy typ węzła jest mapowany na zestaw skalowania maszyny wirtualnej. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. [Właściwości węzła i ograniczenia umieszczania][placementconstraints] można skonfigurować w celu ograniczenia określonych usług do określonych typów węzłów.  Aby uzyskać więcej informacji, zobacz [Liczba typów węzłów, z które klaster musi się rozpocząć.](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)

### <a name="select-node-properties-for-each-node-type"></a>Wybieranie właściwości węzła dla każdego typu węzła
Typy węzłów definiują jednostkę SKU maszyny Wirtualnej, liczbę i właściwości maszyn wirtualnych w skojarzonym zestawie skalowania.

Minimalny rozmiar maszyn wirtualnych dla każdego typu węzła jest określany przez [warstwę trwałości,][durability] którą wybierzesz dla typu węzła.

Minimalna liczba maszyn wirtualnych dla typu węzła podstawowego jest określana przez warstwę [niezawodności,][reliability] którą wybierzesz.

Zobacz minimalne zalecenia dotyczące [typów węzłów podstawowych,](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance) [obciążeń stanowych dla typów węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)i obciążeń [bezstanowych dla typów węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads).

Więcej niż minimalna liczba węzłów powinna być oparta na liczbie replik aplikacji/usług, które mają być uruchamiane w tym typie węzła.  [Planowanie pojemności aplikacji sieci szkieletowej](service-fabric-capacity-planning.md) usług pomaga oszacować zasoby potrzebne do uruchamiania aplikacji. Zawsze można skalować klaster w górę lub w dół później, aby dostosować się do zmiany obciążenia aplikacji. 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>Użyj efemerycznych dysków systemu operacyjnego dla zestawów skalowania maszyn wirtualnych

*Dyski efemerycznego systemu operacyjnego* są magazynem utworzonym na lokalnej maszynie wirtualnej (VM) i nie są zapisywane w zdalnym usłudze Azure Storage. Są one zalecane dla wszystkich typów węzłów sieci szkieletowej usług (podstawowy i pomocniczy), ponieważ w porównaniu do tradycyjnych dysków systemu operacyjnego trwałe, efemeryczne dyski systemu operacyjnego:

* Zmniejszanie opóźnienia odczytu/zapisu na dysku systemu operacyjnego
* Włącz szybsze operacje resetowania/ponownego zarządzania węzłami
* Zmniejsz ogólne koszty (dyski są bezpłatne i nie ponoszą żadnych dodatkowych kosztów magazynowania)

Efemeryczne dyski systemu operacyjnego nie jest określoną funkcją sieci szkieletowej usług, ale raczej funkcją *zestawów skalowania maszyny wirtualnej* platformy Azure, które są mapowane na typy węzłów sieci szkieletowej usług. Używanie ich z siecią szkieletową usług wymaga następujących elementów w szablonie usługi Azure Resource Manager klastra:

1. Upewnij się, że typy węzłów określają [obsługiwane rozmiary maszyn wirtualnych platformy Azure](../virtual-machines/windows/ephemeral-os-disks.md) dla dysków ephemeral OS i że rozmiar maszyny Wirtualnej ma wystarczający rozmiar pamięci podręcznej do obsługi rozmiaru dysku systemu operacyjnego (patrz *uwaga* poniżej). Na przykład:

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > Należy wybrać rozmiar maszyny Wirtualnej o rozmiarze pamięci podręcznej równym lub większym niż rozmiar dysku systemu operacyjnego samej maszyny Wirtualnej, w przeciwnym razie wdrożenie platformy Azure może spowodować błąd (nawet jeśli jest początkowo zaakceptowane).

2. Określ wersję zestawu skalowania maszyny wirtualnej (`vmssApiVersion`) lub nowszą: `2018-06-01`

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. W sekcji zestawu skalowania maszyny wirtualnej `Local` szablonu wdrożenia określ opcję dla: `diffDiskSettings`

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

Aby uzyskać więcej informacji i dalsze opcje konfiguracji, zobacz [Dyski systemu operacyjnego Efemeryczny dla maszyn wirtualnych platformy Azure](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Wybierz poziomy trwałości i niezawodności dla klastra
Warstwa trwałości służy do wskazywania systemowi uprawnień, które mają maszyny wirtualne z podstawową infrastrukturą platformy Azure. W typie węzła podstawowego to uprawnienie umożliwia sieci szkieletowej usług wstrzymanie dowolnego żądania infrastruktury na poziomie maszyny Wirtualnej (takiego jak ponowne uruchomienie maszyny Wirtualnej, odzyskiwanie maszyn wirtualnych lub migracja maszyny wirtualnej), które mają wpływ na wymagania kworum dla usług systemowych i usług stanowych. W typach węzłów innych niż podstawowe to uprawnienie umożliwia sieci szkieletowej usług wstrzymanie wszelkich żądań infrastruktury na poziomie maszyny Wirtualnej (takich jak ponowne uruchomienie maszyny Wirtualnej, ponowne zagospodarowanie maszyn wirtualnych i migracja maszyn wirtualnych), które mają wpływ na wymagania kworum dla usług stanowych.  Aby uzyskać zalety różnych poziomów i zaleceń dotyczących poziomu stosowania i kiedy, zobacz [Charakterystyka trwałości klastra][durability].

Warstwa niezawodności służy do ustawiania liczby replik usług systemowych, które mają być uruchamiane w tym klastrze na typ węzła podstawowego. Im większa liczba replik, tym bardziej niezawodne są usługi systemowe w klastrze.  Aby uzyskać zalety różnych poziomów i zaleceń dotyczących poziomu, na którym i kiedy, zobacz [Charakterystyka niezawodności klastra][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Włączanie odwrotnego serwera proxy i/lub dns
Usługi łączące się ze sobą wewnątrz klastra zazwyczaj mogą bezpośrednio uzyskiwać dostęp do punktów końcowych innych usług, ponieważ węzły w klastrze znajdują się w tej samej sieci lokalnej. Aby ułatwić łączenie się między usługami, sieci szkieletowej usług zapewnia dodatkowe usługi: [usługa DNS](service-fabric-dnsservice.md) i [odwrotnej usługi proxy](service-fabric-reverseproxy.md).  Obie usługi można włączyć podczas wdrażania klastra.

Ponieważ wiele usług, zwłaszcza konteneryzowanych usług, może mieć istniejącą nazwę adresu URL, możliwość ich rozwiązania przy użyciu standardowego protokołu DNS (zamiast protokołu usługi nazewnictwa) jest wygodna, szczególnie w scenariuszach "lift and shift" aplikacji. To jest dokładnie to, co robi usługa DNS. Umożliwia mapowanie nazw DNS na nazwę usługi, a tym samym rozpoznawanie adresów IP punktu końcowego.

Odwrócony serwer proxy adresuje usługi w klastrze, które ujawniają punkty końcowe HTTP (w tym HTTPS). Odwrotny serwer proxy znacznie upraszcza wywoływanie innych usług, zapewniając określony format identyfikatora URI.  Odwrotny serwer proxy obsługuje również kroki rozpoznawania, łączenia i ponawiania próby wymagane dla jednej usługi do komunikowania się z inną.

## <a name="prepare-for-disaster-recovery"></a>Przygotowywanie do odzyskiwania po awarii
Kluczową częścią dostarczania wysokiej dostępności jest zapewnienie, że usługi mogą przetrwać wszystkie rodzaje awarii. Jest to szczególnie ważne w przypadku awarii, które są nieplanowane i poza kontrolą. [Przygotuj się do odzyskiwania po awarii](service-fabric-disaster-recovery.md) opisuje niektóre typowe tryby awarii, które mogą być awarie, jeśli nie są poprawnie modelowane i zarządzane. Omówiono również środki zaradcze i działania do podjęcia, jeśli i tak doszło do katastrofy.

## <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji
Czy aplikacja i klaster są gotowe do podjęcia ruchu produkcyjnego? Przed wdrożeniem klastra w produkcji należy przeprowadzić [listę kontrolną Gotowość produkcyjna.](service-fabric-production-readiness-checklist.md) Utrzymuj bezproblemowe działanie aplikacji i klastra, pracując nad elementami na tej liście kontrolnej. Zdecydowanie zalecamy, aby wszystkie te elementy zostały sprawdzone przed przejściem do produkcji.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie klastra sieci szkieletowej usług z systemem Windows](service-fabric-best-practices-overview.md)
* [Tworzenie klastra sieci szkieletowej usług z systemem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster