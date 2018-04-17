---
title: Skalowanie klastra platformy Azure w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat skalowania usługi Service Fabric klastrów w lub wychodzących i górę lub w dół.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 897370e48b4eb0db5c99c1102910c89f6f56cc23
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="scaling-service-fabric-clusters"></a>Klastry skalowania usługi Service Fabric
Klaster sieci szkieletowej usług jest zestawem połączonych z siecią maszyn wirtualnych lub fizycznych, w których są wdrożone i zarządzane z mikrousług. Komputer lub maszynę Wirtualną, która jest częścią klastra jest nazywany węzłem. Klastry może zawierać potencjalnie tysiącach węzłów. Po utworzeniu klastra sieci szkieletowej usług, można skalować klastra w poziomie (zmianę liczby węzłów) lub w pionie (zmienić zasoby węzłów).  Można skalować klastra w dowolnym momencie, nawet jeśli obciążeń są uruchomione w klastrze.  Ponieważ skalowanie klastra, aplikacje automatycznie skalować również.

Dlaczego skalowanie klastra? Ulec zmianie przez aplikację.  Konieczne może być zwiększanie zasobów klastra, aby osiągnąć aplikacji zwiększenie obciążenia lub sieci ruchu lub Zmniejsz zasobów klastra, gdy żądanie powoduje.

### <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie i wylogowywanie lub skalowanie w poziomie
Zmiany liczby węzłów w klastrze.  Gdy nowe węzły dołączą do klastra, [Menedżera zasobów klastra](service-fabric-cluster-resource-manager-introduction.md) przenosi usług do nich, co zmniejsza obciążenie istniejących węzłów.  Można także zmniejszyć liczbę węzłów, jeśli zasobów klastra nie są wydajnie używane.  Jako węzły pozostaw klastra, usługi opuścić tych węzłów i załadować rośnie w pozostałych węzłach.  Zmniejszenie liczby węzłów w klastrze, które działają na platformie Azure można zmniejszyć koszty, ponieważ płacisz za liczbę maszyn wirtualnych można używany i nie obciążenia na tych maszynach wirtualnych.  

- Zalety: Nieskończone skali, teoretycznie.  Jeśli aplikacja jest przeznaczona dla skalowalność, możesz je włączyć nieograniczonego przyrostu, dodając więcej węzłów.  Narzędzia w środowiskach chmur ułatwia dodawanie lub usuwanie węzłów, więc łatwo Dopasuj pojemności i płacisz tylko za używanych zasobów.  
- Wady: Aplikacje muszą być [przeznaczoną do skalowania](service-fabric-concepts-scalability.md).  Bazy danych aplikacji i trwałości mogą wymagać dodatkowej pracy architektury można skalować również.  [Kolekcje niezawodnej](service-fabric-reliable-services-reliable-collections.md) w sieci szkieletowej usług stanowych usług, jednak znacznie ułatwić skalowanie danych aplikacji.

### <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasobów (procesor CPU, pamięć lub magazynu) węzły w klastrze.
- Zalety: Oprogramowania i architektura pozostaje taki sam.
- Wady: Ograniczone dużą skalę, ponieważ ma limitu ilości można zwiększyć zasobów na poszczególnych węzłach. Czas przestoju, ponieważ będą potrzebne do podjęcia maszyn fizycznych lub wirtualnych w tryb offline w celu dodawania lub usuwania zasobów.

## <a name="scaling-an-azure-cluster-in-or-out"></a>Skalowanie klastra platformy Azure przychodzący lub wychodzący
Zestawy skalowania maszyny wirtualnej są zasobu obliczeń platformy Azure, który służy do wdrażania i zarządzania nimi jako zestaw kolekcji maszyn wirtualnych. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła można skalować w lub wychodzących niezależnie, mają różne zestawy otwartych portów i może mieć inną pojemność metryki. 

Podczas skalowania klastra platformy Azure, pamiętać o następujących wytycznych:
- Typy węzła podstawowego uruchamiania obciążeń produkcyjnych powinien zawsze mieć pięć lub więcej węzłów.
- typy węzłów z systemem innym niż podstawowy uruchamiania obciążeń stanowych produkcji powinien zawsze mieć pięć lub więcej węzłów.
- typy węzłów z systemem innym niż podstawowy uruchamiania obciążeń bezstanowych produkcji powinien zawsze mieć co najmniej dwa węzły.
- Dowolny typ węzła [poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) złota lub Silver powinien zawsze mieć pięć lub więcej węzłów.
- Usuń losowe wirtualna wystąpień węzłów z typu węzła, nie zawsze używaj skalowania zestaw skali maszyny wirtualnej w dół funkcji. Usuwanie losowe wystąpień maszyn wirtualnych może niekorzystnie wpłynąć na możliwość systemów prawidłowo równoważenia obciążenia.
- Jeśli przy użyciu reguł automatycznego skalowania, ustawić reguły, dzięki czemu skalowania w (usuwanie wystąpień maszyn wirtualnych) odbywa się jednym węźle naraz. W czasie skalowania więcej niż jedno wystąpienie nie jest bezpieczne.

Ponieważ sieci szkieletowej usług typy węzłów w klastrze składają się z zestawach skali maszyn wirtualnych w wewnętrznej bazie danych, możesz [umożliwia konfigurowanie reguł automatycznego skalowania lub ręcznie skalować](service-fabric-cluster-scale-up-down.md) zestaw skali maszyny typu/wirtualnego każdego węzła.

### <a name="programmatic-scaling"></a>Skalowanie programowe
W wielu scenariuszach [skalowanie klastra ręcznie lub przy użyciu reguł automatycznego skalowania](service-fabric-cluster-scale-up-down.md) są dobrym rozwiązania. Dla bardziej zaawansowanych scenariuszy jednak mogą nie być rozwiązanie właściwe. Wady do tych metod uwzględnić:

- Ręcznie skalowanie należy zalogować się i jawne żądanie operacji skalowania. Operacje skalowania wymagane są często lub w czasie nieprzewidywalne, ta metoda nie może być dobrym rozwiązaniem.
- Usunięcie reguły automatycznego skalowania wystąpienia z zestawu skalowania maszyn wirtualnych, ich nie automatycznie usuwaj wiedzę na temat tego węzła z klastra usługi sieć szkieletowa skojarzone chyba, że typ węzła ma poziom trwałości Silver lub Gold. Ponieważ reguły automatycznego skalowania działają na dużą skalę, Ustaw poziom (a nie na poziomie sieci szkieletowej usług), reguły automatycznego skalowania można usunąć węzłów sieci szkieletowej usług bez zamykania bezpiecznie zamknąć. Usunięcie węzła niegrzeczny pozostawi "widma" stan węzła sieci szkieletowej usług po operacji w skali. Osoby (lub usługą) należy okresowo Wyczyść stan usuniętym węźle w klastrze usługi sieć szkieletowa usług.
- Typ węzła z poziomu trwałości Gold lub Silver automatycznie oczyszcza usuniętych węzłów, więc nie jest wymagane nie dodatkowe oczyszczanie.
- Mimo że istnieją [wiele metryk](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) obsługiwane przez reguły automatycznego skalowania, nadal jest ograniczony zestaw. Jeśli scenariusz wymaga skalowanie oparte na niektóre metryki nieuwzględnionego w tym zestawie, następnie reguły automatycznego skalowania nie może być dobrym rozwiązaniem.

Jak powinna wynosić skalowania usługi Service Fabric, zależy od danego scenariusza. W przypadku skalowania rzadko, możliwość dodawania lub usuwania węzłów ręcznie jest prawdopodobnie wystarczający. Dla bardziej złożonymi scenariuszami reguły automatyczne skalowanie i zestawy SDK udostępnia możliwość skalowania programowo oferują zaawansowane alternatyw.

Interfejsy API Azure istnieje, co umożliwia aplikacjom programowo pracować z maszyną wirtualną skalowanie zestawów i klastrów sieci szkieletowej usług. Jeśli istniejących opcji automatycznego skalowania nie działa dla danego scenariusza, te interfejsy API umożliwiają wdrożenie niestandardowej logiki skalowania. 

Jednym z podejść do wykonania tej "wprowadzone głównej" funkcji skalowania automatycznego jest dodania nowej usługi bezstanowej do aplikacji sieci szkieletowej usług do zarządzania operacjami skalowania. Tworzenie własnych skalowania usługi zapewnia najwyższy stopień kontroli i dostosowywalności za pośrednictwem aplikacji na skalowanie. Może to być przydatne w scenariuszach wymagających precyzyjną kontrolę, kiedy i jak aplikacja skaluje przychodzący lub wychodzący. Jednak ten formant pochodzi z zależnościami złożoności kodu. Przy użyciu tej metody oznacza, że konieczne do własnych skalowania kod, który jest nieuproszczony. W ramach usługi `RunAsync` metody, zestawu wyzwalaczy można określić, czy skalowanie jest wymagana (w tym sprawdzanie parametry, takie jak maksymalna wielkość klastra i skalowanie cooldowns).   

Interfejs API, używany do interakcji zestaw skali maszyny wirtualnej, (zarówno do sprawdzenia bieżąca liczba wystąpień maszyn wirtualnych, a następnie zmodyfikować go) jest [fluent biblioteki Azure zarządzania obliczeniowe](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Biblioteka fluent obliczeń zapewnia łatwy w użyciu interfejsu API do interakcji z zestawy skalowania maszyny wirtualnej.  Aby pracować interaktywnie z klastra usługi sieć szkieletowa, użyj [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Kod skalowania nie trzeba Uruchom jako usługi w klastrze, aby skalować, mimo że. Zarówno `IAzure` i `FabricClient` mogą łączyć się z zasobami platformy Azure skojarzone zdalnie, więc skalowania usługi można łatwo aplikacji konsoli lub usługa systemu Windows uruchomiony za pośrednictwem poza aplikacją sieci szkieletowej usług.

W oparciu o te ograniczenia, warto [wdrożenie więcej dostosowane automatycznego skalowania modele](service-fabric-cluster-programmatic-scaling.md).


## <a name="scaling-a-standalone-cluster-in-or-out"></a>Skalowanie klastra autonomiczny przychodzący lub wychodzący
Klastry autonomicznego umożliwiają wdrażanie usługi sieć szkieletowa klastra lokalnego lub w chmurze dostawcy wybranych przez użytkownika.  Typy węzłów składają się z maszyn fizycznych lub maszynach wirtualnych, w zależności od wdrożenia. W porównaniu do klastrów działających na platformie Azure, proces skalowanie klastra autonomiczny jest nieco więcej wysiłku.  Należy ręcznie zmienić liczbę węzłów w klastrze, a następnie uruchom uaktualnianie konfiguracji klastra.

Usuwanie węzłów może zainicjować kilku uaktualnień. Niektóre węzły są oznaczone ikoną z `IsSeedNode=”true”` tagu i mogą zostać zidentyfikowane przez wysyłanie zapytań do klastra manifestu za pomocą [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Usunięcie tych węzłów może trwać dłużej niż inne, ponieważ węzłów inicjatora będą musieli można przenosić w takich scenariuszach. Klaster musi obsługiwać co najmniej trzy węzły typu węzła podstawowego.

Podczas skalowania klastra autonomiczny, pamiętać o następujących wytycznych:
- Zastąpienie węzłów głównej powinny być wykonywane jeden węzeł po kolei, zamiast usuwania, a następnie dodanie w partiach.
- Przed usunięciem typu węzła, sprawdź, czy są we wszystkich węzłach, odwołanie do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu wszystkich odpowiadających im węzłów, można usunąć NodeType z konfiguracji klastra i rozpocząć konfigurację aktualizację, używając [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Aby uzyskać więcej informacji, zobacz [skalowanie klastra autonomiczny](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-an-azure-cluster-up-or-down"></a>Skalowanie klastra platformy Azure w górę lub w dół
Zestawy skalowania maszyny wirtualnej są zasobu obliczeń platformy Azure, który służy do wdrażania i zarządzania nimi jako zestaw kolekcji maszyn wirtualnych. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Następnie można zarządzać osobno każdy typ węzła.  Typ węzła skalowanie w górę lub w dół obejmuje zmiany jednostki SKU wystąpień maszyny wirtualnej w zestawie skalowania. 

> [!WARNING]
> Firma Microsoft zaleca, że należy zmieniać SKU maszyny Wirtualnej typu węzeł/zestaw skalowania, chyba że jest uruchomiona na [Silver trwałości lub większą](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjnego danych w miejscu infrastruktury. Możliwość opóźnienia lub monitora tę zmianę może to oznaczać, że operacji może spowodować utratę danych dla stanowych usług lub spowodować innych nieprzewidzianych problemów z działaniem, nawet w przypadku obciążeń bezstanowych. 
>

Podczas skalowania klastra platformy Azure, pamiętać o następujących wytycznych:
- Jeśli skalowania typu węzła podstawowego, to powinno nigdy nie go skalować w dół więcej niż co [warstwa niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) umożliwia.

Proces skalowania typ węzła w górę lub w dół różni się w zależności od tego, czy jest to typ innych niż podstawowe lub głównego węzła.

### <a name="scaling-non-primary-node-types"></a>Typy węzłów z systemem innym niż podstawowy skalowania
Tworzenie nowego typu węzła z zasobami, które są potrzebne.  Zaktualizuj ograniczenia dotyczące umieszczania uruchomionych usług w celu uwzględnienia nowego typu węzła.  Stopniowo (po jednym naraz), zmniejszyć liczby wystąpień starego liczba wystąpień typu węzła od zera, tak aby nie wpływa na niezawodność klastra.  Stary typ węzła jest decommisioned, usługi stopniowo będą migrowane do nowego typu węzła.

### <a name="scaling-the-primary-node-type"></a>Typ węzła podstawowego skalowania
Zaleca się, że nie należy zmieniać SKU wirtualna tego typu węzła podstawowego. Jeśli potrzebujesz większej pojemności klastra, firma Microsoft zaleca dodanie więcej wystąpień. 

Jeśli, nie jest możliwe, możesz utworzyć nowego klastra i [Przywróć stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie należy przywrócić wszystkie stanu usługi systemu, zostaną ponownie utworzone podczas wdrażania aplikacji do nowego klastra. Jeśli właśnie uruchomionych aplikacji bezstanowych w klastrze, a następnie wszystko, co możesz zrobić to wdrażanie aplikacji do nowego klastra, nie niczego do przywrócenia. Jeśli zdecydujesz się przejść nieobsługiwany trasy i chcesz zmienić jednostki SKU maszyny Wirtualnej, następnie utwórz modyfikacje skali maszyny wirtualnej ustawić definicję modelu w celu odzwierciedlenia nowej jednostki SKU. Jeśli klaster ma tylko jeden węzeł typu, następnie upewnij się, że wszystkie aplikacje stanowe odpowiadanie na wszystkie [usługi zdarzenia cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. repliki w kompilacji jest zablokowana) w odpowiednim czasie, który repliki usługi odbudować czas trwania jest mniej niż pięć minut (poziom trwałości srebrny). 

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalność aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure lub](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure obliczeniowe zestawu SDK.
* [Skalowanie klastra standaone przychodzący lub wychodzący](service-fabric-cluster-windows-server-add-remove-nodes.md).

