---
title: Skalowanie klastra autonomicznego usługi Service Fabric platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat skalowania usługi Service Fabric autonomicznych klastrów w lub się i górę lub w dół.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 4a3b3cf128f9d03f93bbde7f1f6edd5b19328b03
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500580"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skalowanie klastrów autonomicznych usługi Service Fabric
Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem. Klastry mogą zawierać potencjalnie tysiącach węzłów. Po utworzeniu klastra usługi Service Fabric można skalować klastra w poziomie (zmienić liczbę węzłów), czy w pionie (zmienić zasoby węzłów).  Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.  Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

Dlaczego skalowanie klastra? Zmień zapotrzebowania aplikacji wraz z upływem czasu.  Może być konieczne zwiększenie zasobów klastra aplikacji zwiększenia obciążenia lub sieci natężenia ruchu lub zmniejszanie zasobów klastra, gdy zapotrzebowanie maleje.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie
Umożliwia zmianę liczby węzłów w klastrze.  Gdy nowe węzły dołączą do klastra, [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md) przenosi usług do nich, co zmniejsza obciążenie istniejących węzłów.  Jeśli klaster nie jest efektywne wykorzystanie zasobów, można także zmniejszyć liczbę węzłów.  Jako węzły klastra należy pozostawić, usługi odejścia od tych węzłów i obciążenia w pozostałych węzłach.  Zmniejszenie liczby węzłów w klastrze, działające na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę maszyn wirtualnych możesz użycia i nie obciążenie na tych maszynach wirtualnych.  

- Zalety: Nieskończone możliwości skalowania, teoretycznie.  Jeśli aplikacja jest przeznaczona dla skalowalność, nieograniczone możliwości wzrostu można włączyć poprzez dodanie kolejnych węzłów.  Narzędzia w środowiskach chmur ułatwia dodawanie lub usuwanie węzłów, dzięki czemu można łatwo dostosować pojemność i zapłacisz tylko za wykorzystane zasoby.  
- Wady: Aplikacje muszą być [przeznaczoną do skalowania](service-fabric-concepts-scalability.md).  Bazy danych aplikacji i trwałości mogą wymagać dodatkowej pracy architektury do skalowania, jak również.  [Elementy Reliable collections](service-fabric-reliable-services-reliable-collections.md) w stanowej usługi Service Fabric, jednak znacznie ułatwić skalowanie danych aplikacji.

Klastry autonomiczne umożliwia wdrażanie usługi Service Fabric klastra w środowisku lokalnym lub w wybranego dostawcy w chmurze.  Typy węzłów składają się z maszyn fizycznych lub maszyn wirtualnych, w zależności od wdrożenia. Proces skalowania klastra autonomicznego w porównaniu do klastrów działających na platformie Azure, jest nieco więcej wysiłku.  Należy ręcznie zmienić liczbę węzłów w klastrze, a następnie uruchom uaktualnianie konfiguracji klastra.

Usuwanie węzłów może zainicjować kilku uaktualnień. Niektóre węzły są oznaczone `IsSeedNode=”true”` tagu i mogą być identyfikowane przez wysyłanie zapytań do klastra manifestu za pomocą [Get ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Usunięcie tych węzłów może trwać dłużej niż inne, ponieważ węzły obiektów początkowych będą musieli można przenosić w takich scenariuszach. Klaster musi zachować co najmniej trzy węzły typu węzła podstawowego.

> [!WARNING]
> Firma Microsoft zaleca obniżają poniżej liczby węzłów [rozmiaru klastra z warstwy niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) klastra. Spowoduje to zakłócać zdolność usług usługi Service Fabric systemu być replikowane w klastrze i będzie zdestabilizować lub prawdopodobnie zniszczyć klaster.
>

Podczas skalowania klastra autonomicznego, należy przestrzegać następujących wytycznych:
- Zastąpienie podstawowe węzły powinna być wykonywane jeden węzeł po drugiej, zamiast usuwania, a następnie dodanie w partiach.
- Przed usunięciem typ węzła, sprawdź, czy wszystkie węzły, które odwołuje się do typu węzła. Przed usunięciem odpowiedni typ węzła, należy usunąć te węzły. Po usunięciu wszystkich odpowiednich węzłów można usunąć elementu NodeType z konfiguracji klastra i rozpocząć konfigurację uaktualnianie za pomocą [Start ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Aby uzyskać więcej informacji, zobacz [skalowanie klastra autonomicznego](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasobów (procesor CPU, pamięcią lub magazynem) z węzłów w klastrze.
- Zalety: Architektura oprogramowania i aplikacji pozostaje taka sama.
- Wady: Ograniczone skalowania, ponieważ ma ograniczenie ile może zwiększyć zasoby na poszczególnych węzłach. Czas przestoju, ponieważ będą potrzebne do podejmowania maszyn fizycznych lub wirtualnych w trybie offline w celu Dodawanie lub usuwanie zasobów.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra usługi Azure wewnątrz lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra usługi Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure compute zestawu SDK.
* [Skalowanie klastra autonomicznego wewnątrz lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

