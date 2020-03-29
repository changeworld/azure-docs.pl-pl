---
title: Skalowanie autonomicznego klastra usługi Azure Service Fabric
description: Dowiedz się więcej o skalowaniu autonomicznych klastrów sieci szkieletowej usług w, w górę i w dół.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451912"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skalowanie autonomicznych klastrów sieci szkieletowej usług
Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem. Klastry mogą zawierać potencjalnie tysiące węzłów. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie (zmieniać liczbę węzłów) lub w pionie (zmienić zasoby węzłów).  Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.  W miarę skalowania klastra aplikacje są również skalowane automatycznie.

Dlaczego warto skalować klaster? Wymagania aplikacji zmieniają się wraz z czasem.  Może być konieczne zwiększenie zasobów klastra w celu zaspokojenia zwiększonego obciążenia aplikacji lub ruchu sieciowego lub zmniejszenia zasobów klastra w przypadku spadku popytu.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie i wyskakowanie lub skalowanie w poziomie
Zmienia liczbę węzłów w klastrze.  Gdy nowe węzły dołączą do klastra, [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md) przenosi do nich usługi, które zmniejszają obciążenie istniejących węzłów.  Można również zmniejszyć liczbę węzłów, jeśli zasoby klastra nie są używane wydajnie.  Gdy węzły opuszczają klaster, usługi przenoszą się z tych węzłów i zwiększają obciążenie pozostałych węzłów.  Zmniejszenie liczby węzłów w klastrze uruchomionym na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę używanych maszyn wirtualnych, a nie za obciążenie tych maszyn wirtualnych.  

- Zalety: Nieskończona skala, teoretycznie.  Jeśli aplikacja jest przeznaczona do skalowalności, można włączyć nieograniczony wzrost, dodając więcej węzłów.  Narzędzia w środowiskach w chmurze ułatwiają dodawanie lub usuwanie węzłów, dzięki czemu można łatwo dostosować pojemność i płacić tylko za używane zasoby.  
- Wady: Aplikacje muszą być [zaprojektowane pod kątem skalowalności.](service-fabric-concepts-scalability.md)  Bazy danych aplikacji i trwałość może wymagać dodatkowych prac architektonicznych do skalowania, jak również.  [Niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md) w sieci szkieletowej usług stanowych usług, jednak znacznie ułatwiają skalowanie danych aplikacji.

Klastry autonomiczne umożliwiają wdrażanie klastra sieci szkieletowej usług lokalnie lub w wybranym dostawcy chmury.  Typy węzłów składają się z maszyn fizycznych lub maszyn wirtualnych, w zależności od wdrożenia. W porównaniu do klastrów uruchomionych na platformie Azure proces skalowania autonomicznego klastra jest nieco bardziej zaangażowany.  Należy ręcznie zmienić liczbę węzłów w klastrze, a następnie uruchomić uaktualnienie konfiguracji klastra.

Usunięcie węzłów może zainicjować wiele uaktualnień. Niektóre węzły są `IsSeedNode=”true”` oznaczone tagiem i można je zidentyfikować, przeprowadzając kwerendę w manifeście klastra za pomocą [programu Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Usuwanie takich węzłów może trwać dłużej niż inne, ponieważ węzły źródłowe będą musiały być przenoszone w takich scenariuszach. Klaster musi obsługiwać co najmniej trzy węzły typu węzła podstawowego.

> [!WARNING]
> Zaleca się, aby nie obniżać liczby węzłów poniżej [rozmiaru klastra warstwy niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) dla klastra. Spowoduje to zakłócenie możliwości replikacji usług systemu sieci szkieletowej sieci szkieletowej w klastrze i zdestabilizuje lub ewentualnie zniszczy klaster.
>

Podczas skalowania autonomicznego klastra należy pamiętać o następujących wskazówkach:
- Zastąpienie węzłów podstawowych należy wykonać jeden węzeł po drugim, zamiast usuwania, a następnie dodawanie w partiach.
- Przed usunięciem typu węzła sprawdź, czy istnieją węzły odwołujące się do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu wszystkich odpowiednich węzłów można usunąć typ węzła z konfiguracji klastra i rozpocząć uaktualnienie konfiguracji przy użyciu [funkcji Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Aby uzyskać więcej informacji, zobacz [skalowanie autonomicznego klastra](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasoby (procesora CPU, pamięci lub magazynu) węzłów w klastrze.
- Zalety: Architektura oprogramowania i aplikacji pozostaje taka sama.
- Wady: Skala skończona, ponieważ istnieje ograniczenie, ile można zwiększyć zasoby w poszczególnych węzłach. Przestoje, ponieważ należy wykonać maszyny fizyczne lub wirtualne w trybie offline w celu dodania lub usunięcia zasobów.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra platformy Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu płynnego zestawu SDK obliczeń platformy Azure.
* [Skalowanie autonomicznego klastra w lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

